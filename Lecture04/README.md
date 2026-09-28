# Lecture 4

## What was discussed

_TBD - to be filled in after the lecture_

## Step-by-Step Tutorial: C# Language Features, and a Real Timezone Bug

This week's theory covered [C# Basics](https://github.com/smagurauskas/software-engineering/blob/main/04-csharp-basics.qmd) (types, operators, generics, enums, records, interfaces), [SOLID](https://github.com/smagurauskas/software-engineering/blob/main/04-solid.qmd), and [Time](https://github.com/smagurauskas/software-engineering/blob/main/04-time.qmd) (`DateTime` vs `DateTimeOffset`, wall-time pitfalls). We applied the C# Basics and Time material live to StudySpot; SOLID's Open/Closed principle frames one of the smaller design choices along the way, without a separate refactor of its own.

This week's focus (still Alpha - see the [ROADMAP](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/03/ROADMAP.md), which spans Lecture 1 through Lecture 6): close out every remaining item in Alpha's "Requirement coverage still needed" list in one sitting, since this week's theory happens to be exactly the set of C# language features that list asks for:

- Add a `SessionStatus` **enum** (`Scheduled`/`Full`), computed from `SeatsAvailable` instead of scattering `if` checks on a raw number wherever "is this session full" matters.
- Add `StudySessionSummary`, an immutable **`record`** with no identity of its own, served by a new `GET /api/studysessions/summary` endpoint alongside the existing full `StudySession` `class`.
- Add `StudySessionExtensions.UpcomingOnly()`, an **extension method** that uses **LINQ** to filter out sessions that have already started, with an **optional, named argument** (`asOf`) for anything that needs a fixed point in time instead of the real clock.
- Implement **`IComparable<StudySession>`** - a standard .NET interface - so `List<StudySession>.Sort()` orders sessions by start time with no comparer to write or pass in.
- **Live demo: a real timezone bug.** The existing "must be in the future" check compares `StartsAt` (a bare `DateTime`) against the *server's* `DateTime.Now` - neither carries explicit timezone information, so "in the future" silently depends on whichever timezone the server process happens to be running in. Switch `StartsAt` to `DateTimeOffset`, validate against `DateTimeOffset.UtcNow`, and fix the React form to convert its local `datetime-local` input into an explicit UTC instant before sending it - closing the gap rather than just relocating it.

Reference Guide: [PSI 2026 Playground PR #7](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/7) (`lectures/04-draft` &rarr; `lectures/04`) has the end result. Start with [WALKTHROUGH-04.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/04-draft/WALKTHROUGH-04.md) for the exact step-by-step (assumes you've already done [WALKTHROUGH-03.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/03/WALKTHROUGH-03.md)) - see [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/04-draft/WALKTHROUGHS.md) for the full index.

Do the same for your own team repository this week: check your `ROADMAP.md`'s remaining Alpha requirements against this list, add whichever of the enum/record/extension-method/LINQ/interface items you're still missing, and audit any place your own project stores or compares a date/time for the same `DateTime`-vs-`DateTimeOffset` gap.

## Homework

No homework was assigned this week. Closing out any of your own team's project's remaining Alpha requirement items (record/enum/named-and-optional-arguments/extension method/LINQ/standard interface), and auditing your own date/time handling as described above, is worth doing on your own time, but nothing is due specifically because of this lecture.
