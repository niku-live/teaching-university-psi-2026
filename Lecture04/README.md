# Lecture 4

## What was discussed

This lecture wasn't delivered as a live class session - attendance was too low across the scheduled slots to run it as planned. The material below is still real and complete (the code, the tutorial, the companion walkthrough all exist and work), so it's published here for anyone who wants to study it on their own time, rather than as a recap of something taught in the room.

## Step-by-Step Tutorial: C# Language Features, and a Real Timezone Bug

This week's theory covered [C# Basics](https://github.com/smagurauskas/software-engineering/blob/main/04-csharp-basics.qmd) (types, operators, generics, enums, records, interfaces), [SOLID](https://github.com/smagurauskas/software-engineering/blob/main/04-solid.qmd), and [Time](https://github.com/smagurauskas/software-engineering/blob/main/04-time.qmd) (`DateTime` vs `DateTimeOffset`, wall-time pitfalls). This tutorial applies the C# Basics and Time material to StudySpot; SOLID's Open/Closed principle frames one of the smaller design choices along the way, without a separate refactor of its own.

This week's focus (still Alpha - see the [ROADMAP](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/03/ROADMAP.md), which spans Lecture 1 through Lecture 6): close out every remaining item in Alpha's "Requirement coverage still needed" list in one sitting, since this week's theory happens to be exactly the set of C# language features that list asks for:

### Fixing Timezone bug
The existing "must be in the future" check compares `StartsAt` (a bare `DateTime`) against the *server's* `DateTime.Now` - neither carries explicit timezone information, so "in the future" silently depends on whichever timezone the server process happens to be running in. Switch `StartsAt` to `DateTimeOffset`, validate against `DateTimeOffset.UtcNow`, and fix the React form to convert its local `datetime-local` input into an explicit UTC instant before sending it - closing the gap rather than just relocating it.

### Implementing technical requirements

Requirements:  
- Creating and using your own `class`, `struct`, `record` and `enum`. 1 type must be immutable.  
Implementation:  
- Add a `Rating` **struct** (`Models/Rating.cs`) - the first `struct` in this codebase (deliberately plain, not a `record struct`, so no built-in `==`), validating 1-5 in its constructor.
- Add `StudySessionSummary`, an immutable **`record`** with no identity of its own, served by a new `GET /api/studysessions/summary` endpoint alongside the existing full `StudySession` `class`.
- Add a `SessionStatus` **enum** (`Scheduled`/`Full`), computed from `SeatsAvailable` instead of scattering `if` checks on a raw number wherever "is this session full" matters.

Requirements:  
- Extension method usage.  
- Named and optional argument usage.  
Implementation:  
- Add `StudySessionExtensions.UpcomingOnly()`, an **extension method** that uses **LINQ** to filter out sessions that have already started, with an **optional, named argument** (`asOf`) for anything that needs a fixed point in time instead of the real clock.
- **`asOf` is called with a real, non-default value.** `GET /api/studysessions` exposes it as an actual `?asOf=...` query parameter, with its own "Upcoming as of" date field on `/study-sessions` - picking a future date really does call `UpcomingOnly(asOf: asOf)` with something other than `null`, not just a parameter that exists so the signature has one.
- **Named arguments where it's not optional.** A second extension method, `StudySessionExtensions.Filter(course, minSeatsAvailable)`, is wired into `GetAll` and `GetSummaries` *differently on purpose*: `GetSummaries` calls `Filter(minSeatsAvailable: minSeatsAvailable)`, skipping `course` entirely - C# has no positional syntax for that, so naming it is the only way, not a style choice. Both pages have matching filter UI: `/study-sessions` has course/seats/as-of fields, `/session-summaries` deliberately has only a seats field, because that's genuinely all `GetSummaries` accepts.

Requirements:  
- Implement at least one of the standard .NET interfaces (`IEnumerable`, `IComparable`, `IComparer`, `IEquatable`, `IEnumerator`, etc.)
Implementation:  
- Implement **`IComparable<StudySession>`** - a standard .NET interface - so `List<StudySession>.Sort()` orders sessions by start time with no comparer to write or pass in.

Reference Guide: [PSI 2026 Playground PR #7](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/7) (`lectures/04-draft` &rarr; `lectures/04`) has the end result. Start with [WALKTHROUGH-04.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/04-draft/WALKTHROUGH-04.md) for the exact step-by-step (assumes you've already done [WALKTHROUGH-03.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/03/WALKTHROUGH-03.md)) - see [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/04-draft/WALKTHROUGHS.md) for the full index.

## Homework

No homework is formally due because of this lecture. Since it wasn't delivered live and most of the class hasn't seen this material, going through the Step-by-Step Tutorial above (or the companion [WALKTHROUGH-04.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/04-draft/WALKTHROUGH-04.md)) on your own time is recommended rather than required - it closes out the rest of your own project's Alpha requirement checklist (record/enum/named-and-optional-arguments/extension method/LINQ/standard interface), and the `DateTime`-vs-`DateTimeOffset` audit is worth doing regardless of whether your own project has hit the same bug yet.
