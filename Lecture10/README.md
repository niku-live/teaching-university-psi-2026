# Lecture 10

## What was discussed

_TBD - to be filled in after the lecture_

## Step-by-Step Tutorial: Code Quality Checked by the Build, then DTOs, a Generic Cache and Shared Memory

The previous theory lecture covered [Software Construction](https://github.com/smagurauskas/software-engineering/blob/main/09-software-construction.qmd) (complexity and how to measure it, coupling, standards enforced by the build, mutation testing, measuring dependencies) and [Generics](https://github.com/smagurauskas/software-engineering/blob/main/09-generics.qmd) (invariance, covariance and contravariance). This lecture applies the first to StudySpot's build, then closes the last open **Lab Assignment #2** requirements (Deadline 2: 2026-11-25): *a generic type with a generic method*, *a shared memory scenario handled with concurrent collections or locks*, plus request and response DTOs.

There are two stages, and the project builds and all tests pass at the end of each one:

- **Stage 1 - Code quality checked by the build**: a `Directory.Build.props` turns on the recommended .NET analyzers and the `.editorconfig` style rules for every project, as in the theory demo. Every warning they find is either fixed or excluded in `.editorconfig` with a comment saying why ("exceptions are decisions too"). Once the build is clean, warnings fail it, so no new ones get merged.
- **Stage 2 - DTOs, a generic cache and shared memory**: the controller stops binding and returning the Entity Framework entity. `CreateStudySessionDto`/`UpdateStudySessionDto` come in and `StudySessionDto` goes out, so a client can no longer "over-post" fields like `version`. Then a generic `ICache<TKey, TValue>` with an `InMemoryCache<TKey, TValue>` on a `ConcurrentDictionary` is shared by every request (a singleton) through a caching decorator around the service. A generic method in that decorator replaces three copies of the same invalidation code. A test calls the cache from 100 threads at once.
- **Optional - mutation testing with Stryker.NET**: does the test suite notice when the code is changed on purpose?

The playground's NUnit and MSTest test projects have the same tests.

End-to-end tests with Playwright are still open from the Beta list.

Reference Guide: [PSI 2026 Playground PR #16](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/16) (`lectures/10-draft` &rarr; `lectures/10`) has the end result. Start with [WALKTHROUGH-10.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/10-draft/WALKTHROUGH-10.md) for the exact step-by-step. It assumes you've already done [WALKTHROUGH-09.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/09/WALKTHROUGH-09.md). See [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/10-draft/WALKTHROUGHS.md) for the full index.

## Homework

TBD
