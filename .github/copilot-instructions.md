# Copilot Instructions for Hugo Digital Garden Theme

## Repository Overview

This is a **Hugo static site theme** called "Digital Garden" that creates a personal website with three main content types:
- **Digital Garden/Blog**: Articles with growth status (🌱 seeding, 🌿 growing, 🌳 evergreen)
- **Projects Portfolio**: Showcase of personal projects with featured images
- **Library Notes**: Book reviews and reading notes

The theme is inspired by Maggie Appleton's digital garden concept and includes responsive design, dark mode, filtering capabilities, and modern web technologies.

## Technology Stack

- **Hugo**: Static site generator (min version 0.81.0)
- **Bootstrap 5**: CSS framework for responsive design
- **FontAwesome 6**: Icon library
- **jQuery**: DOM manipulation
- **Isotope.js**: Filtering and layout library
- **Mermaid**: Diagram rendering
- **Masonry**: Grid layout library

## Architecture & File Structure

### Core Layout Structure
```
layouts/
├── _default/
│   ├── baseof.html          # Main HTML structure template
│   ├── single.html          # Individual content pages
│   └── list.html            # Default list template
├── index.html               # Homepage template
├── garden/
│   └── list.html            # Garden articles with filtering
├── projects/
│   └── list.html            # Projects grid layout
├── library/
│   ├── list.html            # Book library grid
│   └── single.html          # Individual book pages
└── partials/
    ├── head.html            # HTML head section
    ├── header.html          # Navigation bar
    ├── footer.html          # Footer with social links
    ├── script.html          # JavaScript includes
    ├── card_garden.html     # Garden article cards
    ├── card_project.html    # Project cards
    ├── card_book.html       # Book cards
    ├── home_garden.html     # Homepage garden preview
    ├── home_project.html    # Homepage project preview
    ├── status.html          # Growth status indicators
    └── metadata.html        # Page metadata
```

### Content Organization
- **Garden**: Blog-style articles in `/content/garden/` with growth status metadata
- **Projects**: Portfolio items in `/content/projects/` with featured images and links
- **Library**: Book notes in `/content/library/` with author metadata
- **About**: Static about page in `/content/about/`

## Hugo/Go Templating Patterns

### Variable Handling with Safety Checks
```go
// Standard pattern for conditional content
{{ with .Site.Params.googleAnalytics }}
<script async src="https://www.googletagmanager.com/gtag/js?id={{ . }}"></script>
{{ end }}

// Date formatting with fallback defaults
{{ $dateFormat := .Site.Params.dateFormat | default "Jan 2, 2006" }}
{{ .Lastmod.Format $dateFormat }}

// Title generation with context awareness
{{ $title := print .Site.Title " | " .Title }}
{{ if .IsHome }}{{ $title = .Site.Title }}{{ end }}
```

### Image Processing Patterns
```go
// Featured image pattern used throughout the theme
{{ $img := (.Resources.ByType "image").GetMatch "*featured*" }}
{{ $img = $img.Fit "350x500" }}
{{ with $img }}
<img src="{{ .Permalink }}" alt="{{ $.Title }}" class="card-img-top">
{{ end }}
```

### Scratch Variables for Dynamic Classes
```go
// Building dynamic CSS classes for isotope filtering
{{ .Scratch.Set "garden_tags_list" ""}}
{{ range .Params.garden_tags }}
    {{ $.Scratch.Add "garden_tags_list" ( . | printf " js-iso-%s") }}
{{ end }}
<div class='col {{ $.Scratch.Get "garden_tags_list" }}'>
```

### Menu and Navigation Patterns
```go
// Active menu item highlighting
{{- $currentPage := . -}}
{{ range .Site.Menus.main -}}
<li class='nav-item {{ if $currentPage.IsMenuCurrent "main" . }}active{{ end }}'>
    <a class="nav-link" href="{{ .URL }}">{{ .Name }}</a>
</li>
{{ end }}
```

## Content Frontmatter Standards

