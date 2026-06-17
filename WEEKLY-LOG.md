# Weekly Log — Lift Tracker (MP2)

---

## Iteration 1: Structure and First Interaction

**What HTML structure did you choose?**

I started by sketching out what the page actually needs to show: a sticky header, a row of stat cards, a week grid, a log form, and today's session. That translated directly into semantic elements — `<header>` for the top bar, `<nav role="tablist">` for the three tabs (Log, PRs, History), `<main>` for the content area, and `<section>` for each tab panel with `role="tabpanel"`. I wrapped everything in a single `.app` div to control max-width and centering.

The tab system was the key structural decision. I went with three `<section>` elements that toggle `display: none / block` via a CSS class, rather than separate pages. That keeps all the DOM in one place so JavaScript can update any section at any time, even when it's not visible, which matters because the PR board and history both read from the same state as the log form.

**What was your first piece of JavaScript?**

The form submit handler — when the user fills in exercise, weight, reps, and sets and hits Log Set, the handler validates the fields, pushes a new object onto `state.sets`, saves to localStorage, and calls `renderAll()`. Getting that one path working end-to-end (type → validate → push to state → re-render → see it on screen) proved the whole architecture before I wrote the PR logic or the history view.

**If AI generated it, what did you have to understand to make it work with your HTML?**

I used AI to help with parts of the rendering functions, but I had to understand the ID connections between the JS and the HTML — `document.getElementById('today-sets')` only works if the element actually exists in the markup with that exact ID. I also had to understand that `e.preventDefault()` is required on form submit to stop the page from reloading, and that `renderAll()` needs to be called after every state mutation, not just after the form submit.

---

## Iteration 2: Core Functionality

**What was the hardest interaction to implement?**

PR detection. The tricky part is that "is this set a PR?" is a question about the entire history of every set ever logged for that exercise, not just today's session. My first attempt stored PRs separately and updated them on each new set — but that broke when I deleted a set that was the current PR, because the stored PR was now wrong. The fix was to never store derived data: `isSetPR()` scans `state.sets` from scratch every time it's called. That's slower but always correct, and with the size of a personal lifting log it's instant.

The Epley formula edge case also tripped me up. The formula is `weight × (1 + reps/30)`, which gives a wrong answer when reps = 1: `weight × (1 + 1/30) = weight × 1.033`, not `weight`. I added an explicit `if (reps === 1) return weight` branch and tested it by logging a single-rep set.

**What JavaScript concepts did you use?**

- **Arrays**: `state.sets` is the single source of truth. Everything is `.filter()`, `.map()`, `.reduce()`, and `Set` for deduplication (e.g. `new Set(sets.map(s => s.date)).size` counts distinct training days).
- **Objects**: Each set is a plain object `{ id, exercise, weight, reps, setsCount, date }`. PRs are built into a map object keyed by exercise name.
- **Functions**: I separated rendering from logic — `getPRs()`, `isSetPR()`, `lastSession()` are pure functions that take no arguments and read from state. The render functions (`renderStats`, `renderToday`, `renderPRs`, `renderHistory`) only write to the DOM.
- **Conditionals**: Lots of branching for empty states, PR detection, and the vs-last-session delta (up / down / same / first time).
- **DOM methods**: `innerHTML` for bulk re-renders, `querySelector`/`getElementById` to target elements, `addEventListener` for clicks and keyboard events.

**What bugs did you find and fix?**

1. **History tab was silently broken.** `renderHistory()` called `document.getElementById('history-list')` but I never added that element to the HTML. The function ran, found `null`, and crashed without any visible error. Fixed by adding `<div id="history-list">` inside the History section card.

2. **Date parsing shifted by one day.** When I used `new Date("2025-06-17")`, JavaScript parsed it as UTC midnight and displayed it as June 16 in US timezones. Fixed by parsing the date string manually as local time: `const [y, m, d] = str.split('-').map(Number); new Date(y, m-1, d)`.

3. **Chip buttons disappeared after logging.** Quick-pick chips are re-rendered inside `renderQuickChips()`, which is called by `renderAll()`. But the chip click handlers were attached in `renderQuickChips()` too — so they existed, got cleared on re-render, and got re-attached. This actually worked, but I noticed the exercise field was losing focus after every log. Fixed by focusing the weight field (not the exercise field) after submit, so the user's cursor lands in the right place for the next set.

---

## Iteration 3: Polish and Test

**What feedback did you get?**

I had a classmate try it without any explanation. The main thing they got confused by: they didn't notice the tab bar at first and thought the PRs and History didn't exist. Adding the emoji icons to the tab labels (✍️ Log, 🏆 PRs, 🕒 History) helped significantly — they're visually distinct and scannable at a glance.

They also asked what "est. 1RM" meant. I didn't change the label (it's standard lifting terminology) but I noted it in the README's "The math" section.

**What edge cases did you handle?**

- **Empty state on first load**: every panel checks for empty data and shows a prompt rather than an empty box or an error
- **Deleting a PR set**: the next-best set automatically becomes the PR because `isSetPR()` recomputes from the remaining sets
- **Reset with no data**: the button works and produces no errors even if `state.sets` is already empty
- **Reps = 1**: the Epley formula returns weight directly rather than inflating it
- **Long exercise names**: truncated with `text-overflow: ellipsis` in the history chips
- **localStorage unavailable**: all `localStorage` calls are wrapped in try/catch so the app works even in a private browsing tab that blocks storage

**What would you add with more time?**

- A pounds/kg toggle — I lift in kg but most of my classmates use lbs
- A strength-over-time chart per exercise, so I can see a trend line rather than just my best-ever
- Export to clipboard or CSV, so I can paste a session into my notes
- A planned workout template I can pull up before a session and log against, rather than typing each exercise fresh
