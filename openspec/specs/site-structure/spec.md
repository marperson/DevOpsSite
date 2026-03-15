## ADDED Requirements

### Requirement: Navigation structure
The site SHALL have a consistent navigation header with links to Home, Blog, Portfolio, Knowledge Base, About, and Contact pages.

#### Scenario: Desktop navigation
- **WHEN** user visits the site on desktop
- **THEN** the navigation SHALL display all main sections in a horizontal header

#### Scenario: Mobile navigation
- **WHEN** user visits the site on mobile
- **THEN** the navigation SHALL collapse into a hamburger menu with vertical dropdown

### Requirement: Footer content
The site SHALL have a consistent footer with copyright information, social media links, and secondary navigation.

#### Scenario: Footer rendering
- **WHEN** user scrolls to the bottom of any page
- **THEN** they SHALL see the footer with current year copyright and social icons

#### Scenario: Footer links
- **WHEN** user clicks on a social media icon in the footer
- **THEN** they SHALL be taken to the corresponding external profile page

### Requirement: Responsive design
The site SHALL be fully responsive and adapt to different screen sizes (mobile, tablet, desktop) without horizontal scrolling.

#### Scenario: Mobile breakpoint
- **WHEN** viewing site on screen width less than 768px
- **THEN** content SHALL reflow to single column layout with appropriate padding

#### Scenario: Tablet breakpoint
- **WHEN** viewing site on screen width between 768px and 1024px
- **THEN** content SHALL use two-column layouts where appropriate

### Requirement: Page layouts
Each major section (Blog, Portfolio, Knowledge Base) SHALL have a consistent but distinct layout appropriate to its content type.

#### Scenario: Blog layout
- **WHEN** viewing the blog index
- **THEN** posts SHALL be displayed in cards with featured images, excerpts, and metadata

#### Scenario: Portfolio layout
- **WHEN** viewing the portfolio page
- **THEN** projects SHALL be displayed in a masonry or grid layout with filtering controls

### Requirement: Breadcrumb navigation
The site SHALL display breadcrumb navigation for pages deeper than two levels in the hierarchy.

#### Scenario: Knowledge article breadcrumbs
- **WHEN** viewing a knowledge base article
- **THEN** breadcrumbs SHALL show: Home > Knowledge Base > Category > Article Title

#### Scenario: Blog post breadcrumbs
- **WHEN** viewing a blog post
- **THEN** breadcrumbs SHALL show: Home > Blog > Category > Post Title

### Requirement: Site search
The site SHALL have a global search box in the header that searches across blog posts, portfolio items, and knowledge base articles.

#### Scenario: Global search accessibility
- **WHEN** user presses Ctrl+K (Cmd+K on Mac)
- **THEN** the search input SHALL receive focus for quick access

#### Scenario: Cross-section search results
- **WHEN** user performs a global search
- **THEN** results SHALL be grouped by content type (Blog, Portfolio, Knowledge)

### Requirement: Accessibility compliance
The site SHALL meet WCAG 2.1 AA accessibility standards for keyboard navigation, screen readers, and color contrast.

#### Scenario: Keyboard navigation
- **WHEN** user navigates the site using only keyboard
- **THEN** all interactive elements SHALL be reachable and operable

#### Scenario: Screen reader compatibility
- **WHEN** using a screen reader
- **THEN** all content SHALL be properly announced with semantic HTML structure