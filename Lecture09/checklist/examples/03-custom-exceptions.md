# 3. Your own exception type, thrown and meaningfully handled

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

- **Your own type**: a class deriving from `Exception` (or a more specific .NET exception), named after what went wrong **in your app**: `StudySessionConflictException`, `SessionFullException`, `CourseNotFoundException`.
- **Thrown** where the problem is detected, carrying what a handler needs (an id, the conflicting value).
- **Meaningfully handled**: custom logic that decides what happens next - an HTTP status and a message the user can act on, a retry, a fallback value. "Meaningfully" excludes catching and ignoring it, or logging and rethrowing it unchanged.

## ✅ Good

**The exception** (StudySpot, `CoolApp.Api/Exceptions/StudySessionConflictException.cs`)

```csharp
public class StudySessionConflictException(int sessionId, Exception? innerException = null)
    : Exception($"Study session {sessionId} was changed by someone else after you loaded it. Load it again and reapply your change.", innerException)
{
    public int SessionId { get; } = sessionId;
}
```

**Thrown where the problem is detected, translating a library exception into ours** (`EfStudySessionRepository.UpdateAsync`)

```csharp
try
{
    await db.SaveChangesAsync(cancellationToken);
}
catch (DbUpdateConcurrencyException ex)
{
    throw new StudySessionConflictException(session.Id, ex);   // keeps the original as InnerException
}
```

**Handled once, for every endpoint** (`StudySessionConflictExceptionHandler`)

```csharp
public class StudySessionConflictExceptionHandler(IProblemDetailsService problemDetails) : IExceptionHandler
{
    public async ValueTask<bool> TryHandleAsync(HttpContext httpContext, Exception exception, CancellationToken cancellationToken)
    {
        if (exception is not StudySessionConflictException conflict)
        {
            return false;   // not ours: the next handler, or the default 500
        }

        httpContext.Response.StatusCode = StatusCodes.Status409Conflict;
        return await problemDetails.TryWriteAsync(new ProblemDetailsContext
        {
            HttpContext = httpContext,
            ProblemDetails = { Status = 409, Title = "The study session was changed by someone else", Detail = conflict.Message }
        });
    }
}

// Program.cs
builder.Services.AddProblemDetails();
builder.Services.AddExceptionHandler<StudySessionConflictExceptionHandler>();
app.UseExceptionHandler();
```

The React app can then show "someone else changed this session - reload?" instead of a generic error.

**Another meaningful handling: a fallback**

```csharp
try
{
    return await weatherClient.GetForecastAsync(location, ct);
}
catch (WeatherServiceUnavailableException ex)
{
    logger.LogWarning(ex, "Weather service down, showing the session without a forecast");
    return null;   // the page still works, just without the forecast
}
```

## ❌ Bad

```csharp
// Swallowed: the user sees "success", nothing was saved.
try { await repository.UpdateAsync(session, ct); } catch (Exception) { }

// A custom exception that is never caught anywhere: the client gets a 500 with no explanation.
throw new MyException("error");

// Logging and rethrowing changes nothing - and `throw ex;` loses the original stack trace.
catch (Exception ex) { logger.LogError(ex, "Error"); throw ex; }

// Exceptions for normal control flow: "not found" is an expected result, not an exceptional one.
try { var s = sessions.First(s => s.Id == id); } catch (InvalidOperationException) { return NotFound(); }

// A try/catch copy-pasted into every controller action instead of one handler.
```

## Common mistakes

- Naming it after the technology (`DatabaseException`) instead of what happened in the app.
- Losing the original exception: pass it as `innerException`.
- Catching `Exception` everywhere. Catch the specific type you can do something about.
- Returning the stack trace to the client. The handler decides what the client sees; details go to the log.

## Questions you may be asked

- When is an exception the right tool, and when is a return value (`null`, `false`, a result type) better?
- What is the difference between `throw;` and `throw ex;`?
- What does `finally` guarantee? How is `using` related to it?
- Follow your exception: where is it thrown, which layers does it pass through, where is it handled, and what does the user see?
- What does `IExceptionHandler.TryHandleAsync` returning `false` mean?

## Further reading

- [Theory lecture 4: C# basics, Exceptions](https://github.com/smagurauskas/software-engineering/blob/main/04-csharp-basics.qmd)
- [StudySpot WALKTHROUGH-09, stage 2](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/09-draft/WALKTHROUGH-09.md)
- [Best practices for exceptions](https://learn.microsoft.com/en-us/dotnet/standard/exceptions/best-practices-for-exceptions)
- [Handle errors in ASP.NET Core APIs](https://learn.microsoft.com/en-us/aspnet/core/web-api/handle-errors)
