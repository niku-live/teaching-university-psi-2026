# 10. Pull requests and reviews

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

- **All** changes reach `main` through pull requests - no direct pushes, even for small fixes.
- Every PR has a **description**: what changed and why.
- **Every** team member authored at least **3 merged PRs** and reviewed at least **3** PRs written by teammates.
- A PR counts as reviewed only when the review has **meaningful comments and discussion**. "LGTM" does not count. At least some comments should lead to changes.

I look at the repository (commits, PRs, comments), not at what you tell me.

## ✅ Good

**PR description**

```markdown
## What
Add a `Rating` struct and `PUT /api/studysessions/{id}/rating`.

## Why
Hosts need feedback. `Rating` validates 1-5 in one place, so no controller has to.

## How to test
Run the API, send `CoolApp.http` request "Rate a session"; expect 204, then GET shows `hostRating`.

## Notes
`default(Rating)` bypasses the constructor - HostRating is only set from a validated int, see comment in Rating.cs.
```

**Review comments that help**

- "`Filter` is called twice per request here - can we call it once and reuse the result? (line 42)"
- "This catches `Exception` and swallows it. What should the user see when the file is missing?"
- "Nice use of `init` here. Should `Capacity` also reject negative numbers?"
- "Question, not blocking: why a struct and not a record?"

**A discussion that ends in a change**

> **Reviewer:** `SeatsAvailable` can go negative here.
> **Author:** True, I'll add the check in the setter. *(pushes a fix)*
> **Reviewer:** Looks good now, resolving.

## ❌ Bad

- Title `update`, empty description.
- One PR with 40 changed files and 3000 added lines: nobody can review it.
- A PR merged by its own author in two minutes with no review.
- Review comments: "ok", "looks good", "+1", "👍" - on every PR, from everyone.
- Everything in one person's PRs, while others "pair" and never commit.
- Direct commits to `main`, "just a typo".
- Work done in a PR but never merged (does not count as authored).
- Review approvals the same minute the PR opens (nobody read the code).

## Common mistakes

- Leaving the PR template placeholders unfilled.
- Mixing unrelated changes in one PR (a formatting sweep plus a feature).
- Reviewing only the style and never the logic (or the reverse).
- Last-minute rush: 8 PRs on the evening of the deadline. Review needs time, and it shows in the history.

## Questions you may be asked

- Open one of your PRs. What did the reviewer ask and what did you change?
- Show a PR you reviewed. What did you find?
- How did your team decide who reviews what? (See `CODEOWNERS` and `CONTRIBUTING.md` in StudySpot.)

## Further reading

- [About pull requests](https://docs.github.com/en/pull-requests)
- [Reviewing changes in pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests)
- StudySpot's [CONTRIBUTING.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/CONTRIBUTING.md) and [definition of done](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/main/docs/definition-of-done.md)
