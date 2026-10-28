# 7. Unit and integration test coverage of at least 50%

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

- **Unit tests** test one class on its own: the real class, with fakes, stubs or mocks for its dependencies. Fast, precise.
- **Integration tests** test pieces together: the real API through HTTP (`WebApplicationFactory`), the real `Program.cs`, the real database. They catch what unit tests can't: routing, validation, JSON, DI registrations, SQL.
- **Coverage** is the share of your code's lines that ran during the tests. The requirement is at least **50%** for unit + integration tests together, measured on **your** code - exclude generated code (migrations, OpenAPI, compiler-generated) or the number means nothing.

Coverage only shows a line *ran*, not that a test *checked* it. A test without an assert raises coverage and proves nothing.

## ✅ Good

**A unit test with a fixed clock, Arrange/Act/Assert** (StudySpot, Lecture 8)

```csharp
[Fact]
public async Task GetUpcoming_SessionsBeforeAsOf_AreLeftOut()
{
    await AddSessionAsync("Before the cutoff", AsOf.AddDays(-1));
    await AddSessionAsync("After the cutoff", AsOf.AddDays(1));

    var upcoming = await _service.GetUpcomingAsync(course: null, minSeatsAvailable: null, asOf: AsOf, Cancellation);

    Assert.Equal(["After the cutoff"], upcoming.Select(s => s.Topic));
}
```

**A controller test with a mocked service (NSubstitute)**

```csharp
var service = Substitute.For<IStudySessionService>();
service.GetByIdAsync(42, Arg.Any<CancellationToken>()).Returns((StudySession?)null);

var result = await new StudySessionsController(service).GetById(42, CancellationToken.None);

Assert.IsType<NotFoundResult>(result.Result);
```

**An integration test through the real API and a real database** (Lecture 9)

```csharp
public class StudySessionsApiTests(StudySpotApiFactory factory)   // WebApplicationFactory + its own SQLite file
{
    [Fact]
    public async Task Create_StartsInThePast_Returns400()
    {
        var response = await _client.PostAsJsonAsync("/api/studysessions", sessionInThePast, Cancellation);
        Assert.Equal(HttpStatusCode.BadRequest, response.StatusCode);
    }
}
```

**Measuring it** (see WALKTHROUGH-08 step 10)

```pwsh
dotnet test --coverage --coverage-output-format cobertura --coverage-output coverage.cobertura.xml
dotnet reportgenerator -reports:TestResults/coverage.cobertura.xml -targetdir:coverage-report "-classfilters:-*Migrations*;-Microsoft.AspNetCore.OpenApi.Generated*;-System.Runtime.CompilerServices*"
```

## ❌ Bad

```csharp
// No assert: coverage goes up, nothing is checked.
[Fact]
public async Task GetAll() => await _client.GetAsync("/api/studysessions");

// Tests that depend on each other's data or on order.
[Fact] public async Task Test1_Create() { /* creates id 1 */ }
[Fact] public async Task Test2_Update() { /* assumes id 1 exists */ }

// The real clock: passes today, fails next month.
Assert.Single(service.GetUpcoming());   // the sample session "starts tomorrow"...

// Integration tests against the developer's own database, wiping their data.

// Only getters/setters and DTOs tested to reach 50%.
```

## Common mistakes

- Only integration tests, or only unit tests. The requirement says both.
- Coverage measured with generated code included (much lower) or reported without a report to show.
- Testing the EF in-memory provider instead of a relational database for integration tests - it isn't relational, and behaves differently. Use SQLite or Testcontainers.
- Mocks that check *how* the code works (`Received(...)` everywhere) instead of *what* it returns: every refactor breaks them.

## Questions you may be asked

- What's the difference between a unit and an integration test? Show one of each that you wrote.
- Stub vs mock vs fake?
- What does your coverage report exclude, and why?
- Which important code is still not covered? What would you test next?
- How do your integration tests get their own database?

## Further reading

- [Theory lecture 7: Testing](https://github.com/smagurauskas/software-engineering/blob/main/07-testing.qmd)
- [StudySpot WALKTHROUGH-08](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/08/WALKTHROUGH-08.md) and [WALKTHROUGH-09 step 7](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/09-draft/WALKTHROUGH-09.md)
- [Integration tests in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/test/integration-tests)
