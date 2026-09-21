# Lecture 3

## What was discussed

_TBD - to be filled in after the lecture_

## Step-by-Step Tutorial: Validating StudySpot

This week's theory covered [Web UI fundamentals](https://github.com/smagurauskas/software-engineering/blob/main/03-web-ui.qmd) (HTML/CSS/JS, server-side vs. client-side rendering, forms) and [Agile](https://github.com/smagurauskas/software-engineering/blob/main/03-agile.qmd) (the manifesto, XP, Scrum). We applied the Web UI half live to StudySpot, and turned the Agile half into concrete artifacts rather than just discussion.

This week's focus (still Alpha - see the [ROADMAP](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/02/ROADMAP.md), which spans Lecture 1 through Lecture 6): close out the last open Alpha *feature* - basic input validation - and set up lightweight Agile process artifacts for your own team's assignment ahead:

- Add client-side validation to the "Host a new study session" form (`StudySessions.js`) - real feedback beyond the native `required` attribute, tied to this week's Forms material.
- Add server-side validation to the `StudySession` model (`DataAnnotations` + a `ModelState` check in `StudySessionsController`), since client-side validation alone is never enough - the API must reject bad data too, however it arrives.
- A small CSS/flexbox pass on the sessions table and form layout, reinforcing this week's CSS/selectors material with a visible before/after.
- Add a pull request template and a `docs/definition-of-done.md` to the playground repo, turning this week's Agile/Scrum theory ("Definition of Done", "Shared understanding") into artifacts your own team should adopt too.

Reference Guide: [PSI 2026 Playground PR](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/PLACEHOLDER) (`lectures/03-draft` &rarr; `lectures/03`) has the end result. Start with [WALKTHROUGH-03.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/03/WALKTHROUGH-03.md) for the exact step-by-step (assumes you've already done [WALKTHROUGH-02.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/02/WALKTHROUGH-02.md)) - see [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/03/WALKTHROUGHS.md) for the full index.

Do the same for your own team repository this week: validate your own team's model both client- and server-side, and add a PR template + definition of done if you don't have one yet.

## Homework

TBD.
