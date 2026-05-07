---
description: Generate a concise pull request description from the current branch, diff, commits, and test evidence.
argument-hint: "[optional: base branch, issue id, release note, or reviewer focus]"
allowed-tools: Read, Glob, Grep, Bash
---

# /squid:pr

Generate a pull request description. Do not edit files and do not create the PR unless explicitly asked.

## Scope

- If `$ARGUMENTS` names a base branch or range, use it.
- Otherwise infer the base branch from upstream, then try `main`, `master`, and `develop`.
- Include current uncommitted changes if present; otherwise use commits/diff since the base branch.

## Workflow

1. Inspect:
   - `git status --short`
   - current branch and upstream
   - `git log --oneline` for the branch/range
   - `git diff --stat` and focused diffs against the base/range
2. Read changed files when the diff alone is not enough to explain behavior.
3. Identify:
   - user-facing change
   - internal implementation change
   - tests or verification evidence
   - migrations/config changes
   - breaking changes or compatibility risks
4. Produce a PR body only. Do not stage, commit, push, or call a hosting API unless explicitly requested.

## Output

Use this structure:

```markdown
## Summary
- ...

## Changes
- ...

## Tests
- ...

## Risks
- ...

## Notes
- ...
```

Omit empty sections except `Summary` and `Tests`. If tests were not run, write `Not run` with the reason.

## Rules

- Keep it accurate to the diff. Do not invent issue links, reviewers, benchmarks, or test runs.
- Mention breaking changes clearly.
- Prefer concise bullets over narrative.

## Extra notes from invocation

`$ARGUMENTS`
