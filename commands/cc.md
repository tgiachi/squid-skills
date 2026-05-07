---
description: Stage every change (including untracked) and create a Conventional Commit in English without confirmation. Equivalent of /vai.
argument-hint: "[optional: extra context to include in the commit body]"
allowed-tools: Bash, Read
---

# /squid:cc

Replicates squid's `/vai` shortcut: stage everything (modified + untracked), read what changed, and create a single Conventional Commit in English. **Do not ask for confirmation** — just commit.

## Hard rules

- **NEVER** add `Co-Authored-By: Claude` (or any other Claude/Anthropic co-author trailer).
- Commit title and body **must be in English**, regardless of the language of the conversation.
- Use **Conventional Commits**: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`, `perf:`, `style:`, `build:`, `ci:`. Add a scope when it improves clarity (e.g. `feat(engine):`, `fix(plugins):`).
- Title under ~70 chars. Body explains the WHY and lists each distinct change as a bullet.
- Never bypass hooks (`--no-verify`) and never sign-off automatically unless the project already does it.
- Never push. Never amend. Just commit.

## Workflow

1. Run in parallel:
   - `git status` (no `-uall`)
   - `git diff` (working tree, staged + unstaged)
   - `git log --oneline -n 15` (style match)
2. If there is **nothing** to commit and **no** untracked files: report "nothing to commit" and stop.
3. Stage everything that should be committed:
   - Add modified/deleted files explicitly.
   - Add untracked files explicitly by path.
   - **Never** stage files that look like secrets (`.env`, `.env.*`, `credentials*`, `*.pem`, `*.key`, `id_rsa*`, anything matching `*secret*` / `*token*` outside of code). If such a file is the only change, abort and warn the user.
4. Read each modified file briefly to understand the actual change (don't trust the diff alone for renames or large rewrites).
5. Group the changes by intent (feat / fix / refactor / test / docs / chore). Pick the **dominant** intent for the title; list the others in the body.
6. Compose the message:

   ```
   <type>(<scope>): <imperative summary>

   - <change 1>
   - <change 2>
   - <change 3>
   ```

   - One bullet per logically distinct change. Do not collapse multiple fixes into one bullet.
   - If multiple types coexist, use the dominant type in the title and prefix bullets in the body with their own type:
     ```
     feat(engine): add scene manager lifecycle events

     - feat(engine): add SceneStateType enum under Engine.Types
     - fix(core): null check in AssetService.LoadAsync
     - refactor(engine): split SceneManager.Update into Update/Render
     - test(engine): cover scene transition cancellation
     ```
7. Commit using a HEREDOC to preserve formatting:

   ```bash
   git commit -m "$(cat <<'EOF'
   <type>(<scope>): <summary>

   - <bullet>
   - <bullet>
   EOF
   )"
   ```

8. After commit, run `git status` and `git log -1 --stat` and report:
   - Commit SHA + title
   - Files committed (count + names)
   - Any file deliberately skipped (and why)

## If pre-commit hook fails

Do **not** `--amend`. Fix the underlying issue, re-stage, and create a **new** commit. If the hook keeps failing, stop and report the error to the user instead of bypassing it.

## Extra context

`$ARGUMENTS`

If `$ARGUMENTS` is non-empty, weave it into the body as additional context (e.g. issue refs, motivation, related decisions). Do not put it in the title unless it is a concise scope hint.
