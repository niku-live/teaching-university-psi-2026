# Lecture 2

## What was discussed

This practice lecture reviewed [Theory Lecture 2](https://github.com/smagurauskas/software-engineering/blob/main/02-web-services.qmd) material - web services and HTTP fundamentals (REST, GraphQL, SOAP, gRPC), and [building APIs with ASP.NET](https://github.com/smagurauskas/software-engineering/blob/main/02-web-services-with-asp-net.qmd) (Minimal APIs vs. Controllers, route/query parameters, status codes, dependency injection, OpenAPI/Swagger) - then applied it live to StudySpot: completed full CRUD on the API (`PUT`/`DELETE` alongside the existing `GET`/`POST`), added Swagger/OpenAPI documentation, and added a real form so a study session can be created from the UI instead of the API only.

Beyond StudySpot, we stepped back and looked at RESTful APIs in general, using two public, no-auth-required APIs as examples: [PokeAPI](https://pokeapi.co) (simple `GET`-only lookups) and [JSONPlaceholder](https://jsonplaceholder.typicode.com) (a fake but realistic API supporting full `GET`/`POST`/`PUT`/`DELETE` CRUD). The point wasn't the Pokémon or fake posts themselves - it was showing that the same HTTP request can be made from several different clients and looks the same everywhere: the `.http` files below sent via the REST Client extension, the exact same requests sent from PowerShell (`Invoke-RestMethod`), and - for `GET` requests specifically - just typing the URL into a browser's address bar. A REST API isn't tied to any one tool; it's just HTTP.

To close the loop back to StudySpot, we opened the browser's Developer Tools (Network tab) while actually using the app: loading the study sessions page fires a `GET /api/studysessions` request, and submitting the new "create session" form fires a `POST /api/studysessions` - the same two requests we'd been sending by hand all lecture, just triggered by the UI instead.

## Step-by-Step Tutorial: Continuing StudySpot

This week's focus (still Alpha - see the [ROADMAP](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/01/ROADMAP.md), which spans Lecture 1 through Lecture 6): pick up the still-open Alpha items that connect directly to this week's web services material:

- Complete full CRUD on the API: add `PUT`/`DELETE /api/studysessions/{id}` alongside the existing `GET`/`POST`, in `StudySessionsController`.
- Add API documentation (Swagger/OpenAPI via `Microsoft.AspNetCore.OpenApi` + `Swashbuckle.AspNetCore`), tying directly into this week's ASP.NET material.
- Create a study session from the actual UI, not just the API (previously API-only, demonstrated via browser/curl/REST Client) - a real form in `StudySessions.js`.

Reference Guide: [PSI 2026 Playground PR #3](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/3) (`lectures/02-draft` &rarr; `lectures/02`) has the end result. Start with [WALKTHROUGH-02.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/02/WALKTHROUGH-02.md) for the exact step-by-step (assumes you've already done [WALKTHROUGH-01.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/01/WALKTHROUGH.md) from last week) - see [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/02/WALKTHROUGHS.md) for the full index.

## Testing APIs: `.http` Files

The [`http/`](http/) folder in this lecture has the `.http` files used during the demo - see [File Formats & Tools](../README.md#http-request-files-http) in the main README for what a `.http` file is and what to install to run them:

- [`pokemon.http`](http/pokemon.http) - `GET`-only requests against [PokeAPI](https://pokeapi.co), including one deliberately non-existing Pokémon to see what a `404 Not Found` looks like.
- [`jsonplaceholder.http`](http/jsonplaceholder.http) - full `GET`/`POST`/`PUT`/`DELETE` CRUD against [JSONPlaceholder](https://jsonplaceholder.typicode.com), a public fake-but-realistic API (writes don't actually persist, but the request/response shape is real).
- [`studyspot.http`](http/studyspot.http) - the same CRUD requests, this time against your own local StudySpot API (`https://localhost:7039`) - copy this file and adjust the host/paths to test your own team's project the same way.

## Homework

TBD.
