# FlowState

A free, ad-free, offline-first Android app for personal productivity and daily wellbeing: tasks, ideas, checklists, habits and a calendar, plus an evening routine that meets you when you get home and hands you a finished plan for the rest of the night.

Everything lives on your phone. No accounts, no servers, no analytics. The only optional network call is the AI evening planner, and it falls back silently to a fully offline planner when it's unavailable.

## The evening loop

1. **You arrive home.** A geofence (or a weekend-morning alarm, or you tapping *Start check-in*) opens the check-in.
2. **You answer five quick questions.** Energy, sleepiness, stress, headache and motivation, each a 0–10 slider with an optional note.
3. **You get one plan.** The planner looks at your mood, open tasks, habits and their priority, and returns a time-ordered evening: tasks, habits, meals, rest and a reflection near bedtime, each with a short reason.
4. **You shape it.** Agree, regenerate with a note ("skincare is only 5 minutes"), or discard it. Notes are remembered for future evenings.
5. **You move through it.** The Plan tab is a checklist; ticking a task block completes the real task.
6. **At 9 PM** a night review shows what got done, what's pending and what rolls to tomorrow.

## Features

| Area | Highlights |
|---|---|
| **Flow** | Tasks (priority, due dates, reminders, subtasks), colour-coded ideas, checklists, categories, list or grid view, drag to reorder, swipe to delete with undo |
| **Calendar** | Expandable month view with per-day task dots; tick tasks straight from a day |
| **Habits** | Yes/no and numeric habits, weekday schedules and weekly targets, streaks, priority ranking, opt-in mood logging and reminders, detailed stats |
| **Hobbies** | Optional and fully flexible: every field is optional, so a hobby can be a full session log or just a collection of books or artwork. Per-hobby history with a weekly rhythm, and a two-step session logger. The whole feature can be turned off in Settings |
| **Plan** | Tonight's agreed plan as a checklist; edit, remove or add blocks directly |
| **Check-in** | Geofence, weekend and manual triggers; AI planner with offline fallback |
| **Night review** | Daily 9 PM recap of done, pending and pushed-to-tomorrow |
| **Settings** | Theme, dynamic colour and a custom colour wheel, categories, configurable bottom navigation, JSON backup and restore |
| **Widget** | Per-habit home-screen pill; tap to complete |

The full feature inventory is in [`docs/FEATURES.md`](docs/FEATURES.md).

## Design principles

- **Reduce decisions.** One plan and one next step, not menus of options.
- **Opt-in only.** Mood logging, habit reminders, categories and widgets stay off until you ask for them.
- **Degrade, never break.** Every AI or network path has a deterministic offline fallback.
- **Local-first privacy.** Your data stays on the device. The only thing that leaves is the anonymised prompt for the AI planner, and only when you complete a check-in.

## Tech stack

Kotlin, Jetpack Compose (Material 3), Clean Architecture with MVVM, Hilt, Room (schema v31 with full migrations), DataStore, Coroutines/Flow, Navigation 3, Glance widgets, AlarmManager and Play Services Geofencing. The AI planner talks to OpenRouter through an `EveningPlanner` interface, so the backend is swappable.

```
app                      MainActivity, navigation host, theme wiring
feature:flow             tasks, ideas, checklists, categories
feature:calendar         month calendar and daily task list
feature:habits           habit list, editor, detail and stats
feature:checkin          check-in flow, plan screen, night review
feature:settings         settings, appearance, categories, backup, about
core:domain              models, repository interfaces, use cases
core:data                Room, repositories, DataStore, backup, AI planner
core:designsystem        theme, icons, shared UI
core:notifications       reminders, check-in alarms, geofencing, boot receiver
core:widgets             Glance habit widget and its config activity
core:testing             shared test fixtures
```

## Getting started

Requirements: a recent stable Android Studio (its bundled JDK works) and an Android device or emulator.

```bash
git clone https://github.com/<your-username>/flowstate.git
cd flowstate
./gradlew installDebug
```

**Optional: AI planner.** Add your OpenRouter key to `local.properties` (never committed) (the exact property name is read in `core:data`). Without a key the app uses the offline planner. The default model can be overridden in the same file.

**Optional: home geofence.** Set your home coordinates in `HomeLocationLocal.kt` (gitignored). There is no settings screen for this yet.

## Data and backup

Export everything to a single JSON file from Settings and restore it on any build that understands the schema version. Restore merges: records with the same ID are overwritten and everything else is kept.

## Status and known gaps

- No cloud sync or multi-device support, by design so far.
- No recurring tasks; repetition lives in Habits.
- Backup doesn't yet include habit mood ratings, `priorityRank` or `rolloverIfMissed`, so a restore resets them to defaults. Check-in history and evening plans are intentionally not exported.
- The end-of-day cutoff drives plan expiry but has no settings screen yet.
- Home location is set in code, not in the app.
- Mood is logged per habit and drives the check-in; there is no standalone mood diary.

## Localization

English and Spanish.

## Contributing

This is a personal project built for my own use. Issues and suggestions are welcome, but I may not accept pull requests.

## License

Apache-2.0. See [`LICENSE`](LICENSE).
