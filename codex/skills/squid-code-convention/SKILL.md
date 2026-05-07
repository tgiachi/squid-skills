---
name: squid-code-convention
description: Generate or refresh CODE_CONVENTION.md for any project using Squid's portable baseline, detected language conventions, and repository-specific architecture. Use when the user asks for code conventions, CODE_CONVENTION.md, or Squid's /code-convention shortcut.
---

# Squid Code Convention

Generate or refresh `<repo-root>/CODE_CONVENTION.md` with strict, project-specific rules. Never assume a specific codebase beyond the repository being inspected.

## Discovery

Inspect the current repository before writing:

- Primary languages and frameworks: `*.csproj`, `*.sln`, `*.slnx`, `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`.
- Project name from solution/project/package metadata, repository folder name, or README.
- Layout: `src/`, `app/`, `lib/`, `tests/`, `docs/`, packages/workspaces, plugins, adapters, hosts, renderers, scripts.
- Local guidance: root `AGENTS.md`, `CLAUDE.md`, `README.md`, existing `CODE_CONVENTION.md`, `.editorconfig`, formatter/linter configs, build files, and CI.
- Architecture hints: project references, package/workspace boundaries, namespace/module prefixes, adapters, plugin contracts, renderer/backend layers, data/migration folders.
- Test layout: test project/package names, folder conventions, fixtures, integration/contract/performance separation.
- If architecture is not discoverable, generate a useful language/layout baseline and ask questions only when strict architecture rules would require guessing.

## Portable Squid Baseline

Always include these where applicable, adapted by language:

- KISS: no abstraction without a second concrete use case.
- Prefer local project patterns over new abstractions.
- Keep dependencies flowing inward or downward according to the discovered architecture.
- Do not introduce framework-specific references into core/domain layers unless the project already allows it.
- Conventional Commits, title/body in English, no co-author trailers.
- Plan/design docs go under `~/docs/<ProjectName>/`, not inside the repo, unless the user explicitly asks otherwise.

For C# projects:

- Enums live under `Types` namespaces and use a domain prefix.
- DTOs live under `Data`; internal-only DTOs under `Data.Internal.<Subdomain>`.
- Interfaces live under `Interfaces`; one `I<Name>` per file.
- One primary type per `.cs` file; filename matches type; use file-scoped namespaces.
- Class order: `const`, private readonly `_` fields, non-readonly fields, properties, constructors, public methods, protected methods, private methods, `Dispose`/`DisposeAsync` last.
- No primary constructors.
- No expression-bodied constructors.
- Use `""` instead of `string.Empty`.

For C# tests:

- Path: `tests/<Project>/<Domain>/<Subdomain>/<Subject>Tests.cs`.
- Namespace mirrors folder path.
- One test class per subject.
- Keep integration, contract, and performance tests separated.
- Put shared fakes/builders/helpers in `TestSupport/` or `Support/`.
- Use AAA with explicit `// Arrange`, `// Act`, `// Assert`.
- Test names: `Method_Scenario_ExpectedResult`.

For other languages:

- TypeScript/JavaScript: follow existing formatter/linter config, keep public types close to module boundaries, avoid broad `any`, and keep side effects out of shared utility modules.
- Python: follow existing formatter/linter config, keep package boundaries explicit, type public APIs where the project already uses typing, and keep scripts separate from importable modules.
- Go: keep packages small and named by responsibility, avoid circular dependencies, keep generated files marked, and put integration tests behind explicit build tags or folders when the repo does so.
- Rust: keep crate boundaries explicit, avoid broad prelude exports unless already established, and keep integration tests under `tests/` unless the repo uses module-local tests.
- Shell/config/dotfiles: prefer small executable scripts, clear ownership by directory, and idempotent operations.

## Project-Specific Sections

Add only sections justified by files in the repo. Examples:

- Layering and dependency direction.
- Core/domain/shared boundary rules.
- Adapter/backend SDK placement.
- Plugin system.
- Scripting host and bridge projects.
- Event bus and subscribers.
- Logging and telemetry conventions.
- Workers/hosted services and graceful degradation.
- Frontend architecture and state ownership.
- Database/migrations ownership.
- Public API documentation discipline.
- Mandatory namespace/module buckets.

Do not invent constraints. Cite the local evidence in the final report by file path. If local files say nothing about a topic, omit that section or mark it as an open decision only when it blocks review.

## Output Style

- Write strict, enforceable rules, not generic advice.
- Use numbered sections, compact paragraphs, fenced examples, and tables where helpful.
- Preserve user-added project-specific sections when refreshing an existing file unless they conflict with discovered architecture.
- End with "Non-Negotiable Hygiene" and "Additional Conventions" sections.

## Report

After writing, report:

- Path written.
- Section count.
- Project-specific sections added and the files that justified them.
- Rules deliberately omitted because they were language-specific or not evidenced by the repo.
- Short review checklist before commit.
