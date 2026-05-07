# squid-skill

Personal automation pack for squid, available for both Claude Code and Codex.

- Claude Code uses the `.claude-plugin/` manifest and the slash commands in `commands/`.
- Codex uses the skill folders in `codex/skills/`.

## Features

| Claude Code | Codex skill | Purpose |
|---|---|
| `/squid:code-convention` | `squid-code-convention` | Generate or refresh `CODE_CONVENTION.md` at the root of any project, merging Squid's portable baseline with language conventions and architecture discovered from repo context. |
| `/squid:cc` | `squid-commit` | Create a Conventional Commit in English. Equivalent of squid's `/vai`. Never adds AI co-author trailers. |
| `/squid:c` | `squid-interface-docs` | Add `///` XML doc comments to every C# interface and its members in the current project. Equivalent of squid's `/comment`. |
| `/squid:review` | `squid-review` | Review current changes or a requested scope for bugs, regressions, missing tests, and real maintainability risks. |
| `/squid:test-fix` | `squid-test-fix` | Run the relevant build/test command, fix failures in scope, and repeat until passing or blocked. |
| `/squid:pr` | `squid-pr` | Generate an accurate pull request description from the current branch, diff, commits, and test evidence. |

Claude Code commands accept a free-form `$ARGUMENTS` string for extra context. Codex skills are triggered by their descriptions, by names such as `$squid-commit`, or by the matching shortcut text such as `/vai`.

## Install Claude Code

This repo is both a **local marketplace** and the **plugin** itself. From inside Claude Code:

```
/plugin marketplace add /home/squid/projects/personal/squid-skill
/plugin install squid@squid-skill
```

To update later:

```
/plugin marketplace update squid-skill
/plugin install squid@squid-skill   # reinstall picks up the new version
```

## Install Codex

Codex loads skills from `$CODEX_HOME/skills`, usually `~/.codex/skills`. Install from this repo with symlinks so edits here are picked up by new Codex sessions:

```bash
mkdir -p ~/.codex/skills
ln -sfn /home/squid/projects/personal/squid-skill/codex/skills/squid-commit ~/.codex/skills/squid-commit
ln -sfn /home/squid/projects/personal/squid-skill/codex/skills/squid-interface-docs ~/.codex/skills/squid-interface-docs
ln -sfn /home/squid/projects/personal/squid-skill/codex/skills/squid-code-convention ~/.codex/skills/squid-code-convention
ln -sfn /home/squid/projects/personal/squid-skill/codex/skills/squid-review ~/.codex/skills/squid-review
ln -sfn /home/squid/projects/personal/squid-skill/codex/skills/squid-test-fix ~/.codex/skills/squid-test-fix
ln -sfn /home/squid/projects/personal/squid-skill/codex/skills/squid-pr ~/.codex/skills/squid-pr
```

If a real directory already exists at one of those paths, remove or rename it first, then create the symlink.

## Layout

```
squid-skill/
├── .claude-plugin/
│   ├── marketplace.json       # marketplace manifest (lists the "squid" plugin)
│   └── plugin.json            # plugin manifest
├── commands/
│   ├── code-convention.md     → /squid:code-convention
│   ├── cc.md                  → /squid:cc
│   ├── c.md                   → /squid:c
│   ├── pr.md                  → /squid:pr
│   ├── review.md              → /squid:review
│   └── test-fix.md            → /squid:test-fix
└── codex/
    └── skills/
        ├── squid-code-convention/
        │   ├── SKILL.md
        │   └── agents/openai.yaml
        ├── squid-commit/
        │   ├── SKILL.md
        │   └── agents/openai.yaml
        ├── squid-interface-docs/
        │   ├── SKILL.md
        │   └── agents/openai.yaml
        ├── squid-pr/
        │   ├── SKILL.md
        │   └── agents/openai.yaml
        ├── squid-review/
        │   ├── SKILL.md
        │   └── agents/openai.yaml
        └── squid-test-fix/
            ├── SKILL.md
            └── agents/openai.yaml
```
