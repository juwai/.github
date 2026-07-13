# Contributing

How code gets merged around here, in five lines:

1. **Small PRs that do one thing**, with tests, tied to a Linear ticket with clear acceptance criteria.
2. **Self-review before you request review** — you own correctness, especially for agent-written code. See the pre-PR SOP in [CODE_REVIEW.md](CODE_REVIEW.md#author-guide).
3. **Gates must be green** (tests · types · lint) before anyone reads anything. The AI first pass comments; humans are routed by risk via CODEOWNERS.
4. **Review comments use [Conventional Comments](CONVENTIONAL_COMMENTS.md)** — `label (decorations): subject`. Non-blocking means non-blocking; every thread is resolved before merge.
5. **Pick your lane honestly** (trivial-self-merge / announce-then-merge / standard / touches-risk-surface) in the PR template. Risk surfaces always get a full human read.

The full standard lives in [CODE_REVIEW.md](CODE_REVIEW.md). The comment cheat-sheet is [CONVENTIONAL_COMMENTS.md](CONVENTIONAL_COMMENTS.md).

> These files are synced from [`juwai/standards`](https://github.com/juwai/standards) — propose changes there, not here.
