# 4. Named and optional arguments

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

- **Optional argument**: a parameter with a default value; callers may omit it.
- **Named argument**: the caller writes `name: value`, so order does not matter and the call documents itself.

Optional parameters go **after** required ones. Default values must be compile-time constants (`null`, numbers, strings, `default`).

## ✅ Good

**Optional: a sensible default, overridable when needed** (`Extensions/StudySessionExtensions.cs`)

```csharp
public static IEnumerable<StudySession> UpcomingOnly(
    this IEnumerable<StudySession> sessions, DateTimeOffset? asOf = null)
{
    var cutoff = asOf ?? DateTimeOffset.UtcNow;
    return sessions.Where(s => s.StartsAt > cutoff);
}

sessions.UpcomingOnly();                  // "right now"
sessions.UpcomingOnly(asOf: fixedDate);   // repeatable, e.g. in a test
```

**Named: the only way to skip an earlier optional parameter**

```csharp
public static IEnumerable<StudySession> Filter(
    this IEnumerable<StudySession> sessions, string? course = null, int? minSeatsAvailable = null)

sessions.Filter(minSeatsAvailable: 2);   // course stays at its default
```

**Named: when positional arguments are unreadable**

```csharp
// What do true and false mean?
CreateSession("OOP", "LINQ", true, false);

// Obvious:
CreateSession(course: "OOP", topic: "LINQ", isPublic: true, allowWaitlist: false);
```

## ❌ Bad

```csharp
// Optional parameters piled up to avoid designing the method.
void Save(string a = "", string b = "", string c = "", bool d = false, bool e = true) { }

// Named argument that adds nothing (one obvious parameter).
double Square(double value) => value * value;
Square(value: 3);

// A default that is a hidden policy decision and hard to find.
void Send(string to, int retries = 17) { }

// Optional parameter that is always passed explicitly, so the default is dead code.
Print(text, indent: 4);
Print(other, indent: 2);

// Mutable-looking default via null, then crash.
void Add(List<int> items = null) { items.Add(1); }
```

## Common mistakes

- Declaring an optional parameter that every caller passes anyway.
- Changing a default value in a library and expecting callers to pick it up: defaults are baked into the caller at compile time.
- Naming every argument in every call (noisy). Name where it adds clarity: booleans, same-typed parameters, skipped optionals.
- Using optional parameters when two methods with different meanings would be clearer.

## Questions you may be asked

- Why is this parameter optional? Who calls it with a non-default value?
- Why did you name this argument?
- Write a method with one required and one optional parameter and call it two ways.

## Further reading

- [Named and optional arguments](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/named-and-optional-arguments)
