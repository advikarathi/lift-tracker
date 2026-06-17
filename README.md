# Lift Tracker

A personal lifting log that auto-detects PRs, shows session-over-session deltas, and tracks weekly volume — all in the browser with no app, no account, and no install.

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
- **Dark mode toggle** — persists your preference
- **localStorage** — data survives closing the tab

---

## How to use it

1. Type an exercise name (or tap a quick-pick chip), enter weight/reps/sets, hit **Log set** (or press Enter)
2. Watch your session build — PRs are badged automatically, deltas show vs. your last session
3. Tap **PRs** to see your board and volume breakdown
4. Tap **History** to see every past session
5. **Clear all data** at the bottom resets everything with a confirmation step
6. Press **N** from anywhere to jump to the log form

---

## Requirements met

| Requirement | How |
|---|---|
| Semantic HTML | `<header>`, `<nav>`, `<main>`, `<section>`, `role` attributes, `aria-label` throughout |
| Intentional CSS | Custom property token system, dark mode, responsive grid, animations on new set rows |
| JavaScript state | Single `state.sets` array read and written by every interaction |
| Derived output | PRs, 1RM estimates, weekly volume, vs-last-session delta — all computed from state |
| 3+ interaction types | Form submit, button click, text input (datalist), number input, keyboard shortcut (N) |
| Dynamic DOM | Full re-render on every state change, no `alert()` anywhere |
| Reset | Inline confirmation flow clears state + localStorage |
| No server/framework | Vanilla JS only, single HTML file, no CDN |

## Extensions included

- **localStorage** — persists across sessions
- **Dark mode toggle** — preference saved to localStorage
- **Keyboard shortcut** — press N to jump to the log form
- **Animations** — new set rows fade in
- **Multiple views** — Log, PRs, History tabs

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
