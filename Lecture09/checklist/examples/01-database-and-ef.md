# 1. A relational database and Entity Framework store all the data

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

Every piece of data your app keeps - users, the main entities, their relations - lives in a **relational database** and is read and written through **Entity Framework Core**. Restart the app and nothing is lost. Which database is your choice: SQLite (one file, nothing to install), PostgreSQL or SQL Server (a server, usually in Docker), MySQL.

The schema is created by **migrations** (`dotnet ef migrations add ...`), which are committed to the repository like any other code.

## ✅ Good

**A `DbContext` that only configures what conventions can't guess** (StudySpot, `CoolApp.Api/Data/StudySpotDbContext.cs`)

```csharp
public class StudySpotDbContext(DbContextOptions<StudySpotDbContext> options) : DbContext(options)
{
    public DbSet<StudySession> StudySessions => Set<StudySession>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Our own Rating struct: store its int, read it back through the constructor
        modelBuilder.Entity<StudySession>()
            .Property(s => s.HostRating)
            .HasConversion(rating => rating!.Value.Value, value => new Rating(value));
    }
}
```

**Registered once, scoped, with the connection string from configuration**

```csharp
builder.Services.AddDbContext<StudySpotDbContext>((services, options) =>
    options.UseSqlite(services.GetRequiredService<IConfiguration>().GetConnectionString("StudySpot")));
builder.Services.AddScoped<IStudySessionRepository, EfStudySessionRepository>();
```

**Relations mapped as navigation properties**, so EF creates the foreign keys

```csharp
public class StudySession
{
    public int Id { get; set; }
    public int CourseId { get; set; }
    public Course Course { get; set; } = null!;
    public List<Rsvp> Rsvps { get; set; } = [];
}
```

**Migrations in the repository**

```text
CoolApp.Api/Migrations/20261028180033_InitialCreate.cs
CoolApp.Api/Migrations/20261028180104_AddVersion.cs
CoolApp.Api/Migrations/StudySpotDbContextModelSnapshot.cs
```

## ❌ Bad

```csharp
// "Database" that forgets everything on restart, and isn't safe for concurrent requests.
public static class Db { public static List<StudySession> Sessions = new(); }

// Only SOME data in the database; users still in a JSON file.
var users = JsonSerializer.Deserialize<List<User>>(File.ReadAllText("users.json"));

// A DbContext created by hand and shared by every request (not thread-safe, never disposed).
private static readonly StudySpotDbContext Db = new(new DbContextOptionsBuilder<StudySpotDbContext>().UseSqlite("Data Source=app.db").Options);

// Connection string with a real password hard-coded in the code.
options.UseNpgsql("Host=prod.example.com;Username=admin;Password=Hunter2");

// Schema created with EnsureCreated() - no migrations, so the schema can never be changed safely.
db.Database.EnsureCreated();
```

## Common mistakes

- The database file (`*.db`) committed to Git. Add it to `.gitignore`.
- Editing a migration that was already merged. Add a new one instead.
- Two branches each adding a migration: the snapshot conflicts. Merge `main`, `dotnet ef migrations remove`, add yours again.
- Loading a whole table and filtering in C# when the filter could be in the SQL (`Where` on the `DbSet` before `ToListAsync`).
- Returning entities with navigation properties straight from the API (cycles in JSON, over-posting). DTOs help.

## Questions you may be asked

- What is a `DbContext`, and why is it registered as scoped?
- What does a migration contain? What are `Up` and `Down`? What is the model snapshot?
- What SQL does this query send? (Turn on `Microsoft.EntityFrameworkCore.Database.Command` logging and show it.)
- What does `AsNoTracking` do, and when is it right?
- What is the N+1 problem, and how do `Include` or a projection fix it?

## Further reading

- [Theory lecture 8: Persistence and ORM](https://github.com/smagurauskas/software-engineering/blob/main/08-persistence-and-orm.qmd)
- [StudySpot WALKTHROUGH-09](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/09-draft/WALKTHROUGH-09.md)
- [EF Core documentation](https://learn.microsoft.com/en-us/ef/core/)
- [Migrations overview](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/)
