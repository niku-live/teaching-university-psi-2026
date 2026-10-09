# 11. Uniform coding style

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

The project reads as if one person wrote it, no matter who did. That means agreed rules for naming, formatting and structure. And best if **enforced by tools** rather than by memory.

## ✅ Good

**Naming**

```csharp
public class StudySession                  // PascalCase types
{
    private readonly List<Rating> _ratings = new();   // _camelCase private fields

    public int SeatsAvailable { get; set; }           // PascalCase members

    public bool HasFreeSeats(int required)            // camelCase parameters
    {
        var remaining = SeatsAvailable - required;    // camelCase locals
        return remaining >= 0;
    }
}
```

**Tooling in the repository**

```ini
# .editorconfig (excerpt)
root = true

[*.cs]
indent_style = space
indent_size = 4
csharp_new_line_before_open_brace = all
dotnet_naming_rule.private_fields_underscore.severity = warning
```

```bash
dotnet format          # fixes formatting, run before every commit
dotnet format --verify-no-changes   # fails if something is not formatted (usable in CI)
```

- Front-end: Prettier / ESLint with a committed config.
- "Format on save" turned on in everyone's IDE (see StudySpot's `CONTRIBUTING.md`).
- The agreed rules are written down (`CONTRIBUTING.md`), including branch names and commit message style.

## ❌ Bad

```csharp
// Three styles in one file because three people wrote it.
public int seats_available { get; set; }
public string  Topic{get;set;}
private string HostName ;
public void do_stuff( int X ){
  int Y=X*2 ;
    return ;
}

// Inconsistent file layout: some files have one class, some have eight.
// Inconsistent language: variable names in English and Lithuanian mixed.
// Commented-out code left behind "just in case".
// Dead code: unused using directives, unused methods, unused variables.
```

## Common mistakes

- "I'll format it all at the end" - one gigantic formatting commit hides real changes in the history. Format continuously.
- Disagreeing on style in every PR review. Decide once, put it in `.editorconfig`, and let the tool argue.
- Formatting only the C# and ignoring the front-end code.
- Style fixes mixed into feature PRs, making them harder to review.

## Questions you may be asked

- Where are your team's style rules written? How are they enforced?
- Run the formatter now. Does it change anything?
- Why `_camelCase` for private fields?

## Further reading

- [C# coding conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)
- [EditorConfig for .NET](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/code-style-rule-options)
- [`dotnet format`](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-format)
