# Lecture 8

## What was discussed

_TBD - to be filled in after the lecture_

## Step-by-Step Tutorial: Automated Tests, then async/await and Cancellation for StudySpot

The previous theory lectures covered [Testing](https://github.com/smagurauskas/software-engineering/blob/main/07-testing.qmd) (types of tests, test doubles, xUnit, coverage) and [Persistence and ORM](https://github.com/smagurauskas/software-engineering/blob/main/08-persistence-and-orm.qmd), including async and cancellation. Last week's practice lecture went to the Deadline 1 presentations, so this one revisits testing in practice, on StudySpot. It's also the first practice lecture for **Lab Assignment #2** (deadline: week 11), and it works directly toward two of its requirements: *unit and integration tests coverage at least 50%*, and *usage of `async`/`await`, no IO is performed synchronously*.

In Lecture 6 we refactored every layer of StudySpot and checked by hand, request by request, that nothing broke. This time the tests come first, and then a second refactor across every layer uses them as a safety net. There are two stages, and the project builds and all tests pass at the end of each one:

- **Stage 1 - Automated tests**: an xUnit v3 test project (the same tools as the theory lecture's word game demo). It has unit tests for `StudySessionService` that use a fixed point in time instead of the real clock, and a fake repository that returns copies like a database would, which turns Lecture 6's Liskov warning into a test. Integration tests run the real API in memory with `WebApplicationFactory`. On the frontend, Jest and React Testing Library test the "Host a new study session" form. A coverage report with ReportGenerator shows why generated code has to be excluded before the number means anything.
- **Stage 2 - async/await and cancellation**: the repository, the service and the controller become async, from the bottom up, and the request's `CancellationToken` is passed down to the repository. The tests are updated, plus one more test that proves a cancelled request does no work. All the tests from stage 1 still pass.

Storing StudySpot in a real database with Entity Framework Core, and end-to-end tests with Playwright, come in a later lecture.

Reference Guide: [PSI 2026 Playground PR #12](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/12) (`lectures/08-draft` &rarr; `lectures/08`) has the end result. Start with [WALKTHROUGH-08.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08-draft/WALKTHROUGH-08.md) for the exact step-by-step. It assumes you've already done [WALKTHROUGH-06.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/06/WALKTHROUGH-06.md); Lecture 7 was the Deadline 1 presentations and had no walkthrough. See [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08-draft/WALKTHROUGHS.md) for the full index.

## Homework

TBD
