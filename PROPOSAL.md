# PROPOSAL.md

## What I'm building
A personal, offline fitness tracker that logs strength, cardio, and bodyweight activity; supports reusable routines; and turns the saved data into PRs, recovery, and progress insights without an account or server.

## Who it's for / why
For me (Advika). I lift regularly combining weightlifting and Pilates, and I keep losing track of what weight I used last session for a given exercise. I want to know: did I hit a PR today? Am I lifting more than last week? What's my estimated 1RM on squat right now? My Notes app doesn't compute any of that — this tool does.

## The state it tracks
- Profile-scoped workout entries: activity type, exercise, strength/cardio details, and date
- Saved routines, daily recovery check-ins, body measurements, measurable bodyweight goals, theme preference, and a temporary undo/redo action
- Derived output: today's session, per-exercise PRs, strength volume, streak, last-session comparison, goal progress, and rule-based training guidance

## Core features
1. Log strength, cardio, or bodyweight work with quick-picks and a built-in exercise library
2. Auto-detect PRs using the Epley estimated 1RM formula (weight × (1 + reps/30)) — badge the set if it's a new record
3. Show today's session grouped by exercise, with a vs-last-session delta (+kg / −kg / same)
4. PR board: every exercise ranked by best estimated 1RM, with date achieved
5. Save and run reusable routines, including built-in Back & biceps, Lower body, Push day, and Full body options
6. Track recovery, hydration, body measurements, and a bodyweight target in a dedicated Progress view
7. Edit entries, undo/redo a deletion, export a profile's workouts as CSV, and reset an active profile safely
8. Persist all profile data and theme preference with localStorage

## What I don't know yet
- How to implement the Epley formula correctly and use it consistently for PR detection
- How to group and sort past sessions from a flat array without a database
- How to make a datalist autocomplete feel good for exercise names
- How to handle the edge case where reps = 1 in the 1RM formula
- How to structure a profile-safe reset that clears the right saved data and refreshes the UI in one action
- How to migrate older localStorage data safely as the state shape grows
- How to generate a browser-only CSV download without a server
