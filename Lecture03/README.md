# Lecture 3

## What was discussed

This practice lecture ran the planned client-vs-server validation demo on StudySpot end to end, nothing skipped: added client-side validation to the "Host a new study session" form, then deliberately broke it by sending the same invalid data (an empty course, a session dated in the past, zero seats) straight to the API with a direct HTTP request - bypassing the form entirely - and it succeeded, because nothing on the server checked anything yet. Added server-side validation to the `StudySession` model (`DataAnnotations` + a custom `IValidatableObject` check, enforced automatically via the existing `[ApiController]` attribute, no controller changes needed) and re-ran the exact same attack to show it now gets rejected with `400 Bad Request`. Followed with the CSS/flexbox pass on the form and table, and four repository process artifacts: a pull request template, `docs/definition-of-done.md`, `CONTRIBUTING.md`, and `.github/CODEOWNERS`.

Beyond the plan, we also spent time on general HTML/CSS fundamentals: what a CSS class is, and how the cascade/hierarchy works (which rule wins when several could apply). Demonstrated this live with a couple of small, throwaway CSS changes on StudySpot - first a red background applied to the whole sessions table, then narrowed down to just the table headers - to make the effect of selector scope visible rather than abstract.

## Step-by-Step Tutorial: Validating StudySpot

This week's theory covered [Web UI fundamentals](https://github.com/smagurauskas/software-engineering/blob/main/03-web-ui.qmd) (HTML/CSS/JS, server-side vs. client-side rendering, forms) and [Agile](https://github.com/smagurauskas/software-engineering/blob/main/03-agile.qmd) (the manifesto, XP, Scrum). We applied the Web UI half live to StudySpot, and turned the Agile half into concrete artifacts rather than just discussion.

This week's focus (still Alpha - see the [ROADMAP](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/02/ROADMAP.md), which spans Lecture 1 through Lecture 6): close out the last open Alpha *feature* - basic input validation - and set up lightweight Agile process artifacts for your own team's assignment ahead. Deliberately run as a small security demo, not just a feature list:

- Add client-side validation to the "Host a new study session" form (`StudySessions.js`) - real feedback beyond the native `required` attribute, tied to this week's Forms material.
- **Live demo: hack it.** With only client-side validation in place, send the same garbage data (an empty course, a session dated in the past, zero seats) straight to the API with a direct HTTP request, skipping the form entirely - it succeeds, because nothing on the server checks anything. This is the actual point of the lecture: client-side validation is a UX convenience that lives inside one form, not a security boundary.
- Add server-side validation to the `StudySession` model (`DataAnnotations` + a custom `IValidatableObject` check), automatically enforced by the existing `[ApiController]` attribute - no controller changes needed. Re-run the exact same "hack" request and watch it get rejected with `400 Bad Request` this time.
- A small CSS/flexbox pass on the sessions table and form layout, reinforcing this week's CSS/selectors material with a visible before/after.
- Add repository process artifacts to the playground repo, turning this week's Agile/Scrum theory ("Definition of Done", "Shared understanding") into things your own team should adopt too: a pull request template, `docs/definition-of-done.md`, a `CONTRIBUTING.md` consolidating branch naming/formatting/PR process, and a `CODEOWNERS` file for path-based review routing.

Reference Guide: [PSI 2026 Playground PR #5](https://github.com/niku-live/teaching-university-psi-2026-playground/pull/5) (`lectures/03-draft` &rarr; `lectures/03`) has the end result. Start with [WALKTHROUGH-03.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/03-draft/WALKTHROUGH-03.md) for the exact step-by-step (assumes you've already done [WALKTHROUGH-02.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/02/WALKTHROUGH-02.md)) - see [WALKTHROUGHS.md](https://github.com/niku-live/teaching-university-psi-2026-playground/blob/lectures/03-draft/WALKTHROUGHS.md) for the full index.

Do the same for your own team repository this week: add client-side validation first, try to break it with a direct API call to see the gap for yourself, then add server-side validation to close it - and add a PR template, definition of done, CONTRIBUTING.md, and CODEOWNERS (with your own real GitHub usernames) if you don't have them yet.

## Homework

No homework was assigned this week. Applying the same client-side + server-side validation pattern (and the process artifacts) to your own team's project, as described above, is worth doing on your own time, but nothing is due specifically because of this lecture.