### Garden Articles (Blog Posts)
```yaml
---
title: "Article Title"
date: 2024-01-01
lastmod: 2024-01-15
draft: false
garden_tags: ["tag1", "tag2"]  # Used for isotope filtering
summary: "Brief description shown in cards"
status: "seeding"  # Options: seeding|growing|evergreen
---
```

### Projects
```yaml
---
title: "Project Name"
date: 2024-01-01
draft: false
project_tags: ["technology", "design"]
status: "evergreen"
weight: 2  # Controls display order (lower = higher priority)
summary: "Project description for cards"
links:  # External links with FontAwesome icons
    github:
        text: "View Code"
        icon: "fab fa-github"
        href: "https://github.com/username/repo"
        weight: 1
    demo:
        text: "Live Demo"
        icon: "fas fa-external-link-alt"
        href: "https://demo.example.com"
        weight: 2
---
```

### Library Items (Books)
```yaml
---
title: "Book Title"
author: "Author Name"
date: 2024-01-01
draft: false
library_tags: ["fiction", "technology", "productivity"]
summary: "Your thoughts or review summary"
---
```

### Required Images
- **Projects**: `featured.png` or `featured.jpg` in project bundle folder
- **Library**: `featured.jpg` (book cover) in book bundle folder
- **About**: `featured.jpg` for about page header

## CSS Architecture & Design System

### CSS Custom Properties (Root Variables)
```css
:root {
    /* Color Palette */
    --blue: #0A94C8;
    --lighter-blue: #addbec;
    --yellow: #FFB603;
    --red: #FF2F03;
    --dark: #233044;
    --grey: #454963;
    --white: #fff;
    --offwhite: #fafafa;
    
    /* Typography */
    --title: 'Mulish', sans-serif;      # For names and titles
    --summary: 'Frank Ruhl Libre', serif; # For body text and bios
    --headings: 'Encode Sans Semi Condensed', sans-serif; # For headings
}
```

### Key CSS Classes and Components
```css
/* Card Component Pattern */
.card {
    border-radius: 4px;
    background: var(--offwhite);
    box-shadow: 0 6px 10px rgba(0,0,0,.08);
    transition: 1s transform cubic-bezier(.155,1.105,.295,1.12);
}

.card:hover {
    transform: scale(1.05);
    box-shadow: 0 10px 20px rgba(0,0,0,.12);
}

/* Status Indicator Styling */
.status {
    font-weight: lighter;
    color: green;
    font-size: small;
}

/* Typography Classes */
.serif {
    font-family: var(--summary);
    color: var(--grey);
}

.name {
    font-family: var(--title);
    font-weight: 800;
}
```

### Responsive Design Patterns
- Uses Bootstrap 5 grid system
- Mobile-first approach with `@media(max-width: 990px)` breakpoints
- Cards stack vertically on mobile devices
- Navigation collapses to hamburger menu

## JavaScript Functionality

### Dark Mode Implementation
**File**: `static/js/dark.js`
```javascript
// Theme persistence using localStorage
localStorage.setItem("dark-mode-storage", mode);

// CSS filter-based dark mode in dark.css
filter: invert(100%) hue-rotate(180deg) brightness(105%) contrast(85%);
```

### Isotope Filtering System
**File**: `static/js/isotope.js`
```javascript
// Multiple checkbox filtering for garden tags
var inclusives = [];
$checkboxes.each(function(i, elem) {
    if (elem.checked) {
        inclusives.push(elem.id);
    }
});
var filterValue = inclusives.length ? inclusives.join(', ') : '*';
$blogposts.isotope({ filter: filterValue });
```

### Script Loading Order
1. Bootstrap JS
2. jQuery
3. Isotope (filtering library)
4. Masonry (layout)
5. Dark mode script
6. Mermaid (diagrams)
7. Custom isotope configuration

## Shortcodes Available

### Image Processing Shortcode
```hugo
{{< image "filename.jpg" "Fit" "500x300" >}}
Optional caption text here
{{< /image >}}
```
**Commands**: Fit, Resize, Fill, Crop
**Options**: "widthxheight" format

### Mermaid Diagrams
```hugo
{{< mermaid align="center" >}}
graph TD
    A[Start] --> B[Process]
    B --> C[End]
{{< /mermaid >}}
```

