# 8. Pull requests and reviews

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

The same requirement as Deadline 1, counted again for the work **since Deadline 1**:

- **All** changes reach `main` through pull requests - no direct pushes, even for small fixes.
- Every PR has a **description**: what changed and why.
- **Every** team member authored at least **3 merged PRs** and reviewed at least **3** PRs written by teammates since Deadline 1.
- A review counts only with **meaningful comments and discussion**. "LGTM" does not count. At least some comments should lead to changes.

The full explanation, with good and bad PR descriptions and review comments, is on the [Deadline 1 page](../../../Lecture05/checklist/examples/10-pull-requests-and-reviews.md).

## What's new for Deadline 2

- **Migrations in PRs.** Review them like code: a renamed property generated as "drop column + add column" deletes data; a new required column needs a value for existing rows.
- **Tests in PRs.** A PR that adds a feature adds its tests. Reviewers can ask "where is the test for this?"
- **Merge conflicts in the model snapshot.** Two PRs adding migrations conflict in `*ModelSnapshot.cs`. The second one merges `main`, removes its migration and adds it again.

## ✅ Good review comments for this deadline's topics

```text
"This query loads every session and filters in C# - can the Where go before ToListAsync, so it's in the SQL?"
"ToList() here is synchronous IO - ToListAsync(cancellationToken)?"
"This migration drops and re-adds the Title column; existing titles would be lost. Can we use RenameColumn?"
"The catch here swallows the exception - the user sees success even if saving failed."
"_cache is a Dictionary in a singleton - two requests can write to it at once."
```

## ❌ Bad

```text
"LGTM" on a 600-line PR with a new migration.
A PR titled "fixes" with no description.
One member merging everyone's PRs without reviews.
```

## Questions you may be asked

- Open one of your PRs since Deadline 1 and explain what it does and why.
- Show a review comment you wrote that led to a change.
- How did your team resolve a migration conflict?
