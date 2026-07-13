# Conventional Comments — the Juwai comment vocabulary

Every review comment — from a human **or** an AI reviewer — uses this format. Labelling takes five seconds and removes the single biggest source of review friction: the author not knowing whether a comment blocks the merge, and how it should be resolved.

> An unlabelled *"This is not worded correctly."* is ambiguous — is it blocking? An opinion? A label makes the intention clear: `suggestion (non-blocking): …` reads completely differently from `issue (blocking): …`.

## Format

```
<label> (decorations): <subject>

[discussion — the why, context, next steps]
```

Example:

> **issue (security, blocking):** This endpoint reads `user_id` from the request body instead of the session.
>
> Any logged-in user can act on another user's data by editing the payload. We should take the ID from `event.locals.user` — same pattern as `favorites/+server.ts`.

## Labels

| Label | Meaning | Blocks by default? |
| --- | --- | --- |
| **praise** | Highlight something done well. Aim for at least one sincere one per review. | no |
| **nitpick** | Trivial, preference-based request. | no — always non-blocking |
| **suggestion** | A concrete proposed improvement. Be explicit about *what* and *why*. | only with `(blocking)` |
| **issue** | A specific problem, user-facing or internal. Pair with a suggestion where possible. | yes, unless `(non-blocking)` |
| **todo** | Small, trivial, but necessary change (incl. typos). | yes (cheap to resolve) |
| **question** | A potential concern you're not sure is relevant. Ask, don't assert. | no — answer resolves it |
| **thought** | An idea sparked by the review. Explicitly non-blocking; often becomes a follow-up ticket. | no |
| **chore** | Process task before acceptance (run a migration, update changelog). | yes (cheap to resolve) |
| **note** | Something the reader should be aware of. | no — always non-blocking |

## Decorations

| Decoration | Semantics |
| --- | --- |
| `(blocking)` | Must be resolved before the PR is accepted. |
| `(non-blocking)` | Must **not** hold the PR. The author may act on it, ticket it, or decline with a reason. |
| `(if-minor)` | Resolve only if the fix turns out to be minor/trivial. |
| `(security)`, `(ux)`, `(test)`, … | Free-form scope tags. Combine: `issue (ux, non-blocking):`. |

## Severity — shared by humans and the AI reviewers

Every finding maps to one severity. This is the taxonomy the Claude reviewer's consolidated comment uses, and the one humans use when deciding the review verdict:

| Severity | Meaning | Verdict impact |
| --- | --- | --- |
| **Critical** | Will cause an outage, data loss, or is exploitable. | **Request changes** — blocks merge |
| **Warning** | Measurable regression or concrete risk. | Several warnings → request changes; one-off → approve with comments |
| **Suggestion** | Improvement worth considering. | Approve with comments |
| **Optional** | Nice-to-have, style, polish. | Approve |

## Hard rules

1. **Comment on the code, never the person.** Avoid "you" entirely — write "we should…", drop the subject ("suggest renaming to `seconds_remaining`"), or use question form ("could we move this into the connector?").
2. **Explain the why.** Tie feedback to a principle, the style guide, or an observed consequence — not "I don't like this."
3. **The author chooses the fix.** Reviewers point at problems; authors decide how to solve them. Code examples are welcome for clear, uncontroversial improvements (2–3 per round max).
4. **Non-blocking means non-blocking.** `nitpick`, `thought`, `note`, and anything tagged `(non-blocking)` explicitly do **not** hold the PR. This is what makes "post issues, then approve" safe.
5. **Every comment is resolved before merge** (Gerrit-style closure): the author marks each thread **Done** or replies **won't fix + reason** and resolves it. Nothing drifts.
6. **Batch repeats.** If the same issue appears many times, flag 2–3 instances and ask for the pattern to be fixed — don't carpet-bomb.

## Quick examples

```
praise: Beautiful test — the race-condition case especially.

nitpick: `little star` => `little bat` — can we update the other references too?

suggestion (security): We're hand-rolling a DOM purifier here. Could we use
the framework's sanitizer instead? Custom purifiers are where XSS slips in.

issue (ux, non-blocking): These buttons should be red, but let's handle
that in a follow-up ticket.

question (non-blocking): Does it matter which thread wins here? Maybe we
should keep looping until they've all won to avoid a race?

chore (blocking): This needs a `php artisan migrate` on deploy — please add
it to the Deploy notes section of the PR description.
```

*Google-style legacy equivalents, for translation: `Nit:` ≈ `nitpick`, `Optional:`/`Consider:` ≈ `suggestion (non-blocking)`, `FYI:` ≈ `note`.*
