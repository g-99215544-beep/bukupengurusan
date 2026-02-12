# CLAUDE.md - Buku Pengurusan SK Sri Aman 2026

## Project Overview

This is a **single-file HTML5/JavaScript Progressive Web Application** for managing the school administration book (Buku Pengurusan) of SK Sri Aman, a Malaysian primary school. The application serves as an offline-capable reference system for school administrators, teachers, and staff.

**Key Purpose:** Interactive PDF viewer with teacher directory and school calendar functionality

**Target Year:** 2026 academic year

**Language:** Malay (Bahasa Malaysia) UI with English variable names

## File Structure

```
bukupengurusansksa26/
├── index.html              # Main application (single-file SPA, ~1,730 lines)
├── buku-pengurusan-v2.pdf  # Current PDF document (3.67MB)
├── buku-pengurusan-v3.pdf  # Updated PDF version (3.63MB)
├── icon-192.svg            # PWA icon (192x192)
├── icon-512.svg            # PWA icon (512x512)
└── CLAUDE.md               # This file
```

## Technology Stack

- **HTML5/CSS3/JavaScript** (Vanilla ES6, no frameworks)
- **PDF.js v3.11.174** (CDN) - PDF rendering engine
- **Google Fonts** - Plus Jakarta Sans
- No build process or package manager required

## Application Architecture

### Three-Tab Interface

1. **Buku (Book)** - PDF viewer with HD rendering, zoom controls (50-200%), table of contents sidebar, and name highlighting
2. **Guru (Teacher)** - Grid directory of 40+ teachers with detailed profiles and role-based task filtering
3. **Takwim (Calendar)** - Monthly calendar view with color-coded events for the 2026 school year

### Key Data Structures

Located in `index.html` `<script>` section:

1. **`tableOfContents`** (line ~214) - PDF section navigation
2. **`guruDatabase`** (line ~224) - Teacher records with tasks and page references
3. **`takwimData`** (embedded) - Calendar events indexed by date (YYYY-MM-DD)
4. **`categoryConfig`** - Management area color coding (pentadbiran, kurikulum, hem, kokurikulum)
5. **`eventTypeConfig`** - Calendar event type styling

### Global State Variables

```javascript
currentTab        // Active tab: 'buku', 'guru', 'takwim'
currentZoom       // PDF zoom level (50-200)
highlightName     // Teacher name being highlighted in PDF
selectedGuruIndex // Current teacher profile index (-1 for grid view)
selectedDate      // Currently selected calendar date
calendarMonth     // Current calendar month (0-11)
calendarYear      // Current calendar year (2026)
```

## Common Development Tasks

### Updating Teacher Information

Edit the `guruDatabase` array in `index.html`. Each teacher object:

```javascript
{
    nama: "EN. FULL NAME",        // Include title prefix (EN./PN./CIK/DR.)
    jawatan: "Position",          // Role/subject
    avatar: "👨‍🏫",               // Emoji avatar
    tugas: [
        { label: "Task Name", page_number: 49, category: "kurikulum" }
        // Categories: pentadbiran, kurikulum, hem, kokurikulum
    ]
}
```

### Updating Calendar Events

Events in `takwimData` are keyed by date string:

```javascript
"2026-01-08": [
    { event: "Event Description", type: "sekolah", color: "#10b981" }
    // Types: cuti, sekolah, aktiviti, kurikulum, kokurikulum, hem, peperiksaan
]
```

### Updating PDF Document

1. Replace `buku-pengurusan-v2.pdf` with the new PDF
2. Update page references in `tableOfContents` if section pages changed
3. Update `page_number` values in `guruDatabase` if teacher references moved

## Key Functions Reference

| Function | Purpose |
|----------|---------|
| `loadPDF()` | Initialize PDF.js and render document |
| `renderPage(pn, hl)` | Render specific page with optional name highlighting |
| `scrollToPage(pn)` | Navigate to page with smooth scroll |
| `switchTab(tab)` | Toggle between buku/guru/takwim views |
| `selectGuru(i)` | Display teacher detail profile |
| `renderCalendar()` | Generate monthly calendar grid |
| `handleSearch()` | Real-time search across all data |
| `setHighlight(n)` | Enable name highlighting in PDF |

## Styling Conventions

### Color Scheme

- **Primary Gradient:** `#6366f1` to `#8b5cf6` (Indigo/Purple)
- **Pentadbiran:** `#f59e0b` (Orange)
- **Kurikulum:** `#10b981` (Green)
- **HEM:** `#3b82f6` (Blue)
- **Kokurikulum:** `#ec4899` (Pink)
- **Highlight:** `#fef3c7` (Yellow)

### CSS Architecture

All styles are embedded in `<style>` tags (lines 8-138). Uses:
- CSS Grid and Flexbox for layouts
- Media queries implied but minimal
- BEM-like class naming

## Important Notes for AI Assistants

1. **Single-file architecture** - All code is in `index.html`. Do not split into separate files unless explicitly requested.

2. **Data is hardcoded** - Teacher and calendar data are embedded JavaScript arrays. Changes require editing the source file.

3. **Malay language context** - UI text uses Bahasa Malaysia:
   - "Guru" = Teacher
   - "Takwim" = Calendar
   - "Pentadbiran" = Administration
   - "Kurikulum" = Curriculum
   - "HEM" = Hal Ehwal Murid (Student Affairs)
   - "Kokurikulum" = Co-curriculum
   - "m/s" = muka surat (page)

4. **Title prefixes** - Teacher names include titles:
   - EN. = Encik (Mr.)
   - PN. = Puan (Mrs.)
   - CIK = Cik (Miss)
   - DR. = Doctor
   - HJH. = Hajjah (female who completed Hajj)

5. **PDF.js dependency** - Loaded from CDN. Offline use requires local hosting.

6. **No testing framework** - Manual testing required. Test PDF rendering, search, and navigation.

7. **Browser compatibility** - Requires modern browser with ES6, Canvas API, Fetch API support.

## Git Workflow

This repository uses feature branches for development. Recent commits show:
- Teacher position updates require careful verification
- PDF externalization improved from base64 embedding
- PR-based workflow for changes

## Troubleshooting

**PDF not loading:** Check `buku-pengurusan-v2.pdf` exists and PDF.js CDN is accessible

**Search not finding teacher:** Verify name spelling matches exactly in `guruDatabase`

**Calendar events missing:** Check date format is `YYYY-MM-DD` in `takwimData`

**Highlighting not working:** Ensure teacher name in database matches PDF text exactly
