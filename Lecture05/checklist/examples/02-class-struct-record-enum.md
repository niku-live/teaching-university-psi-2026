# 2. `class`, `struct`, `record`, `enum` (one immutable)

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

Your team must create **at least four different types of its own - one `class`, one `struct`, one `record` and one `enum`** - and use each for the job it is good at. One type cannot count for two kinds, and types that come from .NET or a library do not count. Also make **at least one of your types immutable** (its values cannot change after construction).

| Kind | Your type in the project | Where it is used |
|------|--------------------------|------------------|
| `class` | | |
| `struct` | | |
| `record` | | |
| `enum` | | |

Fill this table in before the presentation; every team member should be able to point to each row.

| Type | Kind | Equality | Typical use |
|------|------|----------|-------------|
| `class` | reference | by reference (identity) | entities with identity and behavior (`StudySession`) |
| `struct` | value | member-wise `Equals`, no `==` by default | small, self-contained values (`Rating`) |
| `record` | reference | by value (all members) | immutable data carriers, DTOs, projections |
| `enum` | value | by number | a fixed set of named options |

## ✅ Good

**`class` - has identity and behavior** (StudySpot: `Models/StudySession.cs`)

```csharp
public class StudySession : IValidatableObject, IComparable<StudySession>
{
    public int Id { get; set; }
    public SessionStatus Status => SeatsAvailable > 0 ? SessionStatus.Scheduled : SessionStatus.Full;
}
```

**`struct` - a small, immutable value** (`Models/Rating.cs`)

```csharp
public readonly struct Rating
{
    public int Value { get; }

    public Rating(int value)
    {
        if (value < 1 || value > 5)
            throw new ArgumentOutOfRangeException(nameof(value), "Rating must be between 1 and 5.");
        Value = value;
    }
}
```

**`record` - immutable, value equality for free** (`Models/StudySessionSummary.cs`)

```csharp
public record StudySessionSummary(string Course, string Topic, DateTimeOffset StartsAt, int SeatsAvailable, Rating? HostRating);

var a = new StudySessionSummary("OOP", "LINQ", when, 3, null);
var b = new StudySessionSummary("OOP", "LINQ", when, 3, null);
Console.WriteLine(a == b); // True - same values
```

**`enum` - replaces magic numbers** (`Models/SessionStatus.cs`)

```csharp
public enum SessionStatus { Scheduled, Full }
```

**`enum` with `[Flags]` - combinable options**

```csharp
[Flags]
public enum DayOfWeekMask
{
    None = 0,
    Monday = 1,
    Tuesday = 2,
    Wednesday = 4, // each value is a distinct bit: 1, 2, 4, 8, ...
}

var days = DayOfWeekMask.Monday | DayOfWeekMask.Wednesday;
bool hasMonday = days.HasFlag(DayOfWeekMask.Monday); // true
```

**Immutable class with `init`** (alternative to a record)

```csharp
public class Venue
{
    public required string Name { get; init; }
    public required int Capacity { get; init; }
}
```

## ❌ Bad

```csharp
// Large mutable struct: copied on every assignment and method call. Should be a class.
public struct StudySession
{
    public int Id;
    public string Topic;
    public string Location;
    public string HostName;
    public DateTime StartsAt;
}

// A record used as a mutable entity: records can have setters, but that throws away their purpose.
public record User { public string Name { get; set; } }

// Enum used where a string or a database table is needed: values change at runtime.
public enum Course { OOP, Algorithms, /* every course the university ever teaches */ }

// A "wrapper" type that adds nothing.
public class StringHolder { public string Value { get; set; } }

// Magic numbers instead of an enum.
if (session.Status == 1) { /* ??? */ }
```

## Common mistakes

- Making everything a class (the checklist wants all four kinds, each used with a reason).
- Declaring a type only to tick the box - a struct or enum that nothing uses does not count.
- Using a struct "because it is faster". Large structs are slower; use structs for small value-like data.
- Assuming the struct constructor always runs: `default(Rating)` bypasses it and gives `Value = 0`.
- Using `==` on a plain struct: it does not compile unless you define it (records have it built in).
- Defining a record and then mutating it through public setters.
- An enum with `[Flags]` whose values are not powers of two.

## Questions you may be asked

- Why is this a struct and not a class? What happens when you pass it to a method?
- What does `a == b` do for a class? For a record? For a struct?
- Which of your types is immutable? How did you make it so?
- What does `[Flags]` change? Which values must the members have?

## Further reading

- [Choosing between class and struct](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/choosing-between-class-and-struct)
- [Records](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record)
- [Enumeration types](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/enum)
