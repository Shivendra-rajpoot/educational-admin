# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Kiaalap is a modern education management dashboard that has been fully modernized from a legacy Bootstrap 4 template to a Bootstrap 5 + Vite build system. It contains 65 HTML pages using Handlebars templating with a consistent, modular architecture.

## Build System & Commands

```bash
# Development server (port 3000, auto-opens browser)
npm run dev

# Production build (outputs to dist/)
npm run build

# Preview production build
npm run preview

# Linting
npm run lint:html    # HTML validation
npm run lint:css     # SCSS linting with Stylelint
npm run lint:js      # JavaScript linting with ESLint

# Code formatting
npm run format       # Prettier formatting for src/**/*.{js,scss,html}

# Clean build directory
npm run clean
```

## Architecture

### Core Technologies

- **Framework**: Bootstrap 5.3.8 (100% jQuery-free, except Simple-DataTables)
- **Build Tool**: Vite 7.1.7 with vite-plugin-handlebars 2.0
- **Charts**: Chart.js 4.5.0 (replaced legacy Morris.js/C3/D3)
- **Data Tables**: Simple-DataTables 10.0 (vanilla JS, replaced jQuery DataTables)
- **Icons**: Bootstrap Icons 1.13.1 (loaded from node_modules, no CDN)
- **CSS**: Sass preprocessor with PostCSS (autoprefixer, cssnano)

### File Structure
```
kiaalap/
├── src/
│   ├── css/              # Custom stylesheets (dashboard.css, charts-layout.css)
│   ├── js/               # JavaScript modules (dashboard.js, charts-responsive.js, etc.)
│   ├── partials/         # Handlebars partials (*.hbs)
│   ├── helpers/          # Handlebars helpers (handlebars-helpers.js)
│   └── scss/             # Sass source files
├── node_modules/         # NPM packages (all assets load locally from here)
├── *.html               # 65 HTML pages using {{> partials}}
├── vite.config.js       # Vite + Handlebars configuration
└── package.json         # Dependencies and scripts
```

### Handlebars Template System

**Partials Structure** (`src/partials/*.hbs`):

- `{{> head}}` - Meta tags, title, CSS imports from node_modules
- `{{> sidebar}}` - Navigation sidebar with collapsible submenus
- `{{> header}}` - Top header with search, notifications, user dropdown
- `{{> footer}}` - Footer with current year and copyright
- `{{> scripts}}` - Bootstrap and core JavaScript imports from node_modules

**Context Management** (`vite.config.js`):

The `getPageContext(filename)` function provides dynamic context to each page:

- **Navigation States**: Sets active menu and expanded submenu based on filename patterns
- **Page Configuration**: Provides title, pageTitle, pageDescription, breadcrumb array
- **Additional Resources**: Specifies extra CSS/JS files needed (e.g., charts pages load Chart.js)
- **Base Context**: Includes currentYear, meta tags, user object with messages/notifications

**Available Handlebars Helpers** (`src/helpers/handlebars-helpers.js`):

- `eq` - Equality comparison for conditional rendering (e.g., `{{#if (eq page 'index')}}`)
- `isActive` - Check if current page matches target
- `exists` - Check if value exists and is not empty
- `formatDate` - Date formatting (short/long)
- `truncate` - Text truncation with suffix
- `conditionalClass` - Dynamic CSS classes
- `uniqueId` - Generate unique ID with optional prefix
- `json` - JSON stringify for debugging
- Math: `add`, `subtract`, `multiply`
- Array: `length`, `first`, `last`
- String: `lowercase`, `uppercase`, `startsWith`, `endsWith`, `contains`

### JavaScript Architecture

**Core JavaScript** (`src/js/`):

- `dashboard.js` - Main dashboard functionality: sidebar toggle, search, responsive behavior, Bootstrap component initialization
- `charts-responsive.js` - Responsive chart handling for Chart.js across dashboard pages
- `dashboard-modules.js` - Additional dashboard-specific modules
- `layout.js` - Layout management and utilities
- `main.js` - Global entry point

**Key JavaScript Patterns**:

- **DOMContentLoaded**: All scripts wait for DOM ready before initializing
- **localStorage**: Sidebar collapsed state persists across sessions (desktop only)
- **Responsive Handling**: Different behaviors for mobile (≤768px) vs desktop
  - Mobile: Sidebar opens as overlay with backdrop
  - Desktop: Sidebar collapses/expands in place
