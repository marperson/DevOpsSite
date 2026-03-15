## ADDED Requirements

### Requirement: Knowledge article structure
Knowledge base articles (KTs) SHALL be organized with clear hierarchy: categories → subcategories → articles.

#### Scenario: Category navigation
- **WHEN** user visits the knowledge base
- **THEN** they SHALL see top-level categories with article counts

#### Scenario: Article metadata
- **WHEN** viewing a knowledge article
- **THEN** it SHALL display author, last updated date, and estimated reading time

### Requirement: Search functionality
The knowledge repository SHALL support full-text search across all articles.

#### Scenario: Basic search
- **WHEN** user enters search terms in the knowledge base search box
- **THEN** relevant articles SHALL be returned ranked by relevance

#### Scenario: Search filters
- **WHEN** user performs a search
- **THEN** they SHALL be able to filter results by category, date range, and reading time

### Requirement: Article formatting
Knowledge articles SHALL support rich formatting including code blocks, diagrams, tables, and callouts for best practices, warnings, and tips.

#### Scenario: Code block rendering
- **WHEN** an article contains a code block with language specification
- **THEN** it SHALL be rendered with syntax highlighting

#### Scenario: Best practice callout
- **WHEN** an article contains a best practice callout
- **THEN** it SHALL be visually distinguished with an appropriate icon and background

### Requirement: Content organization
Knowledge articles SHALL be taggable with relevant technologies, difficulty levels (beginner/intermediate/advanced), and related concepts.

#### Scenario: Tag-based filtering
- **WHEN** user clicks on a technology tag within an article
- **THEN** they SHALL see all articles tagged with that technology

#### Scenario: Difficulty level indication
- **WHEN** viewing a knowledge article
- **THEN** its difficulty level SHALL be clearly indicated (e.g., "Beginner", "Advanced")

### Requirement: Related articles
Each knowledge article SHALL display links to related articles based on shared tags, categories, or content similarity.

#### Scenario: Automatic related articles
- **WHEN** viewing a knowledge article
- **THEN** a "Related Articles" section SHALL suggest other relevant content

#### Scenario: Manual article linking
- **WHEN** author creates a knowledge article
- **THEN** they SHALL be able to manually specify related articles in the frontmatter

### Requirement: Table of contents
Long knowledge articles SHALL automatically generate a table of contents based on heading hierarchy.

#### Scenario: TOC generation
- **WHEN** an article has multiple heading levels (H2, H3, etc.)
- **THEN** a clickable table of contents SHALL be generated with anchor links

#### Scenario: Mobile TOC behavior
- **WHEN** viewing on mobile devices
- **THEN** the table of contents SHALL be collapsible to save screen space