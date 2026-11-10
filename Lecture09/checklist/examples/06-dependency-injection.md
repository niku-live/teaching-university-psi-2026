# 6. Dependency Injection everywhere reasonable

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

A **dependency** is something a class needs to do its job: a repository, a service, the `DbContext`, an `HttpClient`, a clock, a logger. With Dependency Injection, a class **receives** its dependencies through its constructor, usually as interfaces, and the container in `Program.cs` decides which implementation it gets. The class never creates them with `new`.

**Data is not a dependency.** `new StudySession { ... }`, `new StudySessionDto(...)`, `new Rating(5)`, `new List<T>()`, `throw new SomethingException(...)` are fine.

## ✅ Good

**Constructor injection, interfaces, registered once** (StudySpot, Lecture 6 + 9)

```csharp
public class StudySessionsController(IStudySessionService service) : ControllerBase { /* ... */ }
public class StudySessionService(IStudySessionRepository repository) : IStudySessionService { /* ... */ }
public class EfStudySessionRepository(StudySpotDbContext db) : IStudySessionRepository { /* ... */ }

// Program.cs
builder.Services.AddDbContext<StudySpotDbContext>(...);                               // scoped
builder.Services.AddScoped<IStudySessionRepository, EfStudySessionRepository>();
builder.Services.AddScoped<IStudySessionService, StudySessionService>();
```

**`HttpClient` from the factory, the clock injected so tests control it**

```csharp
builder.Services.AddHttpClient<ICourseApiClient, CourseApiClient>(c => c.BaseAddress = new Uri("https://api.example.com"));
builder.Services.AddSingleton(TimeProvider.System);

public class ReminderService(TimeProvider time, IStudySessionRepository repository)
{
    public async Task<IReadOnlyList<StudySession>> DueSoonAsync(CancellationToken ct)
    {
        var now = time.GetUtcNow();
        // ...
    }
}
```

## ❌ Bad

```csharp
// The controller builds its whole dependency chain: no way to test it with a fake.
private readonly StudySessionService _service = new(new EfStudySessionRepository(new StudySpotDbContext(...)));

// new HttpClient() per call: exhausts sockets under load.
using var client = new HttpClient();

// Service locator: dependencies hidden inside the method instead of visible in the constructor.
var repo = HttpContext.RequestServices.GetRequiredService<IStudySessionRepository>();

// Captive dependency: a singleton holding a scoped DbContext forever.
builder.Services.AddSingleton<IStudySessionRepository, EfStudySessionRepository>();

// static helper classes with hidden state instead of an injected service.
public static class SessionStore { public static StudySpotDbContext Db = ...; }
```

## Common mistakes

- Registering everything as singleton "because it's faster". Lifetimes follow what the class holds: a `DbContext` is scoped, so everything that uses it is scoped (or shorter).
- Injecting the concrete class (`StudySessionService`) instead of the interface - works, but the controller can't be tested with a mock.
- Constructors with 8+ dependencies: a sign the class does too much (Single Responsibility).

## Questions you may be asked

- What are the three lifetimes (transient, scoped, singleton), and which one does your `DbContext` have? Why?
- What is a captive dependency? Why does ASP.NET Core refuse to start with one in Development?
- How does DI make your controller testable? Show a test that uses a mock.
- Where in your code do you still use `new`, and why is that not a dependency?

## Further reading

- [Theory lecture 6: Dependency Injection](https://github.com/smagurauskas/software-engineering/blob/main/06-dependency-injection.qmd)
- [StudySpot WALKTHROUGH-06](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/06/WALKTHROUGH-06.md)
- [Dependency injection in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection)
