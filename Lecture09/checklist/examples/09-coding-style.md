# 9. Uniform coding style

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

The same requirement as Deadline 1: one consistent style across the whole project, ideally enforced by tooling (`.editorconfig` + `dotnet format`, a formatter such as Prettier on the front-end) rather than by hoping. The full explanation is on the [Deadline 1 page](../../../Lecture05/checklist/examples/11-coding-style.md).

## What's new for Deadline 2

- **Test code is code.** Test names follow one pattern (`Method_Scenario_ExpectedResult`), and tests are formatted like everything else.
- **Async naming.** Methods returning `Task` end in `Async` (`GetByIdAsync`) - except controller actions, where MVC strips the suffix (see WALKTHROUGH-08).
- **Generated code is left alone.** Migrations and the model snapshot are generated; exclude `Migrations/` from formatting and analyzers rather than reformatting them by hand.
- **EF configuration in one place.** Either attributes or `OnModelCreating` (or `IEntityTypeConfiguration<T>` classes) - pick a convention as a team and stick to it.

## Questions you may be asked

- Where are your team's style rules written down? How are they enforced?
- Run the formatter on-site: does anything change?
