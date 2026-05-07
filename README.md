# squid-skills

Personal automation pack for Claude Code and Codex.

Repository:

```text
https://github.com/tgiachi/squid-skills
```

This repository keeps the same workflows available in both tools:

- Claude Code reads the local plugin in `.claude-plugin/` and exposes slash commands from `commands/`.
- Codex reads the skill folders in `codex/skills/`.

The goal is to keep project hygiene repeatable across repositories: commits, code reviews, test repair, PR descriptions, interface documentation, and project-specific coding conventions.

## Skills

| Claude Code | Codex skill | Use it when | What it does |
|---|---|---|---|
| `/squid:cc` | `squid-commit` | You want the current worktree committed. | Stages safe intended changes and creates one English Conventional Commit. It never pushes, amends, bypasses hooks, or adds AI co-author trailers. |
| `/squid:review` | `squid-review` | You want a serious review before merging or committing. | Reviews the requested scope or current changes for bugs, regressions, missing tests, security risks, lifecycle issues, and maintainability risks. Output starts with findings and file references. |
| `/squid:test-fix` | `squid-test-fix` | Build or tests fail and you want the smallest real fix. | Detects the project-native verification command, runs it, fixes failures in scope, and repeats until passing, blocked, or an unrelated failure remains. |
| `/squid:pr` | `squid-pr` | You need a pull request body. | Reads branch status, commits, diff, and test evidence, then writes a concise PR description with summary, changes, tests, risks, and notes. It does not create or push the PR. |
| `/squid:code-convention` | `squid-code-convention` | A repo needs a `CODE_CONVENTION.md`. | Generates or refreshes project-specific conventions from detected language, layout, local guidance, tooling, test structure, and architecture. It uses Squid's portable baseline without assuming a specific project. |
| `/squid:c` | `squid-interface-docs` | C# interfaces need XML docs. | Adds concise `///` XML documentation to interfaces and interface members only. It skips generated files and preserves existing useful docs. |

## Behavior

These workflows are intentionally conservative:

- They prefer local repository patterns over new abstractions.
- They keep edits scoped to the requested task.
- They do not invent test runs, issue links, reviewers, or architecture rules.
- They avoid AI/vendor commit trailers.
- They separate read-only commands (`review`, `pr`) from editing commands (`test-fix`, `code-convention`, `c`).

## Install Claude Code

Clone the repository first:

```bash
mkdir -p ~/projects
git clone https://github.com/tgiachi/squid-skills.git ~/projects/squid-skills
```

This repository is both the local marketplace and the plugin itself. Point Claude Code at the cloned checkout.

From inside Claude Code:

```text
/plugin marketplace add ~/projects/squid-skills
/plugin install squid@squid-skill
```

To update later:

```bash
git -C ~/projects/squid-skills pull --ff-only
```

Then refresh Claude Code:

```text
/plugin marketplace update squid-skill
/plugin install squid@squid-skill
```

Claude Code commands accept a free-form `$ARGUMENTS` string for extra context, such as a path, branch, test command, or focus area.

## Install Codex

Codex loads skills from `$CODEX_HOME/skills`, usually `~/.codex/skills`.

Clone the repository if you have not already done so:

```bash
mkdir -p ~/projects
git clone https://github.com/tgiachi/squid-skills.git ~/projects/squid-skills
```

Install with symlinks so updates to the checkout are picked up by new Codex sessions:

```bash
mkdir -p ~/.codex/skills
ln -sfn ~/projects/squid-skills/codex/skills/squid-commit ~/.codex/skills/squid-commit
ln -sfn ~/projects/squid-skills/codex/skills/squid-review ~/.codex/skills/squid-review
ln -sfn ~/projects/squid-skills/codex/skills/squid-test-fix ~/.codex/skills/squid-test-fix
ln -sfn ~/projects/squid-skills/codex/skills/squid-pr ~/.codex/skills/squid-pr
ln -sfn ~/projects/squid-skills/codex/skills/squid-code-convention ~/.codex/skills/squid-code-convention
ln -sfn ~/projects/squid-skills/codex/skills/squid-interface-docs ~/.codex/skills/squid-interface-docs
```

If a real directory already exists at one of those paths, remove or rename it before creating the symlink.

To update later:

```bash
git -C ~/projects/squid-skills pull --ff-only
```

Codex skills can be triggered by name, for example `$squid-commit`, or by a matching request such as `/vai`, "review this diff", or "make the tests pass".

## Repository Layout

```text
squid-skills/
|-- .claude-plugin/
|   |-- marketplace.json
|   `-- plugin.json
|-- commands/
|   |-- c.md
|   |-- cc.md
|   |-- code-convention.md
|   |-- pr.md
|   |-- review.md
|   `-- test-fix.md
`-- codex/
    `-- skills/
        |-- squid-code-convention/
        |   |-- SKILL.md
        |   `-- agents/openai.yaml
        |-- squid-commit/
        |   |-- SKILL.md
        |   `-- agents/openai.yaml
        |-- squid-interface-docs/
        |   |-- SKILL.md
        |   `-- agents/openai.yaml
        |-- squid-pr/
        |   |-- SKILL.md
        |   `-- agents/openai.yaml
        |-- squid-review/
        |   |-- SKILL.md
        |   `-- agents/openai.yaml
        `-- squid-test-fix/
            |-- SKILL.md
            `-- agents/openai.yaml
```

## Maintenance

When adding a workflow, add both sides:

1. A Claude Code command under `commands/<name>.md`.
2. A Codex skill under `codex/skills/squid-<name>/SKILL.md`.
3. Codex UI metadata under `codex/skills/squid-<name>/agents/openai.yaml`.
4. A row in the skills table above.

Validate Codex skills with:

```bash
for d in codex/skills/squid-*; do
  python ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py "$d"
done
```
