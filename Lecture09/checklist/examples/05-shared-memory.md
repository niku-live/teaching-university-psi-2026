# 5. A shared memory scenario, handled with concurrent collections or synchronization

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

A web server handles many requests **at the same time**, on different threads. Anything in memory that more than one of them can touch - a field of a **singleton** service, a `static` field, data shared with a background service - is **shared memory**. If two threads change it at once, the result can be wrong (a lost update), or the collection can be corrupted.

The requirement has two parts: **identify** such a scenario in your app (it should be a real one), and **deal with it** with the right tool:

- `ConcurrentDictionary`, `ConcurrentQueue`, `ConcurrentBag`, ... - collections that are safe for several threads.
- `Interlocked` - for a single counter.
- `lock` - for a few statements that must run together (no `await` inside).
- `SemaphoreSlim` - like `lock`, but works with `await`.

Note: once your data is in a database, the database handles concurrent access to **it** (and EF concurrency tokens handle conflicting edits - see requirement 3's example). This requirement is about data in **memory**.

## ✅ Good

**A cache in a singleton, shared by all requests**

```csharp
// Registered with AddSingleton: one instance for every request, so every request reads and writes this.
public class CourseCatalogCache(ICourseApiClient client)
{
    private readonly ConcurrentDictionary<string, Task<CourseInfo>> _courses = new();

    // GetOrAdd is atomic per key: two requests for the same course share one Task, one API call.
    public Task<CourseInfo> GetAsync(string code, CancellationToken ct) =>
        _courses.GetOrAdd(code, c => client.GetCourseAsync(c, ct));
}
```

**A live counter of people currently viewing a session**

```csharp
public class ViewerCounter
{
    private readonly ConcurrentDictionary<int, int> _viewers = new();

    public int Join(int sessionId) => _viewers.AddOrUpdate(sessionId, 1, (_, count) => count + 1);
    public int Leave(int sessionId) => _viewers.AddOrUpdate(sessionId, 0, (_, count) => Math.Max(0, count - 1));
}
```

**A background job queue: requests add, a `BackgroundService` takes**

```csharp
public class ReminderQueue
{
    private readonly ConcurrentQueue<int> _sessionIds = new();
    public void Enqueue(int sessionId) => _sessionIds.Enqueue(sessionId);
    public bool TryDequeue(out int sessionId) => _sessionIds.TryDequeue(out sessionId);
}
```

**Async code that must not run twice at once**

```csharp
private readonly SemaphoreSlim _refreshLock = new(1, 1);

public async Task RefreshAsync(CancellationToken ct)
{
    await _refreshLock.WaitAsync(ct);
    try { /* reload the catalog from the external API */ }
    finally { _refreshLock.Release(); }
}
```

## ❌ Bad

```csharp
// A plain Dictionary in a singleton: two requests adding at once can corrupt it.
private readonly Dictionary<string, CourseInfo> _cache = new();

// count++ is read-modify-write: two threads can both read 5 and both write 6.
private static int _requestCount;
public void OnRequest() => _requestCount++;          // use Interlocked.Increment(ref _requestCount)

// await inside lock doesn't compile; and .Result inside lock to get around it can deadlock.
lock (_sync) { await LoadAsync(); }

// A ConcurrentBag in a scoped service that only one request ever uses - nothing is shared,
// so this "solves" a problem that doesn't exist. It doesn't count.
public class StudySessionService { private readonly ConcurrentBag<StudySession> _temp = new(); }

// Check-then-act on a concurrent collection is still a race.
if (!_cache.ContainsKey(code)) _cache[code] = Load(code);   // use GetOrAdd
```

## Common mistakes

- Picking a concurrent collection first and looking for a place to put it. Start from the scenario: what is shared, by which threads?
- Not knowing your service lifetimes: only singletons and `static` fields are shared between requests.
- Locking on `this` or on a public object. Lock on a `private readonly object`.

## Questions you may be asked

- Which threads touch this data, and what goes wrong without the concurrent collection? Show the race.
- What is a race condition? A deadlock?
- Why is `count++` not thread-safe? What does `Interlocked.Increment` do?
- `lock` vs `SemaphoreSlim`: why can't you `await` inside a `lock`?
- Why are `Try...` methods (`TryAdd`, `TryRemove`) the API of concurrent collections?

## Further reading

- [Theory lecture 11: Threading, lock, Interlocked, concurrent collections](https://github.com/smagurauskas/software-engineering/blob/main/11-threading-async-await.qmd)
- [Thread-safe collections](https://learn.microsoft.com/en-us/dotnet/standard/collections/thread-safe/)
- [Service lifetimes](https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection#service-lifetimes)