### Layout Helpers
```hugo
{{< row >}}
{{< column >}}
Content for left column
{{< /column >}}
{{< column >}}
Content for right column
{{< /column >}}
{{< /row >}}
```

## Configuration Standards

### Essential Site Parameters
```toml
[params]
  name = "Your Full Name"
  description = "Your bio/tagline"
  initials = "YN"  # Used in navbar if preferred over full name
  googleAnalytics = "GA-MEASUREMENT-ID"
  darkModeDefault = "light"  # Options: light|dark

# Social media links with FontAwesome icons
[[params.social]]
  name = "GitHub"
  link = "https://github.com/username"
  icon = "fab fa-github"

[[params.social]]
  name = "Twitter"
  link = "https://twitter.com/username"
  icon = "fab fa-twitter"
```

### Required Taxonomies
```toml
[taxonomies]
  garden_tags = 'garden_tags'    # For garden article filtering
  project_tags = 'project_tags'  # For project categorization
  library_tags = 'library_tags'  # For book categorization
```

### Menu Configuration
```toml
[menu]
  [[menu.main]]
    name = "Digital Garden"
    url = "/garden/"
    weight = 1
  [[menu.main]]
    name = "Projects"
    url = "/projects/"
    weight = 2
  [[menu.main]]
    name = "Library"
    url = "/library/"
    weight = 3
  [[menu.main]]
    name = "About"
    url = "/about/"
    weight = 4
```

## Development Guidelines

### Adding New Content Types
1. **Create Directory**: Add new section under `/content/newsection/`
2. **Layout Template**: Create `/layouts/newsection/list.html` and optionally `single.html`
3. **Partial Cards**: Create `/layouts/partials/card_newsection.html` for consistent styling
4. **Taxonomy**: Add to `config.toml` if filtering needed
5. **Navigation**: Update menu configuration
6. **Homepage**: Optionally add preview section to `layouts/index.html`

### Styling Best Practices
- **Use CSS Custom Properties**: Reference existing `--variable-name` patterns
- **Follow Bootstrap**: Leverage existing grid and component classes
- **Maintain Consistency**: Use established card, button, and typography patterns
- **Test Dark Mode**: Ensure new styles work with CSS filter inversion
- **Mobile First**: Test responsive behavior on small screens

### Content Creation Workflow
1. **Use Archetypes**: Hugo automatically applies frontmatter from `/archetypes/default.md`
2. **Bundle Resources**: Place images in same folder as `index.md` for page bundles
3. **Featured Images**: Name primary image `featured.jpg` or `featured.png`
4. **Status Progression**: Start with "seeding", move to "growing", finalize as "evergreen"
5. **Tag Consistently**: Use existing tags when possible for better filtering

### Performance Considerations
- **Image Processing**: Hugo automatically optimizes images via `.Fit`, `.Resize` etc.
- **External Dependencies**: Most CSS/JS loaded from CDN for caching
- **Lazy Loading**: Theme supports modern browser lazy loading
- **Minification**: CSS and JS automatically minified in production builds

### Accessibility Features
- **Semantic HTML**: Uses proper heading hierarchy and ARIA labels
- **Color Contrast**: Meets WCAG guidelines for text readability
- **Keyboard Navigation**: All interactive elements accessible via keyboard
- **Screen Readers**: Proper alt text and descriptive link text

### Common Customizations
- **Colors**: Modify CSS custom properties in `static/css/style.css`
- **Fonts**: Update font imports in `layouts/partials/head.html`
- **Custom Styling**: Add to `static/css/my_style.css` (automatically loaded)
- **Additional Scripts**: Include in `layouts/partials/script.html`

### Troubleshooting
- **Isotope Filtering**: Ensure `garden_tags` exactly match class names in CSS
- **Images Not Showing**: Check file names match `*featured*` pattern
- **Dark Mode Issues**: Test CSS filter compatibility with custom styles
- **Build Errors**: Verify Hugo version meets minimum requirement (0.81.0+)

This theme follows Hugo best practices and modern web development standards, making it maintainable and extensible for digital garden and portfolio websites.
