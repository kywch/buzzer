# AGENTS.md - Buzzer/Buz2er Codebase Guide

This document provides AI agents with a comprehensive understanding of the Buzzer (Buz2er) codebase.

## Project Overview

**Buz2er** is a cyberpunk-styled exercise reminder web application designed to help users stay active throughout the day. It's a privacy-first, single-page application with no backend dependencies.

**Live URL:** https://buz2er.com

### Key Characteristics
- **Zero Backend:** All data stored locally in browser LocalStorage
- **Zero Dependencies:** No npm packages, frameworks, or external libraries
- **Privacy-First:** No tracking, analytics, or server communication
- **Single File:** Entire application in [index.html](index.html)
- **Fully Responsive:** Mobile-optimized with CSS media queries

## Technology Stack

- **Frontend:** Vanilla JavaScript (ES6+)
- **Styling:** Embedded CSS with cyberpunk theme
- **Storage:** Browser LocalStorage API
- **Fonts:** Google Fonts (Orbitron, Space Mono)
- **Deployment:** Static HTML file hosting

## Project Structure

```
/workspace/hobby/buzzer/
├── index.html          # Main application file (~1085 lines)
├── README.md           # Project documentation
├── LICENSE             # MIT License
├── AGENTS.md           # This file
├── sandbox/            # Temporary files for planning and interim docs
└── .git/               # Version control
```

### Sandbox Directory
The `sandbox/` directory is used for:
- Planning documents during development
- Interim documentation drafts
- Temporary files that shouldn't be committed
- AI agent workspace for exploration and analysis

## Architecture Overview

### Single-File SPA Design
The entire application is contained in [index.html](index.html) with three main sections:
1. **HTML Structure** (lines ~1-100): Semantic layout with panels
2. **CSS Styles** (lines ~100-600): Cyberpunk-themed responsive design
3. **JavaScript Logic** (lines ~600-1085): App functionality and state management

### Main UI Components

#### 1. Activity Log Panel (Left Column, Top)
- Exercise history with timestamps
- Countdown timer to next reminder
- Interval slider (15-90 minutes, 5-minute increments)
- "Exercise Now!" quick action button

#### 2. Exercise List Panel (Right Column, Top)
- Editable list of exercises
- Add/delete exercise functionality
- Default exercises: Push-ups, Plank, Hip Bridge, Sumo Squat, Stretches

#### 3. Stats Panel (Full Width, Bottom)
- **Today's Count:** Exercises completed since midnight
- **Weekly Count:** Exercises since week start (Sunday)
- **Current Streak:** Consecutive days with ≥1 exercise
- **7-Day Total:** Exercises in last 7 days
- **12-Week History:** Visual grid showing weekly exercise counts

#### 4. Exercise Reminder Modal
- Popup with random exercise selection
- 60-second countdown timer
- Actions: Start Timer, Delay, Close, Done

## Data Model

### LocalStorage Structure
Key: `"exerciseData"`

```javascript
{
  exercises: string[],              // List of exercise names
  history: Array<{                  // Exercise completion log
    timestamp: number,              // Unix timestamp (ms)
    exercise: string                // Exercise name
  }>,
  weeklySummary: Array<{            // Weekly aggregations
    week: string,                   // ISO week string (YYYY-WW)
    count: number                   // Exercise count for week
  }>,
  nextReminderTime: number | null,  // Scheduled reminder timestamp
  reminderIntervalMinutes: number   // User's interval preference
}
```

### Data Retention Policies
- **History:** Last 14 days only (auto-cleanup)
- **Weekly Summary:** Last 12 weeks only (auto-cleanup)
- Cleanup runs on every `loadData()` call

## Core Functions Reference

### Initialization & Data Management
- `init()` - App initialization, loads data, starts timers
- `loadData()` - Load from LocalStorage with validation and cleanup
- `saveData()` - Persist state to LocalStorage

### Exercise Management
- `addExercise()` - Add new exercise to list
- `deleteExercise(index)` - Remove exercise from list
- `addToHistory(exercise, timestamp)` - Log completed exercise
- `renderHistory()` - Display exercise history with timestamps

### Reminder System
- `scheduleNextReminder()` - Calculate and schedule next reminder
- `showReminder()` - Display modal with random exercise
- `delayReminder()` - Postpone reminder by configured interval
- `updateCountdown()` - Update "Next reminder in..." display

### Exercise Timer
- `startTimer()` - Begin 60-second countdown
- `stopTimer()` - Clear timer interval
- `completeExercise()` - Log exercise and reschedule reminder

### Statistics & Analytics
- `updateStats()` - Refresh all visible statistics
- `calculateStreak()` - Compute consecutive day streak
- `updateWeeklySummary()` - Recalculate weekly aggregations
- `renderWeeklySummary()` - Display 12-week history grid

### Utilities
- `getWeekString(date)` - Format date as ISO week (YYYY-WW)
- `clearAllData()` - Reset all data with user confirmation

