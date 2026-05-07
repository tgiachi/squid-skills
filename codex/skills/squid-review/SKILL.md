---
name: squid-review
description: Review code changes for bugs, regressions, missing tests, and maintainability risks. Use when the user asks for a review, code review, /review, or Squid's /squid:review shortcut.
---

# Squid Review

Perform a code review. This skill is review-only: do not edit files unless the user explicitly asks for fixes after the review.

## Scope

- If the user names files, folders, commits, or a branch/range, review that scope.
- Otherwise review the current worktree: staged, unstaged, and relevant untracked files.
- If the worktree is clean and no scope is provided, compare the current branch against upstream or the default base branch (`main`, `master`, then `develop`).

## Workflow

1. Inspect in parallel where possible:
   - `git status --short`
   - `git diff`
   - `git diff --cached`
   - branch/base information when reviewing a branch
2. Read changed files and nearby code needed to understand behavior.
3. Look for correctness bugs, regressions, missing tests, security risks, data loss, concurrency/lifecycle/resource issues, and maintainability problems that create real risk.
4. Avoid style-only findings unless they create review burden or likely defects.

## Output

Lead with findings, ordered by severity:

```text
Findings
- High: <issue> (<file>:<line>)
  Why: <impact>
  Fix: <specific suggested fix>

Open questions
- <only if needed>

Test gaps
- <missing verification or "None found">
```

If there are no findings, say that clearly and mention residual risk or test gaps.

## Rules

- Use exact file and line references whenever possible.
- Do not speculate. Mark assumptions explicitly.
- Keep summaries brief; findings are the main output.
