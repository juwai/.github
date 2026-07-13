# Code Review — the Juwai standard

## What review is for

Review exists to find **bugs, maintenance issues, logic errors, simplifications, optimizations, misunderstandings, missed use cases, and missed tests** — *and* to spread understanding: knowledge-sharing, onboarding, and design sanity-checks. We explicitly reject the "review can't find bugs" hedge: on risk surfaces you **are** expected to hunt bugs by reading the code, not rubber-stamp.

**The standard** (Google's, adopted verbatim): *approve a PR once it definitely improves the overall code health of the system, even if it isn't perfect.* There is no perfect code — only better code. Don't hold a PR hostage to polish; do hold the line on anything that degrades the codebase.

## The three layers

Every PR flows through three layers. The first two clear the boring 80%; the third is where humans spend judgment.

1. **Automated gates** (required status checks): unit tests · integration/smoke · type checks · lint. Red → the author fixes it; no human reads anything.
2. **AI first pass** (non-blocking): Copilot (tuned by repo custom instructions) + the Claude multi-agent reviewer (risk-tiered lenses → coordinator → **one consolidated comment**, severity-labelled per [CONVENTIONAL_COMMENTS.md](CONVENTIONAL_COMMENTS.md)).
3. **Human review, routed by risk** (CODEOWNERS): a risk surface touched → a careful human read is required regardless of diff size. Low-risk + green gates → the lightweight lanes below.

**AI is triage, never the reviewer of record.** It points human attention at what deserves it; it does not approve, and its silence is not an approval.

## Lanes

Declared by the author in the PR description; kept honest by CODEOWNERS and the risk checklist.

| Lane | Applies to | Process |
| --- | --- | --- |
| **trivial-self-merge** | Docs, comments, config text, dead-code deletion; no behavior change | Gates green → merge. No human review needed. |
| **announce-then-merge** | Small, low-blast-radius, fully understood changes | Post the PR in the team channel, merge after gates; review happens after merge if anyone bites. |
| **standard** | Everything else | AI first pass → human review → post issues, then approve. |
| **touches-risk-surface** | Anything under a CODEOWNERS risk path — auth, payments, migrations, deploy config, external API contracts | Full human read by the routed owner, regardless of size. Fast lanes **never** apply. |

The fast lanes only exist for **low-risk, understood** changes — never for risk surfaces and never for AI output the author can't explain (see [Reviewing AI-authored changes](#reviewing-ai-authored-changes)).

**Revert-first for low-risk regressions.** In the fail-small spirit: if a fast-lane merge breaks something, revert immediately and investigate afterwards — don't hold main broken while debugging. A revert is cheap; that's the point of keeping changes small.

---

## Reviewer Guide

### Respond fast

- **SLA: one business day, maximum**, to first response. Check the review queue at the start and end of your day. A blocked teammate costs more than almost anything else you're doing.
- Don't interrupt deep work mid-flow — respond at your next natural break. Even a partial review or "can't get to this today, try X" unblocks people.
- Slow review is what kills the whole system: it pressures rubber-stamping and punishes the people who keep PRs small.

### Post issues, then approve

Default to **approving with comments** and trusting the author to push fixes and merge — block only when a finding is `Critical` (would break production, lose data, or open a security hole) or the PR degrades code health. `(non-blocking)`, `nitpick`, `thought`, and `note` never hold a PR — that's the contract that makes this safe.

**Closure discipline (Gerrit-style):** every comment thread is explicitly resolved before merge — the author marks **Done** or **won't fix + reason** via *Resolve conversation*. Nothing drifts, nothing is silently ignored.

### What to look for (in this order)

1. **Design** — is this the right change at all? Does it belong here? Is now the right time? *This is the one thing AI cannot judge — it's the core of the human job.*
2. **Functionality** — does it do what the ticket says, and *only* that? Edge cases, concurrency, races. For UI: ask for screenshots/demo.
3. **Complexity** — could a reader understand this quickly? Watch for over-engineering: solving speculative future problems instead of the present one.
4. **Tests** — present, in the same PR, and would actually fail if the code broke. Tests don't test themselves.
5. **Naming, comments, style** — comments should explain *why*, not what. Style-guide points are law; personal preference is labelled `nitpick`.
6. **Documentation** — build/test/deploy docs updated if the PR changes them.

On **risk surfaces**: read every line, read *around* the diff, and understand it well enough that you could maintain it. High-level first — architecture and interfaces in round one, naming and nits later (they often go moot after a redesign).

### How to write comments

Use [Conventional Comments](CONVENTIONAL_COMMENTS.md). Beyond the format:

- **Never "you".** Rewrite as "we", drop the subject, or ask a question.
- **Requests, not commands.** "Can we move `Foo` to its own file?" invites dialogue; "Move `Foo`" invites defensiveness.
- **Principles, not opinions.** Cite the style guide, a measured consequence, or say "I found this hard to follow" (an observation) rather than "this is confusing" (a verdict).
- **Praise sincerely.** At least one real `praise:` per review, especially for juniors.
- Aim to bring the PR up a letter grade or two — not to an A+.

### Disagreements

Resolution ladder: technical facts and data → the style guide → engineering principles → consistency with the codebase. If several approaches are demonstrably equally valid, the author's choice stands. Still stuck after a couple of rounds of text? **Talk face-to-face/video** — text makes people forget there's a human on the other end — then escalate to a team design discussion. And weigh conceding: an imperfect approved PR is often cheaper than a damaged working relationship. Don't accept "I'll clean it up later" — later rarely comes; the only exception is a genuine emergency.

---

## Author Guide

### Golden rule

**Value your reviewer's time.** They arrive each day with a finite supply of focus; every trivial thing they catch for you is capacity the team loses.

### Before opening the PR (the pre-PR self-review SOP)

1. **Self-review the diff** — in the reviewer's diff view, with fresh eyes. You will catch more than your reviewer will; that's consistently the highest-value step in the whole process.
2. **AI-assisted self-review** (recommended flow): run a strong-model review over your branch, have it write findings to a file, action them with an agent, and only then open the PR. Raise the PR's grade *before* a human spends focus on it.
3. **Check the ticket is fully satisfied** — the PR addresses the ticket and *only* the ticket.
4. **Tests and optimization to the point of diminishing returns** — new code covered by new tests; obvious inefficiencies fixed.
5. **Automate the easy stuff** — if a reviewer is telling you about formatting, the linter isn't wired up properly; fix the pipeline, not the comment.

### The PR itself

- **Do one thing.** Summarizable in one sentence. ~100 changed logic lines is a good size; past **~400 logic lines**, justify it in the description or split it. (Mechanical changes — renames, generated code, whole-file deletions — don't count against the budget, but say so.)
- **Separate functional from non-functional changes.** Two-line logic changes buried in a sea of whitespace/refactor noise are maddening. Refactor in its own PR.
- **Description = what + why.** First line: imperative, specific ("Remove size limit on RPC freelist", not "fix bug"). Body: the problem, why this approach, tradeoffs, and **the why behind non-obvious decisions** — links rot, so include context inline. Fill the template's Why/Lane/Size/Risk/Tests/Deploy-notes block honestly.
- **Draft PRs early** for anything risk-surface or architecturally interesting — get eyes on direction before the code hardens. Architecture debates at the 11th hour burn days; have the catch-up *before* the PR.

### Handling review comments

- Never respond in anger. The review is of the code, not of you.
- **If a reviewer misunderstood, the code is the bug.** Clarify the code itself first, then a code comment; an explanation that lives only in the review thread helps no future reader.
- Respond to every thread explicitly (Done / won't fix + reason) so it's always clear who holds the baton, and keep rounds fast — driving your review to completion is your highest priority.
- Award ties to the reviewer — they know what the code reads like fresh; you don't anymore.

---

## Reviewing AI-authored changes

The team uses coding agents heavily, so the process names what changes when a human didn't write the diff.

1. **Accountability — no unvetted-AI dumps.** AI collapses the effort signal reviewers used to infer quality from: polished-looking code no longer implies anyone thought it through. The human who *directed* the agent **owns correctness**. Requesting review certifies *"I have already reviewed this myself."* Opening an unvetted agent PR and offloading verification onto a reviewer is a norm violation, not a shortcut.
2. **Comprehension gate.** The merge bar for AI-authored code is: *a named human can explain what this does and why it's designed this way, well enough to maintain it.* Not just "is it well-written" — clean, working code still carries cognitive debt if nobody can articulate its rationale. The PR must **document the why** for non-obvious decisions, because AI code doesn't carry it implicitly. This gate is what scopes the fast lanes: merge-now-rewrite-later and review-after-merge apply to low-risk, *understood* changes only — **never** to risk surfaces or opaque AI output.
3. **Meta-review, don't re-read every line.** For low/medium-risk changes, the human validates the **AI reviewer's consolidated comment** — a numbered, severity-labelled findings list — and iterates with the agent by disposition ("3: fix", "2b: won't fix — X"), rerunning until findings converge on items already consciously dispositioned. Then skim the final diff. *Convergence is not comprehension:* risk surfaces still get a full human read.
4. **Lean harder on the safety nets.** Because the effort signal is gone, agent-heavy repos get more integration/E2E tests and static analysis, not less.

---

## Routing by risk — CODEOWNERS

CODEOWNERS auto-requests the owning reviewer when a risk path changes; with branch protection, their approval is required. Risk surfaces get a careful read **regardless of diff size** — a 3-line auth change outranks a 300-line UI change.

| Repo | Representative risk paths |
| --- | --- |
| `juwai-web` | `src/lib/server/**` · `src/hooks.server.ts` · `src/routes/api/**` · drizzle migrations · `Dockerfile` |
| `core-api` | auth modules · payment paths · `migrations/**` · public API surface · external-contract modules |
| `himalayas-api` | `app/Http/Middleware/**` · auth controllers · `database/migrations/**` · `config/**` · `routes/**` |
| `iqiglobal-site` | `app/Http/Integrations/**` (ERP/Paynet/Meta) · auth & middleware · `database/migrations/**` · `config/**` · deploy workflows |
| `feed-engine` / `dagster-pipelines` | `@repository` factory files (a `raise` there crashes prod) · any DDL / migration / AUTO_INCREMENT-touching code |

Per-repo CODEOWNERS files are seeded from [`templates/codeowners/`](../templates/codeowners/) in `juwai/standards`.

## CI gates & flaky tests

Required status checks (as each repo gains green CI): **unit · integration/staging · types · lint**. Green on low-risk code → don't read it. Red, or a risk surface touched → read carefully.

**Flaky-test policy:** once checks gate merges, one flaky test blocks everyone. A test observed to flake gets one automatic retry; if it flakes again it is **quarantined the same day** (skipped with a linked ticket) rather than left to train people to click re-run. Quarantined tests have an owner and a deadline; deleting a flaky test beats keeping a lying one.

## Discipline that keeps the lanes safe

Clear acceptance criteria on every ticket · small tickets → small PRs · architecture catch-ups **before** code for risk-surface or >~400-logic-line work · draft PRs early · coach in 1:1s — and put your foot down on giant, untested, unannounced PRs. The whole system collapses the moment those sneak through.
