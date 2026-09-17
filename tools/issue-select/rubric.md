# Rubric: is this a good first issue?

All recency thresholds are measured against the bundle's capture date in
eval mode, and against today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| repo-alive | Repo facts: the `archived:` flag, "last 5 default-branch commits" (dates and authors), "last push to any branch", "latest release" | Fail if `archived: yes`. Otherwise pass only if at least one of the last 5 default-branch commits is human activity dated within 180 days of the capture date. Human activity = a commit authored by a non-bot account, OR a bot merging a pull request whose branch belongs to a human (e.g. `kubernetes-prow[bot]: Merge pull request #N from someuser/...`). Commits by `[bot]` accounts or `*-bot` accounts that only merge other bots' PRs (dependabot, auto-merge, minikube-bot) do not count. A missing release does not fail this check; "last push to any branch" alone does not pass it. | required |
| unclaimed | Repo facts "this issue: assignees" and "linked PRs"; every PR number mentioned in the Comments section; claim comments in the thread ("I'll take this", "working on this", "/assign", "opened PR #N") | Pass only if ALL hold: (a) assignees is none; (b) no linked PR is `open`; (c) no PR mentioned in the thread as implementing this issue, unless the thread states that PR was closed or rejected (a mentioned PR with unknown state counts as open); (d) no claim comment dated within 90 days of the capture date. A claim older than 90 days with no open PR is stale and does not block, especially when a maintainer later invites takers. Closed unmerged linked PRs are not claims (see bounded-scope). Bot reminder comments do not clear an assignee or an open PR. | required |
| bounded-scope | Issue body, labels, opener's author_association, the comment thread, and closed linked PRs | Pass only if the issue asks for one concrete change whose expected result is stated (a bug with expected vs actual, a regression back to prior behavior, or a docs/cleanup task with named files or pages). Fail if ANY: (1) it is an umbrella, tracking, or "megaissue" list, or invites many PRs across the codebase; (2) it proposes a NEW feature, option, flag, or UI element and no OWNER/MEMBER/COLLABORATOR has opened or endorsed it with a settled design; (3) the thread shows design still being debated (competing proposals, "confirm the design first") with no maintainer decision; (4) 2 or more closed unmerged PRs have already attempted it; (5) it is a usage/support question. A short body or a missing good-first-issue label does not fail this check. | required |
| ai-policy-ok | Repo facts "contribution policy" line | Fail only if the policy is an outright ban on AI-generated/AI-assisted code or docs ("we do not accept AI-generated code"). Conditions (disclose, understand, test, review, "fully AI-generated not accepted but assistive use allowed", "discouraged") and silence both pass. | required |
| maintainer-responsive | Repo facts "maintainer first-response sample"; OWNER/MEMBER/COLLABORATOR comments in this issue's thread | At least one sampled issue opened within 180 days of capture got a maintainer first response within 14 days, OR a maintainer commented in this thread within 90 days of capture. | preferred |
| newcomer-label | Issue labels | Labeled `good first issue`, `easy`, or `help wanted`. | preferred |

## Verdict rule

Accept if and only if every required check (repo-alive, unclaimed,
bounded-scope, ai-policy-ok) passes. Any required `fail` rejects.
`unclear` on a required check counts as `fail`, with one exception:
`ai-policy-ok` is `pass` when the bundle says nothing about AI (silence
passes). Preferred checks (maintainer-responsive, newcomer-label) never
change the verdict; they only rank accepted issues.
