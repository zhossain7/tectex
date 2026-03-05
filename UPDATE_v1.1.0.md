# TecTex Update v1.1.0
Date: 2026-03-06

## Release Summary
This release completes the frontend evolution of TecTex into a full LaTeX resume editor with a modern code editor, inline resume builder workflow, stronger TeX round-trip handling, diagnostics, templates, and improved build/release flow.

Backend endpoints are unchanged:
- `/api/pdf`
- `/api/engines`

## What Is Completed

### 1. Editor Overhaul (CodeMirror 6)
- Replaced plain textarea editing with CodeMirror 6.
- Added syntax highlighting, line numbers, active-line highlight, bracket matching, indentation support, selection/search integration, and folding gutter.
- Migrated editor read/write paths to CodeMirror state/dispatch APIs.
- Added dirty tracking, autosave scheduling, live compile scheduling, and word/character count updates through CodeMirror update listeners.

### 2. Inline Resume Builder (No Blocking Modal)
- Replaced modal editing flow with panel tabs: `TeX Source` and `Resume Builder`.
- Added preamble/packages collapsible section at top of builder.
- Added section cards with drag handles and HTML5 drag-and-drop reordering.
- Added deferred sync behavior:
  - Parse source when entering builder.
  - Auto-apply builder changes back to source on tab switch to source when valid.
  - Block switch and show inline validation errors when invalid.
- Kept explicit `Apply & Compile` action and routed it through the same sync pipeline.

### 3. Parser and Round-Trip Robustness (Balanced Mode)
- Improved section parsing with nested-brace-safe heading extraction.
- Preserved heading command shape (`section`/`subsection`/`subsubsection`, starred variants).
- Preserved leading/trailing comment chunks and section whitespace metadata.
- Preserved preamble and custom commands through builder round-trip.
- Added common environment detection metadata:
  - `itemize`
  - `enumerate`
  - `tabular`
  - `description`
- Added `serializeSection()` and newline-style preservation (`LF` / `CRLF`) on regeneration.

### 4. Template System
- Added template picker modal launched from toolbar.
- Added 3 embedded templates:
  - Classic
  - Modern
  - Minimal
- `Use Template` behavior now loads template TeX, opens Resume Builder, and compiles preview.

### 5. UX and Styling Polish
- Added tab UI with animated indicator and transitions.
- Added full CodeMirror visual integration to match app palette.
- Added drag/drop feedback states for section cards.
- Added compile overlay spinner.
- Added collapsible build log (collapsed by default, auto-expands on compile failure).
- Added template card previews and responsive layout improvements.
- Added status-bar word/character count.

### 6. Quality-of-Life and Reliability
- Added localStorage autosave + restore.
- Added `Ctrl+S` save + compile behavior.
- Preserved `Ctrl+Enter` compile behavior.
- Added compile request coalescing guard (last request wins) to avoid stale preview updates.
- Added compile log parsing for line-based diagnostics and CodeMirror gutter markers/highlights.

### 7. Build and Packaging Flow
- Added Node/esbuild frontend build workflow via `package.json` scripts.
- Documented required frontend bundle generation in README.
- `local_app/index.html` now loads `local_app/bundle.js`.
- EXE packaging remains standard PyInstaller flow after bundle generation.

## Key Files Updated
- `local_app/app.js`
- `local_app/index.html`
- `local_app/styles.css`
- `README.md`
- `package.json`
- `package-lock.json`
- `.gitignore`

## Build Commands (Current)
```powershell
npm install
npm run build
.\.venv\Scripts\python -m PyInstaller --clean --noconfirm .\texviewer_app.spec
```

If `npm` is not on PATH in the current terminal session, use:
```powershell
& "C:\Program Files\nodejs\npm.cmd" install
& "C:\Program Files\nodejs\npm.cmd" run build
```
