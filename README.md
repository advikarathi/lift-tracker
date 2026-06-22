# Lift Tracker

A personal, offline fitness tracker that auto-detects PRs, supports repeatable routines, and turns training and recovery data into useful progress insights — all in the browser with no app, no account, and no install.

**Live site:** [advikarathi.github.io/lift-tracker](https://advikarathi.github.io/lift-tracker)
**Repo:** [github.com/advikarathi/lift-tracker](https://github.com/advikarathi/lift-tracker)

---

## What it does

Log sets (exercise, weight, reps, sets count) and the tool computes everything else:

- **PR detection** — auto-badges any set that beats your previous best estimated 1RM for that exercise
- **vs last session** — shows +kg / −kg / same compared to the last time you did that lift
- **Weekly dot grid** — shows which days you've trained this week at a glance
- **PR board** — every exercise ranked by estimated 1RM, with date achieved
- **Volume chart** — total volume per exercise across all time
- **Session history** — every past workout with volume totals and PR-highlighted chips
- **Edit, undo, redo & export** — correct today's entry, undo or redo a deletion, or download the active profile's workout history as a CSV
- **Workout routines** — start built-in Back & biceps, Lower body, Push day, or Full body routines, or save your own; each logged exercise advances to the next editable entry
- **Recovery check-ins** — track sleep, readiness, and daily water intake
- **Body progress** — log bodyweight and measurements, set a bodyweight target, and see an in-browser trend chart
- **Training coach** — offline, rule-based guidance derived only from your own workout and recovery data
- **Dark mode toggle** — persists your preference
- **localStorage** — data survives closing the tab

---

## How to use it

1. Type an exercise name (or tap a quick-pick chip), enter weight/reps/sets, hit **Log set** (or press Enter)
2. Watch your session build — PRs are badged automatically, deltas show vs. your last session
3. Start a built-in or saved routine; after each entry, the next exercise pre-fills while you choose today's weight, reps, and sets
4. Tap **PRs** to see your board and volume breakdown
5. Tap **Progress** to log body measurements and review your goal and trend
6. Tap **History** to see every past session
7. Use **Export CSV** in History to save your active profile's workout data
8. **Clear all data** at the bottom resets the active profile with a confirmation step
9. Press **N** from anywhere to jump to the log form

---

## Requirements met

| Requirement | How |
|---|---|
| Semantic HTML | `<header>`, `<nav>`, `<main>`, `<section>`, `role` attributes, `aria-label` throughout |
| Intentional CSS | Custom property token system, dark mode, responsive grid, animations on new set rows |
| JavaScript state | Profile-scoped workout, routine, recovery, and measurement data read and written by interactions |
| Derived output | PRs, 1RM estimates, strength volume, vs-last-session delta, streaks, and goal progress — all computed from state |
| 3+ interaction types | Form submit, button click, text input (datalist), number input, keyboard shortcut (N) |
| Dynamic DOM | Full re-render on every state change, no `alert()` anywhere |
| Reset | Inline confirmation flow clears the active profile's saved tracking data |
| No server/framework | Vanilla JS only, single HTML file, no CDN |

## Extensions included

- **localStorage** — persists across sessions
- **Dark mode toggle** — preference saved to localStorage
- **Keyboard shortcut** — press N to jump to the log form
- **Animations** — new set rows fade in
- **Multiple views** — Log, PRs, History, and Progress tabs
- **Data portability** — active-profile workout export as a CSV download
- **Safe corrections** — edit entries and undo/redo deletions
- **Offline-only coaching** — deterministic suggestions, no API calls or account required

---

## The math

**Epley estimated 1RM:** `weight × (1 + reps / 30)`

When reps = 1, this returns weight exactly. Used only for PR comparison — not stored. A set is a PR if its e1RM is greater than every other set ever logged for that exercise.

---

## What I learned

- How to derive all output from a single flat array and re-render on every change — no stale derived state stored
- The `localStorage` API: `setItem`, `getItem`, `JSON.stringify`, `JSON.parse`, and error handling when storage is unavailable
- How to implement the Epley formula and use it consistently for PR detection across all sets
- How to group a flat array by date and by exercise without a database
- How `<datalist>` autocomplete works with a dynamically updated `<option>` list
- Edge cases: reps = 1 in the formula, deleting the only PR set (next-best auto-promotes), empty state on reset

---

## Tech

Vanilla JavaScript only. No frameworks, no libraries, no CDN dependencies. Single `index.html` file with embedded CSS and JS. Runs as plain static files on GitHub Pages.
