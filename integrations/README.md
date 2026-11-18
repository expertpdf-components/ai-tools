# Integrations — Cursor, GitHub Copilot, Codex

The ExpertPdf knowledge in this repo is authored as a **Claude Code Agent Skill** under
[`plugins/expertpdf/skills/`](../plugins/expertpdf/skills/). Its `reference/*.md` files are the
**single source of truth** (plain, tool-agnostic Markdown, every sample in C# and VB.NET).

This folder adapts that knowledge for other AI coding assistants. Each file is a **condensed,
self-contained** guide (install, license key, quickstart, mental model, routing, gotchas) that **links to the
full reference files on GitHub** for depth — so there is one place to maintain the deep content.

One guide, mirrored across every tool:

| File stem | Library | Package |
|---|---|---|
| `expertpdf-anywhere-net` | ExpertPdf Anywhere, cross-platform (Windows, Linux, macOS on Apple Silicon, Docker, remote engine) | `ExpertPdf.Anywhere` + `ExpertPdf.Anywhere.Native.<rid>`, or `ExpertPdf.Anywhere.<Platform>` |

Install it in projects that reference ExpertPdf Anywhere, or that move to it from the classic ExpertPdf products.

## Cursor — `cursor/*.mdc`

Cursor [project rules](https://docs.cursor.com/context/rules). Copy the `.mdc` file into your project's
`.cursor/rules/` folder:

```text
your-project/.cursor/rules/expertpdf-anywhere-net.mdc
```

It is authored as an **Agent Requested** rule (a `description`, no `globs`, `alwaysApply: false`), so Cursor pulls
it in when your request is about ExpertPdf. To always apply, set `alwaysApply: true`; to auto-attach on .NET
files, set `globs: **/*.cs,**/*.vb` (noisier).

## GitHub Copilot — `copilot/*.instructions.md`

Copilot [custom instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot).
Copy into your project's `.github/instructions/`:

```text
your-project/.github/instructions/expertpdf-anywhere-net.instructions.md
```

The `applyTo` frontmatter defaults to `**/*.cs,**/*.vb`. Narrow it to where you actually do PDF work
(e.g. `applyTo: "src/Pdf/**"`) to keep it out of unrelated context. Requires the instructions-files
setting enabled in your Copilot client.

## Codex — `codex/*.md`

OpenAI Codex reads [`AGENTS.md`](https://agents.md). Either paste the file's contents into your project's root
`AGENTS.md`, or keep the file in your repo and add one line to `AGENTS.md`:

```markdown
For ExpertPdf .NET work, follow ./docs/expertpdf-anywhere-net.md
```

## Keeping these in sync

The guides are generated from the skill's `SKILL.md`. When the library changes, update the `reference/*.md`
files and `SKILL.md` under `plugins/expertpdf/skills/` first, then regenerate the guides here. The reference
links always point at the canonical content, so depth never goes stale.
