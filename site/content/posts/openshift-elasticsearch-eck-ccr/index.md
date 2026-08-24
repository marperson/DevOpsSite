---
title: "Running Elasticsearch on OpenShift with ECK: Ingress, East-West Networking, and CCR"
date: 2026-08-24
tags: ["elasticsearch", "openshift", "eck", "kubernetes", "ccr", "observability"]
description: "A practical guide to deploying Elasticsearch with ECK on OpenShift, exposing it through T2 and East-West gateways, and validating two-cluster cross-cluster replication."
featuredImage: "feature.png"
draft: false
---

# Running Elasticsearch on OpenShift with ECK

This article documents a two-cluster Elasticsearch deployment on OpenShift using [Elastic Cloud on Kubernetes (ECK)](https://www.elastic.co/guide/en/cloud-on-k8s/current/index.html). The environment has a working T2 Ingress Gateway for north-south traffic, an East-West Gateway for cluster-to-cluster traffic, and Elasticsearch cross-cluster replication (CCR) operating successfully.

A production-ready design must establish reliable storage, TLS, controlled access, predictable network paths, and evidence that changes are captured, replicated, and applied on the target cluster.

## Architecture

```mermaid
flowchart LR
    Client[Application or operator]
    T2[T2 Ingress Gateway]
    Source[OpenShift Cluster A\nSource Elasticsearch]
    EW[East-West Gateway\nPrivate cluster path]
    Target[OpenShift Cluster B\nTarget Elasticsearch]

    Client --> T2 --> Source
    Source --> EW --> Target
```

The T2 gateway is the north-south entry point for approved clients. The East-West Gateway provides the private path between the two OpenShift clusters. CCR uses that path to pull changes from the leader index in the source cluster into a follower index in the target cluster.

CCR is an Elasticsearch feature, not an ECK custom resource. ECK manages the clusters and certificates; CCR is configured through the Elasticsearch API. Exact endpoints and licensing requirements depend on the Elasticsearch version and subscription.

## Prerequisites

Confirm the following before installing Elasticsearch:

- Two OpenShift clusters with routable, policy-approved connectivity.
- A supported ECK version for the selected Elasticsearch version.
- A storage class with sufficient capacity and IOPS.
- DNS names and certificates for the T2 and East-West gateway endpoints.
- NetworkPolicies, firewall rules, and gateway routes allowing only required traffic.
- An Elastic subscription that includes CCR for the deployed version.

The examples use `openshift-operators` for ECK and `logging` for Elasticsearch. Change these names to match your platform standards.

## Install ECK

Pin the operator version rather than using a floating production manifest:

```bash
oc create -f https://download.elastic.co/downloads/eck/2.16.1/crds.yaml
oc apply -f https://download.elastic.co/downloads/eck/2.16.1/operator.yaml
oc get pods -n openshift-operators
oc new-project logging
```

ECK creates and rotates the internal TLS material used by the Elasticsearch HTTP and transport services. Keep TLS enabled; disabling it only hides certificate and gateway problems until later.

## Deploy Elasticsearch with ECK

Apply this baseline to both clusters. Adjust node counts, resources, and storage for the workload:

```yaml
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: logs
  namespace: logging
spec:
  version: 8.15.3
  nodeSets:
    - name: data
      count: 3
      config:
        node.roles: [master, data, ingest]
      podTemplate:
        spec:
          containers:
            - name: elasticsearch
              resources:
                requests:
                  cpu: "2"
                  memory: 8Gi
                limits:
                  cpu: "4"
                  memory: 8Gi
      volumeClaimTemplates:
        - metadata:
            name: elasticsearch-data
          spec:
            accessModes: [ReadWriteOnce]
            storageClassName: fast-ssd
            resources:
              requests:
                storage: 200Gi
```

Validate both deployments:

```bash
oc get elasticsearch -n logging
oc get pods -n logging
oc get svc -n logging
oc get secret logs-es-elastic-user -n logging
PASSWORD=$(oc get secret logs-es-elastic-user -n logging \
  -o go-template='{{.data.elastic | base64decode}}')
```

Use the ECK-managed service internally, or expose it through a gateway that preserves HTTPS and forwards to the Elasticsearch HTTP port. Do not expose the transport port publicly.

## T2 Ingress Gateway

The T2 route is the north-south entry point for approved clients such as operators, CI jobs, and applications. Configure it to:

1. Terminate or pass through TLS according to the certificate policy.
2. Forward only to the ECK HTTP service.
3. Restrict methods and source networks where possible.
4. Apply authentication and request-size limits.
5. Preserve the host and authorization headers required by Elasticsearch.

Test the route from an approved client:

```bash
curl --fail --user "elastic:${PASSWORD}" \
  "https://search.example.com/_cluster/health?pretty"
```

The route should return cluster health without exposing pod or Kubernetes service addresses.

## East-West Gateway

The East-West Gateway carries replication traffic from Cluster A to Cluster B. Give each cluster a stable DNS name, for example:

```text
cluster-a-search.example.net
cluster-b-search.example.net
```

The path must support HTTPS to the Elasticsearch HTTP endpoint, certificate validation, DNS resolution, and the expected request timeout:

```bash
curl --fail --user "elastic:${PASSWORD}" \
  "https://cluster-b-search.example.net/_cluster/health?pretty"
```

Configure firewall rules and NetworkPolicies for the smallest required source and destination set. In production, use a dedicated CCR user with minimum privileges instead of the `elastic` superuser. Store credentials in a secret or gateway-managed credential store.

## Configure CCR

CCR requires a leader index in Cluster A and a follower index in Cluster B. Register Cluster A as a remote cluster from Cluster B using the CCR API supported by your Elasticsearch version. A representative flow is:

```bash
# On the target cluster, configure the remote connection.
curl --fail --user "elastic:${PASSWORD}" -X PUT \
  "https://cluster-b-search.example.net/_cluster/settings" \
  -H 'Content-Type: application/json' \
  -d '{
    "persistent": {
      "cluster.remote.cluster_a.seeds": ["cluster-a-search.example.net:443"]
    }
  }'

# On the target cluster, create a follower.
curl --fail --user "elastic:${PASSWORD}" -X PUT \
  "https://cluster-b-search.example.net/test-index-target/_ccr/follow" \
  -H 'Content-Type: application/json' \
  -d '{
    "remote_cluster": "cluster_a",
    "leader_index": "test-index-source"
  }'
```

The endpoint shape varies by Elasticsearch release and CCR setup. The `/_ccr/replication` endpoint sometimes shown in internal test notes is not a universal Elasticsearch API. Confirm the actual request using the CCR documentation for the installed version.

Create the leader index and document on Cluster A:

```bash
curl --fail --user "elastic:${PASSWORD}" -X PUT \
  "https://cluster-a-search.example.net/test-index-source"

curl --fail --user "elastic:${PASSWORD}" -X PUT \
  "https://cluster-a-search.example.net/test-index-source/_doc/1" \
  -H 'Content-Type: application/json' \
  -d '{"name":"John","email":"john@example.com"}'

curl --fail --user "elastic:${PASSWORD}" \
  "https://cluster-b-search.example.net/test-index-target/_doc/1"
```

Follower indices are read-only on the target side while following. Updates and deletes must be performed on the leader index.

## CCR Test Suite

Record document IDs, timestamps, cluster names, Elasticsearch versions, and observed lag for every test.

### Basic replication

| ID | Test | Expected result |
| --- | --- | --- |
| TC01 | Create an index on the source | Follower has the expected mapping and settings. |
| TC02 | Create a document on the source | Document appears on target within the agreed SLO. |
| TC03 | Delete a document on the source | Deletion is reflected on target. |

### Write operations

| ID | Test | Expected result |
| --- | --- | --- |
| TC04 | Update a source document | Target reflects the updated `_source`. |
| TC05 | Send bulk writes | All accepted items eventually appear on target. |
| TC06 | Insert a document with `_source` | Document exists on both clusters. |
| TC07 | Delete by ID | Same ID is absent from target. |

Example update:

```bash
curl --fail --user "elastic:${PASSWORD}" -X POST \
  "https://cluster-a-search.example.net/test-index-source/_update/1" \
  -H 'Content-Type: application/json' \
  -d '{"doc":{"name":"Jane","email":"jane@example.com"}}'

curl --fail --user "elastic:${PASSWORD}" \
  "https://cluster-b-search.example.net/test-index-target/_doc/1"
```

### Consistency and latency

| ID | Test | Expected result |
| --- | --- | --- |
| TC08 | Measure write-to-read replication lag | Lag remains below the agreed threshold, such as five seconds. |
| TC09 | Read immediately after a source write | Test records eventual consistency; an immediate miss may be expected. |
| TC10 | Perform concurrent writes | All successful writes replicate without loss or duplication. |

```bash
curl --fail --user "elastic:${PASSWORD}" -X PUT \
  "https://cluster-a-search.example.net/test-index-source/_doc/lag-1" \
  -H 'Content-Type: application/json' \
  -d "{\"written_at\":\"$(date -u +%s)\"}"

for i in {1..30}; do
  if curl -fsS --user "elastic:${PASSWORD}" \
    "https://cluster-b-search.example.net/test-index-target/_doc/lag-1" | grep -q '"found":true'; then
    break
  fi
  sleep 1
done
```

### Network failure and recovery

| ID | Test | Expected result |
| --- | --- | --- |
| TC11 | Make source unreachable from target | Replication pauses and exposes a clear error. |
| TC12 | Make target unreachable | Source writes continue; target catches up after recovery. |
| TC13 | Restore connectivity | Replication resumes automatically or reports the required resume action. |

Use a controlled firewall rule or gateway policy in a test environment. Do not manipulate production DNS or firewall rules to simulate failure.

### Configuration and edge cases

| ID | Test | Expected result |
| --- | --- | --- |
| TC14 | Change supported follower or remote settings | Setting is accepted and its effect is observable. |
| TC15 | Pause, unfollow, or remove CCR | Target stops receiving changes and reports final state. |
| TC16 | Add a new source index and follower | New index replicates according to policy. |
| TC17 | Use an invalid index or remote name | Request fails with a clear validation error. |
| TC18 | Repeat an idempotent write with the same ID | Final document state is consistent. |
| TC19 | Write a large document within limits | Replication succeeds or gives an explicit size error. |
| TC20 | Apply a compatible mapping change | Behavior matches the installed version and CCR constraints. |

Do not assume every mapping change can propagate to an existing follower. Test only changes supported by the deployed Elasticsearch version; recreate the follower when required.

## Monitoring and validation

```bash
# Cluster health
curl --fail --user "elastic:${PASSWORD}" \
  "https://cluster-a-search.example.net/_cluster/health?pretty"

# Remote-cluster connectivity
curl --fail --user "elastic:${PASSWORD}" \
  "https://cluster-b-search.example.net/_remote/info?pretty"

# CCR follow information and stats
curl --fail --user "elastic:${PASSWORD}" \
  "https://cluster-b-search.example.net/test-index-target/_ccr/info?pretty"
curl --fail --user "elastic:${PASSWORD}" \
  "https://cluster-b-search.example.net/test-index-target/_ccr/stats?pretty"

# Compare counts after catch-up
curl --fail --user "elastic:${PASSWORD}" \
  "https://cluster-a-search.example.net/test-index-source/_count"
curl --fail --user "elastic:${PASSWORD}" \
  "https://cluster-b-search.example.net/test-index-target/_count"
```

Alert on follower failures, increasing lag, disconnected remote clusters, unassigned shards, disk watermarks, and gateway TLS or authorization errors. Count equality is useful but not sufficient; compare representative IDs and document content too.

## Recommended execution order

Run TC01-TC03, then TC04-TC07. Establish normal latency with TC08-TC10 before testing invalid inputs or large documents. Run TC17-TC20 next, then TC11-TC13 in a controlled environment. Finish with TC14-TC16 and document the exact pause, resume, and cleanup behavior for the installed version.

## Conclusion

ECK provides the Kubernetes lifecycle and certificate management, the gateways provide controlled north-south and east-west paths, and CCR provides asynchronous replication. The deployment is complete only when all three layers are validated together. A repeatable test suite makes replication lag, failure recovery, and version-specific behavior visible before the platform carries production data.
