# 1. Interactive UI and an end-to-end scenario

[← Checklist](../checklist.qmd) · [Index](README.md)

## What it means

Your application has a user interface a person can use: pressing a button or submitting a form leads to a result. At least one **user scenario** works from start to end:

```
User action in UI  →  HTTP call to the API  →  API logic / data change  →  response  →  UI shows the new state
```

A scenario is something a user wants to achieve ("host a study session and see it in the list"), not a technical step ("the endpoint returns JSON").

## ✅ Good

- The form posts to the API, the API validates, the list refreshes and shows the new item.
- The error case is visible too: invalid input gives a message in the UI, not a silent failure or a console error.
- You can demonstrate the scenario live in under a minute without editing code or data files.

StudySpot's "Host a new study session" form (`ClientApp`) → `POST /api/studysessions` (`Controllers/StudySessionsController.cs`) → the new session in the list.

## ❌ Bad

- A UI that only shows hard-coded data (nothing is sent to the API).
- A button wired to an empty handler, or to a feature that "will be done later".
- An API that works only when called from Postman or an `.http` file, with no UI using it.
- A scenario that works only on one team member's machine, or only if they edit the database by hand first.

## Common mistakes

- Demoing screens separately instead of a story. Pick one scenario and walk through it.
- Front-end calls that ignore errors: `fetch(...)` without checking `response.ok`.
- Unreproducible state: it works only once and needs a restart to demo again.

## Questions you may be asked

- Show me the scenario. Which file handles the click? Which controller action receives it?
- What does the API return when validation fails? Where does the UI show it?

## Further reading

- [Fetch API (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)
- [Controller actions in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/web-api/)
