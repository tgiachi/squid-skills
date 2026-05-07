---
name: squid-pr
description: Generate a concise pull request description from the current branch, diff, commits, and test evidence. Use when the user asks for a PR description, pull request body, /pr, or Squid's /squid:pr shortcut.
---

# Squid PR

Generate a pull request description. Do not edit files, stage, commit, push, or create the PR unless explicitly asked.

## Scope

- If the user names a base branch or range, use it.
- Otherwise infer the base branch from upstream, then try `main`, `master`, and `develop`.
- Include current uncommitted changes if present; otherwise use commits/diff since the base branch.

## Workflow

1. Inspect in parallel where possible:
   - `git status --short`
   - current branch and upstream
   - `git log --oneline` for the branch/range
   - `git diff --stat` and focused diffs against the base/range
2. Read changed files when the diff alone is not enough to explain behavior.
3. Identify user-facing changes, implementation changes, tests, migrations/config changes, breaking changes, and compatibility risks.
4. Produce a PR body only.

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

- Keep it accurate to the diff.
- Do not invent issue links, reviewers, benchmarks, or test runs.
- Mention breaking changes clearly.
- Prefer concise bullets over narrative.
