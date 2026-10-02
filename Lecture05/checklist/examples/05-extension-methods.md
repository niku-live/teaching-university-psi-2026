# 5. Extension methods

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

An extension method is a `static` method in a `static` class whose first parameter has `this`. It looks like an instance method on a type you did not write (or cannot change), but it is only syntax - it cannot see private members.

## ✅ Good

**Extending an interface you do not own, with domain knowledge** (StudySpot: `Extensions/StudySessionExtensions.cs`)

```csharp
public static class StudySessionExtensions
{
    public static IEnumerable<StudySession> UpcomingOnly(
        this IEnumerable<StudySession> sessions, DateTimeOffset? asOf = null)
    {
        var cutoff = asOf ?? DateTimeOffset.UtcNow;
        return sessions.Where(s => s.StartsAt > cutoff);
    }
}

var upcoming = sessions.UpcomingOnly().Filter(minSeatsAvailable: 2);
```

**Extending a built-in type** with something it lacks:

```csharp
public static class StringExtensions
{
    public static string Truncate(this string value, int maxLength, string suffix = "...")
    {
        if (string.IsNullOrEmpty(value) || value.Length <= maxLength) return value;
        return value[..(maxLength - suffix.Length)] + suffix;
    }
}

"Introduction to C# generics".Truncate(15); // "Introduction..."
```

**Chainable collection operations** (the same style LINQ itself uses):

```csharp
public static IEnumerable<IEnumerable<T>> Batch<T>(this IEnumerable<T> source, int size)
{
    var batch = new List<T>(size);
    foreach (var item in source)
    {
        batch.Add(item);
        if (batch.Count == size)
        {
            yield return batch;
            batch = new List<T>(size);
        }
    }
    if (batch.Count > 0) yield return batch;
}
```

## ❌ Bad

```csharp
// An extension on your OWN class - you own the code, so add a normal method to it.
public static void PrintInfo(this StudySession session) =>
    Console.WriteLine($"{session.Course}: {session.Topic}");

// A wrapper that only renames something.
public static bool IsEmpty(this string s) => string.IsNullOrEmpty(s);

// An extension on object (or on a type so general that it pollutes IntelliSense everywhere).
public static string AsJson(this object o) => JsonSerializer.Serialize(o);

// An extension that hides expensive or surprising work (network call, global state).
public static Task<string> Name(this int id) => LoadNameFromServerAsync(id);

// Not static / not in a static class: does not compile - a good "what is the rule" question.
```

## Common mistakes

- Using extensions for things that belong on the class (behavior that needs private data).
- Placing all extensions in one giant `Helpers` class. Group by the type they extend.
- Forgetting the `using`/namespace: the extension "does not exist" until it is imported.
- Naming clashes with instance methods: an instance method always wins over an extension.
- Not handling `null` in `this` parameter (extension methods can be called on `null`, unlike instance methods).

## Questions you may be asked

- Why is this an extension method and not a method on the class?
- What happens when a type has an instance method with the same name?
- Write an extension on `string` or `IEnumerable<int>`.

## Further reading

- [Extension members](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)
