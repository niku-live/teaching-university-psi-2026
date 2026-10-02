# 7. Using a stream to load data

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

A **stream** is a sequence of bytes you read piece by piece (a file, an HTTP response, a socket, a memory buffer). A **reader** (`StreamReader`, `BinaryReader`, ...) turns those bytes into text or values. Streams hold operating-system resources, so they must be **disposed** - that is what `using` does.

`File.ReadAllText` is fine for tiny files in real life, but for this assignment you must use a stream and `using` (or at least understand them).

## ✅ Good

**Line by line, async, disposed automatically**

```csharp
public static async Task<List<StudySession>> LoadAsync(string path)
{
    var sessions = new List<StudySession>();

    await using var stream = File.OpenRead(path);
    using var reader = new StreamReader(stream);

    while (await reader.ReadLineAsync() is { } line)
    {
        var parts = line.Split(',');
        if (parts.Length < 2) continue; // skip malformed lines

        sessions.Add(new StudySession { Course = parts[0], Topic = parts[1] });
    }

    return sessions;
} // stream and reader disposed here, even if an exception was thrown
```

**An HTTP response as a stream** (no giant string in memory)

```csharp
using var client = new HttpClient();
await using var stream = await client.GetStreamAsync(url);
var sessions = await JsonSerializer.DeserializeAsync<List<StudySession>>(stream);
```

**Classic `using` block** (same idea, explicit scope)

```csharp
using (var reader = new StreamReader("sessions.csv"))
{
    var header = reader.ReadLine();
}
```

## ❌ Bad

```csharp
// Never disposed: the file stays locked until the garbage collector finalizes it.
var reader = new StreamReader("sessions.csv");
var text = reader.ReadToEnd();

// Manual Close() that is skipped when an exception happens.
var stream = File.OpenRead(path);
var data = Parse(stream);   // throws -> Close never runs
stream.Close();

// Blocking call in an async context (and ReadToEnd of a huge file into memory).
var content = reader.ReadToEnd();

// Reading a stream twice without resetting its position, then wondering why it is empty.
var first = new StreamReader(stream).ReadToEnd();
var second = new StreamReader(stream).ReadToEnd(); // ""

// Hard-coded absolute path that exists only on one machine.
File.OpenRead(@"C:\Users\Tomas\Desktop\sessions.csv");
```

## Common mistakes

- Hard-coded absolute paths. Use a relative path, configuration, or the app's content root.
- Forgetting the file is copied to the output directory (`Copy to Output Directory` in the project) and failing only on other machines.
- No handling for missing/malformed files: decide whether to throw, log or return an empty result, and be able to say why.
- Using `using` on something that must outlive the method (returning a disposed stream).

## Questions you may be asked

- What does `using` compile to? (`try`/`finally` with `Dispose`.)
- What is the difference between `Stream` and `StreamReader`?
- What happens if you do not dispose a `FileStream`?
- Why `ReadLineAsync` instead of `ReadLine`?

## Further reading

- [Files and streams](https://learn.microsoft.com/en-us/dotnet/standard/io/)
- [using statement](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/using)
