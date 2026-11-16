# Lecture 11

## What was discussed

_TBD - to be filled in after the lecture_

## Part 1: Deadline 2 Readiness - Where Each Requirement Lives in StudySpot

Deadline 2 is next week (**2026-11-25 20:00**). Lecture 10 was only touched on briefly, so here is the [Deadline 2 checklist](../Lecture09/checklist/checklist.qmd) mapped to real code in the playground. Use it to check your own project: for each row, can every team member find the same thing in **your** code and explain it?

| # | Requirement | Where it is in StudySpot | Walkthrough |
|---|---|---|---|
| 1 | A relational database through Entity Framework, with migrations | [`StudySpotDbContext`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/Data/StudySpotDbContext.cs), [`EfStudySessionRepository`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/Repositories/EfStudySessionRepository.cs), `CoolApp.Api/Migrations/`, connection string in [`appsettings.json`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/appsettings.json) | [09, stage 1](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-09.md) |
| 2 | A generic type with a generic method that removes duplication | [`ICache<TKey, TValue>`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/Caching/ICache.cs) / [`InMemoryCache<TKey, TValue>`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/Caching/InMemoryCache.cs), and the generic method `ChangeThenForgetAsync<TResult>` in [`CachedStudySessionService`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/Services/CachedStudySessionService.cs) | [10, stage 2](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-10.md) |
| 3 | Your own exception, thrown and meaningfully handled | [`StudySessionConflictException`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/Exceptions/StudySessionConflictException.cs), turned into `409 Conflict` by [`StudySessionConflictExceptionHandler`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/ExceptionHandling/StudySessionConflictExceptionHandler.cs) | [09, stage 2](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-09.md) |
| 4 | `async`/`await`, no synchronous IO | Every action in [`StudySessionsController`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/Controllers/StudySessionsController.cs), down to the EF repository, with a `CancellationToken` | [08, stage 2](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-08.md) |
| 5 | A shared memory scenario with a concurrent collection or a lock | The singleton [`InMemoryCache`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/Caching/InMemoryCache.cs) on a `ConcurrentDictionary`, shared by every request, tested from 100 threads | [10, stage 2](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-10.md) |
| 6 | Dependency Injection everywhere reasonable | [`Program.cs`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CoolApp.Api/Program.cs): the DbContext, repository, service, caching decorator and exception handler are all registered there | [06](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-06.md) |
| 7 | Unit and integration tests, at least 50% coverage | `CoolApp.Api.Tests.XUnit/Unit` and `/Integration` (`WebApplicationFactory`), Jest tests in `CoolApp.Client/tests/`, one coverage report | [08, stage 1](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-08.md) |
| 8 | Pull requests and reviews | The playground's own PRs, the [PR template](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/.github/pull_request_template.md) and [`CONTRIBUTING.md`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CONTRIBUTING.md) | [03](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-03.md) |
| 9 | Uniform coding style | [`.editorconfig`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/.editorconfig) and [`Directory.Build.props`](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/Directory.Build.props): the build checks the style | [10, stage 1](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-10.md) |

## Part 2: UI/UX in Practice - Seven Small Fixes to One Page

The last theory lecture covered [UI/UX](https://github.com/smagurauskas/software-engineering/blob/main/10-ui-ux.qmd): the action cycle (did it work? what do I do next?), the four states of every request, error messages per cause, the peak and the end, and how to measure usability. This part applies it to one real page: the React "Study Sessions" page of StudySpot.

Every fix starts from a problem the page really had, names the principle from the theory lecture, changes the code, and adds a Jest test that fails before the fix and passes after it. **Every fix is independent**: each one starts from the Lecture 10 code and adds its own test file. You can read them in any order, or skip any of them.

**Stage 1 - Did it work?**

1. **Four loading states.** Before the fix, a failed load showed "Loading..." forever. Now the page shows "Still loading..." after 1 s, and a failed load says so and offers "Try again".
2. **Creating a session while offline.** Before the fix, the button hung on "Creating...". Now the page says the server could not be reached, and the typed answers are kept.
3. **One message per cause.** "Please try again" was shown for every failure. Now a server error, a busy server and a conflict each get their own message.
4. **A failed rating is not silent.** A deleted session or a lost connection is now reported, and the list is refreshed.

**Stage 2 - What next?**

5. **Focus moves to the first field to fix**, after both the browser's check and the API's `400`.
6. **A clear ending after a create**: a confirmation, the new row highlighted, and the cursor back on the first field.
7. **An empty list explains itself**, with "Clear filters" or an invitation to host the first session.

**Optional:** measure the page with Lighthouse and the Core Web Vitals.

The walkthrough ends with the theory lecture's task for your team project: walk your main task through the action cycle, fix three problems (with before/after screenshots), and count one metric from what users do.

Reference Guide: [PSI 2026 Playground PR #19](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/19) (`lectures/11-draft` &rarr; `lectures/11`) has the end result, one commit per fix. Start with [WALKTHROUGH-11.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/11-draft/WALKTHROUGH-11.md) for the exact step-by-step. It assumes a project at the state of [WALKTHROUGH-10.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-10.md) (or any from [WALKTHROUGH-08.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/WALKTHROUGH-08.md) on). See [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/11-draft/WALKTHROUGHS.md) for the full index.

## Homework

TBD
