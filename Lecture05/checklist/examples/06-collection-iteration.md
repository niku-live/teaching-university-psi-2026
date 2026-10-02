# 6. Iterating through collections the right way

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

Pick the iteration construct that matches the job, and iterate safely.

| Need | Use |
|------|-----|
| Read every item | `foreach` |
| Need the index, or walk backwards, or modify by position | `for` |
| Filter / transform / aggregate | LINQ (see [requirement 8](08-linq-to-objects.md)) |
| Remove while walking a `List<T>` | `RemoveAll(predicate)` or iterate a copy / backwards |
| Lazy, possibly infinite sequence | `IEnumerable<T>` with `yield return` |

## ✅ Good

**`foreach` for plain reading**

```csharp
foreach (var session in sessions)
{
    Console.WriteLine($"{session.StartsAt:g} {session.Topic}");
}
```

**`for` when the index matters**

```csharp
for (var i = 0; i < sessions.Count; i++)
{
    Console.WriteLine($"{i + 1}. {sessions[i].Topic}");
}
```

**Removing items safely**

```csharp
sessions.RemoveAll(s => s.SeatsAvailable == 0);
```

**Iterating a dictionary by key and value**

```csharp
foreach (var (course, count) in sessionsPerCourse)
{
    Console.WriteLine($"{course}: {count}");
}
```

**Guarding `null` before iterating**

```csharp
foreach (var tag in session.Tags ?? Enumerable.Empty<string>())
{
    // ...
}
```

## ❌ Bad

```csharp
// Modifying a collection while enumerating it: InvalidOperationException at runtime.
foreach (var session in sessions)
{
    if (session.SeatsAvailable == 0) sessions.Remove(session);
}

// Index loop with no use for the index, and a repeated expensive call in the condition.
for (var i = 0; i < GetAllSessions().Count; i++)
{
    Console.WriteLine(GetAllSessions()[i].Topic);
}

// Enumerating the same lazy query many times (the work, or the database call, repeats each time).
var query = LoadSessions().Where(s => s.SeatsAvailable > 0);
if (query.Any()) Console.WriteLine(query.Count());
foreach (var s in query) { /* third evaluation */ }

// Using foreach + manual list-building where one LINQ line says it.
var topics = new List<string>();
foreach (var s in sessions) { if (s.SeatsAvailable > 0) topics.Add(s.Topic); }
```

## Common mistakes

- Removing from or adding to a list inside its own `foreach`.
- Calling `.Count()` on a lazy sequence in a loop condition.
- Forgetting that `foreach` over `null` throws `NullReferenceException`.
- Using `for` on a `Dictionary` or `HashSet` (no index) or on something where `foreach` would do.
- Mutating the loop variable's *properties* when you meant to replace the item (works for classes, silently does nothing for structs).

## Questions you may be asked

- Why `foreach` here and `for` there?
- What exception does removing in a `foreach` give? How do you fix it?
- What does `foreach` compile to? (`GetEnumerator`, `MoveNext`, `Current`.)

## Further reading

- [foreach statement](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/iteration-statements#the-foreach-statement)
- [Iterators / yield](https://learn.microsoft.com/en-us/dotnet/csharp/iterators)
