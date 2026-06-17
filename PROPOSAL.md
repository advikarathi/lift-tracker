# PROPOSAL.md

## What I'm building
A personal lifting tracker that logs sets, reps, and weight for each exercise, auto-detects personal records, and shows weekly volume and progress over time — all in the browser with no app or account required.

## Who it's for / why
For me (Advika). I lift regularly combining weightlifting and Pilates, and I keep losing track of what weight I used last session for a given exercise. I want to know: did I hit a PR today? Am I lifting more than last week? What's my estimated 1RM on squat right now? My Notes app doesn't compute any of that — this tool does.

## The state it tracks
- Every set ever logged: exercise name, weight (kg), reps, sets count, and date
- Derived from that single array: today's session, per-exercise PRs (by estimated 1RM), weekly volume (kg lifted), days trained this week, and a comparison against the last session for each exercise

## Core features
1. Log a set: exercise name (with quick-pick chips for common lifts), weight, reps, sets count
2. Auto-detect PRs using the Epley estimated 1RM formula (weight × (1 + reps/30)) — badge the set if it's a new record
3. Show today's session grouped by exercise, with a vs-last-session delta (+kg / −kg / same)
4. PR board: every exercise ranked by best estimated 1RM, with date achieved
5. Session history: every past workout by date with volume totals
6. Weekly dot tracker showing which days you trained
7. Reset / clear all data with a confirmation step
8. localStorage so data survives closing the tab

## What I don't know yet
- How to implement the Epley formula correctly and use it consistently for PR detection
- How to group and sort past sessions from a flat array without a database
- How to make a datalist autocomplete feel good for exercise names
- How to handle the edge case where reps = 1 in the 1RM formula
- How to structure the reset so it clears localStorage AND resets the UI in one action
