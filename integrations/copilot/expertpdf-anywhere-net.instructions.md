---
applyTo: "**/*.cs,**/*.vb"
---

# ExpertPdf Anywhere (cross-platform)

Expert guidance for **ExpertPdf Anywhere**, the cross-platform ExpertPdf library for .NET: Windows (x64, x86),
Linux (x64, ARM64), macOS on Apple Silicon and Docker containers. It keeps the API of the ExpertPdf products
(HTML to PDF Converter, PDF Creator, PDF Merge, PDF Split, PDF Security, PDF to Text, PDF to Image, PDF Images
Extractor, RTF to PDF) in one package, under the `ExpertPdf.Anywhere.*` namespaces. HTML is rendered with
Chromium. Current as of **v22.0**.

## Pick the right library first

- **ExpertPdf Anywhere (this instructions)** - packages `ExpertPdf.Anywhere` + `ExpertPdf.Anywhere.Native.<rid>` (or one
  `ExpertPdf.Anywhere.<Platform>` package); code uses `using ExpertPdf.Anywhere.HtmlToPdf;`,
  `using ExpertPdf.Anywhere.PdfDocument;` and the other `ExpertPdf.Anywhere.*` namespaces.
- **The classic ExpertPdf products** - packages `ExpertPdf.HtmlToPdf`, `ExpertPdf.HtmlToPdf.NetCore`
  (with `.x64`, `.Chromium.Windows` or `.Blink` engine packages), `ExpertPdf.PdfCreator`, `ExpertPdf.MergePdf`,
  `ExpertPdf.SplitPdf`, `ExpertPdf.PdfSecurity`, `ExpertPdf.PdfToText`, `ExpertPdf.PdfToImage`,
  `ExpertPdf.PdfImgExtract`, `ExpertPdf.RtfToPdf`; code uses `using ExpertPdf.HtmlToPdf;` (no `.Anywhere`),
  `System.Drawing` types and WebKit / Blink / Internet Explorer engine settings. They are built for Windows. The
  APIs are close but not identical: do not mix the two, and do not give Anywhere code to a classic project or
  the reverse.
  - A classic project that must run on **Linux, macOS or in Docker** moves to ExpertPdf Anywhere: read
    [`migrating-from-expertpdf.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/migrating-from-expertpdf.md) (packages, namespaces, removed engine settings, images as bytes).
  - A classic project staying on Windows keeps its own API; the member names here are a guide only.

Check the `.csproj`: `ExpertPdf.Anywhere*` = this skill; `ExpertPdf.HtmlToPdf*`, `ExpertPdf.PdfCreator*` and the
other `ExpertPdf.<Product>` packages = the classic products.

## Install

For an application that runs on one platform, one package installs the library and that platform's engines:

```bash
dotnet add package ExpertPdf.Anywhere.Linux
```

`ExpertPdf.Anywhere.Windows` (x64), `ExpertPdf.Anywhere.Linux` (x64, Docker), `ExpertPdf.Anywhere.Linux.Arm64`,
`ExpertPdf.Anywhere.MacOS` (Apple Silicon, macOS 13 or newer). Or install the managed library (AnyCPU) and the
native engines for the runtime you deploy to:

```bash
dotnet add package ExpertPdf.Anywhere
dotnet add package ExpertPdf.Anywhere.Native.linux-x64
```

Native packages: `win-x64`, `win-x86`, `linux-x64`, `linux-arm64`, `osx-arm64`. Each brings the Chromium engine
(HTML to PDF and image) and, through `ExpertPdf.Anywhere.Tools.<rid>`, the PDF tool (PDF to text, PDF to image,
image extraction); an application that converts no HTML can reference the Tools package alone. **A project
references one native package**: all of them install the engine into the same `Chromium-CEF-154.0.28` folder.
To publish one app for several platforms, choose the package with a `Condition` on `$(RuntimeIdentifier)` and
publish once per platform (see [`installation-and-deployment.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/installation-and-deployment.md)). The library targets .NET 11, 10, 9, 8
and .NET Standard 2.0 (.NET Framework 4.6.1+). Optional: `ExpertPdf.Anywhere.Fonts` adds Noto fonts for every
script, for Linux and macOS hosts with no fonts installed (Docker images, AWS Lambda, Azure App Service on Linux).

