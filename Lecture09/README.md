# Lecture 9

## What was discussed

_TBD - to be filled in after the lecture_

## Step-by-Step Tutorial: A Real Database with Entity Framework Core, then Concurrency Conflicts and a Custom Exception

The previous theory lecture covered [Persistence and ORM](https://github.com/smagurauskas/software-engineering/blob/main/08-persistence-and-orm.qmd): what an ORM solves, `DbContext` and entities, migrations, connection strings, seeing the SQL, async queries, testing with databases and concurrency. Lecture 8 already made StudySpot's repository async and covered it with tests, so this lecture puts a real database behind that same interface. It works toward two **Lab Assignment #2** requirements (deadline: week 11): *relational database and Entity Framework is used for storing all the data*, and *create at least 1 exception type and throw it; meaningfully deal with it by attaching custom handling logic*.

There are two stages, and the project builds and all tests pass at the end of each one:

- **Stage 1 - Entity Framework Core with SQLite**: a `DbContext` with seeding and a value conversion for our own `Rating` type, an Entity Framework implementation of `IStudySessionRepository` (async all the way, with the request's `CancellationToken`), swapped in with one line in `Program.cs`, and the first migration with the `dotnet ef` tool. The integration tests from Lecture 8 now run against a real database of their own, and a new test catches a forgotten migration. SQLite keeps the whole database in one file, so there's nothing to install. The theory demo uses PostgreSQL in Docker, and the walkthrough lists what changes if your team prefers it.
- **Stage 2 - Concurrency conflicts, a custom exception and a global handler**: two people editing the same session would silently overwrite each other's changes (a "lost update"). Every session gets a version, Entity Framework only saves when the version is still the one the client read, and a conflict becomes our own `StudySessionConflictException`. One `IExceptionHandler` turns it into a `409 Conflict` for every endpoint, with a problem details body, with no `try`/`catch` in the controller.

The playground's NUnit and MSTest test projects run the same tests against the database too.

A generic type, the shared memory scenario with concurrent collections, request/response DTOs and end-to-end tests with Playwright come next lecture, the last one before Deadline 2.

Reference Guide: [PSI 2026 Playground PR #14](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/14) (`lectures/09-draft` &rarr; `lectures/09`) has the end result. Start with [WALKTHROUGH-09.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/09-draft/WALKTHROUGH-09.md) for the exact step-by-step. It assumes you've already done [WALKTHROUGH-08.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08/WALKTHROUGH-08.md). See [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/09-draft/WALKTHROUGHS.md) for the full index.

## Homework

TBD
