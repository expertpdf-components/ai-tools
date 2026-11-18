<p align="center">
  <a href="https://www.html-to-pdf.net">
    <img src="images/logos/expertpdf-icon.png" alt="ExpertPdf" height="96">
  </a>
</p>

<h1 align="center">ExpertPdf — AI Tools</h1>

<p align="center">
  Skills that teach AI coding assistants (Claude Code, and any agent that reads
  <a href="https://docs.claude.com/en/docs/claude-code/skills">Agent Skills</a>) how to use
  <a href="https://www.html-to-pdf.net">ExpertPdf</a> correctly — accurate APIs, real code, both C# and VB.NET.
</p>

---

## What's in here

This repository is the home for ExpertPdf AI tooling. It is packaged as a single Claude Code plugin,
**`expertpdf`**, exposed through a plugin marketplace.

### Plugin: `expertpdf`

| Skill | Covers | Package |
|---|---|---|
| **`expertpdf-anywhere-net`** | **ExpertPdf Anywhere**, the cross-platform ExpertPdf library for Windows, Linux (x64, ARM64), macOS on Apple Silicon and Docker, or anywhere through the remote engine container — HTML→PDF and HTML→image with Chromium (sync and async), headers and footers, bookmarks and tables of contents, HTML forms to PDF fields, Word, Excel, PowerPoint and RTF to PDF, PDF to Word, Excel and PowerPoint, PDF creation and editing, merge and split, form filling, redaction, page resizing, portfolios, encryption, digital signatures (PFX, certificate store, PKCS#11 tokens, external signers, long-term validation) and validation, PDF→text/image and image extraction, PDF/A-1a to PDF/A-4f, PDF/X-1a, tagged PDF/UA-1 and PDF/UA-2, ZUGFeRD / Factur-X. | [`ExpertPdf.Anywhere`](https://www.nuget.org/packages/ExpertPdf.Anywhere/) + `ExpertPdf.Anywhere.Native.<rid>`, or one `ExpertPdf.Anywhere.<Platform>` package |

The skill tells the assistant how to recognise its package and how it differs from the classic ExpertPdf products
for Windows (`ExpertPdf.HtmlToPdf`, `ExpertPdf.HtmlToPdf.NetCore`, `ExpertPdf.PdfCreator`, `ExpertPdf.MergePdf`...),
so a project on ExpertPdf Anywhere (namespaces `ExpertPdf.Anywhere.*`) never gets classic-only code, and a classic
project that needs Linux, macOS or Docker is shown how to move over.

It ships a lean `SKILL.md` (quickstart, decision routing, gotchas) plus topic-by-topic reference files that load
on demand. All code samples are provided in **C# and VB.NET**.

## Install (Claude Code)

```text
/plugin marketplace add expertpdf/ai-tools
/plugin install expertpdf@expertpdf
```

Then the skill activates automatically when you ask Claude to generate or manipulate PDFs with ExpertPdf in a
.NET project. You can also copy the `skills/expertpdf-anywhere-net/` folder into your project's `.claude/skills/`
directory to use it without the plugin.

## Other assistants — Cursor, GitHub Copilot, Codex

The same knowledge is available for other AI coding assistants under [`integrations/`](integrations/): condensed,
self-contained guides in each tool's native format (Cursor `.mdc` rules, Copilot `.instructions.md`, Codex
`AGENTS.md`) that link back to the reference files here for depth. See
[`integrations/README.md`](integrations/README.md) for where to drop each one.

## Versioning

The skill tracks **ExpertPdf Anywhere v22.0**. Its reference files are converted from the v22.0 help topics, every
C# and VB.NET sample in them was compiled against the 22.0.0 assembly, and every member declaration they show was
checked against its public API.

## Resources

- 📖 ExpertPdf Anywhere documentation — https://docs.html-to-pdf.net/anywhere-library/html/Index.htm
- 🧪 Online samples, with their source code — https://samples.html-to-pdf.net/anywhere/
- 🌐 Website — https://www.html-to-pdf.net
- ✉️ Support — office@html-to-pdf.net

---

<p align="center">© Outside Software SRL — <a href="https://www.html-to-pdf.net">www.html-to-pdf.net</a></p>
