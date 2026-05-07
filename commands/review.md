---
description: Review the current changes or requested scope for bugs, regressions, missing tests, and maintainability risks.
argument-hint: "[optional: files, branch, PR range, or focus area]"
allowed-tools: Read, Glob, Grep, Bash
---

# /squid:review

Perform a code review. Prioritize concrete findings over summaries.

## Scope

- If `$ARGUMENTS` names files, folders, commits, or a branch/range, review that scope.
- Otherwise review the current worktree: staged, unstaged, and relevant untracked files.
- If the worktree is clean and no scope is provided, compare the current branch against its upstream or the default base branch (`main`, `master`, then `develop`).

## Workflow

1. Inspect:
   - `git status --short`
   - `git diff`
   - `git diff --cached`
   - branch/base information when reviewing a branch
2. Read the changed files and nearby code needed to understand behavior.
3. Look for:
   - correctness bugs and edge cases
   - regressions in public behavior or contracts
   - missing or weak tests
   - security risks, data loss, unsafe filesystem/network behavior
   - concurrency, lifecycle, resource cleanup, and error-handling issues
   - maintainability problems only when they create real risk
4. Do not edit files. This command is review-only.

## Output

Lead with findings, ordered by severity.

Use this format:

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

If there are no findings, say that clearly and still mention residual risk or test gaps.

## Rules

- Use exact file and line references whenever possible.
- Do not report style preferences unless they can cause a bug or review burden.
- Do not speculate. Mark assumptions explicitly.
- Keep the summary short; the findings are the main output.

## Extra notes from invocation

`$ARGUMENTS`
