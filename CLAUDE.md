# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Kiaalap is a Bootstrap 5 education management dashboard with 65+ HTML pages using Handlebars templating and Vite build system. Fully modernized from legacy Bootstrap 4, it's 100% jQuery-free with vanilla JavaScript.

## Build Commands

```bash
npm run dev          # Dev server on port 3000 (auto-opens browser)
npm run build        # Production build to dist/
npm run preview      # Preview production build
npm run lint:html    # HTML validation
npm run lint:css     # SCSS linting with Stylelint
npm run lint:js      # ESLint for JavaScript
npm run format       # Prettier formatting
npm run clean        # Remove dist/
```

## Architecture

### Tech Stack
- **Bootstrap 5.3.8** - CSS framework (no jQuery)
- **Vite 7.1.7** - Build tool with vite-plugin-handlebars
- **Chart.js 4.5.0** - Charting (replaced Morris.js/C3/D3)
- **Simple-DataTables 10.0** - Vanilla JS data tables
- **Bootstrap Icons 1.13.1** - Loaded from node_modules (no CDN)

### Key Directories
```
src/
├── partials/         # Handlebars partials (head, sidebar, header, footer, scripts)
├── helpers/          # Handlebars helpers (handlebars-helpers.js)
├── js/               # JavaScript (dashboard.js is main entry)
├── css/              # Custom CSS (dashboard.css, charts-layout.css)
└── scss/             # Sass source (app.scss is entry point)
```

### Handlebars Template System

Every HTML page follows this structure:
```html
{{> head}}
{{> sidebar}}
<div class="main-wrapper" id="mainWrapper">
    {{> header}}
    <main class="dashboard-content" id="main-content">
        <div class="container-fluid">
            <!-- Page content -->
        </div>
    </main>
    {{> footer}}
</div>
{{> scripts}}
```

**Context is set per-page in `vite.config.js`** via `getPageContext(filename)`:
- Navigation states (which menu is active/expanded)
- Page title, breadcrumbs, description
- Additional CSS/JS files to load

### Navigation State Logic

Navigation states are set in `vite.config.js` based on filename patterns:
- Dashboard pages: `index`, `index-1`, `index-2`, `analytics`, `widgets`
- Academic: pages containing `professor`, `student`, `course`, `library`, `department`
- Interface: `buttons`, `alerts`, `modals`, `tabs`, `accordion`, `*form*`, `*chart*`, `*table*`
- Communication: `mailbox*`
- Auth/Error pages: `login`, `register`, `lock`, `password-recovery`, `404`, `500`

### Sidebar Behavior

The sidebar has two modes managed in `src/js/dashboard.js`:
- **Desktop (>768px)**: Collapses in place, state persisted to `localStorage` key `sidebarCollapsed`
- **Mobile (≤768px)**: Opens as overlay with backdrop, no persistence

### Adding a New Page

1. Create `newpage.html` in root with standard Handlebars template
2. Add page config in `vite.config.js` → `pageConfigs` object
3. Update navigation state logic if needed (lines 22-50)
4. Add to sidebar in `src/partials/sidebar.hbs` if needed
5. Build includes all `*.html` files automatically (excludes `*template*` and `*-new*`)

### Chart Pages

Pages with charts need this in their `vite.config.js` page config:
```javascript
additionalCSS: ['src/css/charts-layout.css'],
additionalJS: ['node_modules/chart.js/dist/chart.umd.js', 'src/js/charts-responsive.js']
```

### Handlebars Helpers

Available in `src/helpers/handlebars-helpers.js`:
- `eq` - Equality check: `{{#if (eq page 'index')}}`
- `exists`, `isActive`, `truncate`, `formatDate`
- Math: `add`, `subtract`, `multiply`
- String: `lowercase`, `uppercase`, `startsWith`, `endsWith`, `contains`
- Array: `length`, `first`, `last`

### CSS Architecture

Entry point: `src/scss/app.scss`

Key files:
- `components/sidebar.scss` - Sidebar styling
- `components/dashboard.scss` - Dashboard-specific styles
- `bootstrap-overrides/` - Bootstrap customizations

Standard spacing: `.dashboard-card { margin-bottom: 24px; }`

## Deployment

**Recommended: Full source deployment** (not static build)

Upload these to server:
```
node_modules/        # Required - all assets load from here
src/
images/
img/
*.html
package.json
package-lock.json
```

Server must allow access to `node_modules/`. See `DEPLOYMENT_GUIDE.md` for Apache/Nginx configs.

## Key Libraries

| Library | File | Usage |
|---------|------|-------|
| Simple-DataTables | `data-table.html` | `new DataTable('#tableId', options)` |
| Quill | `tinymc.html` | Rich text editor |
| Chart.js | Dashboard/chart pages | All charting |
| CropperJS | `images-cropper.html` | Image cropping |
| FullCalendar | `events.html` | Calendar events |
| Leaflet | Map pages | Interactive maps |
