## ADDED Requirements

### Requirement: Markdown-based blog posts
Blog posts SHALL be written in markdown format with frontmatter metadata including title, date, author, categories, and tags.

#### Scenario: Create new blog post
- **WHEN** author creates a new markdown file in the content/posts directory with frontmatter
- **THEN** the post appears in the blog index with correct metadata displayed

#### Scenario: Invalid frontmatter handling
- **WHEN** a markdown file has missing required frontmatter fields
- **THEN** the build process SHALL fail with a descriptive error message

### Requirement: Categorization and tagging
Blog posts SHALL support assignment to one or more categories and multiple tags for organization.

#### Scenario: Filter posts by category
- **WHEN** user navigates to a category page (e.g., /categories/kubernetes)
- **THEN** only posts assigned to that category SHALL be displayed

#### Scenario: Tag cloud display
- **WHEN** user views the tag cloud page
- **THEN** all tags used across posts SHALL be displayed with post counts

### Requirement: Publication metadata
Blog posts SHALL display publication date, last modified date (if different), and estimated reading time.

#### Scenario: Reading time calculation
- **WHEN** a blog post is rendered
- **THEN** an estimated reading time SHALL be displayed based on word count

#### Scenario: Date formatting
- **WHEN** a blog post is displayed
- **THEN** the publication date SHALL be formatted as "Month Day, Year" (e.g., "March 15, 2026")

### Requirement: Chronological ordering
Blog index and category pages SHALL display posts in reverse chronological order (newest first).

#### Scenario: New post appears first
- **WHEN** a new blog post is published
- **THEN** it SHALL appear at the top of the blog index page

### Requirement: Pagination support
The blog index SHALL support pagination when the number of posts exceeds a configurable limit (default: 10 posts per page).

#### Scenario: Multiple pages navigation
- **WHEN** there are more than 10 blog posts
- **THEN** the blog index SHALL display pagination controls with page numbers

#### Scenario: Page URL structure
- **WHEN** user navigates to page 2 of the blog
- **THEN** the URL SHALL be /page/2/ and display posts 11-20