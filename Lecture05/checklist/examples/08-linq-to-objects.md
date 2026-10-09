# 8. LINQ to Objects

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

**LINQ to Objects** queries in-memory collections (`List<T>`, arrays, dictionaries, strings) with `System.Linq`. It comes in two syntaxes that mean the same thing:

```csharp
// Method syntax
var a = sessions.Where(s => s.SeatsAvailable > 0).OrderBy(s => s.StartsAt).Select(s => s.Topic);

// Query syntax
var b = from s in sessions
        where s.SeatsAvailable > 0
        orderby s.StartsAt
        select s.Topic;
```

> LINQ queries against a database through Entity Framework (**LINQ to Entities**) use the same syntax but are translated to SQL. That is **not** LINQ to Objects.

Queries are **lazy**: nothing runs until you enumerate (`foreach`, `ToList()`, `Count()`, ...).

If you do not use LINQ where it clearly fits, be ready to justify that.

## ✅ Good

**A readable pipeline** (StudySpot: `Extensions/StudySessionExtensions.cs`)

```csharp
sessions = sessions.Where(s => s.Course.Contains(course, StringComparison.OrdinalIgnoreCase));
```

**Projection into a record** (`GetSummaries` in the controller)

```csharp
Sessions.UpcomingOnly().Filter(minSeatsAvailable: minSeatsAvailable)
    .Select(s => new StudySessionSummary(s.Course, s.Topic, s.StartsAt, s.SeatsAvailable, s.HostRating));
```

**Grouping and aggregating** - where LINQ shines over hand-written loops:

```csharp
var seatsPerCourse = sessions
    .GroupBy(s => s.Course)
    .Select(g => new { Course = g.Key, Seats = g.Sum(s => s.SeatsAvailable) })
    .OrderByDescending(x => x.Seats)
    .ToList();
```

**Joining two in-memory collections (query syntax is nicer here)**

```csharp
var hostedBy =
    from s in sessions
    join u in users on s.HostName equals u.Name
    select new { s.Topic, u.Email };
```

## ❌ Bad

```csharp
// Pointless operators.
var count = sessions.Select(s => s).Where(s => true).ToList().Count();   // sessions.Count

// Materializing too early, then filtering.
var open = sessions.ToList().Where(s => s.SeatsAvailable > 0);

// Enumerating the same lazy query several times.
var query = sessions.Where(Expensive);
if (query.Count() > 0) { var first = query.First(); }   // use Any()/FirstOrDefault(), or ToList() once

// A query so long nobody can read it - split it into named steps.
var x = a.Where(...).Select(...).GroupBy(...).Select(...).Where(...).OrderBy(...).ThenBy(...).Select(...).ToList();

// Side effects inside a query.
sessions.Select(s => { s.SeatsAvailable--; return s; });   // never executed unless enumerated, then runs each time

// LINQ on a tiny fixed list where a property would do.
var first = new List<int> { 1 }.Where(x => x == 1).First();
```

## Common mistakes

- Calling `.Count()` for "is it empty?" - use `.Any()`.
- Using `First()` when "nothing found" is a normal case - use `FirstOrDefault()` and handle `null`.
- Deferred execution surprises: changing the source after defining the query changes its result.
- Claiming Entity Framework queries as "LINQ to Objects".

## Questions you may be asked

- What is deferred execution? Show where it matters in your code.
- Rewrite this query in the other syntax.
- What is the difference between `First`, `FirstOrDefault`, `Single`?
- `Select` vs `SelectMany`?

## Further reading

- [LINQ overview](https://learn.microsoft.com/en-us/dotnet/csharp/linq/)
- [Standard query operators](https://learn.microsoft.com/en-us/dotnet/csharp/linq/standard-query-operators/)
- Tool: [LINQPad](https://www.linqpad.net/) for trying queries quickly.
