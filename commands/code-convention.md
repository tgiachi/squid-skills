---
description: Generate or refresh CODE_CONVENTION.md for any current project, merging Squid's portable baseline with project-specific architecture discovered from the repository.
argument-hint: "[optional: extra notes or focus area]"
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
---

# /squid:code-convention

Generate or refresh a `CODE_CONVENTION.md` at the **root of the current project**. The document must be strict, project-specific, and portable across different languages and repository shapes.

## Goal

Produce an authoritative, project-specific code convention document that combines:
1. **Squid's portable baseline** (the default rules below, adapted to the language)
2. **Language/framework conventions** discovered from the repo
3. **Project-specific architecture rules** discovered from local files

The output must be enforceable in review and free of generic filler. Never assume a specific codebase beyond the repository being inspected.

## Step 1 — Discover the project

Inspect the current working directory to determine:

- **Primary language(s)**: look for `*.csproj` / `*.slnx` / `*.sln` (C#), `package.json` (TS/JS), `pyproject.toml` / `requirements.txt` (Python), `go.mod` (Go), `Cargo.toml` (Rust), etc.
- **Project name**: from solution/project/package metadata, repository folder name, or README.
- **Project layout**: `src/`, `app/`, `lib/`, `tests/`, `docs/`, packages/workspaces, plugins, adapters, hosts, scripts.
- **Local guidance**: root `AGENTS.md`, `CLAUDE.md`, `README.md`, existing `CODE_CONVENTION.md`, `.editorconfig`, formatter/linter configs, build files, and CI.
- **Architecture hints**: project references, package/workspace boundaries, namespace/module prefixes, adapters, plugin contracts, renderer/backend layers, data/migration folders.
- **Test layout**: test project/package names, folder conventions, fixtures, integration/contract/performance separation.

If the project has no local guidance and no obvious architecture, still generate a useful baseline from the detected language and layout. Ask 2–3 short questions only when writing strict architecture rules would require guessing.

## Step 2 — Portable Squid baseline

Always include these rules where applicable. Adapt by language; do not translate C#-only rules literally into other ecosystems.

**Architecture**
- KISS: no abstraction without a second concrete use case.
- Prefer local project patterns over new abstractions.
- Keep dependencies flowing inward or downward according to the discovered architecture.
- Do not introduce framework-specific references into core/domain layers unless the project already allows it.

**C# code conventions (when applicable)**
- Enums always under `Types` namespace, with domain prefix in the name (e.g. `TcpServerType` in `<Project>.Types.Tcp`).
- DTOs always under `Data`. Internal-only DTOs under `Data.Internal.<Subdomain>`.
- Interfaces always under `Interfaces` namespace; one `I<Name>` per file.
- One primary type per `.cs` file (one class OR one record OR one enum OR one interface).
- File name matches type name. File-scoped namespaces.
- Inside a class: `const` → `private readonly` (prefixed `_`) → non-readonly fields → properties → constructor(s) → public methods → protected → private → `Dispose`/`DisposeAsync` always last.
- **No primary constructors.**
- **No expression-bodied constructors** (`public X(...) => ...`); constructors must always have a body `{ }`.
- Always use `""` instead of `string.Empty`.

**Tests (C#)**
- Path: `tests/<Project>/<Domain>/<Subdomain>/<Subject>Tests.cs`
- Namespace mirrors folder path.
- One test class per subject. File name `<Subject>Tests.cs`.
- No test files in the test project root — every test under a domain folder.
- Integration / contract / performance tests separated (`Integration/`, `Contract/`, `Performance/`).
- Shared fakes/builders/helpers in `TestSupport/` or `Support/`, never mixed with domain tests.
- AAA pattern with explicit `// Arrange / // Act / // Assert` comments.
- Test method naming: `Method_Scenario_ExpectedResult`.

**Commits**
- Conventional Commits.
- Title and body in **English**.
- Never add `Co-Authored-By: Claude`.

**Documentation**
- Plan/design docs go to `~/docs/<ProjectName>/`. They must never be committed inside the repo.
- Plan docs under `docs/plans/` or `docs/superpowers/` must never be committed.

**Other languages**
- TypeScript/JavaScript: follow existing formatter/linter config, keep public types close to module boundaries, avoid broad `any`, and keep side effects out of shared utility modules.
- Python: follow existing formatter/linter config, keep package boundaries explicit, type public APIs where the project already uses typing, and keep scripts separate from importable modules.
- Go: keep packages small and named by responsibility, avoid circular dependencies, keep generated files marked, and put integration tests behind explicit build tags or folders when the repo does so.
- Rust: keep crate boundaries explicit, avoid broad prelude exports unless already established, and keep integration tests under `tests/` unless the repo uses module-local tests.
- Shell/config/dotfiles: prefer small executable scripts, clear ownership by directory, and idempotent operations.

## Step 3 — Project-specific sections (derived from discovery)

After the baseline, add sections that capture *this project*'s reality. Inspect local guidance and the source tree, then add only sections justified by files in the repo. Examples:

- **Layering / dependency direction** — actual modules/packages/projects and allowed references.
- **Core/domain boundary rule** — what core/domain/shared modules must not reference.
- **Adapter / backend rules** — where backend SDKs are allowed.
- **Plugin system** — plugin id format, host glue, hot-reload constraints.
- **Scripting host** — host vs bridge packages, module bindings, sandboxing.
- **Event bus / messaging** — marker interfaces, subscriber registration.
- **Logging / telemetry** — project-approved logger, static templates, tracing boundaries.
- **Workers / hosted services** — graceful degradation and shutdown rules.
- **Frontend architecture** — component placement, route/layout boundaries, state ownership, API clients.
- **Database / migrations** — migration ownership, naming, rollback expectations, generated files.
- **Documentation discipline** — public API docs, generated docs, uncommitted planning docs.
- **Mandatory namespace/module buckets** — table of allowed top-level namespaces, packages, or directories.

Do not invent constraints the project does not enforce. If local files say nothing about a topic, omit that section or mark it as an open decision only when it blocks review.

## Step 4 — Write the file

- Output path: `<repo-root>/CODE_CONVENTION.md`.
- If the file exists, refresh it carefully: preserve user-added project-specific rules unless they conflict with current repo evidence.
- If no file exists, create a new document with numbered sections, short paragraphs, fenced examples, and tables where useful.
- Keep it strict and concrete. Every rule should be enforceable on review.
- Close with "Non-Negotiable Hygiene" and "Additional Conventions" sections adapted to the detected language.

## Step 5 — Report

After writing, print:
- Path of the file.
- Section count.
- Project-specific sections added and the local files that justified them.
- Any rule deliberately omitted because it was language-specific or not evidenced by the repo.
- A short "Review checklist" the user can run through before committing the doc.

## Extra notes from invocation

`$ARGUMENTS`

If `$ARGUMENTS` is non-empty, treat it as additional emphasis or focus areas the user wants the document to cover (e.g. "extra section on protocol versioning", "skip Lua section, not used here").
