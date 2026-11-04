# 2. A generic type with a generic method that removes duplication

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

A **generic type** has type parameters (`class Page<T>`), so one implementation works for many types, type-safely, without casts. A **generic method** has its own type parameters (`Map<TResult>(...)`). The requirement asks for both, written by you, and for a **reason**: show the duplication the generic code removed - two or more classes or methods that were the same apart from the type.

Constraints (`where T : class`, `where T : IEntity`, `where T : new()`) say what `T` must be able to do. Add them when the code needs them, not as decoration.

## ✅ Good

**Before: the same paging code twice**

```csharp
public record StudySessionPage(IReadOnlyList<StudySession> Items, int PageNumber, int TotalCount);
public record CoursePage(IReadOnlyList<Course> Items, int PageNumber, int TotalCount);
// ... and the same Skip/Take/Count code in two repositories
```

**After: one generic type, with a generic method of its own**

```csharp
public record Page<T>(IReadOnlyList<T> Items, int PageNumber, int TotalCount)
{
    public Page<TResult> Map<TResult>(Func<T, TResult> map) =>
        new(Items.Select(map).ToList(), PageNumber, TotalCount);
}

public static class QueryablePaging
{
    public static async Task<Page<T>> ToPageAsync<T>(this IQueryable<T> query, int pageNumber, int pageSize, CancellationToken cancellationToken)
    {
        var total = await query.CountAsync(cancellationToken);
        var items = await query.Skip((pageNumber - 1) * pageSize).Take(pageSize).ToListAsync(cancellationToken);
        return new Page<T>(items, pageNumber, total);
    }
}

// Used for two entities, and mapped to DTOs:
var sessions = await db.StudySessions.OrderBy(s => s.StartsAt).ToPageAsync(1, 20, ct);
var courses = await db.Courses.OrderBy(c => c.Name).ToPageAsync(1, 50, ct);
var dtos = sessions.Map(s => new StudySessionDto(s.Id, s.Topic));
```

**A generic repository with a constraint the code actually uses**

```csharp
public interface IEntity { int Id { get; } }

public class EfRepository<T>(AppDbContext db) : IRepository<T> where T : class, IEntity
{
    public Task<T?> GetByIdAsync(int id, CancellationToken ct) =>
        db.Set<T>().FirstOrDefaultAsync(e => e.Id == id, ct);   // needs IEntity for e.Id, class for Set<T>()
}
```

## ❌ Bad

```csharp
// Generic in name only: used with exactly one type.
public class Validator<T> { /* only ever Validator<StudySession> */ }

// Casting T back to a concrete type defeats the point.
public void Save<T>(T item)
{
    if (item is StudySession s) { /* ... */ }
    else if (item is Course c) { /* ... */ }
}

// object instead of generics: casts everywhere, errors only at run time.
public object GetById(Type type, int id) { /* ... */ }

// Constraints that the code doesn't need.
public T First<T>(List<T> items) where T : class, IComparable<T>, new() => items[0];
```

## Common mistakes

- Only *using* generic .NET types (`List<T>`, `Task<T>`) and calling that "generics". The requirement is your **own** generic type and method.
- A generic repository that hides everything EF can do (no `Include`, no projections) - fine for simple CRUD, a pain for anything else. Keep specific queries in specific repositories.
- Not being able to say what the code looked like without generics.

## Questions you may be asked

- What is the difference between a generic type and a generic method? Show both in your code.
- Why is `List<T>` better than `ArrayList`? (Type safety, no boxing.)
- What does `where T : class` / `where T : new()` / `where T : ISomething` allow you to do inside the method?
- What are covariance (`out T`) and contravariance (`in T`)? Why can an `IEnumerable<StudySession>` be used as an `IEnumerable<object>`, but a `List<StudySession>` not as a `List<object>`?

## Further reading

- [Theory lecture 9: Generics](https://github.com/smagurauskas/software-engineering/blob/main/09-generics.qmd)
- [Generics in .NET](https://learn.microsoft.com/en-us/dotnet/standard/generics/)
- [Constraints on type parameters](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters)