## State Management

### Global Variables
```javascript
let exercises = []                   // Exercise list
let history = []                     // Completion log
let weeklySummary = []               // Weekly stats
let nextReminderTime = null          // Next reminder timestamp
let reminderTimeoutId = null         // Current setTimeout ID
let timerInterval = null             // Exercise timer interval ID
let currentExercise = null           // Currently displayed exercise
let timeLeft = 60                    // Timer countdown value
let reminderIntervalMinutes = 30     // User's interval setting
```

All state changes are immediately persisted to LocalStorage via `saveData()`.

## Key Workflows

### Reminder Flow
1. User sets interval via slider (15-90 minutes)
2. `scheduleNextReminder()` calculates `nextReminderTime = now + interval`
3. `setTimeout()` waits until reminder time
4. `showReminder()` displays modal with random exercise
5. User choices:
   - **Start:** Begin 60-second timer
   - **Delay:** Close modal, reschedule after interval
   - **Close (X):** Close modal, reschedule after interval
6. If timer completes or user clicks "Done!":
   - Exercise logged via `addToHistory()`
   - Stats updated via `updateStats()`
   - Modal hidden and reset
   - New reminder scheduled

### Exercise Completion Flow
1. Modal shows random exercise from list
2. User clicks "Start" → 60-second countdown begins
3. Timer updates every second
4. User clicks "Done!" or timer reaches 0
5. Exercise added to history with timestamp
6. Weekly summary recalculated if needed
7. All stats panels updated
8. Modal closes
9. Next reminder scheduled

### Data Persistence Flow
1. User performs action (add exercise, complete exercise, etc.)
2. In-memory state updated (global variables)
3. `saveData()` immediately called
4. Data serialized to JSON
5. Stored in LocalStorage under `"exerciseData"` key
6. On page load, `loadData()` retrieves and validates data

## Styling System

### Cyberpunk Color Palette
```css
--bg-primary: #0a0e27     /* Dark navy */
--bg-secondary: #1a1f3a   /* Dark purple */
--accent-cyan: #00fff9    /* Bright cyan */
--accent-magenta: #ff00e5 /* Bright magenta */
--accent-yellow: #ffed00  /* Bright yellow */
--text-primary: #e0e0e0   /* Light gray */
```

### Visual Effects
- **Glow Effects:** 20px blur with color-specific opacity
- **Gradient Backgrounds:** Multi-color linear gradients
- **Scanline Texture:** Repeating horizontal lines overlay
- **Animations:** Fade-in on load, bounce on modal show
- **Hover States:** Color shifts and enhanced glows

### Responsive Breakpoints
- **Desktop:** Two-column grid layout
- **Mobile (≤768px):** Single-column stacked layout
- **Grid Adjustments:** 12-week history changes from 1×12 to 2×6 grid

## Browser API Usage

### LocalStorage
- `localStorage.getItem("exerciseData")` - Load data
- `localStorage.setItem("exerciseData", json)` - Save data
- `localStorage.removeItem("exerciseData")` - Clear all data

### Timers
- `setTimeout(fn, ms)` - Schedule reminders
- `clearTimeout(id)` - Cancel scheduled reminder
- `setInterval(fn, 1000)` - Exercise timer updates
- `clearInterval(id)` - Stop exercise timer

### Date Handling
- `new Date()` - Current timestamp
- `date.getDay()` - Day of week (0=Sunday)
- `date.toLocaleDateString()` - Formatted date strings
- `date.toLocaleTimeString()` - Formatted time strings

## Development Guidelines

### Code Philosophy

**Try very hard NOT to increase code. Decreasing code is highly valued.**

#### Principles
1. **Prefer deletion over addition**
   - Remove unused code, functions, and variables
   - Consolidate duplicate logic
   - Simplify complex implementations

2. **Favor inline simplicity over abstraction**
   - Don't create utilities for one-off operations
   - Avoid premature abstraction
   - Only extract when there's clear duplication (3+ times)

3. **Question every line**
   - Can this be done more simply?
   - Is this abstraction worth the cost?
   - Does this helper function save more lines than it adds?

4. **Metrics that matter**
   - Line count reduction is a success metric
   - Complexity reduction is valuable
   - Readability should not sacrifice brevity

#### Examples

**❌ Bad: Adding code to "improve" structure**
```javascript
// Adding 5 lines for 3 uses - net loss
function getReminderInterval() {
    return reminderIntervalMinutes * 60 * 1000;
}
// Use in 3 places = 3 lines
// Total: 8 lines
```

**✅ Good: Keep it inline if only used 3 times**
```javascript
// Just use the calculation where needed = 3 lines
const interval = reminderIntervalMinutes * 60 * 1000;
```

**❌ Bad: Creating utility for unclear benefit**
```javascript
function escapeHtml(text) {
    const div = document.createElement('div');
    div.textContent = text;
    return div.innerHTML;
}
```

