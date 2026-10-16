# Lecture 6

> [!IMPORTANT]
> **Next week is Deadline 1** (Lab Assignment #1): **2026-10-21 20:00 (Vilnius time)**. Use the [Lecture 5 checklist](../Lecture05/README.md) to check your project against every requirement before then. Nothing in this lecture is required for Deadline 1.

## What was discussed

Part of this lecture was used for one team's Deadline 1 presentation, so the practice part was kept short and focused on SOLID applied to real StudySpot code (the full step-by-step is below):

- Started from `StudySessionsController`, which owned the data, the filtering, ordering and rating rules, and the HTTP handling all at once, and asked what would have to change for a database, a new rule or a test.
- **Single Responsibility**: extracted `IStudySessionService` and `StudySessionService`, so the controller only handles routing, model binding and status codes. The app was built and run at this checkpoint to show nothing behaved differently.
- **Dependency Inversion + Dependency Injection**: moved the data behind `IStudySessionRepository` with an in-memory implementation, passed it to the service through the constructor, and registered both in `Program.cs` (with a note on why the repository is a singleton and the service is scoped).
- **Liskov Substitution**: swapped in a repository that returns copies and showed that the service keeps working only because every change ends with an explicit `repository.Update(...)` - every implementation of an interface has to honor the same contract.
- Open/Closed and Interface Segregation were pointed out in the existing code, together with the warning that over-applying SOLID can make code worse.

## Step-by-Step Tutorial: SOLID in Practice - a Service, a Repository, and Dependency Injection

This week's theory covered [Dependency Injection](https://github.com/smagurauskas/software-engineering/blob/main/06-dependency-injection.qmd), [Extension methods](https://github.com/smagurauskas/software-engineering/blob/main/06-extension-methods.qmd) and [LINQ](https://github.com/smagurauskas/software-engineering/blob/main/06-linq.qmd). Because this lecture is short (Deadline 1 team presentations take most of the time), it picks the one idea that ties directly into Deadline 1 and the rest of the course: the [SOLID](https://github.com/smagurauskas/software-engineering/blob/main/04-solid.qmd) principles from Lecture 4's theory, applied to real StudySpot code with this week's Dependency Injection - instead of toy examples with shapes and printers.

This week's focus (still Alpha - see the [ROADMAP](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/06/ROADMAP.md), which spans Lecture 1 through Lecture 6): take `StudySessionsController`, which currently owns the data, the filtering and ordering rules, the rating rule, *and* the HTTP handling, and split it up:

- **Single Responsibility**: move the rules into a `StudySessionService` (behind an `IStudySessionService` interface the controller depends on). The controller shrinks to routing, model binding and status codes.
- **Dependency Inversion + Dependency Injection**: hide where sessions are stored behind an `IStudySessionRepository` (with an in-memory implementation for now), pass it to the service through the constructor, and register both in `Program.cs`.
- **Liskov Substitution**: a short "gotcha" showing why every implementation of that interface must honor the same contract - today's in-memory repository would hide a bug that a database-backed one would expose.
- **Open/Closed, Interface Segregation**: pointed out in existing code (`Filter`, `IEnumerable<T>` parameters), not refactored.

It is a refactor: no endpoint changes behavior. It also gives Beta's Entity Framework lecture a natural place to plug in later - only the repository will need to change.

Reference Guide: [PSI 2026 Playground PR #9](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/9) (`lectures/06-draft` &rarr; `lectures/06`) has the end result. Start with [WALKTHROUGH-06.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/06/WALKTHROUGH-06.md) for the exact step-by-step (assumes you've already done [WALKTHROUGH-04.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/04/WALKTHROUGH-04.md); Lecture 5 had no walkthrough) - see [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/06/WALKTHROUGHS.md) for the full index.

## Homework

Not mandatory, and nothing here counts toward Deadline 1 - **focus on your Deadline 1 submission first**. For the curious, after you've submitted:

- Look at your own team's controllers. Can you find one class that has more than one reason to change (data, rules and HTTP handling mixed together)? Try the same service + repository split from [WALKTHROUGH-06.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/06/WALKTHROUGH-06.md) on it.
- Write a second implementation of your repository interface (for example one that returns copies, as in step 6 of the walkthrough) and check that your service still behaves correctly with it.
- Read [Lecture 4's SOLID slides](https://github.com/smagurauskas/software-engineering/blob/main/04-solid.qmd), including the warning about over-applying the principles, and decide whether the split was worth it for your project's size.
