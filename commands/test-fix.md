---
description: Run the relevant build/test command, fix failures in scope, and repeat until passing or blocked.
argument-hint: "[optional: test/build command, path, or failure focus]"
allowed-tools: Read, Edit, Write, Glob, Grep, Bash
---

# /squid:test-fix

Run verification, fix the failures, and repeat until the relevant command passes or there is a clear blocker.

## Scope

- If `$ARGUMENTS` contains a command, run that command.
- If `$ARGUMENTS` names a path or failure area, focus on that path.
- Otherwise infer the smallest useful verification command from the repository.

## Command selection

Prefer project-native commands already present in the repo:

- .NET: `dotnet test`, or `dotnet build` when tests are absent.
- Node: package-manager scripts from `package.json`, preferring `test`, then `lint`, then `build`.
- Python: `pytest`, `python -m pytest`, or configured test commands in `pyproject.toml`.
- Go: `go test ./...`.
- Rust: `cargo test`.
- Shell/dotfiles: existing test scripts, `shellcheck` if configured, then syntax checks for touched scripts.

Do not install dependencies or change package manager state unless the user explicitly asks.

## Workflow

1. Inspect repo layout, status, and available verification commands.
2. Run the selected command and capture the first meaningful failure.
3. Read the failing code, tests, and nearby implementation.
4. Fix the smallest real cause. Do not mask failures by weakening tests unless the test expectation is demonstrably wrong.
5. Re-run the same command. If a new failure appears, repeat.
6. Stop when the command passes, the remaining failure is unrelated to the requested scope, or a blocker requires user input.

## Editing rules

- Keep edits tightly scoped to the failing behavior.
- Do not reformat unrelated files.
- Do not delete tests to make the suite pass.
- Do not commit.
- If generated files are required, run the existing generator rather than hand-editing generated output.

## Output

Report:

- command(s) run
- failures fixed
- files changed
- final status: passing, blocked, or unrelated failure remains
- any skipped command and why

## Extra notes from invocation

`$ARGUMENTS`