- **Bootstrap Components**: Tooltips, popovers, collapses initialized via Bootstrap 5 API
- **Navigation**: Active states managed by comparing `window.location.pathname` with link hrefs

### Key Modernization Changes

1. **Bootstrap 5 Migration**: All pages converted from Bootstrap 4 to Bootstrap 5
2. **jQuery Removal**: 100% jQuery-free! Vanilla JavaScript everywhere (except Simple-DataTables dependency)
3. **Local Dependencies**: All CDN references replaced with node_modules imports
4. **Consistent Grid System**: Custom dashboard-grid classes with responsive breakpoints
5. **Year Updates**: All dates updated to 2025/current year
6. **Spacing Standards**: 24px margin-bottom for dashboard cards

## Page Categories & Navigation States

Navigation states are automatically set based on filename patterns in `vite.config.js`:

- **Dashboard** (`dashboard.*`): index, index-1, index-2, analytics, widgets
- **Academic** (`academic.*`):
  - Professors: professor-profile, all-professors, add-professor, edit-professor
  - Students: all-students, student-profile, add-student, edit-student
  - Courses: all-courses, course-info, add-course, edit-course, course-payment
  - Library: library-assets, add-library-assets, edit-library-assets
  - Departments: departments, add-department, edit-department
- **Interface** (`interface.*`):
  - Components: buttons, alerts, modals, tabs, accordion
  - Forms: basic-form-element, advance-form-element, password-meter, multi-upload, images-cropper
  - Charts: bar-charts, line-charts, area-charts, c3, peity, sparkline, rounded-chart
  - Tables: static-table, data-table
- **Communication**: mailbox, mailbox-compose, mailbox-view
- **Developer Tools** (`tools.*`):
  - UI: preloader, notifications, tree-view
  - Viewers: pdf-viewer
  - Maps: google-map, data-maps
  - Other: code-editor
- **Pages**:
  - Authentication: login, register, lock, password-recovery
  - Error Pages: 404, 500
- **Other**: events

## Important Libraries & Their Usage

### Simple-DataTables (data-table.html)
- Vanilla JavaScript data tables with no jQuery dependency
- Installed via: `npm install simple-datatables`
- Initialized with: `new DataTable('#tableId', { options })`
- Features: sorting, searching, pagination, CSV export, print functionality
- Fully customizable with Bootstrap 5 styling

### Quill Editor (tinymc.html)
- Rich text editor loaded from node_modules/quill
- Multiple editor instances with different themes (snow, bubble)

### Chart.js (multiple pages)
- Replaces all legacy charting libraries
- Loaded from node_modules/chart.js/dist/chart.umd.min.js
- Used in: analytics, index variants, course-info, professor-profile, all chart pages

## Development Notes

- **Vite Dev Server**: Automatically handles Handlebars compilation and hot reload
- **Build Process**: Terser minification with console/debugger stripping in production
- **CSS Optimization**: PostCSS with autoprefixer and cssnano
- **Port**: Development server runs on port 3000
- **Auto-open**: Browser opens automatically on `npm run dev`

## Common Patterns

### Adding a New Page

**Step 1: Create HTML file** in root directory using standard Handlebars template structure:

```html
{{> head}}
{{> sidebar}}

<div class="main-wrapper" id="mainWrapper">
    {{> header}}

    <main class="dashboard-content" id="main-content">
        <div class="container-fluid">
            <!-- Your page content here -->
        </div>
    </main>

    {{> footer}}
</div>

{{> scripts}}
```

**Step 2: Add page context** in `vite.config.js` inside `getPageContext()`:

- Update navigation state logic (lines 23-50) to set active menu items
- Add page-specific config in `pageConfigs` object (lines 53-267) for custom title, breadcrumbs, additionalCSS/JS

**Step 3: Add to build** (if needed): Include in `build.rollupOptions.input` array in `vite.config.js` (line 368)

**Step 4: Update sidebar** (if needed): Add link to `src/partials/sidebar.hbs` in appropriate menu section

### Dashboard Card Spacing

```css
.dashboard-card {
    margin-bottom: 24px;
}
```

### Grid System

```html
<div class="dashboard-grid grid-cols-2 gap-4">
    <!-- Responsive grid that becomes single column on mobile -->
</div>

<div class="dashboard-grid grid-cols-4">
    <!-- 4 columns on desktop, 1 on mobile -->
</div>
```

