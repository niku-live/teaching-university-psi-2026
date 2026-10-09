# 9. Standard .NET interfaces

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

Implement at least one standard interface so your type plugs into the rest of .NET:

| Interface | Gives your type |
|-----------|-----------------|
| `IComparable<T>` | one natural order - `List<T>.Sort()`, `Array.Sort`, `Min/Max` |
| `IComparer<T>` | a separate, swappable ordering (sort by host, by seats, ...) |
| `IEquatable<T>` | meaningful equality for `Equals`, `HashSet`, `Dictionary` keys |
| `IEnumerable<T>` / `IEnumerator<T>` | `foreach` and LINQ over your own collection type |

## ✅ Good

**`IComparable<T>` - natural ordering** (StudySpot: `Models/StudySession.cs`)

```csharp
public class StudySession : IComparable<StudySession>
{
    public int CompareTo(StudySession? other)
    {
        if (other is null) return 1;              // any instance sorts after null
        if (StartsAt < other.StartsAt) return -1;
        if (StartsAt > other.StartsAt) return 1;
        return 0;
    }
}

upcoming.Sort();   // no comparer needed
```

**`IComparer<T>` - an alternative ordering**

```csharp
public class BySeatsDescending : IComparer<StudySession>
{
    public int Compare(StudySession? x, StudySession? y) =>
        (y?.SeatsAvailable ?? 0).CompareTo(x?.SeatsAvailable ?? 0);
}

sessions.Sort(new BySeatsDescending());
```

**`IEquatable<T>` - equality by business identity**

```csharp
public class StudySession : IEquatable<StudySession>
{
    public bool Equals(StudySession? other) => other is not null && Id == other.Id;
    public override bool Equals(object? obj) => Equals(obj as StudySession);
    public override int GetHashCode() => Id.GetHashCode();   // must agree with Equals
}
```

**`IEnumerable<T>` - a custom collection that works with `foreach` and LINQ**

```csharp
public class Timetable : IEnumerable<StudySession>
{
    private readonly List<StudySession> _sessions = new();

    public void Add(StudySession s) => _sessions.Add(s);

    public IEnumerator<StudySession> GetEnumerator() => _sessions.OrderBy(s => s).GetEnumerator();
    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();
}
```

## ❌ Bad

```csharp
// Implemented to tick the box, then not implemented.
public int CompareTo(StudySession? other) => throw new NotImplementedException();

// Subtraction can overflow and give the wrong sign.
public int CompareTo(StudySession? other) => SeatsAvailable - other!.SeatsAvailable;   // subtraction can overflow

// Equals without GetHashCode: HashSet and Dictionary silently misbehave.
public bool Equals(StudySession? other) => Id == other?.Id;

// Ordering inconsistent with equality, or mutable fields in GetHashCode.
public override int GetHashCode() => Topic.GetHashCode();   // Topic can change after insertion into a HashSet

// Ignoring null.
public int CompareTo(StudySession other) => StartsAt.CompareTo(other.StartsAt);   // NullReferenceException

// Interface used for nothing: implemented, never exercised by any code path.
```

## Common mistakes

- Returning inconsistent results (`a < b` and `b < a`).
- `Equals` and `GetHashCode` out of sync.
- Hashing mutable properties.
- Implementing the interface but not showing anywhere it is used (sort call, `HashSet`, `foreach`).
- Using `IEnumerable<T>` and exposing the internal list directly instead of a safe enumeration.

## Questions you may be asked

- Where in your project is the interface actually used?
- What is the contract of `CompareTo` - what do negative, zero, positive mean?
- Why must `Equals` and `GetHashCode` agree?
- What does `foreach` call on an `IEnumerable`? What is `yield return`?

## Further reading

- [IComparable<T>](https://learn.microsoft.com/en-us/dotnet/api/system.icomparable-1)
- [IEquatable<T>](https://learn.microsoft.com/en-us/dotnet/api/system.iequatable-1)
- [IEnumerable<T>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1)
