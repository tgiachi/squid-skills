---
name: squid-test-fix
description: Run relevant build or test commands, fix failures in scope, and repeat until passing or blocked. Use when the user asks to fix tests, run /test-fix, make the build pass, or repair failing verification.
---

# Squid Test Fix

Run verification, fix failures, and repeat until the relevant command passes or there is a clear blocker.

## Scope

- If the user provides a command, run that command.
- If the user names a path or failure area, focus on that path.
- Otherwise infer the smallest useful verification command from the repository.

## Command Selection

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
5. Re-run the same command. Repeat until passing, blocked, or an unrelated failure remains.

## Editing Rules

- Keep edits tightly scoped to the failing behavior.
- Do not reformat unrelated files.
- Do not delete tests to make the suite pass.
- Do not commit.
- If generated files are required, run the existing generator rather than hand-editing generated output.

## Report

Report commands run, failures fixed, files changed, final status, and any skipped command with the reason.
