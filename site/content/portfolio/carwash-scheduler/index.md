---
title: "Car Wash Scheduler"
date: 2026-03-15
tags: ["python", "flask", "docker", "web-app", "community"]
description: "A Flask web application for managing shared car wash card scheduling among neighbors"
draft: false
showHero: true
heroStyle: "background"
featuredImage: "feature.png"
---

# Car Wash Scheduler

A lightweight Flask web application built to help neighbors share a Petro Canada car wash card by coordinating weekly schedules.

**Live Demo:** [carwashscheduler.onrender.com](https://carwashscheduler.onrender.com/)
**Source Code:** [GitHub](https://github.com/frankfanghe/CarWashScheduler)

---

## Problem Statement

Our neighborhood shares a Petro Canada car wash card, but coordinating who uses it each day was becoming chaotic. We needed a simple, accessible way for everyone to see and update the weekly schedule without complex authentication or setup.

---

## Solution

Built a minimal Flask application with:

- **Simple weekly schedule table** (Monday-Sunday)
- **Dropdown selection** for pre-configured neighbor names
- **Comment field** for shift notes
- **Automatic weekly reset** every Monday at 1:00 AM
- **Mobile-responsive design** for on-the-go access
- **No database required** - uses local JSON storage

---

## Technical Stack

- **Backend:** Python Flask
- **Storage:** JSON file (no external database)
- **Scheduling:** APScheduler for automatic weekly resets
- **Deployment:** Docker container on Render.com
- **Frontend:** Responsive HTML/CSS with background image

---

## Key Features

### 1. Weekly Auto-Reset
Background scheduler automatically resets the schedule to default values every Monday at 1:00 AM, ensuring a fresh start each week.

### 2. Simple Data Model
```python
{
  'id': 1,
  'day': 'Monday',
  'name': '壬子',
  'comment': ''
}
```

### 3. Docker Deployment
Containerized for easy deployment across platforms:
```bash
docker build -t carwashscheduler:latest .
docker run -p 8000:8000 carwashscheduler:latest
```

### 4. Mobile-First Design
Responsive UI works seamlessly on desktop and mobile devices, allowing neighbors to check and update schedules from anywhere.

---

## Architecture

The application follows a simple architecture:

- **Flask app** handles HTTP requests and JSON persistence
- **APScheduler** runs background task for weekly reset
- **Local JSON file** stores schedule data
- **Gunicorn** serves the app in production
- **Docker** packages everything for deployment

---

## Deployment

Deployed on Render.com using Docker:

1. Built multi-platform Docker image (linux/amd64)
2. Pushed to Docker Hub: `frankfanghe/carwashscheduler:latest`
3. Deployed to Render.com with automatic restarts
4. Accessible at: https://carwashscheduler.onrender.com/

---

## Challenges & Solutions

**Challenge:** Ensuring only one scheduler instance runs
**Solution:** Used threading with proper initialization to prevent duplicate reset timers

**Challenge:** Data persistence without a database
**Solution:** Atomic JSON file writes with temporary file + rename pattern

**Challenge:** Mobile accessibility
**Solution:** Responsive CSS design with viewport meta tags

---

## Impact

- **7 neighbors** now coordinate seamlessly
- **Zero conflicts** since deployment
- **Weekly automation** saves manual coordination effort
- **Mobile access** enables real-time updates

---

## Technologies Used

- Python 3
- Flask
- APScheduler
- Docker
- Gunicorn
- Render.com
- HTML/CSS

---

## Future Enhancements

- Add notification system for schedule changes
- Implement user authentication for edit permissions
- Add schedule history/audit log
- Support multiple car wash locations

---

## Lessons Learned

1. **Keep it simple** - JSON storage was sufficient for this use case
2. **Mobile-first matters** - Most users access from their phones
3. **Automation is key** - Weekly reset eliminated manual coordination
4. **Docker simplifies deployment** - One container, multiple platforms

---

This project demonstrates how a simple web application can solve real community coordination problems with minimal complexity.
