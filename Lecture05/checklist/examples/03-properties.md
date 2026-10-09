# 3. Properties in `struct` and `class`

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

A property controls access to data: validation on the way in, computation or protection on the way out. You should know every kind:

| Kind | Example |
|------|---------|
| Auto-implemented | `public string Topic { get; set; } = string.Empty;` |
| Read-only / `init` | `public int Value { get; }` / `public int Id { get; init; }` |
| Full (backing field) | `get { return _x; } set { _x = value; }` |
| Expression-bodied / computed | `public SessionStatus Status => ...;` |
| Indexer | `public Session this[int index] => _items[index];` |

## ✅ Good

**Computed property in a class** (`Models/StudySession.cs`) - always consistent with the data, nothing to keep in sync:

```csharp
public int SeatsAvailable { get; set; }
public SessionStatus Status => SeatsAvailable > 0 ? SessionStatus.Scheduled : SessionStatus.Full;
```

**Read-only property in a struct** (`Models/Rating.cs`) - set once, by a validating constructor:

```csharp
public readonly struct Rating
{
    public int Value { get; }
    public Rating(int value) { /* validate */ Value = value; }
}
```

**Property with validation (backing field)**

```csharp
private int _seatsAvailable;

public int SeatsAvailable
{
    get => _seatsAvailable;
    set
    {
        if (value < 0) throw new ArgumentOutOfRangeException(nameof(value));
        _seatsAvailable = value;
    }
}
```

**Indexer** - makes a custom collection indexable:

```csharp
public class Timetable
{
    private readonly List<StudySession> _sessions = new();
    public StudySession this[int index] => _sessions[index];
}
```

## ❌ Bad

```csharp
// Java-style getter/setter methods instead of properties.
private string _topic;
public string GetTopic() => _topic;
public void SetTopic(string value) => _topic = value;

// Public field instead of a property: no way to add validation later without breaking callers.
public string Topic;

// A property with side effects and surprising cost.
public List<StudySession> Sessions => LoadFromDiskEveryTime();

// A setter that silently accepts garbage.
public int SeatsAvailable { get; set; } // -50 is fine?

// A property with a setter that is never meant to be called from outside.
public DateTime CreatedAt { get; set; } // should be { get; } or { get; init; }
```

## Common mistakes

- Everything is `{ get; set; }` even when the value should be fixed after construction.
- Computed values stored in a separate property and updated by hand (`IsFull` that must be changed whenever `SeatsAvailable` changes).
- Properties that do slow work (disk, network): callers expect them to be cheap.
- A struct with public setters: mutating a copy and wondering why the original did not change.

## Questions you may be asked

- Why is this property read-only? Why does this one have a backing field?
- Difference between `{ get; set; }`, `{ get; init; }` and `{ get; }`?
- What is an indexer? Write one.

## Further reading

- [Properties](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/properties)
- [Indexers](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/)
