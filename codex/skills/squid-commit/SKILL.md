---
name: squid-commit
description: Create Squid-style git commits. Use when the user says /vai, asks to run the squid commit shortcut, or asks Codex to stage safe changes and create an English Conventional Commit from the current worktree.
---

# Squid Commit

Create one Conventional Commit in English from the current worktree.

## Hard Rules

- Never add `Co-Authored-By`, `Signed-off-by`, or AI/vendor trailers.
- Never push, amend, rebase, squash, or bypass hooks.
- Write the commit title and body in English, regardless of the conversation language.
- Use Conventional Commit types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, `style`, `build`, `ci`.
- Keep the title under about 70 characters. Add a scope only when it improves clarity.
- Respect Codex worktree rules: do not revert user changes, and do not include clearly unrelated pre-existing work unless the user explicitly requested a full worktree commit.

## Workflow

1. Inspect in parallel:
   - `git status --short`
   - `git diff`
   - `git diff --cached`
   - `git log --oneline -n 15`
2. If there is nothing to commit, say that and stop.
3. Identify files that belong to the requested commit.
   - For explicit `/vai` with no narrower scope, treat all non-secret changes as intended unless they are clearly unrelated generated clutter.
   - Never stage likely secrets: `.env`, `.env.*`, `credentials*`, `*.pem`, `*.key`, `id_rsa*`, files containing `secret` or `token` in the name unless they are obvious test fixtures.
4. Read enough of changed files to understand intent. Do not rely only on filenames.
5. Stage files explicitly by path. Include untracked files that belong to the commit.
6. Compose:

   ```text
   <type>(<scope>): <imperative summary>

   - <change 1>
   - <change 2>
   - <change 3>
   ```

   If multiple intents are present, use the dominant type in the title and prefix bullets with their own type when useful.

7. Commit using a heredoc-style command so formatting is preserved.
8. Run `git status --short` and `git log -1 --stat`.
9. Report commit SHA/title, committed files, and any skipped files with reasons.

## Hook Failure

If a hook fails, fix the underlying issue when it is clearly in scope, re-stage, and make the commit. If the fix is unclear or the hook keeps failing, stop and report the exact failure. Do not use `--no-verify`.
