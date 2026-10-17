# Working in this repository

Notes for Claude sessions: how to work here without running up avoidable cost.

## Keeping GitHub cost down

Sessions are billed on tokens, and CI is billed on minutes. Most of the waste
is not in the work — it is in the waiting, the re-reading, and the check-ins
nobody reads.

**Do not poll.** Subscribe to a PR and end the turn. Ending the turn is how you
wait; webhooks wake the session when CI finishes or a review lands. Never a
`sleep` loop.

**Cadence (ENG-COST-2).** A PR that is green, mergeable and review-clean is
*finished* — say so once and schedule nothing. If something genuinely needs a
fallback check-in, arm at most **one at a time**, at most **two over the PR's
whole lifetime**, and never closer than **6 hours**. Cancel any check-in you
armed the moment its reason is gone. This governs *when to wake*, never what
gets fixed, tested or verified. A red or conflicted PR is work now, not a
check-in.

**Read the cheapest call that answers the question.** The cost of a GitHub read
is the size of what comes back, not the number of calls:

| Question | Call | Cost |
| --- | --- | --- |
| Did CI pass? | `get_check_runs` | small |
| Why did CI fail? | `get_job_logs` (failed job only) | medium |
| What did reviewers say? | `get_review_comments` | small |
| What is the diff? | `get_diff` | proportional to the change |
| Anything else | `pull_request_read: get` | **echoes the entire PR body** |

`pull_request_read: get` is the expensive one. Use it to learn a PR's state —
not as a habit, and never for facts a cheaper call already gave you.

**Do not trust a PR's `merged` flag.** It has come back `false` for pull
requests that had plainly been merged. When it matters whether a change is
live, read the file on the default branch.

**One validated push.** Run the repo's own fast checks locally before pushing.
A push that turns CI red costs a full cycle plus the reviewers' attention.

**Size the writing to the change.** A one-file fix does not need a design
document.

**Don't re-derive what you already know.** Within a session, do not re-read
files you have read or restate decisions already made.

## If CI stops running entirely

Jobs that complete in a few seconds with no downloadable log are jobs that
never started. GitHub bills Actions minutes only for **private** repositories,
so if public repos still run and private ones die instantly, it is a spending
limit, not an outage. It is under Settings → Billing and licensing → there is
no page called "Actions" any more; the cap lives under budgets.

## Before starting a piece of work

- `main` may have moved under you — fetch before branching.
- More than one Claude session may be working this repo. Check for collisions
  (branch names, open PRs) before assuming you are alone.
- After any auto-merge or "Update branch", re-run the checks against the
  **current head**. A line-based merge can concatenate two independently-valid
  edits into invalid code without reporting a conflict; that has happened twice
  in these repositories.
