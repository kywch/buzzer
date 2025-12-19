# Claude Code Rules for Buzzer/Buz2er Project

## Sandbox Usage

### Use sandbox/ for planning and temporary files
- All planning documents should be created in the `sandbox/` directory
- Interim documentation drafts belong in `sandbox/`
- Temporary analysis files should go in `sandbox/`
- Exploration notes and agent workspace files belong in `sandbox/`

### Examples of sandbox files
- `sandbox/plan-*.md` - Planning documents for features (COMMITTED to git)
- `sandbox/notes-*.md` - Exploration and analysis notes (NOT committed)
- `sandbox/draft-*.md` - Documentation drafts (NOT committed)
- `sandbox/temp-*.txt` - Temporary working files (NOT committed)

### Git commit policy for sandbox files
- **DO COMMIT:** Plan files (`sandbox/plan-*.md`) - these track feature development
- **DO NOT COMMIT:** Notes, drafts, and temporary files - these are ephemeral
- Clean up non-plan sandbox files when work is complete

## Project-Specific Guidelines

### Single-File Architecture
- The entire application is in `index.html`
- Never split code into separate JS/CSS files
- Maintain the single-file SPA design

### Code Modification
- Always read `index.html` before making changes
- Preserve the cyberpunk aesthetic (colors, glows, gradients)
- Test responsive design at 768px breakpoint
- Ensure LocalStorage operations work correctly

### Privacy & Dependencies
- No external libraries or frameworks
- No tracking or analytics code
- No server communication (except Google Fonts)
- Keep the app fully client-side

### Data Management
- All data stored in LocalStorage
- Maintain 14-day history retention limit
- Keep 12-week summary retention limit
- Auto-cleanup on every data load

## Documentation

- Refer to `AGENTS.md` for comprehensive codebase understanding
- Refer to `README.md` for user-facing documentation
- Update `AGENTS.md` when making significant architectural changes
