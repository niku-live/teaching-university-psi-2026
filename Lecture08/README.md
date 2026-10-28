# Lecture 8

## What was discussed

The practice part revisited this week's testing theory on StudySpot, then started the async refactor with the tests as a safety net (the full step-by-step is below):

- **Repository layout**: the API moved into `CoolApp.Api/` and the React app into `CoolApp.Client/`, side by side, so test projects can live next to them instead of inside the app. Done with `git mv`, so history follows the files. `SpaRoot` is the only link between the two.
- **Unit tests** with xUnit v3 for `StudySessionService`: Arrange/Act/Assert, `Method_Scenario_ExpectedResult` names, a fixed point in time instead of the real clock, and `[Theory]` for several cases in one test.
- **A fake repository** that returns copies like a database would. It turned Lecture 6's Liskov warning into a test: breaking `Rate` on purpose made exactly that one test fail, while the test using the in-memory repository still passed.
- **Controller tests** that call the controller directly with an NSubstitute service, used as a stub (choosing the case) and as a mock (checking how the service was called), plus what these tests can't see.
- **Integration tests** with `WebApplicationFactory`, running the real API in memory: routing, validation, JSON and Dependency Injection.
- **Frontend tests** with Jest and React Testing Library in `CoolApp.Client/tests/`: unit tests for the validation function, and component tests that find elements by label and role, with a mocked `fetch`.
- **Coverage**: one ReportGenerator report for the backend and the frontend. Excluding generated code changes the C# number from about 26% to about 80%, and coverage only shows which lines ran, not what was checked.
- **async/await and cancellation**: the repository, the service and the controller became async from the bottom up, with a required `CancellationToken` passed down from the request. The updated tests for this stage, including the cancellation test and the token check in the controller tests, were left for optional review at home (WALKTHROUGH-08 steps 16-18).
- xUnit, NUnit and MSTest were compared. The playground has the same tests in all three frameworks.

## Step-by-Step Tutorial: Automated Tests, then async/await and Cancellation for StudySpot

The previous theory lectures covered [Testing](https://github.com/smagurauskas/software-engineering/blob/main/07-testing.qmd) (types of tests, test doubles, xUnit, coverage) and [Persistence and ORM](https://github.com/smagurauskas/software-engineering/blob/main/08-persistence-and-orm.qmd), including async and cancellation. Last week's practice lecture went to the Deadline 1 presentations, so this one revisits testing in practice, on StudySpot. It's also the first practice lecture for **Lab Assignment #2** (deadline: week 11), and it works directly toward two of its requirements: *unit and integration tests coverage at least 50%*, and *usage of `async`/`await`, no IO is performed synchronously*.

In Lecture 6 we refactored every layer of StudySpot and checked by hand, request by request, that nothing broke. This time the tests come first, and then a second refactor across every layer uses them as a safety net. There are two stages, and the project builds and all tests pass at the end of each one:

- **Stage 1 - Automated tests**: first the repository is split into folders: the API goes into `CoolApp.Api/`, the React app into `CoolApp.Client/` next to it, and the test project gets its own folder beside them, `CoolApp.Api.Tests.XUnit/`, instead of living inside the app (the usual .NET layout: the solution at the root, one folder per project). Then comes an xUnit v3 test project (the same tools as the theory lecture's word game demo). It has unit tests for `StudySessionService` that use a fixed point in time instead of the real clock, and a fake repository that returns copies like a database would, which turns Lecture 6's Liskov warning into a test. Controller tests call the controller directly, with a service mocked by NSubstitute, and check what it returns. Integration tests run the real API in memory with `WebApplicationFactory`. On the frontend, Jest and React Testing Library test the "Host a new study session" form, from a `tests/` folder kept out of `src/`. One coverage report with ReportGenerator combines the backend and the frontend, and shows why generated code has to be excluded before the number means anything.
- **Stage 2 - async/await and cancellation**: the repository, the service and the controller become async, from the bottom up, and the request's `CancellationToken` is passed down to the repository. The tests are updated, plus one more test that proves a cancelled request does no work. All the tests from stage 1 still pass, and a last, optional step runs the app once more by hand.

The playground also has the same API tests written with NUnit and MSTest (`CoolApp.Api.Tests.NUnit/`, `CoolApp.Api.Tests.MSTest/`), and WALKTHROUGH-08 compares the three frameworks.

Storing StudySpot in a real database with Entity Framework Core, and end-to-end tests with Playwright, come in a later lecture.

Reference Guide: [PSI 2026 Playground PR #12](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/12) (`lectures/08-draft` &rarr; `lectures/08`) has the end result. Start with [WALKTHROUGH-08.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08/WALKTHROUGH-08.md) for the exact step-by-step. It assumes you've already done [WALKTHROUGH-06.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/06/WALKTHROUGH-06.md); Lecture 7 was the Deadline 1 presentations and had no walkthrough. See [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08/WALKTHROUGHS.md) for the full index.

## Homework

TBD
