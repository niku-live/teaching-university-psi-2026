# Lecture 2

## What was discussed

_TBD - to be filled in after the lecture, matching how [Lecture 01](../Lecture01/README.md) documents what actually happened rather than what was planned._

This practice lecture reviews [Theory Lecture 2](https://github.com/smagurauskas/software-engineering/blob/main/02-web-services.qmd) material - web services and HTTP fundamentals (REST, GraphQL, SOAP, gRPC), and [building APIs with ASP.NET](https://github.com/smagurauskas/software-engineering/blob/main/02-web-services-with-asp-net.qmd) (Minimal APIs vs. Controllers, route/query parameters, status codes, dependency injection, OpenAPI/Swagger).

## Step-by-Step Tutorial: Continuing StudySpot

This week's focus (still Alpha - see the [ROADMAP](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/01/ROADMAP.md), which spans Lecture 1 through Lecture 6): pick up the still-open Alpha items that connect directly to this week's web services material:

- Complete full CRUD on the API: add `PUT`/`DELETE /api/studysessions/{id}` alongside the existing `GET`/`POST`, in `StudySessionsController`.
- Add API documentation (Swagger/OpenAPI via `Microsoft.AspNetCore.OpenApi` + `Swashbuckle.AspNetCore`), tying directly into this week's ASP.NET material.
- Create a study session from the actual UI, not just the API (previously API-only, demonstrated via browser/curl/REST Client) - a real form in `StudySessions.js`.

Reference Guide: [PSI 2026 Playground PR #3](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/3) (`lectures/02-draft` &rarr; `lectures/02`) has the end result.

## Homework

TBD.
