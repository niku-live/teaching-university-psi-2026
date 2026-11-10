# 4. `async`/`await`, no IO performed synchronously

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

IO - the database, files, HTTP calls to other services - is slow compared to the CPU. Synchronous IO **blocks** a thread while it waits; with `await`, the thread goes back to the pool and serves other requests until the answer arrives. The requirement: **every** IO operation in the app is async and awaited, all the way up to the controller, and nothing blocks on a `Task`.

The request's `CancellationToken` belongs to the same idea: if the client gives up, the work below it should stop too.

## ✅ Good

**Async all the way, with the token passed down** (StudySpot, Lecture 8 + 9)

```csharp
// Controller: ASP.NET Core fills in the token (HttpContext.RequestAborted)
[HttpGet("{id:int}")]
public async Task<ActionResult<StudySession>> GetById(int id, CancellationToken cancellationToken)
{
    var session = await service.GetByIdAsync(id, cancellationToken);
    return session is null ? NotFound() : session;
}

// Repository: the async EF method gets the token
public Task<StudySession?> GetByIdAsync(int id, CancellationToken cancellationToken) =>
    db.StudySessions.AsNoTracking().FirstOrDefaultAsync(s => s.Id == id, cancellationToken);
```

**Files and HTTP, async too**

```csharp
await using var stream = File.OpenRead(path);
using var reader = new StreamReader(stream);
while (await reader.ReadLineAsync(cancellationToken) is { } line) { /* ... */ }

var forecast = await httpClient.GetFromJsonAsync<Forecast>(url, cancellationToken);
```

**Several independent calls at once**

```csharp
var sessionsTask = sessionRepository.GetAllAsync(ct);
var coursesTask = courseClient.GetCoursesAsync(ct);   // different resources - not the same DbContext!
await Task.WhenAll(sessionsTask, coursesTask);
```

## ❌ Bad

```csharp
// Synchronous EF calls on a request path.
var sessions = db.StudySessions.ToList();
db.SaveChanges();

// Blocking on a Task: no benefit from async, and it can deadlock.
var sessions = service.GetAllAsync(ct).Result;
service.SaveAsync(session, ct).Wait();

// async void: the caller can't await it, and an exception crashes the process.
public async void SaveInBackground(StudySession session) { await repository.AddAsync(session, default); }

// A token that's accepted and then dropped.
public async Task<List<StudySession>> GetAllAsync(CancellationToken cancellationToken = default) =>
    await db.StudySessions.ToListAsync();   // cancellationToken never used

// Two queries at the same time on the same DbContext - EF throws.
await Task.WhenAll(db.StudySessions.ToListAsync(ct), db.Courses.ToListAsync(ct));
```

## Common mistakes

- `CancellationToken cancellationToken = default` on every layer: the compiler then lets callers skip it, and they do. Make it required below the controller.
- Async methods that don't await anything (warning CS1998). Either there's IO to await, or return `Task.FromResult(...)`.
- Calling an async method without `await` (fire-and-forget by accident) - in tests too.

## Questions you may be asked

- What happens to the thread at an `await`?
- Why are `.Result` and `.Wait()` bad?
- Follow one request's `CancellationToken` from the controller to the database call. What happens when the browser tab closes mid-request?
- Why can't one `DbContext` run two queries at once?
- What is the difference between `Task.Run` and `await`ing an IO method?

## Further reading

- [Theory lecture 11: Threading, async and await](https://github.com/smagurauskas/software-engineering/blob/main/11-threading-async-await.qmd)
- [StudySpot WALKTHROUGH-08, stage 2](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08/WALKTHROUGH-08.md)
- [Asynchronous programming with async and await](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/)
