# Lecture 8

## What was discussed

_TBD - to be filled in after the lecture_

## Step-by-Step Tutorial: Entity Framework Core and async/await for StudySpot

This week's theory covered [Persistence and ORM](https://github.com/smagurauskas/software-engineering/blob/main/08-persistence-and-orm.qmd) (Entity Framework, migrations, change tracking, context lifetime, async and cancellation). This is the first practice lecture for **Lab Assignment #2** (deadline: week 11), and it covers two of its requirements directly: *a relational database and Entity Framework is used for storing all the data*, and *usage of `async`/`await`, no IO is performed synchronously*.

Until now, StudySpot kept its sessions in an in-memory list, so every restart lost all the data. In Lecture 6 we put that list behind `IStudySessionRepository` and said only the repository would have to change when a database arrived. This lecture checks that claim, in two stages. The project builds and works at the end of each one.

- **Stage 1 - Entity Framework Core**: add a `StudySpotDbContext` for SQLite, a second implementation of the same interface (`EfStudySessionRepository`), and the first migration, applied automatically at startup. Swap it in through Dependency Injection: the service and the controller don't change. Along the way: a value converter for our own `Rating` struct, why the repository becomes scoped (and the "captive dependency" error you get if it isn't), and the Lecture 6 Liskov bug becoming real once the repository returns copies.
- **Stage 2 - async/await and cancellation**: make the repository, the service and the controller async from the bottom up, and pass the request's `CancellationToken` from the controller all the way down to every database query.

Reference Guide: [PSI 2026 Playground PR](https://github.com/niku-live/teaching-university-psi-2026-playground/compare/lectures/08...lectures/08-draft) (`lectures/08-draft` &rarr; `lectures/08`) has the end result. Start with [WALKTHROUGH-08.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08-draft/WALKTHROUGH-08.md) for the exact step-by-step (assumes you've already done [WALKTHROUGH-06.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/06/WALKTHROUGH-06.md); Lecture 7 was the Deadline 1 presentations and had no walkthrough). See [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08-draft/WALKTHROUGHS.md) for the full index.

## Homework

TBD
