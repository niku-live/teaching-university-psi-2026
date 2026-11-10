# Lecture 9

## What was discussed

Went through the Deadline 2 (Lab Assignment #2) requirements one by one, using the checklist below: what each requirement means, what will be checked during the review, and what every team member must be able to explain about their own project. That took the whole lecture, so the Entity Framework and custom exception tutorial below wasn't covered in class. It is optional home review.

## Deadline 2 Review Checklist

This lecture prepares you for **Lab Assignment #2 (Deadline 2)**: **2026-11-25 20:00 (Vilnius time)**, maximum **1.5** points. It covers material from lectures 7-10, and everything from Deadline 1 must still be in place.

- [**Checklist**](checklist/checklist.qmd) - every requirement, what is checked, what every team member must be able to explain, and a short good/bad example for each. Render or preview it with `quarto preview checklist/checklist.qmd` (see the main [README](../README.md) for Quarto setup).
- [**Detailed examples**](checklist/examples/README.md) - one page per requirement with more good/bad examples, common mistakes, and questions you may be asked:
  1. [A relational database and Entity Framework store all the data](checklist/examples/01-database-and-ef.md)
  2. [A generic type with a generic method](checklist/examples/02-generics.md)
  3. [Your own exception type, thrown and meaningfully handled](checklist/examples/03-custom-exceptions.md)
  4. [`async`/`await`, no synchronous IO](checklist/examples/04-async-await.md)
  5. [A shared memory scenario](checklist/examples/05-shared-memory.md)
  6. [Dependency Injection](checklist/examples/06-dependency-injection.md)
  7. [Unit and integration test coverage of at least 50%](checklist/examples/07-tests-and-coverage.md)
  8. [Pull requests and reviews](checklist/examples/08-pull-requests-and-reviews.md)
  9. [Uniform coding style](checklist/examples/09-coding-style.md)

## Optional Home Review: A Real Database with Entity Framework Core, then Concurrency Conflicts and a Custom Exception

The previous theory lecture covered [Persistence and ORM](https://github.com/smagurauskas/software-engineering/blob/main/08-persistence-and-orm.qmd): what an ORM solves, `DbContext` and entities, migrations, connection strings, seeing the SQL, async queries, testing with databases and concurrency. Lecture 8 already made StudySpot's repository async and covered it with tests, so this lecture puts a real database behind that same interface. It works toward two **Lab Assignment #2** requirements (deadline: 2026-11-25): *relational database and Entity Framework is used for storing all the data*, and *create at least 1 exception type and throw it; meaningfully deal with it by attaching custom handling logic*.

We didn't get to this in class, so it is **optional home review**: the walkthrough has every step, and you can follow it on your own. There are two stages, and the project builds and all tests pass at the end of each one:

- **Stage 1 - Entity Framework Core with SQLite**: a `DbContext` with seeding and a value conversion for our own `Rating` type, an Entity Framework implementation of `IStudySessionRepository` (async all the way, with the request's `CancellationToken`), swapped in with one line in `Program.cs`, and the first migration with the `dotnet ef` tool. The integration tests from Lecture 8 now run against a real database of their own, and a new test catches a forgotten migration. SQLite keeps the whole database in one file, so there's nothing to install. The theory demo uses PostgreSQL in Docker, and the walkthrough lists what changes if your team prefers it.
- **Stage 2 - Concurrency conflicts, a custom exception and a global handler**: two people editing the same session would silently overwrite each other's changes (a "lost update"). Every session gets a version, Entity Framework only saves when the version is still the one the client read, and a conflict becomes our own `StudySessionConflictException`. One `IExceptionHandler` turns it into a `409 Conflict` for every endpoint, with a problem details body, with no `try`/`catch` in the controller.

The playground's NUnit and MSTest test projects run the same tests against the database too.

A generic type, the shared memory scenario with concurrent collections, request/response DTOs and end-to-end tests with Playwright come next lecture.

Reference Guide: [PSI 2026 Playground PR #14](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/14) (`lectures/09-draft` &rarr; `lectures/09`) has the end result. Start with [WALKTHROUGH-09.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/09/WALKTHROUGH-09.md) for the exact step-by-step. It assumes you've already done [WALKTHROUGH-08.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08/WALKTHROUGH-08.md). See [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/09/WALKTHROUGHS.md) for the full index.

## Homework

There's no homework for this lecture. Work on Deadline 2 with your team: go through the checklist together and make sure every member can explain each requirement in your own project.

For optional review, follow [WALKTHROUGH-09.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/09/WALKTHROUGH-09.md) (see the tutorial section above):

- Stage 1 (steps 1-8): Entity Framework Core with SQLite, migrations, and integration tests against a real database. This covers the *relational database and Entity Framework* requirement.
- Stage 2 (steps 9-17): optimistic concurrency, a custom exception and a global exception handler. This covers the *custom exception with meaningful handling* requirement.