### Chart Pages

Pages with Chart.js need these in their `vite.config.js` page config:

```javascript
additionalCSS: ['src/css/charts-layout.css'],
additionalJS: ['node_modules/chart.js/dist/chart.umd.js', 'src/js/charts-responsive.js']
```

### Animated Counters

The `charts-responsive.js` includes a counter animation function:

```javascript
// Automatically animates elements with .counter class
<span class="counter">1234</span>  // Will animate from 0 to 1234
```

## SCSS Architecture

Sass files are organized in `src/scss/`:

```
src/scss/
├── main.scss               # Entry point (imports all other files)
├── variables.scss          # Global Sass variables
├── base/
│   └── _variables.scss     # Base variables
├── components/
│   ├── _sidebar.scss       # Sidebar-specific styles
│   ├── _header.scss        # Header-specific styles
│   ├── _buttons.scss       # Button customizations
│   ├── charts.scss         # Chart styling
│   ├── dashboard.scss      # Dashboard-specific styles
│   ├── tables.scss         # Table styling
│   └── utilities.scss      # Utility classes
├── bootstrap-overrides/
│   ├── cards.scss          # Bootstrap card overrides
│   ├── buttons.scss        # Bootstrap button overrides
│   ├── navbar.scss         # Bootstrap navbar overrides
│   └── forms.scss          # Bootstrap form overrides
└── utilities/
    └── _responsive.scss    # Responsive utility classes
```

**Important**: All SCSS is processed by Vite with PostCSS (autoprefixer + cssnano in production).

## Production Deployment

**Recommended Approach**: Full source deployment (not static build)

### Why Full Source Deployment?
- All 65 HTML pages work immediately (only 11 pages configured in Vite build)
- Handlebars templating works correctly
- Easier to update individual files
- No build configuration needed

### Required Files for Deployment

```
node_modules/        # REQUIRED - all assets load from here
src/                 # All source files
images/              # Image assets
img/                 # Additional images
*.html              # All 65 HTML pages
package.json        # Dependencies manifest
package-lock.json   # Locked versions
```

### Server Configuration

**Apache** - Ensure node_modules is accessible:
```apache
<Directory "/var/www/html/node_modules">
    Options +FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

**Nginx** - Serve node_modules:
```nginx
location /node_modules {
    alias /var/www/html/node_modules;
}
```

See [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md) for complete deployment instructions.

## Build System Details

### Vite Configuration Highlights

- **Port**: 3000 (auto-opens browser on `npm run dev`)
- **Hot Module Replacement**: Automatic for CSS, JS, and HTML changes
- **Handlebars Compilation**: Automatic via `vite-plugin-handlebars`
- **Terser Minification**: Production builds strip console.log and debugger
- **CSS Processing**: SCSS → PostCSS → Autoprefixer → cssnano (production)
- **Build Exclusions**: Files with `-new` or `template` in name are excluded from build

### Adding Pages to Build

If you need to add a page to the static build (in `vite.config.js`):

1. Add to `build.rollupOptions.input` object (line 367-374)
2. Ensure page context is configured in `getPageContext()` function
3. Run `npm run build` to verify

**Note**: Currently only 11 pages are configured for build. For production, use full source deployment instead.

## Utility Scripts

The project includes several shell scripts for maintenance and deployment:

- **deploy.sh** - Production deployment script for transferring files to server
- **update_navigation.sh** - Batch updates navigation structure across all HTML files
- **check-migration.sh** - Validates Bootstrap 5 migration status across all pages

These scripts are primarily for maintenance and bulk operations. Normal development should use npm commands.

## Critical Implementation Details

### Sidebar State Persistence

The sidebar collapse state persists across sessions using localStorage (desktop only):

- **Desktop (>768px)**: Sidebar toggles between collapsed and expanded, state saved to localStorage
- **Mobile (≤768px)**: Sidebar opens as overlay with backdrop, no state persistence
- **localStorage key**: `sidebarCollapsed` (boolean as string)

On page load, if `sidebarCollapsed === 'true'` and viewport is desktop, sidebar automatically collapses.

### Navigation Active States

Navigation active states are managed in two ways:

1. **Server-side**: `vite.config.js` `getPageContext()` sets navigation object based on filename patterns
2. **Client-side**: `dashboard.js` compares `window.location.pathname` with link hrefs for runtime highlighting

Both approaches ensure consistent active menu item highlighting across all 65 pages.
