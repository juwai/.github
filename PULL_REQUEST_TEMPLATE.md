## 🎫 Ticket Details

[`ticket number`](`ticket link`)

**Why (one line):** _the problem this solves / business reason_

## 🚦 Review Routing

**Lane:** ⬜️ trivial-self-merge · ⬜️ announce-then-merge · ✅ standard · ⬜️ touches-risk-surface

**Size:** _~N logic lines · mechanical or logic? (>~400 logic lines → justify or split)_

**Risk surfaces touched?**
⬜️ auth / permissions
⬜️ payments / money
⬜️ DB migrations / DDL
⬜️ deploy config / infra
⬜️ external API contracts
✅ none

> Any box ticked above → the lane is `touches-risk-surface` and CODEOWNERS review applies, regardless of size.

## 📦 Type of Change

⬜️ 🐛 Bug fix
⬜️ ✨ New feature
⬜️ 🎨 UI/UX update
⬜️ ⚡ Enhancement / Improvement
⬜️ 🔨 Refactor (no functional change)

## 🛠 Summary of Changes

- _change 1_
- _change 2_

_For non-obvious decisions, include the **why** — especially for AI-authored code, the rationale must live here, not in the agent's context window._

## ✅ Author Checklist

⬜️ Diff self-reviewed (fresh eyes, reviewer's diff view)
⬜️ New code covered by new tests
⬜️ Ticket fully satisfied — and nothing beyond the ticket
⬜️ AI-authored portions vetted — I can explain what & why

## 🚀 Deploy Notes

_Migrations, env vars, queue restarts, cache clears, one-off commands — anything that must happen on deploy. Default:_ **none**

## 🧪 Testing

_Optional for trivial-self-merge / announce-then-merge lanes._

### Pre-requisites

- Commands
  - _e.g. `composer install`_
- `.env` updates
  - _e.g. `variable_1=value`_

| No. | Description     | Expected Result |
| --- | --------------- | --------------- |
| 1   | _description 1_ | _result 1_      |

## ℹ️ Extra Info

_screenshots / videos / examples / links, if any_
