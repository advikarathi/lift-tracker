# PROPOSAL.md

## What I'm building
A personal, offline fitness tracker that logs strength, cardio, bodyweight, Pilates, barre, yoga, and mobility work; supports reusable workout routines; and turns saved entries into PRs, weekly training stats, recovery notes, body-progress charts, and simple coaching feedback. It runs entirely in the browser with vanilla JavaScript, localStorage, and no account, server, API, framework, or CDN.

## Who it's for / why
For me (Advika), and for anyone who trains across a mix of lifting and recovery-style workouts. I lift regularly and also do Pilates/mobility, so I wanted one place to answer: what did I do last time, did I improve, did I hit a PR, and am I staying consistent this week? A notes app can store workouts, but this tool computes progress from them.

## The state it tracks
- Multiple profiles, each with its own workout history and short-term/long-term goals
- Workout entries with activity type, exercise/activity name, date, and the right fields for that type: weight/reps/sets, distance/duration, or duration only
- Built-in starter routines plus user-saved training templates
- Daily recovery check-ins: sleep, readiness, and water intake
- Body measurements and an optional bodyweight target/date
- Theme preference, edit mode, and temporary undo/redo state for deleted entries
- Derived output that is calculated from saved state: today's session, PRs, estimated 1RM, volume, weekly training days, streak, last-session comparison, bodyweight trend, goal progress, and rule-based training guidance

## Core features
1. Log strength, cardio, bodyweight, Pilates, barre, yoga, or mobility work from a type-aware exercise/activity dropdown, with custom entries still allowed
2. Validate the form differently depending on workout type, so strength requires weight/reps, cardio requires distance/duration, and yoga/Pilates/barre/mobility require duration
3. Auto-detect PRs for strength work using the Epley estimated 1RM formula: `weight * (1 + reps / 30)`
4. Show today's session grouped by exercise, including PR badges, estimated 1RM, and a vs-last-session comparison
5. Show a PR board, five-week strength-volume chart, and top-lifts-by-volume chart
6. Save and run reusable routines, including built-in Back & biceps, Lower body, Push day, and Full body options; each logged exercise advances to the next editable entry
7. Track sleep, readiness, water intake, body measurements, and a bodyweight goal in the Progress view
8. Provide edit, delete, undo delete, redo delete, CSV export, dark mode, keyboard shortcut, and profile-safe reset controls
9. Persist the active data with localStorage and re-render the interface after each meaningful state change

## JavaScript I can explain
- `e1rm(weight, reps)` calculates an estimated one-rep max and handles the `reps === 1` edge case by returning the weight directly
- `getPRs()` loops through saved strength entries, groups them by normalized exercise name, and keeps the best estimated 1RM for each exercise
- `isSetPR(set)` checks whether one visible set should receive a PR badge by comparing it against all other saved strength entries for that exercise
- `renderAll()` refreshes the app after state changes so the stats, today's session, PR board, history, templates, recovery, and progress views stay in sync
- The log-form submit handler validates the selected activity type, builds one entry object, saves it, re-renders the page, and advances the active routine when needed

## What I had to figure out
- How to keep one browser-only state model flexible enough for strength, cardio, bodyweight, and timed workouts
- How to calculate PRs and progress from a flat array of entries without a database
- How to make the exercise library dropdown feel cleaner than a long wall of visible buttons
- How to make reusable routines preserve exercise order while still letting the user change today's weight/reps/sets
- How to edit and delete entries without breaking derived PR and history calculations
- How to generate a CSV download in the browser without a server
- How to keep the app assignment-compliant: vanilla JavaScript only, static GitHub Pages hosting, no backend, and no external APIs