**✅ Good: Use textContent directly in DOM**
```javascript
// Most rendering can use textContent instead of innerHTML
element.textContent = userInput;
```

**✅ Great: Delete code**
```javascript
// Remove unused CSS classes
// Remove dead code paths
// Consolidate duplicate styles
// Inline single-use functions
```

#### Refactoring Priorities
1. **First priority**: Delete unused code
2. **Second priority**: Consolidate duplicates (only if net reduction)
3. **Last priority**: Extract abstractions (only if significant duplication exists)

Net line count should ideally decrease or stay neutral. Increasing line count requires strong justification.

### Code Modification Best Practices
1. **Always read [index.html](index.html) before making changes**
2. **Preserve the single-file architecture** - no splitting into modules
3. **Maintain cyberpunk aesthetic** - consistent colors and effects
4. **Test LocalStorage operations** - ensure data persists correctly
5. **Validate responsive design** - test on mobile breakpoints
6. **Keep zero dependencies** - no external libraries
7. **Preserve privacy** - no network requests (except fonts)

### Common Modification Scenarios

#### Adding a New Exercise
1. User enters exercise in input field
2. `addExercise()` validates input (non-empty, not duplicate)
3. Exercise added to `exercises` array
4. `saveData()` persists change
5. Exercise list re-rendered

#### Modifying Statistics
1. Locate relevant function: `updateStats()`, `calculateStreak()`, etc.
2. Modify calculation logic
3. Ensure data sources are correct (history, weeklySummary)
4. Test with various data scenarios (empty, single day, multi-week)

#### Changing UI Layout
1. Locate relevant HTML section in [index.html](index.html)
2. Modify structure while preserving class names
3. Update corresponding CSS rules
4. Test responsive behavior at 768px breakpoint
5. Verify modal overlays correctly

#### Adjusting Reminder Logic
1. Find `scheduleNextReminder()` function
2. Modify interval calculation if needed
3. Ensure `nextReminderTime` is correctly saved
4. Test reminder cancellation and rescheduling
5. Verify countdown display updates correctly

## Testing Checklist

When making changes, verify:
- [ ] Exercise list add/delete works
- [ ] Reminder modal appears at correct interval
- [ ] Timer countdown functions (60 seconds)
- [ ] Exercise completion logs to history
- [ ] Statistics update correctly (today, weekly, streak, 7-day)
- [ ] 12-week history grid displays correctly
- [ ] Interval slider updates next reminder time
- [ ] "Exercise Now!" button works immediately
- [ ] "Clear All Data" requires confirmation
- [ ] LocalStorage persists across page reloads
- [ ] Mobile layout displays correctly (≤768px)
- [ ] Cyberpunk styling intact (colors, glows, gradients)

## Git Workflow

### Recent Commits
- `a9d97e3` - Improve UI layout and fix weekly summary calculation
- `c2a0077` - Fix mobile horizontal scrollbar issue
- `67b5cdb` - Rebrand to Buz2er and improve UX
- `494095f` - Update index.html
- `39fd640` - Create index.html

### Branch Structure
- **main:** Production-ready code (current branch)
- Clean working directory status

## Performance Considerations

- **File Size:** ~36.9 KB (includes all CSS/JS)
- **Load Time:** Instant (single file, no bundling)
- **Runtime:** Lightweight DOM manipulation
- **Storage:** Minimal LocalStorage usage
- **Network:** Only Google Fonts (cacheable)

## Known Limitations

1. **Data Loss Risk:** Browser cache clear wipes all data
2. **No Sync:** Data not shared across devices/browsers
3. **Tab Dependency:** Reminders only work if page is open
4. **No Service Worker:** App doesn't work truly offline (needs fonts)
5. **Single User:** No multi-user support

## Future Enhancement Ideas

- Export/import data functionality
- Custom reminder sounds
- Exercise categories or difficulty levels
- Progress charts and visualizations
- Dark/light theme toggle (currently cyberpunk only)
- PWA with service worker for offline support
- Backup to cloud (optional, privacy-preserving)

## Quick Reference

### File Locations
- Main app: [index.html](index.html)
- Documentation: [README.md](README.md)
- License: [LICENSE](LICENSE)
- This guide: [AGENTS.md](AGENTS.md)

### Key Line Numbers (approximate)
- HTML structure: lines 1-100
- CSS styles: lines 100-600
- JavaScript: lines 600-1085
- Core state management: lines 610-650
- Reminder logic: lines 750-850
- Statistics calculation: lines 900-1000

### Important Selectors
- `#interval` - Interval slider
- `#interval-value` - Interval display
- `#exercise-input` - New exercise input
- `#exercise-list` - Exercise list container
- `#history-list` - History log container
- `#countdown` - Next reminder countdown
- `#reminder-modal` - Exercise reminder popup
- `.stats-grid` - Statistics panel

---

**Last Updated:** 2025-12-19
**Codebase Version:** main branch (commit a9d97e3)
**Total Lines of Code:** ~1085 (single file)
