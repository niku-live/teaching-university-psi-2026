# Lecture 1

## What was discussed

This practice lecture reviewed [Theory Lecture 1](https://github.com/smagurauskas/software-engineering/blob/main/01-intro.qmd) material - the [course intro](https://github.com/smagurauskas/software-engineering/blob/main/01-intro.qmd), [.NET/C# fundamentals](https://github.com/smagurauskas/software-engineering/blob/main/01-dotnet.qmd), and [Pull Requests](https://github.com/smagurauskas/software-engineering/blob/main/01-pull-requests.qmd) - focusing on the parts that are easy to misread on a first pass, some practical tips for this week, and a hands-on walkthrough of turning your team's project template into the start of a real product.

See [dotnet-review.qmd](dotnet-review.qmd) for the slides used during the review portion.

## Review: Things That Are Easy to Misread From Theory

### .NET / C# fundamentals

- **"Compiled" is ambiguous.** C# is compiled to IL ahead of time, then the CLR JIT-compiles that IL to native code *at runtime*. It's neither purely "compiled" nor "interpreted" in the way those words are usually used for C or Python.
- **"Value types live on the stack" is a simplification.** A value type lives wherever it's declared - a `struct` field on a heap-allocated `class` instance lives on the heap along with the rest of that object.
- **Boxing is a hidden allocation.** Using a value type where an `object` (or an implemented interface) is expected causes an implicit heap allocation and copy. This is one practical reason to prefer generic collections (`List<int>`) over old non-generic ones (`ArrayList`).
- **Copy semantics matter for correctness, not just performance.** Copying a `struct` copies its fields; copying a `class` reference means both variables point at the same object. Getting this backwards is a common source of "why didn't my change take effect?" bugs.

### Pull Requests

- "Approve" / "Request changes" / "Comment" are distinct review outcomes on GitHub - only "Request changes" can block a merge, and only if branch protection requires it.
- A good PR description isn't busywork - it's what lets a reviewer know what to look for before reading the diff.
- "Keep PRs small" means one reviewable idea per PR, not "trivial changes only."

## Practical Tips for This Week

- Set up **branch protection** on your team repository, requiring at least one review before merging (see your [Lecture 0 TO-DO list](../Lecture00/TODO-LIST.md)).
- Agree on **line endings** across operating systems before anyone commits code - see [Tools Usage Notes](../Lecture00/TOOLS-USAGE.md) from Lecture 0.
- Pick an IDE and confirm the whole team can build the project with it: Visual Studio 2022, Visual Studio Code with C# Dev Kit, or JetBrains Rider all work.
- Write PR descriptions that explain *what* and *why*, not just *what* - this satisfies the Lab Assignment #1 pull request requirement and makes review much faster.

## Step-by-Step Tutorial: From Template to Your Project

The live demo took the generic ASP.NET Core + React starter template and turned it into the start of a real product, using the course's own [PSI 2026 Playground](https://github.com/niku-live/teaching-university-psi-2026-playground) as the example - turning the plain template into **StudySpot**, a small app for finding and hosting study sessions.

Steps demonstrated:

1. Write a real README that describes the *product*, not the template's placeholder text.
2. Write a lightweight `ROADMAP.md` with Alpha / Beta / Final scope, matching what your [Lecture 0 TO-DO list](../Lecture00/TODO-LIST.md) asked your team to define.
3. Replace the placeholder landing page with real copy about your product.
4. Replace the sample data model and endpoint (`WeatherForecast`) with your first real domain model and API endpoint.
5. Delete template boilerplate you don't need (unused demo pages/components).

**Reference Guide**: [PSI 2026 Playground - `lectures/01` branch](https://github.com/niku-live/teaching-university-psi-2026-playground/tree/lectures/01) has the end result. Start with [WALKTHROUGH.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/01/WALKTHROUGH.md) if you need to prepare your computer and create the project from scratch first; the [README](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/01/README.md) and [ROADMAP.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/01/ROADMAP.md) explain each "template to product" step, and the branch's commit history shows the diff for each one. For comparison, [`lectures/00`](https://github.com/niku-live/teaching-university-psi-2026-playground/tree/lectures/00) is the untouched template this all started from.

Do the same for your own team repository this week - see [TODO-LIST.md](TODO-LIST.md).

## Homework

See [TODO-LIST.md](TODO-LIST.md).
