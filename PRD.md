# PRD.md — Lift Tracker

## Overview
A static, self-contained lifting log built with vanilla JavaScript. Users log sets by exercise with weight and reps, and the tool computes derived output — estimated 1RMs, personal records, weekly training volume, and session-over-session deltas — from a single persisted state array. No account, no server, no app install.

---

## Problem Statement
Lifters who train consistently need to remember what they did last session to make progressive overload decisions. Without a log, they guess — and guessing leads to stalled progress or injury. Existing apps (Strong, Hevy) are mobile-only and require accounts. A browser-based tool that works offline, saves automatically, and shows the three things that actually matter — did I PR, what did I do last time, and am I training consistently — is genuinely useful.

**Primary user:** Advika — lifts regularly, wants session-to-session comparison and automatic PR detection without a dedicated app.

---

## Goals

| Goal | Success metric |
|------|----------------|
| Log a set in under 15 seconds | ≤4 fields, quick-pick chips for common exercises |
| Know immediately if a set is a PR | PR badge appears on set row after logging |
| Compare against last session | Delta shown inline (e.g. "+5kg vs last time") |
| See training consistency at a glance | Weekly dot tracker on main screen |
| Data survives closing the tab | localStorage persistence |
| Start fresh cleanly | Reset clears state + localStorage, returns to empty state |

---

## User Stories

1. As a lifter, I want to tap a quick-chip for "Back squat" and fill in weight/reps so logging is fast.
2. As a lifter, I want to see a PR badge when I log a weight that beats my previous best estimated 1RM.
3. As a lifter, I want to see what weight I used for this exercise last session so I know whether today is an improvement.
4. As a lifter, I want to see my estimated 1RM for each exercise so I can track strength progress.
5. As a lifter, I want a PR leaderboard showing my best lifts across all exercises.
6. As a lifter, I want to see my full session history with volume totals so I can spot patterns.
7. As a lifter, I want a weekly dot grid showing which days I trained so I can see consistency.
8. As a lifter, I want to delete a mistakenly logged set without clearing the whole session.
9. As a lifter, I want my data to persist across browser sessions.
10. As a lifter, I want to reset everything cleanly when I want to start fresh.

---

## Features

### Log a set (Interaction types: text input, number input, button click, keyboard Enter, datalist autocomplete)
- Exercise field: free text with `<datalist>` autocomplete from previously used exercises + a built-in list of common lifts
- Quick-pick chips: 8 common exercises shown as tappable buttons that populate the exercise field
- Weight (kg): number input, step 0.5
- Reps: number input, step 1, min 1
- Sets: number input, step 1, min 1, defaults to 1
- On submit: appends to state array, clears weight/reps/sets fields (keeps exercise for easy re-logging), triggers full re-render
- Validation: exercise name required, weight > 0, reps ≥ 1; inline error message, no alert() boxes

### Today's session panel (derived output — updates on every state change)
- Grouped by exercise, in order first logged
- Per exercise: list of sets with set number, sets×reps@weight, estimated 1RM
- PR badge on any set that beats the previous best estimated 1RM for that exercise (across all time)
- vs-last-session delta: compares today's max weight for the exercise to last session's max weight (+Xkg / −Xkg / same / first time)
- Delete button on each set row

### Weekly dot tracker (derived output)
- 7 dots for Sun–Sat of the current week
- Filled dot = at least one set logged that day
- Today's dot has a ring if not yet trained
- Computed fresh from state on every render

### Stats bar (derived output)
- Today's set count
- Days trained this week
- Total weekly volume (kg × reps × sets, summed across the week)

### PR board — "PRs" tab (derived output)
- One row per exercise ever logged
- Columns: exercise name, best weight × reps, estimated 1RM, date achieved
- Sorted by estimated 1RM descending
- Volume bar chart: total volume lifted per exercise, top 8 by volume

### Session history — "History" tab (derived output)
- Every past training day, newest first
- Each day: date, exercises trained, total volume, chips for each set (PR chips highlighted)

### Reset
- "Clear all data" button in the Log tab footer
- Confirmation step: requires clicking a second "Yes, clear everything" button that appears inline (no modal, no alert)
- On confirm: clears state array, removes localStorage key, re-renders to empty state

---

## State Shape

```javascript
const state = {
  sets: [
    {
      id: "1718123456789",      // Date.now().toString()
      exercise: "Back squat",
      weight: 80,               // kg
      reps: 5,
      setsCount: 3,             // how many sets this entry represents
      date: "2025-06-17"        // YYYY-MM-DD, local date
    }
  ]
};
```

All derived values — PRs, weekly volume, session groups, deltas — are computed fresh from `state.sets` on every render. No derived data is stored.

---

## Interaction Types (satisfies ≥3 requirement)
1. **Button click** — log set, delete set, quick-pick chip, tab navigation, reset, confirm reset
2. **Form / text input** — exercise name field with datalist autocomplete
3. **Number input** — weight, reps, sets
4. **Keyboard** — Enter on any field submits the form; exercises autocomplete via datalist
5. **Dropdown/tab** — three-tab navigation (Log, PRs, History)

---

## Derived Output (satisfies requirement)
Every piece of data shown beyond the raw logged values is computed:
- **Estimated 1RM**: `weight × (1 + reps/30)` — Epley formula; when reps = 1, returns weight directly
- **PR detection**: set is a PR if its e1RM > all previous e1RMs for that exercise
- **vs-last-session delta**: max weight today minus max weight in the most recent prior session
- **Weekly volume**: sum of (weight × reps × setsCount) for all sets in the current calendar week
- **Days trained this week**: count of distinct dates in current week with at least one set
- **Volume bar chart**: sum of all-time volume per exercise, normalized to bar widths

---

## Epley Formula Note
`e1RM = weight × (1 + reps / 30)`
- When reps = 1: result equals weight (correct — 1RM is the weight itself)
- Rounds to 1 decimal place for display
- Used only for PR comparison, not stored

---

## Edge Cases
- First set for an exercise: always a PR (no prior record exists)
- Logging reps = 1: e1RM equals weight; handles cleanly
- No sets logged: all panels show empty states with prompts to log
- Deleting the only PR set: next-best set automatically becomes the PR (recomputed from remaining data)
- Two sets with equal e1RM in same session: both marked as PR (tied)
- Very long exercise name: truncated with ellipsis in history chips
- Reset with no data: button still works, no errors

---

## Out of Scope (for MP2)
- Planned workouts / templates
- Rest timer
- Body weight tracking
- Multiple weight units (lbs) — kg only for now
- Export to CSV or clipboard (would be an extension)
- Charts of strength over time (history view shows raw sessions)