**Remote engine:** with `GlobalSettings.RenderingEngine = RenderingEngine.ChromiumRemote`,
`GlobalSettings.EngineEndpoint` and `GlobalSettings.EngineAccessToken` (or the `EXPERTPDF_RENDERING_ENGINE`,
`EXPERTPDF_ENGINE_ENDPOINT`, `EXPERTPDF_ENGINE_TOKEN` environment variables), HTML to PDF / image and PDF to
text / image run in the `ghcr.io/expertpdf-components/engine:22.0` container and the application needs no native package.
This is the supported way to run on **Alpine / musl** Linux. See [`installation-and-deployment.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/installation-and-deployment.md),
"The remote engine".

> **Platform limits for the native engines:** glibc-based Linux only (glibc 2.30+: Ubuntu 20.04+, Debian 11+,
> RHEL 9+, Amazon Linux 2023). macOS 13+ on Apple Silicon only. Windows Nano Server is not supported (use Server
> Core). Elsewhere, use the remote engine.

## License key

Without a key the library runs in **evaluation mode**: it works fully and stamps an evaluation notice on every
page it produces. Set the key once, at startup, before any other ExpertPdf call:

```csharp
ExpertPdf.Anywhere.LicensingManager.LicenseKey = "your license key";
```
```vbnet
ExpertPdf.Anywhere.LicensingManager.LicenseKey = "your license key"
```

Every class also has a `LicenseKey` property of its own (e.g. `PdfConverter.LicenseKey`), which wins over the
global key. A key that is not valid throws `LicenseException`. ExpertPdf Anywhere has its own keys: the keys of
the separate classic products do not unlock it (an ExpertPdf Toolkit or ExpertComponents Toolkit Total license of
version 22 or later does).

## Quickstart

```csharp
using ExpertPdf.Anywhere.HtmlToPdf;

// the same code on Windows, Linux and macOS
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4;
pdfConverter.SavePdfFromUrlToFile("https://www.html-to-pdf.net", "output.pdf");

// an HTML string, with a base URL for its relative images and styles, to bytes
byte[] pdf = pdfConverter.GetPdfBytesFromHtmlString(
    "<h1>Hello</h1><img src='logo.png'>", "https://www.example.com/");
```
```vbnet
Imports ExpertPdf.Anywhere.HtmlToPdf

' the same code on Windows, Linux and macOS
Dim pdfConverter As New PdfConverter()
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4
pdfConverter.SavePdfFromUrlToFile("https://www.html-to-pdf.net", "output.pdf")

' an HTML string, with a base URL for its relative images and styles, to bytes
Dim pdf As Byte() = pdfConverter.GetPdfBytesFromHtmlString(
    "<h1>Hello</h1><img src='logo.png'>", "https://www.example.com/")
```

`GetPdfDocumentObjectFromUrl` / `GetPdfDocumentObjectFromHtmlString` return a `Document` to change before
saving. `GetPdfBytesFromUrlAsync`, `SavePdfFromUrlToFileAsync` and the other `...Async` methods (with an
optional `CancellationToken`) convert without holding a thread.

## Mental model

1. **Converters** - `HtmlToPdf.PdfConverter`, `HtmlToPdf.ImgConverter` (returns image bytes),
   `WordToPdf.PdfConverter`, `ExcelToPdf.PdfConverter`, `PowerPointToPdf.PdfConverter`, `RtfToPdf.PdfConverter`,
   and `PdfToWordConverter` / `PdfToExcelConverter` / `PdfToPowerPointConverter`. Each converter has
   `PdfDocumentOptions`, `PdfHeaderOptions`, `PdfFooterOptions`, `PdfSecurityOptions` and `PdfDocumentInfo` of its
   own namespace. Several namespaces declare a `PdfConverter` and a `PdfDocumentOptions`: a file that imports two
   of them needs the namespace on the class name.
2. **The PDF document** - `PdfDocument.Document`: create one (`new Document()`), load one
   (`new Document("file.pdf")`) or get one from a converter; `AddPage`, `AddFont`, `PdfPage.AddElement` with
   `TextElement`, `ImageElement`, `HtmlToPdfElement`, `WordToPdfElement`, shapes, links, templates; `Save` and
   `Close` (or `using`).
3. **Tools for existing files** - `MergePdf.PDFMerge`, `PDFSplit.PDFSplitManager`,
   `PDFSecurity.PdfSecurityManager`, `PDFForms.PdfFormManager`, `PDFRedaction.PdfRedactionManager`,
   `PDFResize.PdfResizeManager`, `PDFPortfolio.PdfPortfolioManager`, and in `ExpertPdf.Anywhere`
   `PdfToTextConverter`, `PdfToImageConverter`, `PdfImgExtract`.

Rules that always apply:
- **No `System.Drawing`**: colors, fonts, points, rectangles, sizes and `ImageFormat` come from
  `ExpertPdf.Anywhere.Drawing`; images go in and out as bytes (or file paths), never `System.Drawing.Image`.
- **Chromium is the only engine.** `RenderingEngine` has `Chromium` (the default, on this machine) and
  `ChromiumRemote`; there is no WebKit, Blink or Internet Explorer engine and none of their settings.
- Always `Close()` documents and tools, or use `using`.
- One converter per conversion. Conversions running at once are limited by the static
  `PdfConverter.PdfConverterConcurrencyLevel` (default 8); extra ones wait in order.
- The Chromium engine runs inside the application by default (`GlobalSettings.ChromiumEngineHostMode`); set the
  mode at startup, it is fixed by the first conversion. `PdfConverter.ForceOutOfProcess` isolates one conversion.

## What do you need to do? (routing)

| Task | Reference |
|---|---|
| Packages (one per platform, or managed + native), requirements, license key, the Chromium engine and its in-process / out-of-process modes, the remote engine container, fonts and languages, deployment to Windows, IIS, Linux, macOS, Docker, Azure, AWS, Google Cloud | [`installation-and-deployment.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/installation-and-deployment.md) |
| Port code from the classic ExpertPdf products (packages, namespaces, removed engine settings, images as bytes, other differences) | [`migrating-from-expertpdf.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/migrating-from-expertpdf.md) |
| Convert a URL / HTML string / file; PdfConverter methods and options; page size, margins, orientation, document info, page breaks, repeating table headers, JavaScript and delays, start from JavaScript, fine control, conversion summary, JPEG compression, color space, HTML forms to PDF fields, prepend / append PDFs, post-convert changes, async, multithreading | [`html-to-pdf.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/html-to-pdf.md) |
| Headers, footers, page numbering, HTML / text / image / Word areas in them | [`headers-footers.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/headers-footers.md) |
| Live links, internal links, automatic bookmarks, automatic table of contents, document bookmarks | [`links-bookmarks-toc.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/links-bookmarks-toc.md) |
| HTML element positions in the PDF; excluding elements, converting part of a page; authentication, login forms, cookies, HTTP headers, proxy | [`web-elements-and-auth.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/web-elements-and-auth.md) |
| Render a web page or HTML to an image (`ImgConverter`) | [`html-to-image.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/html-to-image.md) |
| Build or edit a PDF: `Document`, pages, coordinates, templates, text and fonts, images, shapes, HTML and HTML-image elements | [`pdf-creation-elements.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/pdf-creation-elements.md) |
| Word (DOCX, DOC, RTF, markdown...), RTF, Excel and PowerPoint to PDF; the matching page elements | [`office-to-pdf.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/office-to-pdf.md) |
| PDF to Word, Excel and PowerPoint | [`pdf-to-office.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/pdf-to-office.md) |
| Merge (`Document.AppendDocument`, `PDFMerge`) and split (`PDFSplitManager`); resize pages; portfolios; page objects; layers | [`edit-merge-split.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/edit-merge-split.md) |
| Passwords, permissions, RC4 / AES encryption; digital signatures (PFX, certificate store, PKCS#11, external signers, timestamps, long-term validation), signature validation, the signature element | [`security-and-signatures.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/security-and-signatures.md) |
| Fill and flatten PDF forms | [`forms.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/forms.md) |
| Remove content by text, regular expression or area | [`redaction.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/redaction.md) |
| PDF to text, text search, PDF to image, image extraction | [`extract-text-image-search.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/extract-text-image-search.md) |
| PDF/A (1a to 4f), PDF/X-1a, tagged PDF and PDF/UA-1 / PDF/UA-2, ZUGFeRD / Factur-X invoices | [`accessibility-and-compliance.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/accessibility-and-compliance.md) |
| Frequently asked questions, troubleshooting | [`troubleshooting-faq.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/troubleshooting-faq.md) |

## Gotchas (read before debugging)

- **Classic code does not compile** -> it uses `ExpertPdf.HtmlToPdf` without `.Anywhere`, `System.Drawing`
  types, or engine settings that only WebKit / Blink / Internet Explorer read (`FitWidth`, `EmbedFonts`,
  `AvoidImageBreak`, `InternetSecurityZone`, `ProxyOptions.Type`...). Change the namespaces and remove those
  lines: see [`migrating-from-expertpdf.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/migrating-from-expertpdf.md).
- **Engine not found / conversion fails on Linux or macOS** -> the native package of that runtime is missing, or
  two native packages fight over one folder. Reference the right `ExpertPdf.Anywhere.Native.<rid>` (one per
  project), or set `PdfConverter.CefEnginePath` to the `Chromium-CEF-154.0.28` folder. On Alpine / musl, use the
  remote engine.
- **Boxes instead of text on Linux, in Docker or on AWS Lambda** -> the host has no fonts for that script. Add
  `ExpertPdf.Anywhere.Fonts`, or put font files in a `fonts` folder next to the application.
- **Azure App Service or Functions cannot start the engine** -> do not use `WEBSITE_RUN_FROM_PACKAGE`; Functions
  need a Dedicated (App Service) plan, not Consumption. A 32-bit worker works with the x64 package (the engine then
  runs in its own process).
- **Output differs from the classic products** -> those could render with WebKit, Blink or Internet Explorer;
  Anywhere always renders with Chromium and fits the page to the PDF width. Control pagination with CSS
  (`break-before`, `break-after`, `break-inside: avoid`).
- **Content cut or missing on a JavaScript page** -> raise `PdfConverter.ConversionDelay`, or wait for
  a condition; see "JavaScript and page loading" in [`html-to-pdf.md`](https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/html-to-pdf.md).
- **Several users' pages share cached content** -> the HTTP cache is kept between in-process conversions by
  default; a multi-tenant service should set `PdfConverter.HttpCache = HttpCacheMode.Clear`.
- **A page needs WebGL** -> set `WebGlEnabled = true` (with `ForceOutOfProcess = true` if the in-process engine
  already started without it).
- **Appending to a PDF/A document fails** -> only documents that conform to the same level can be appended;
  convert them first with `Document.ConvertToPdfA`.
- **PDF/UA-2** pairs only with **PDF/A-4** (4, 4e, 4f) and **PDF/UA-1** not with PDF/A-4: an impossible
  combination throws. A tagged conversion (`Tagged`, or PDF/A-3a) declares PDF/UA-1. Both PDF/UA parts need a
  document title (`PdfDocumentInfo.Title`, or the page title) and a language (`PdfConverter.Language`).
- **Encryption**: a 128-bit key uses AES; `EncryptKey256Bit` is AES-256 (revision 6).

## Faithfulness

The reference files are converted from the ExpertPdf Anywhere v22.0 help topics, with the official C# and VB.NET
samples; every sample was compiled against the ExpertPdf Anywhere 22.0.0 assembly, and every member declaration
shown was checked against its public API. Only use APIs, enums and options that appear in them; do not invent
members, and do not carry members of the classic ExpertPdf products over. Online documentation:
https://docs.html-to-pdf.net/anywhere-library/html/Index.htm. Support: office@html-to-pdf.net.

## Full reference (open the relevant file)

Base: `https://github.com/expertpdf-components/ai-tools/blob/main/plugins/expertpdf/skills/expertpdf-anywhere-net/reference/`

- `accessibility-and-compliance.md`
- `edit-merge-split.md`
- `extract-text-image-search.md`
- `forms.md`
- `headers-footers.md`
- `html-to-image.md`
- `html-to-pdf.md`
- `installation-and-deployment.md`
- `links-bookmarks-toc.md`
- `migrating-from-expertpdf.md`
- `office-to-pdf.md`
- `pdf-creation-elements.md`
- `pdf-to-office.md`
- `redaction.md`
- `security-and-signatures.md`
- `troubleshooting-faq.md`
- `web-elements-and-auth.md`
