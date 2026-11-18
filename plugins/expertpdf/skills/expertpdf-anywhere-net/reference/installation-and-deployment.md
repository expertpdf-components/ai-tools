# Installation, license key, engines and deployment (Windows, Linux, macOS, Docker, cloud)

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Overview and namespaces

Online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm

ExpertPdf Anywhere is the cross-platform ExpertPdf library for .NET. It converts web pages, HTML, Word, Excel, PowerPoint and RTF documents to PDF and images, creates and edits PDF documents, merges and splits them, fills forms, redacts, secures and signs them, and reads them back as text and images - on Windows, Linux and macOS, in applications, services, containers and the cloud.

The library keeps the API of the ExpertPdf products (HTML to PDF Converter, PDF Creator, PDF Merge, PDF Split, PDF Security, PDF to Text, PDF to Image, PDF Images Extractor, RTF to PDF) in one package, under the *ExpertPdf.Anywhere* namespaces. HTML is rendered by a Chromium engine: HTML5, CSS3, web fonts and modern JavaScript.

### The namespaces

| Namespace | Content |
|---|---|
| ExpertPdf.Anywhere.HtmlToPdf | The HTML to PDF and HTML to image converters: PdfConverter, ImgConverter and their options. |
| ExpertPdf.Anywhere.PdfDocument | The PDF document: Document, pages, templates, and the elements added to pages - text, images, shapes, links, HTML, Word, Excel, PowerPoint, digital signatures. |
| ExpertPdf.Anywhere.WordToPdf, ExpertPdf.Anywhere.RtfToPdf | The Word to PDF and RTF to PDF converters. |
| ExpertPdf.Anywhere.ExcelToPdf, ExpertPdf.Anywhere.PowerPointToPdf | The Excel to PDF and PowerPoint to PDF converters. |
| ExpertPdf.Anywhere.MergePdf, ExpertPdf.Anywhere.PDFSplit | Merging documents and images into one PDF, splitting a PDF. |
| ExpertPdf.Anywhere.PDFSecurity | Passwords, permissions, encryption, digital signatures and their validation. |
| ExpertPdf.Anywhere.PDFForms, PDFRedaction, PDFResize, PDFPortfolio | Filling and flattening forms, removing content, resizing pages, PDF portfolios. |
| ExpertPdf.Anywhere | PDF to text, PDF to image, image extraction, the license key and the global settings. |
| ExpertPdf.Anywhere.Drawing | Colors, fonts, points, rectangles and image formats used by the other namespaces. |

### Where to start

- "Installation" (installation-and-deployment.md) and "Requirements" (installation-and-deployment.md)
- "Getting started" (html-to-pdf.md) - convert a URL or HTML to PDF
- "Overview" (pdf-creation-elements.md) - create and edit PDF documents
- "Word to PDF" (office-to-pdf.md), "RTF to PDF" (office-to-pdf.md), "Excel to PDF" (office-to-pdf.md), "PowerPoint to PDF" (office-to-pdf.md)
- "Merging PDF documents" (edit-merge-split.md), "Splitting PDF documents" (edit-merge-split.md), "Passwords, permissions and encryption" (security-and-signatures.md), "Digital signatures and signature validation" (security-and-signatures.md)
- "PDF to text" (extract-text-image-search.md), "PDF to image" (extract-text-image-search.md), "Extracting images" (extract-text-image-search.md)
- "Deployment" (installation-and-deployment.md) - Windows, Linux, macOS, Docker and the cloud
- "Migrating to ExpertPdf Anywhere" (migrating-from-expertpdf.md) - moving an application from the ExpertPdf products

## Installation

Online: https://docs.html-to-pdf.net/anywhere-library/html/Installation.htm

ExpertPdf Anywhere is installed from NuGet. There is no installer, nothing to register on the server and no system package to install on Linux: the packages copy everything the library needs next to your application when it builds and when it is published.

### The packages

| Package | Content |
|---|---|
| ExpertPdf.Anywhere | The library (ExpertPdf.Anywhere.dll), AnyCPU, for .NET 11, .NET 10, .NET 9, .NET 8 and .NET Standard 2.0 (.NET Framework 4.6.1 and later). |
| ExpertPdf.Anywhere.Native.win-x64, .win-x86, .linux-x64, .linux-arm64, .osx-arm64 | The native engines for one platform: the Chromium engine (HTML to PDF, HTML to image) in the Chromium-CEF-154.0.28 folder, and the PDF tool through the Tools package of the same platform, which each Native package depends on. |
| ExpertPdf.Anywhere.Tools.win-x64, .win-x86, .linux-x64, .linux-arm64, .osx-arm64 | The PDF tool ExpertPdf.Tools for one platform (PDF to text, PDF to image, image extraction), without the Chromium engine. An application that converts no HTML can reference it instead of the Native package. |
| ExpertPdf.Anywhere.Windows, .Linux, .Linux.Arm64, .MacOS | Platform packages: the library and the native engines of one platform (Windows x64, Linux x64, Linux ARM64, macOS on Apple Silicon) in a single reference. |
| ExpertPdf.Anywhere.Fonts | Google's Noto fonts for every script, copied to a fonts folder next to the application on Linux and macOS. For hosts with no fonts installed: Docker images, AWS Lambda, Azure App Service on Linux. |

The simplest setup is one platform package:

```text
dotnet add package ExpertPdf.Anywhere.Windows
```

or the library and the native package of your runtime:

```text
dotnet add package ExpertPdf.Anywhere
dotnet add package ExpertPdf.Anywhere.Native.linux-x64
```

### One native package per project

Every native package copies its engine to the same Chromium-CEF-154.0.28 folder, so a project references the native package of the platform it runs on, not several of them. To build on one platform and publish for another, choose the package by the runtime identifier:

```xml
<ItemGroup>
  <PackageReference Include="ExpertPdf.Anywhere" Version="22.0.0" />
  <PackageReference Include="ExpertPdf.Anywhere.Native.win-x64" Version="22.0.0"
      Condition="'$(RuntimeIdentifier)' != 'linux-x64'" />
  <PackageReference Include="ExpertPdf.Anywhere.Native.linux-x64" Version="22.0.0"
      Condition="'$(RuntimeIdentifier)' == 'linux-x64'" />
</ItemGroup>
```

Then *dotnet publish -r linux-x64* deploys the Linux engine and a build on the development machine the Windows one. The engines are separate processes or a native library loaded into your process (see "The Chromium engine" (installation-and-deployment.md)); the managed package itself is the same on every platform.

### Your first conversion

```csharp
using ExpertPdf.Anywhere.HtmlToPdf;

PdfConverter converter = new PdfConverter();
// converter.LicenseKey = "your license key";
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4;
converter.SavePdfFromUrlToFile("https://www.html-to-pdf.net", "page.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere.HtmlToPdf

Dim converter As New PdfConverter()
' converter.LicenseKey = "your license key"
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4
converter.SavePdfFromUrlToFile("https://www.html-to-pdf.net", "page.pdf")
```

Without a license key the library runs in evaluation mode and marks the documents it creates. See "License key" (installation-and-deployment.md).

### Samples

The samples download has, for Windows, Linux and macOS, an ASP.NET Core web site with a page for every feature and console applications in C# and VB.NET for the HTML to PDF converter, merge, split, security, PDF to text, PDF to image and image extraction. Each folder has a solution that restores the packages from NuGet.

## Requirements

Online: https://docs.html-to-pdf.net/anywhere-library/html/Requirements.htm

ExpertPdf Anywhere runs on Windows, Linux and macOS, on .NET and on .NET Framework.

### Frameworks

- **.NET 11, .NET 10, .NET 9 and .NET 8**, and every framework that runs .NET Standard 2.0 libraries: **.NET Framework 4.6.1** and later, .NET Core 2.0 and later.
- Console applications, services, ASP.NET Core and ASP.NET web applications, Azure Functions, AWS Lambda functions and desktop applications.
- The managed library is AnyCPU. The process can be 32-bit or 64-bit with either engine package: when the architectures differ, the Chromium engine runs in a separate process.

### Platforms

| Platform | Native package | Notes |
|---|---|---|
| Windows x64 | ExpertPdf.Anywhere.Native.win-x64 | Windows 10, Windows 11, Windows Server 2016 and later; Windows Server Core containers. |
| Windows x86 | ExpertPdf.Anywhere.Native.win-x86 | 32-bit processes on the same systems. |
| Linux x64 | ExpertPdf.Anywhere.Native.linux-x64 | glibc 2.30 or later: Ubuntu 20.04+, Debian 11+, RHEL 9 and compatible, Amazon Linux 2023, the official .NET container images. No system package is needed: the Chromium libraries come with the package. |
| Linux ARM64 | ExpertPdf.Anywhere.Native.linux-arm64 | The same distributions on ARM64 (AWS Graviton, Azure Cobalt, Ampere). |
| macOS | ExpertPdf.Anywhere.Native.osx-arm64 | macOS 13 and later on Apple Silicon. |

Windows Nano Server is not supported: the Chromium engine needs the Windows desktop libraries on Windows. On Alpine Linux (musl), where the native engines cannot run, use the remote engine (see "The remote engine" (installation-and-deployment.md)).

### Fonts

The library uses the fonts installed on the system. On Linux and macOS hosts without fonts - Docker images, AWS Lambda, Azure App Service on Linux - add the ExpertPdf.Anywhere.Fonts package (Noto fonts for every script); without it only Latin text has a font there. Fonts added to a *fonts* folder next to the application are used on every platform.

### Resources

A conversion needs memory in proportion to the size of the page; complex pages with many images can need several hundred MB. The Chromium engine runs inside the application by default and keeps its browser processes for the life of the application, which makes each conversion faster. For high volumes, see "Multithreading" (html-to-pdf.md).

## License key

Online: https://docs.html-to-pdf.net/anywhere-library/html/Licensing.htm

A license key is generated for each purchase. It holds the product, the version and the license type, and is associated with the order. An ExpertPdf Anywhere license key is valid for ExpertPdf Anywhere version 22.

Without a license key the library runs in evaluation mode: it works fully and stamps an evaluation notice on every page it produces (the PDF to text converter adds the notice to the extracted text). A key that is set but not valid - one that cannot be read, is for another product or version, or has expired - makes the next conversion or save throw `LicenseException`.

### Setting the license key once

Set `LicensingManager.LicenseKey` once, when the application starts. It unlocks every class of the library: the converters, the PDF document, merge, split, security, forms, redaction and the PDF to text and image tools.

```csharp
ExpertPdf.Anywhere.LicensingManager.LicenseKey = "your license key";
```

```vbnet
ExpertPdf.Anywhere.LicensingManager.LicenseKey = "your license key"
```

### Setting the license key on an object

The classes also have a *LicenseKey* property of their own, for example `PdfConverter.LicenseKey`. When it is set, the object uses it instead of the key of the licensing manager.

```csharp
ExpertPdf.Anywhere.HtmlToPdf.PdfConverter pdfConverter =
    new ExpertPdf.Anywhere.HtmlToPdf.PdfConverter();
pdfConverter.LicenseKey = "your license key";
```

```vbnet
Dim pdfConverter As New ExpertPdf.Anywhere.HtmlToPdf.PdfConverter()
pdfConverter.LicenseKey = "your license key"
```

## The Chromium engine

Online: https://docs.html-to-pdf.net/anywhere-library/html/RenderingEngine.htm

ExpertPdf Anywhere renders HTML with **Chromium**, the engine of the Chrome browser (Chromium 154). The converter loads the page in a Chromium browser, runs its JavaScript, then builds the PDF from the rendered page, so a page comes out the way Chrome prints it: HTML5, CSS3 with flexbox and grid, web fonts, SVG, canvas and modern JavaScript frameworks are all supported.

Chromium is the only engine, on every platform: Windows x64 and x86, Linux x64 and ARM64, and macOS on Apple Silicon. There is no WebKit, Blink or Internet Explorer engine. `RenderingEngine` says where the Chromium engine runs: *Chromium*, the default, runs it on the application's machine, so the `PdfConverter.RenderingEngine` property of the converters and of the HTML elements does not need to be set; *ChromiumRemote* sends the work to the ExpertPdf engine container (see "The remote engine" (installation-and-deployment.md)).

### Where the engine comes from

The engine is in the native packages, one per platform (**ExpertPdf.Anywhere.Native.win-x64**, **.win-x86**, **.linux-x64**, **.linux-arm64**, **.osx-arm64**), which the platform packages (**ExpertPdf.Anywhere.Windows**, **.Linux**, **.Linux.Arm64**, **.MacOS**) include. The package copies a **Chromium-CEF-154.0.28** folder next to your application when it builds and when it is published, and the converter finds it there. Nothing is installed on the machine, and on Linux no system package is needed. See "Installation" (installation-and-deployment.md) and "Deployment" (installation-and-deployment.md).

To load the engine from another folder, set `PdfConverter.CefEnginePath` to the full path of the Chromium-CEF-154.0.28 folder. `PdfConverter.CefEngineTemporaryFilesPath` sets the folder the engine writes its temporary files to (the system temporary folder by default); the application must be allowed to write there.

Chromium draws the text with the fonts installed on the machine and embeds in the PDF every font a page uses. On Linux and macOS hosts that have no fonts, such as container images, add the **ExpertPdf.Anywhere.Fonts** package.

### Inside the application or in a separate process

The engine can run in two ways, chosen for the whole application with `GlobalSettings.ChromiumEngineHostMode`:

- `ChromiumEngineHostMode.InProcess` - the engine is loaded into your application and kept ready between conversions, which removes the time a new process takes to start. This is the default when the in-process engine host (ExpertPdf.Cef.Host.dll, .so or .dylib, part of the native package) is beside the engine and built for the architecture of your process.
- `ChromiumEngineHostMode.OutOfProcess` - each conversion runs in a short-lived engine process of its own (ExpertPdf.Cef), outside your application, so a failure inside the engine cannot affect the application. This is the default otherwise, for example in a 32-bit process using the 64-bit engine package.

The engine is initialized once per process, so set the mode at start-up: the value in force at the first conversion is used for the life of the process. In both modes each conversion has a browser of its own, and cookies and site storage are cleared after every conversion, so nothing a page stores reaches the next conversion. What the HTTP cache keeps between in-process conversions is set with `PdfConverter.HttpCache`.

The environment variable `EXPERTPDF_CHROMIUM_ENGINE_HOST`, set to `outofprocess` or `inprocess`, chooses the mode without a code change; a value assigned to the property in code takes precedence over it.

```csharp
// at application start-up, before the first conversion: run every conversion in a
// separate engine process
ExpertPdf.Anywhere.GlobalSettings.ChromiumEngineHostMode =
    ExpertPdf.Anywhere.ChromiumEngineHostMode.OutOfProcess;
```

```vbnet
' at application start-up, before the first conversion: run every conversion in a
' separate engine process
ExpertPdf.Anywhere.GlobalSettings.ChromiumEngineHostMode =
    ExpertPdf.Anywhere.ChromiumEngineHostMode.OutOfProcess
```

One conversion can be sent to a separate engine process whatever the mode is, with `PdfConverter.ForceOutOfProcess`: to isolate a page you do not trust, or to give one conversion settings that the in-process engine fixes for the whole process at its first conversion (WebGL, the proxy, local file access).

```csharp
PdfConverter pdfConverter = new PdfConverter();
// this page draws with WebGL, and runs in its own engine process
pdfConverter.ForceOutOfProcess = true;
pdfConverter.WebGlEnabled = true;
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/map", "map.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()
' this page draws with WebGL, and runs in its own engine process
pdfConverter.ForceOutOfProcess = True
pdfConverter.WebGlEnabled = True
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/map", "map.pdf")
```

The number of conversions that run at the same time is set with `PdfConverter.PdfConverterConcurrencyLevel`, in both modes; see "Multithreading" (html-to-pdf.md).

## The remote engine

Online: https://docs.html-to-pdf.net/anywhere-library/html/Remote-Engine.htm

The conversion engines normally run on the machine the application runs on, from the **ExpertPdf.Anywhere.Native.<rid>** package. With `RenderingEngine.ChromiumRemote` the application sends that work over HTTP to the **ExpertPdf engine** instead - a service that ships as the container image `ghcr.io/expertpdf-components/engine` and runs on another machine, as another container, or beside the application in the same pod.

Every native operation goes to the engine: HTML to PDF and HTML to image (the Chromium engine), and PDF to text and PDF to image (the PDF tool). Everything else stays in the application and works as before: building the document, headers and footers, bookmarks, PDF/A and PDF/UA, security and signatures, Word, Excel and PowerPoint to PDF, redaction, forms - and the license check. The engine itself needs no license.

Use the remote engine to:

- **Run on Alpine Linux** and other musl-based systems, where the native engines cannot run. This is the supported way to use the library there.
- **Keep the application small**: it references only the **ExpertPdf.Anywhere** package - no native package and no fonts package.
- **Isolate rendering** from the application, scale it separately, or share one engine between several applications.

### Quick start

Start the engine with an access token:

```text
docker run -d --name expertpdf-engine -p 8080:8080 \
  --read-only --tmpfs /tmp \
  -e EXPERTPDF_ENGINE_TOKEN="$(openssl rand -base64 32)" \
  ghcr.io/expertpdf-components/engine:22.0
```

Then point the application at it, once at start-up. The conversion code does not change:

```csharp
using ExpertPdf.Anywhere;
using ExpertPdf.Anywhere.HtmlToPdf;

GlobalSettings.RenderingEngine = RenderingEngine.ChromiumRemote;
GlobalSettings.EngineEndpoint = "http://localhost:8080";
GlobalSettings.EngineAccessToken = "the token the engine was started with";

PdfConverter pdfConverter = new PdfConverter();
pdfConverter.SavePdfFromUrlToFile("https://www.example.com", "test.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere
Imports ExpertPdf.Anywhere.HtmlToPdf

GlobalSettings.RenderingEngine = RenderingEngine.ChromiumRemote
GlobalSettings.EngineEndpoint = "http://localhost:8080"
GlobalSettings.EngineAccessToken = "the token the engine was started with"

Dim pdfConverter As New PdfConverter()
pdfConverter.SavePdfFromUrlToFile("https://www.example.com", "test.pdf")
```

The same settings can come from environment variables instead, with no code at all - which is how a container deployment usually does it:

```text
EXPERTPDF_RENDERING_ENGINE=ChromiumRemote
EXPERTPDF_ENGINE_ENDPOINT=http://expertpdf-engine:8080
EXPERTPDF_ENGINE_TOKEN=the token the engine was started with
```

### Application settings

| Property | Environment variable | Meaning |
|---|---|---|
| `GlobalSettings.RenderingEngine` | `EXPERTPDF_RENDERING_ENGINE` | `Chromium` (the default: the engines run on this machine) or `ChromiumRemote`. Every converter and HTML element follows it unless its own **RenderingEngine** property is set, and the PDF tools always follow it. |
| `GlobalSettings.EngineEndpoint` | `EXPERTPDF_ENGINE_ENDPOINT` | The engine's url. Default `http://expertpdf-engine:8080`: an engine service named `expertpdf-engine` on the same Docker network or cluster. |
| `GlobalSettings.EngineAccessToken` | `EXPERTPDF_ENGINE_TOKEN` | One of the tokens the engine was started with, or none when the engine requires none. |
| `GlobalSettings.EngineTimeout` | `EXPERTPDF_ENGINE_TIMEOUT` (seconds) | How long one request may take. By default it follows the conversion's own navigation and print timeouts, plus time to wait for a free slot on a busy engine. |

A single converter can use the other engine than the global setting through its own `PdfConverter.RenderingEngine` (and the **RenderingEngine** property of **ImgConverter**, **HtmlToPdfElement**, **HtmlToImageElement** and **HtmlToPdfArea**).

```csharp
// this converter uses the remote engine, whatever the global setting is
PdfConverter remoteConverter = new PdfConverter();
remoteConverter.RenderingEngine = RenderingEngine.ChromiumRemote;
byte[] pdf = remoteConverter.GetPdfBytesFromUrl("https://www.example.com");
```

```vbnet
' this converter uses the remote engine, whatever the global setting is
Dim remoteConverter As New PdfConverter()
remoteConverter.RenderingEngine = RenderingEngine.ChromiumRemote
Dim pdf As Byte() = remoteConverter.GetPdfBytesFromUrl("https://www.example.com")
```

### Running the engine

The image is `ghcr.io/expertpdf-components/engine` (mirrored on Docker Hub as `expertpdf/engine`), for **x64** and **arm64**. Use the tag of the library's version - `22.0.0`, or `22.0` for its latest build; an engine that does not speak the library's protocol is reported as such. The engine listens on port 8080, runs as a non-root user, works with a read-only file system and a writable `/tmp`, and needs no extra capabilities. It carries fonts for every language (the Noto family), so it renders every script without a fonts package.

**Health:** `GET /health` answers 200 once the engine is ready to convert (the image's own `HEALTHCHECK` uses it), and 503 before that or when it cannot convert. `/healthz` answers the same. `GET /v1/info` (with the token) reports the engine's version, its Chromium version and its limits.

| Engine environment variable | Meaning |
|---|---|
| `EXPERTPDF_ENGINE_TOKEN` | The access tokens the engine accepts, separated by commas - several at once let a token be rotated without downtime. Each must be at least 32 characters, or the engine does not start; generate one with `openssl rand -base64 32`. Without a token anyone who can reach the port can use the engine (it logs a warning). |
| `EXPERTPDF_ENGINE_TOKEN_FILE` | A file holding the tokens, one per line - a Docker or Kubernetes secret. Instead of `EXPERTPDF_ENGINE_TOKEN`. |
| `EXPERTPDF_MAX_CONCURRENCY` | HTML conversions run at once. Default: the number of CPUs the container has. |
| `EXPERTPDF_MAX_TOOL_CONCURRENCY` | PDF to text / image operations run at once. Default: the number of CPUs. |
| `EXPERTPDF_QUEUE_TIMEOUT` | Seconds a request waits for a free slot (default 30). After that the engine answers 503, so a retry can reach another replica; the library retries such an answer. |
| `EXPERTPDF_MAX_BODY_MB` | The largest request accepted, in MB (default 100). |
| `EXPERTPDF_ENGINE_HTTP_CACHE` | The most of the HTTP cache a conversion may keep: `clear`, `thirdParty` or `all` (the default - each conversion keeps what it asks for through `PdfConverter.HttpCache`). Set `clear` on an engine shared by different callers. |
| `EXPERTPDF_ENGINE_PORT` | The port to listen on (default 8080) - change it when the engine runs beside an application that uses 8080 in the same pod. |
| `EXPERTPDF_TEMP_DIR` | Where conversions work (default `/tmp/expertpdf-engine`). Each request has a folder of its own, deleted when it ends. |

**Docker Compose:** name the engine service `expertpdf-engine` and the application needs no endpoint setting at all:

```text
services:
  expertpdf-engine:
    image: ghcr.io/expertpdf-components/engine:22.0
    read_only: true
    tmpfs:
      - /tmp
    environment:
      EXPERTPDF_ENGINE_TOKEN: ${EXPERTPDF_ENGINE_TOKEN}
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      EXPERTPDF_RENDERING_ENGINE: ChromiumRemote
      EXPERTPDF_ENGINE_TOKEN: ${EXPERTPDF_ENGINE_TOKEN}
    depends_on:
      expertpdf-engine:
        condition: service_healthy
```

In Kubernetes, run the engine as a Deployment behind a Service named `expertpdf-engine`, with `/health` as its readiness and liveness probe and the token in a Secret; from another namespace use `http://expertpdf-engine.<namespace>.svc.cluster.local:8080`.

### Security

- **Use a token** and keep the engine off the public internet - on the Docker network, the cluster network or a private subnet of the application.
- **The token is never sent in clear text to another network.** With a token set, the library requires an `https` endpoint unless the endpoint is a local one: a service name without a dot, `localhost`, a loopback or a private address. The engine itself serves plain HTTP; for an engine on another machine, put TLS in front of it (an ingress, a load balancer or a reverse proxy).
- **A page cannot read the engine's files**: local file access is always off in the engine, whatever a conversion asks for.
- **The engine fetches what the pages reference**, from its own network. When it converts html you do not control, limit where the engine's network can reach (internal services, cloud metadata addresses).
- **A shared engine**: set `EXPERTPDF_ENGINE_HTTP_CACHE=clear`, so no conversion is served a response another caller's conversion fetched. Cookies and storage are cleared after every conversion in any case.

### What is different from a local engine

- **Local files:** the engine cannot see the application's files, so a `file://` url or base url is refused before anything is sent. Convert the html as a string, or serve the files over http. Local files an html string references - images, stylesheets, fonts and media - are sent with it, so they are drawn as a local engine draws them (not when local file access is denied, as locally).
- **localhost** in a url means the engine's own machine, not the application's - except when the engine runs in the application's pod or on its host network. Use an address the engine can reach, such as the application's service name.
- **Fonts** in the application's own **fonts** folder are sent to the engine with each conversion.
- **Per-conversion settings that a local in-process engine fixes for the whole process** - WebGL, a proxy, insecure content - are honoured per conversion. A proxy must be reachable from the engine.
- **Errors** are the same exception types as locally. When the engine itself could not be reached or refused the request, the message says so and names the request id the engine logs its runs under.
- **Logging:** with a log file set (`GlobalSettings.LogFile`), the engine's own log of the conversion is appended to it.
- **Cancellation:** cancelling an awaited conversion cancels it in the engine.

## Deployment

Online: https://docs.html-to-pdf.net/anywhere-library/html/Deployment.htm

An application that uses ExpertPdf Anywhere is deployed like any other .NET application: publish it and copy the publish folder. The native packages place the Chromium engine (the Chromium-CEF-154.0.28 folder) and the PDF tool (ExpertPdf.Tools) in the output, and the library finds them there. Nothing is installed or registered on the server.

### Windows, IIS and Azure App Service

- Reference ExpertPdf.Anywhere.Windows (or the library and ExpertPdf.Anywhere.Native.win-x64, or .win-x86 for a 32-bit process).
- IIS and Azure App Service: a 32-bit worker process works with the x64 engine package; the engine then runs in a separate process. Choose a 64-bit worker to run it inside the application.
- Azure App Service: Basic plan or above, deployed with Zip Deploy or Web Deploy, without *WEBSITE_RUN_FROM_PACKAGE* - see "Azure App Service" (installation-and-deployment.md).
- Windows Server Core containers: see "Docker" (installation-and-deployment.md).

### Linux

- Reference ExpertPdf.Anywhere.Linux (x64) or ExpertPdf.Anywhere.Linux.Arm64, or the library and the matching native package, and publish for the runtime: *dotnet publish -r linux-x64*.
- No system package is needed: the native package brings the Chromium shared libraries, and the engine renders headless, without an X server.
- The engine files arrive executable; if a copy lost the permission (for example a zip made on Windows), the library restores it before it starts the engine.
- Hosts without fonts (container images, AWS Lambda, Azure App Service on Linux): add ExpertPdf.Anywhere.Fonts - see "Fonts and languages" (installation-and-deployment.md).

### Docker

Linux containers run on the standard glibc-based .NET images (mcr.microsoft.com/dotnet/aspnet, mcr.microsoft.com/dotnet/runtime) with nothing installed in the image: no apt-get, no special *docker run* options, no GPU and no *--shm-size*, as a non-root user if you want. Windows containers run on Windows Server Core, with the standard Windows font families added to the image. Alpine (musl) images convert through the remote engine (see "The remote engine" (installation-and-deployment.md)).

Dockerfiles, ARM64 images, non-root users, Windows containers and the ready-to-run Docker samples: "Docker" (installation-and-deployment.md).

### macOS

Reference ExpertPdf.Anywhere.MacOS (or the library and ExpertPdf.Anywhere.Native.osx-arm64) for applications that run on macOS 13 or later on Apple Silicon. For Docker on a Mac, see "Docker" (installation-and-deployment.md): containers are Linux, so an image needs the linux-arm64 package.

### Serverless and cloud services

ExpertPdf Anywhere runs on Azure (App Service, Functions on a Dedicated or Premium plan, Container Apps), Amazon Web Services (Lambda as a container image, Elastic Beanstalk, ECS, EKS) and Google Cloud Run, on Windows and on Linux. The Azure Functions Consumption plans cannot run the engine. Step-by-step instructions for each platform: "Cloud deployments" (installation-and-deployment.md).

## Docker

Online: https://docs.html-to-pdf.net/anywhere-library/html/Deployment-Docker.htm

Docker packages an application together with everything it needs to run into a portable container image, which then runs identically on a developer machine, an on-premise server or any cloud container service (Azure Container Apps / AKS, AWS ECS / EKS, Google Cloud Run, and so on).

**ExpertPdf Anywhere is container-ready**: reference the NuGet packages, publish, run - no extra installation steps inside the image. Both **Linux containers** and **Windows containers** are supported.

### Linux containers

Reference **ExpertPdf.Anywhere.Linux** (or **ExpertPdf.Anywhere.Linux.Arm64**), or the managed **ExpertPdf.Anywhere** package together with **ExpertPdf.Anywhere.Native.linux-x64** (or **linux-arm64**). `dotnet publish` places the native engines in the output automatically, and nothing else needs to be installed in the image:

- **No system packages / no `apt-get`** - the native Linux package bundles the complete Chromium shared-library closure (including the runtime-loaded NSS / SQLite modules required for HTTPS), and ExpertPdf Anywhere wires it up automatically when it launches the engine. The HTML-to-PDF and HTML-to-image converters run on the bare `mcr.microsoft.com/dotnet/aspnet` / `runtime` images.
- **No display server** - the Chromium engine renders headless / off-screen; no X server or Xvfb.
- **Fonts from a package, not from the image** - the base images have no fonts, and the fonts built into the library cover Latin, Greek and Cyrillic only. Reference the **ExpertPdf.Anywhere.Fonts** package (Noto fonts for every script, copied into the publish output) to render every language - see "Fonts and languages" (installation-and-deployment.md).
- **No special `docker run` flags** - no `--no-sandbox`, no extra capabilities, no privileged mode. A plain `docker run` works.
- **No manual `chmod`** - the native package makes the engine executable when the application is published on Linux, and the library restores the permission before launching the engine if it was lost (see the note about non-root containers below).
- **No GPU and no `--shm-size` tuning required** - the engine renders in software, and Chromium does not keep its shared memory in `/dev/shm`, so Docker's default 64 MB is enough. The engine stays loaded inside the application between conversions (`GlobalSettings.ChromiumEngineHostMode`, the default) in a plain `docker run` as well.
- The PDF-to-text and PDF-to-image converters use a statically-linked native tool (ExpertPdf.Tools) with no dependencies at all, and the rest of the library is fully managed.

**Base images:** any glibc-based .NET image works. Verified end-to-end (a real HTTPS page converted, with no packages installed): the default Debian 12 based `aspnet:8.0` / `runtime:8.0` tags, Ubuntu 22.04 (`8.0-jammy`), Ubuntu 24.04 (`10.0-noble`), and - via a self-contained publish - the RPM family (`amazonlinux:2023`, zero `dnf` packages). The engines require glibc 2.30 or newer - satisfied by every mainstream distribution since about 2020 (Ubuntu 20.04+, Debian 11+, RHEL 9+).

**Resources:** Chromium-based rendering benefits from CPU and memory - plan for at least 1 core and 2 GB of RAM per container, and prefer 2+ cores for consistently fast conversions under load.

A minimal multi-stage Dockerfile for an ASP.NET application using ExpertPdf Anywhere (any supported .NET version works the same way):

```text
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish MyApp.csproj -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:10.0
WORKDIR /app
COPY --from=build /app .
ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

Build and run:

```text
docker build -t myapp .
docker run --rm -p 8080:8080 myapp
```

> **Important:** Use a **glibc-based** Linux image (the default Debian / Ubuntu based `mcr.microsoft.com/dotnet` tags) for an application that runs the engines itself. **Alpine** (musl) images cannot run the native engines: an application on Alpine converts through the remote engine container (`ghcr.io/expertpdf-components/engine`) instead - see "The remote engine" (installation-and-deployment.md).

#### Alternative: publish on the host, copy into the image

If restoring NuGet packages inside the Docker build is inconvenient (private feeds, offline build agents), publish on the host first - `dotnet publish -c Release -o publish` - and use a single-stage Dockerfile that just copies the publish output. The native engines are already in the publish folder, so the result is identical:

```text
FROM mcr.microsoft.com/dotnet/aspnet:10.0
WORKDIR /app
COPY publish/ .
ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

#### Running the container as a non-root user

A non-root user needs nothing extra. When the application is published on Linux - inside the Docker build, as above - the native package makes the engine files readable and executable for every user, so the image can switch to the built-in `app` user before the entry point:

```text
USER app
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

An application published on Windows and copied into the image gets the same permissions from Docker, which marks files copied from a Windows build context as executable for every user. Conversions run identically under the `app` user, on Docker and on platforms that pick the user themselves (AWS Lambda, Google Cloud Run functions).

#### Linux ARM64

For ARM64 containers, reference **ExpertPdf.Anywhere.Linux.Arm64** (or **ExpertPdf.Anywhere.Native.linux-arm64** instead of **linux-x64**) - everything else stays the same (the `mcr.microsoft.com/dotnet` base tags are multi-architecture, so the same image reference selects the matching architecture automatically). On an ARM64 host, build and run as usual; from an x64 host you can cross-build and run under emulation:

```text
docker build --platform linux/arm64 -t myapp-arm64 .
docker run --rm --platform linux/arm64 -p 8080:8080 myapp-arm64
```

> **Note:** This is also the package to use when building on an Apple Silicon Mac. Docker Desktop for Mac runs **Linux** containers, so a container image needs **ExpertPdf.Anywhere.Native.linux-arm64** - not **osx-arm64** (or ExpertPdf.Anywhere.MacOS), which is for applications running natively on macOS.

### Windows containers

Reference **ExpertPdf.Anywhere.Windows** (or the managed package together with **ExpertPdf.Anywhere.Native.win-x64**) and base the runtime image on **Windows Server Core** - for example `mcr.microsoft.com/dotnet/aspnet:10.0-windowsservercore-ltsc2022`. Server Core provides the GDI components the Chromium engine uses; the smaller **Nano Server** images do not and are not supported. No Visual C++ Redistributable is required - the native engines are static builds.

One container-specific step is required in the image (illustrated by the Docker samples): **install the core Windows font families**. Server Core images ship almost no fonts, and the Chromium engine cannot start unless standard Windows font families are installed (third-party fonts alone are not enough; Segoe UI, Arial, Times New Roman, Verdana or Tahoma are). Copy them from Microsoft's full Windows Server image in a build stage and register them:

```text
FROM mcr.microsoft.com/windows/server:ltsc2022 AS fonts
# ... in the runtime stage:
COPY --from=fonts ["C:/Windows/Fonts/segoeui.ttf", \
                   "C:/Windows/Fonts/segoeuib.ttf", \
                   "C:/Windows/Fonts/arial.ttf", \
                   "C:/Windows/Fonts/arialbd.ttf", "C:/Windows/Fonts/"]
RUN powershell -Command "Get-ChildItem C:\Windows\Fonts -Filter *.ttf | \
    ForEach-Object { New-ItemProperty \
    -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Fonts' \
    -Name ($_.BaseName + ' (TrueType)') -Value $_.Name \
    -PropertyType String -Force | Out-Null }"
```

Add the italic variants and any other families your pages use the same way. Alternatively, base the runtime on the **full Windows Server image** (`mcr.microsoft.com/windows/server:ltsc2022`, which ships the complete font set) and install the ASP.NET Core runtime in the Dockerfile - no font step is needed there, at the cost of a larger image.

Nothing else is required: the engine detects that it is running in a Windows container and renders in software, which is all PDF and image output needs.

Windows container images can only be built and run on a Windows host with Docker switched to Windows-container mode. When Windows itself is not a requirement, prefer the Linux containers above - they are smaller and need no font step at all.

### Run-once (batch) containers

For batch conversion jobs, package a console application instead of a web application and mount a host folder to collect the produced files:

```text
docker run --rm -v "$PWD/out:/out" myconverter \
    --url https://www.example.com -o /out/site.pdf
```

The container starts, performs the conversion, writes the PDF to the mounted folder and exits.

### Ready-to-run Docker samples

The ExpertPdf Anywhere samples include complete Docker samples - multi-stage Dockerfiles (with `compose.yaml` files and per-sample instructions) that containerize the sample web application and a command-line converter, for both Linux and Windows containers, in the `docker/linux/web`, `docker/linux/console`, `docker/windows/web` and `docker/windows/console` folders, plus `docker/remote-engine` for an application that converts through the remote engine container. They are a working starting point for your own images.

The images you build are standard OCI images - push them to any container registry (Azure Container Registry, Amazon ECR, Docker Hub, GitHub Container Registry) and deploy them from your orchestrator as usual.

## Fonts and languages

Online: https://docs.html-to-pdf.net/anywhere-library/html/Fonts-And-Languages.htm

HTML is rendered by a Chromium engine, which takes the fonts a page asks for from the computer it runs on, and text the library draws itself (headers, footers, text elements) uses fonts from the same place. On Windows and macOS that just works: both come with fonts for every major script. A Linux server, and almost every Linux container image, comes with few fonts or none.

ExpertPdf Anywhere brings three things to deal with that, from the smallest to the most complete: fonts built into the library, the **ExpertPdf.Anywhere.Fonts** package, and your application's own `fonts` folder.

### Fonts built into the library

The library carries the Liberation fonts (Sans, Serif and Mono, regular, bold and italic), which have the same measurements as Arial, Times New Roman and Courier New. When the computer has no font for one of those families, or for the generic families sans-serif, serif and monospace, these are used, so a page in a Western European language prints correctly on any Linux host, even one with no fonts at all.

They cover Latin, Greek and Cyrillic text only. Without other fonts, every other script prints as empty boxes.

### The ExpertPdf.Anywhere.Fonts package

The **ExpertPdf.Anywhere.Fonts** package contains Google's Noto fonts (SIL Open Font License 1.1): Sans, Serif and Mono for every script Noto covers - Latin, Greek, Cyrillic, Arabic, Hebrew, the Indic and Southeast Asian scripts, Georgian, Armenian, Ethiopic, Tibetan and more - plus Noto Sans CJK for Chinese, Japanese and Korean (regular and bold) and Noto Color Emoji. About 270 files and 92 MB.

Reference it next to the other packages (a platform package such as **ExpertPdf.Anywhere.Linux** does not include it):

```xml
<ItemGroup>
  <PackageReference Include="ExpertPdf.Anywhere" Version="22.0.0" />
  <PackageReference Include="ExpertPdf.Anywhere.Native.linux-x64"
                    Version="22.0.0" />
  <PackageReference Include="ExpertPdf.Anywhere.Fonts" Version="22.0.0" />
</ItemGroup>
```

On build and publish the fonts are copied to `fonts/noto` next to the application, where the Chromium engine and the library find them; nothing has to be installed on the server and no setting is needed. They are used only for text the computer has no font for: fonts installed on the computer still win.

- Use it for every deployment to Linux - containers, AWS Lambda, Azure App Service for Linux, Google Cloud Run - unless you know the target has the fonts your pages need.
- The fonts are not copied when the application is built for a Windows runtime identifier (`win-x64`, `win-x86`), because Windows has these scripts already. To leave them out of any other build, set the `ExpertPdfFonts` property to `false`.

### Your own fonts

When your HTML asks for a font by name - a corporate font, for example - that the server does not have, put the font files (.ttf, .otf, .ttc) in a `fonts` folder of your project and have them copied to the output:

```xml
<ItemGroup>
  <None Include="fonts\**" CopyToOutputDirectory="PreserveNewest"
        CopyToPublishDirectory="PreserveNewest" />
</ItemGroup>
```

The folder ends up next to the application, and ExpertPdf Anywhere uses its fonts on every platform, with or without the fonts package (whose files go to the `noto` subfolder of the same folder):

- **Linux** - the Chromium engine is pointed at the folder, so the fonts behave exactly like installed fonts.
- **Windows and macOS** - Chromium looks only at the fonts installed on the computer there, so ExpertPdf Anywhere offers each font of the folder (outside `noto`) to the page as a web font. A page asking for the family by name gets it. Fonts over 16 MB, and fonts past 32 MB in total, are not offered.
- **Text the library draws** - a font named in a header or footer text, a `Font` added to a document, and `SystemFonts` find the families of the folder too, after the fonts installed on the computer.

With the font in place, nothing else changes in the code:

```csharp
// fonts/LobsterTwo-Regular.otf is copied next to the application
PdfConverter pdfConverter = new PdfConverter();

// text drawn by the library finds the font too
pdfConverter.PdfDocumentOptions.ShowHeader = true;
pdfConverter.PdfHeaderOptions.HeaderHeight = 40;
pdfConverter.PdfHeaderOptions.HeaderText = "Monthly report";
pdfConverter.PdfHeaderOptions.HeaderTextFontName = "Lobster Two";
pdfConverter.PdfHeaderOptions.HeaderTextFontSize = 14;

// and so does the page
pdfConverter.SavePdfFromHtmlStringToFile(
    "<p style=\"font-family: 'Lobster Two'\">Hello</p>", "report.pdf");
```

```vbnet
' fonts/LobsterTwo-Regular.otf is copied next to the application
Dim pdfConverter As New PdfConverter()

' text drawn by the library finds the font too
pdfConverter.PdfDocumentOptions.ShowHeader = True
pdfConverter.PdfHeaderOptions.HeaderHeight = 40
pdfConverter.PdfHeaderOptions.HeaderText = "Monthly report"
pdfConverter.PdfHeaderOptions.HeaderTextFontName = "Lobster Two"
pdfConverter.PdfHeaderOptions.HeaderTextFontSize = 14

' and so does the page
pdfConverter.SavePdfFromHtmlStringToFile(
    "<p style=""font-family: 'Lobster Two'"">Hello</p>", "report.pdf")
```

> **Note:** Check that the license of each font allows you to distribute it with your application.

### Installing fonts in a container image instead

Fonts installed in a Linux image the usual way (`apt-get install fonts-noto-core fonts-noto-cjk` on Debian and Ubuntu, `dnf install google-noto-cjk-fonts` on Amazon Linux) are used too, even on minimal images that do not include fontconfig's own configuration. The fonts package does the same without a package manager, which also covers platforms where the image cannot be changed, such as Azure App Service for Linux.

## Cloud deployments

Online: https://docs.html-to-pdf.net/anywhere-library/html/Cloud-Deployments.htm

ExpertPdf Anywhere runs on the main cloud platforms of Microsoft, Amazon and Google. The conversion code is the same everywhere; what differs is how the application is packaged and how much processor and memory the platform gives it. Each page below walks through one platform step by step, with the settings that matter and what to expect there.

### Supported platforms

| Platform | Operating system | How ExpertPdf Anywhere is deployed |
|---|---|---|
| "Azure App Service" (installation-and-deployment.md) | Windows, Linux | a web application (zip deploy or Web Deploy) |
| "Azure Functions" (installation-and-deployment.md) | Windows, Linux | a function app on a Dedicated (App Service) or Premium plan |
| "Azure Container Apps" (installation-and-deployment.md) | Linux | a container image |
| "AWS Lambda" (installation-and-deployment.md) | Linux (x64, arm64) | a container image |
| "AWS Elastic Beanstalk" (installation-and-deployment.md) | Windows (IIS), Linux | a source bundle of the web application |
| "Google Cloud Run" (installation-and-deployment.md) | Linux | a container image, or a Cloud Run function |

Each of them was tested with the same set of live web sites and with pages in English, Arabic, Hebrew, Hindi, Thai, Chinese, Japanese and Korean, every PDF compared with a conversion of the same page on a local Windows computer.

Any service that runs a Linux or Windows container also runs ExpertPdf Anywhere - see "Docker" (installation-and-deployment.md) - and so does any virtual machine (Azure Virtual Machines, Amazon EC2, Google Compute Engine). The Chromium engine can also run in a container of its own, next to the application or shared by several of them - see "The remote engine" (installation-and-deployment.md).

### Not supported

- **Azure Functions Consumption and Flex Consumption plans** - see "Azure Functions" (installation-and-deployment.md).
- **Azure App Service Free and Shared plans** - they do not allow the engine processes.
- **Alpine (musl) Linux images running the engines themselves** - the native engines need a glibc-based distribution. An application on Alpine converts through the remote engine container instead: see "The remote engine" (installation-and-deployment.md).

### What every platform needs

- **The engines for the platform** - the platform package **ExpertPdf.Anywhere.Linux** (or **ExpertPdf.Anywhere.Linux.Arm64**) on Linux and **ExpertPdf.Anywhere.Windows** on Windows, or the managed **ExpertPdf.Anywhere** package with the native package **ExpertPdf.Anywhere.Native.linux-x64** (**linux-arm64**, **win-x64**, **win-x86**). Nothing is installed on the server: no system packages, no display server, no `chmod`.
- **Fonts on Linux** - the **ExpertPdf.Anywhere.Fonts** package; see "Fonts and languages" (installation-and-deployment.md).
- **Processor and memory** - HTML conversion runs a complete Chromium browser. Plan for 2 virtual CPUs and 2 to 4 GB of memory per instance; a heavy page needs more than 2 GB.
- **A writable, unpacked deployment** - the engine cannot be started from inside a mounted package (Azure's Run From Package on Windows, Flex Consumption).

An instance keeps the Chromium engine loaded between conversions, so a conversion on a warm instance takes about as long as on a developer computer. The first conversion of a new instance takes longer while the engine starts; platforms that stop idle instances (AWS Lambda, and container services scaled to zero) pay that again after a quiet period.

An instance converts up to `PdfConverter.PdfConverterConcurrencyLevel` pages at once (8 by default); set a platform's per-instance concurrency to the same number.

The conversion code itself does not change:

```csharp
// the conversion code is the same on every platform
PdfConverter pdfConverter = new PdfConverter();
byte[] pdf = pdfConverter.GetPdfBytesFromUrl("https://www.example.com");
```

```vbnet
' the conversion code is the same on every platform
Dim pdfConverter As New PdfConverter()
Dim pdf As Byte() = pdfConverter.GetPdfBytesFromUrl("https://www.example.com")
```

## Azure App Service

Online: https://docs.html-to-pdf.net/anywhere-library/html/Azure-App-Service.htm

ExpertPdf Anywhere runs on Azure App Service on Windows and on Linux, on the Basic plan or above, with the complete feature set. Free and Shared plans are not supported. The web application is deployed as it is: nothing is installed on the server and no setting is required.

### Packages

| App Service | Packages | Publish |
|---|---|---|
| Windows | **ExpertPdf.Anywhere.Windows** (or **ExpertPdf.Anywhere** and **ExpertPdf.Anywhere.Native.win-x64**) | Web Deploy or zip deploy |
| Linux | **ExpertPdf.Anywhere.Linux** (or **ExpertPdf.Anywhere** and **ExpertPdf.Anywhere.Native.linux-x64**), **ExpertPdf.Anywhere.Fonts** | zip deploy (Web Deploy is not available on Linux) |

The Linux image of App Service has no fonts for Chinese, Japanese and Korean, and a code deployment cannot install any, so reference the fonts package for Linux. A Linux application can be built and published from Windows: the native package carries every library the engine needs, and ExpertPdf Anywhere restores the engine's execute permission, which a zip deployment loses.

### Step by step

The steps use the Azure CLI; the Azure portal offers the same choices (Create a resource > Web App). An App Service plan is created for Windows or for Linux and cannot be changed later.

**1. Create a resource group and a plan.** Premium v3 gives the fastest conversions (see Performance below); add `--is-linux` for a Linux plan:

```text
az login
az group create --name expertpdf-rg --location westeurope
az appservice plan create --resource-group expertpdf-rg \
    --name expertpdf-plan --sku P1v3 --is-linux
```

**2. Create the web app** with the .NET 10 runtime - `DOTNETCORE:10.0` on Linux, `dotnet:10` on Windows:

```text
az webapp create --resource-group expertpdf-rg --plan expertpdf-plan \
    --name expertpdf-web --runtime "DOTNETCORE:10.0"
```

**3. Apply two settings.** **Always On** keeps the application loaded while idle, so the Chromium engine stays warm and a request never meets a starting worker. On Windows, also use a **64-bit** worker process: a new Windows web app starts as 32-bit.

```text
az webapp config set --resource-group expertpdf-rg --name expertpdf-web \
    --always-on true --use-32bit-worker-process false
```

**4. Publish and deploy** the application as a zip file:

```text
dotnet publish -c Release -o publish
cd publish && zip -r ../app.zip . && cd ..
az webapp deploy --resource-group expertpdf-rg --name expertpdf-web \
    --src-path app.zip --type zip
```

On Windows, create the zip with `Compress-Archive publish\* app.zip`. From Visual Studio, use Publish > Azure > Azure App Service (Windows or Linux) instead.

**5. Open the application** at `https://expertpdf-web.azurewebsites.net`.

### Do not use Run From Package on Windows

> **Important:** With the **WEBSITE_RUN_FROM_PACKAGE** setting, App Service mounts the deployment zip as wwwroot instead of extracting it, and the engine cannot be started from that mount. Every conversion then fails with *"Chromium engine process failed (exit code -1073741515) and no output JSON was produced"* - the Windows status STATUS_DLL_NOT_FOUND: the engine's libraries are in the package, but cannot be loaded from it.
> Remove the setting and deploy normally; zip deploy and Web Deploy extract the application into a writable wwwroot. In Visual Studio, leave **Run from package file** unselected in the publish profile.

If Run From Package has to stay, copy the engine folder (`Chromium-CEF-154.0.28`) to a writable folder such as `%HOME%\site\engine` when the application starts, and set `PdfConverter.CefEnginePath` to the copy. App Service for Linux ignores the setting for a code application, so Linux is not affected.

### Performance and choosing a plan

HTML conversion runs a complete browser and is processor bound, so the plan decides the speed:

| Plan | Use |
|---|---|
| **Premium v3** (P1v3 and up) | the plan to choose when conversion time matters |
| **Basic** or **Standard** (for example S1) | occasional or background conversions |

Tested in West Europe on Windows and Linux. An S1 instance has a single virtual core; Premium v3 gives a conversion more processor.

Leave `PdfConverter.WebGlEnabled` off (the default) unless your pages draw with WebGL.

### Windows worker bitness

A 32-bit worker process can use the win-x64 package: the engine then runs in a process of its own. With the **win-x86** package a 32-bit worker keeps the engine loaded. A 64-bit worker with win-x64 is the recommended combination.

### Sample

Deploy the web sample of the ExpertPdf Anywhere samples (`windows/Web` or `linux/Web`) to try it: it converts pages, HTML and files with every option of the library.

## Azure Functions

Online: https://docs.html-to-pdf.net/anywhere-library/html/Azure-Functions.htm

ExpertPdf Anywhere runs in Azure Functions (version 4, .NET isolated worker) on Windows and on Linux, on a **Dedicated (App Service) plan** of Basic or above, or on an **Elastic Premium** plan.

### Hosting plans

| Plan | ExpertPdf Anywhere |
|---|---|
| Dedicated (App Service), Basic or above | supported, Windows and Linux |
| Elastic Premium (EP1 and up) | supported, Windows and Linux |
| Consumption (Windows) | **not supported** |
| Flex Consumption (Linux) | **not supported** |

A Windows Consumption plan cannot run the engine: the deployment does not fit the disk space the plan gives an application, Run From Package mounts the engine where it cannot be loaded, and the plan's sandbox stops the engine even when it is copied elsewhere. Flex Consumption always runs the application from the mounted, read-only package.

### Packages

The same packages as a web application: **ExpertPdf.Anywhere.Windows** on Windows, or **ExpertPdf.Anywhere.Linux** and **ExpertPdf.Anywhere.Fonts** on Linux (or the managed **ExpertPdf.Anywhere** package with **ExpertPdf.Anywhere.Native.win-x64** or **ExpertPdf.Anywhere.Native.linux-x64**).

### Step by step

**1. Create a resource group, a storage account and a plan** (`--is-linux` for Linux; `--sku EP1` for Elastic Premium):

```text
az login
az group create --name expertpdf-rg --location westeurope
az storage account create --resource-group expertpdf-rg \
    --name expertpdfstorage --sku Standard_LRS
az functionapp plan create --resource-group expertpdf-rg \
    --name expertpdf-fnplan --sku P1v3 --is-linux
```

**2. Create the function app** on that plan, with Always On (a Dedicated plan option):

```text
az functionapp create --resource-group expertpdf-rg \
    --name expertpdf-fn --plan expertpdf-fnplan \
    --storage-account expertpdfstorage --functions-version 4 \
    --runtime dotnet-isolated --runtime-version 10 --os-type Linux
az functionapp config set --resource-group expertpdf-rg \
    --name expertpdf-fn --always-on true
```

On Windows use `--os-type Windows` and also pass `--use-32bit-worker-process false` to the second command.

**3. Publish and deploy**:

```text
dotnet publish -c Release -o publish
cd publish && zip -r ../fn.zip . && cd ..
az functionapp deployment source config-zip --resource-group expertpdf-rg \
    --name expertpdf-fn --src fn.zip
```

A function app on a Dedicated plan does not use Run From Package, and the zip deployment does not turn it on, so the engine runs from a writable folder as in a web application. Do not set **WEBSITE_RUN_FROM_PACKAGE** (see "Azure App Service" (installation-and-deployment.md)).

### The function

A function that converts the page given in the query string:

```csharp
[Function("Convert")]
public static HttpResponseData Run(
    [HttpTrigger(AuthorizationLevel.Function, "get")] HttpRequestData req)
{
    string url = HttpUtility.ParseQueryString(req.Url.Query)["url"];
    PdfConverter pdfConverter = new PdfConverter();
    byte[] pdf = pdfConverter.GetPdfBytesFromUrl(url);

    HttpResponseData response = req.CreateResponse(HttpStatusCode.OK);
    response.Headers.Add("Content-Type", "application/pdf");
    response.Body.Write(pdf, 0, pdf.Length);
    return response;
}
```

```vbnet
<[Function]("Convert")>
Public Shared Function Run(
    <HttpTrigger(AuthorizationLevel.Function, "get")> req As HttpRequestData
) As HttpResponseData
    Dim url As String = HttpUtility.ParseQueryString(req.Url.Query)("url")
    Dim pdfConverter As New PdfConverter()
    Dim pdf As Byte() = pdfConverter.GetPdfBytesFromUrl(url)

    Dim response As HttpResponseData = req.CreateResponse(HttpStatusCode.OK)
    response.Headers.Add("Content-Type", "application/pdf")
    response.Body.Write(pdf, 0, pdf.Length)
    Return response
End Function
```

The ExpertPdf Anywhere samples include a complete Azure Functions project for Windows and for Linux (`windows/Functions`, `linux/Functions`).

### What to expect

Function apps run on the same machines as web applications, so the plan advice of "Azure App Service" (installation-and-deployment.md) applies: choose Premium v3 when conversion time matters, and Basic or Standard for occasional or background conversions.

## Azure Container Apps

Online: https://docs.html-to-pdf.net/anywhere-library/html/Azure-Container-Apps.htm

Azure Container Apps runs container images and scales them with the traffic, down to zero when idle. ExpertPdf Anywhere runs there in a Linux image built from the standard `mcr.microsoft.com/dotnet/aspnet` image, as its non-root user, with nothing installed.

### The image

Reference the packages in the application:

```xml
<ItemGroup>
  <PackageReference Include="ExpertPdf.Anywhere.Linux" Version="22.0.0" />
  <PackageReference Include="ExpertPdf.Anywhere.Fonts" Version="22.0.0" />
</ItemGroup>
```

and publish it into a .NET image:

```text
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish MyApp.csproj -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:10.0
WORKDIR /app
COPY --from=build /app .
USER app
EXPOSE 8080
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

See "Docker" (installation-and-deployment.md) for more about images.

### Step by step

**1. Prepare the subscription** (once):

```text
az login
az extension add --name containerapp --upgrade
az provider register --namespace Microsoft.App --wait
az provider register --namespace Microsoft.OperationalInsights --wait
```

**2. Create a resource group and a container registry, and push the image** (the registry name is global):

```text
az group create --name expertpdf-rg --location westeurope
az acr create --resource-group expertpdf-rg --name expertpdfregistry \
    --sku Basic --admin-enabled true
az acr login --name expertpdfregistry
docker build --platform linux/amd64 \
    -t expertpdfregistry.azurecr.io/myapp:1 .
docker push expertpdfregistry.azurecr.io/myapp:1
```

**3. Create the environment** and wait until it reports `Succeeded`, which can take several minutes:

```text
az containerapp env create --name expertpdf-env \
    --resource-group expertpdf-rg --location westeurope
az containerapp env show --name expertpdf-env --resource-group expertpdf-rg \
    --query properties.provisioningState
```

**4. Create the container app**:

```text
az containerapp create --name expertpdf-app --resource-group expertpdf-rg \
    --environment expertpdf-env \
    --image expertpdfregistry.azurecr.io/myapp:1 \
    --registry-server expertpdfregistry.azurecr.io \
    --target-port 8080 --ingress external --cpu 2 --memory 4Gi \
    --min-replicas 0 --max-replicas 3 \
    --scale-rule-name http --scale-rule-type http \
    --scale-rule-http-concurrency 8
```

- `--cpu 2 --memory 4Gi` is the largest consumption size; Chromium needs both.
- `--scale-rule-http-concurrency 8` starts another replica when one is converting 8 pages, the number ExpertPdf Anywhere converts at once by default.
- `--min-replicas 0` scales to zero when idle, and the first request then waits for a replica to start. Use `--min-replicas 1` to keep one ready.

### What to expect

Tested (West Europe, consumption profile, 2 vCPU / 4 GiB): every conversion succeeded and every language rendered.

### Sample

The ExpertPdf Anywhere samples include this deployment, with a step-by-step README, in the `cloud/azure-container-apps` folder. The `cloud/remote-engine/azure-container-apps` folder runs the Chromium engine in a container app of its own instead (see "The remote engine" (installation-and-deployment.md)).

## Amazon Web Services

Online: https://docs.html-to-pdf.net/anywhere-library/html/Amazon-Web-Services.htm

ExpertPdf Anywhere runs on Amazon Web Services on Linux (x64 and arm64/Graviton) and on Windows.

### Choosing an AWS service

| Service | Use it for | ExpertPdf Anywhere |
|---|---|---|
| "AWS Lambda" (installation-and-deployment.md) | functions, paying per conversion | a container image, x64 or arm64 |
| "AWS Elastic Beanstalk" (installation-and-deployment.md) | web applications on managed servers | Windows Server (IIS) and Amazon Linux 2023 |
| Amazon ECS, EKS, App Runner | container images | the images described in "Docker" (installation-and-deployment.md) |
| Amazon EC2 | full control of the server | Windows and Linux, no restrictions |

On Linux, reference **ExpertPdf.Anywhere.Linux** (or **ExpertPdf.Anywhere.Linux.Arm64** for Graviton) and **ExpertPdf.Anywhere.Fonts**; on Windows, **ExpertPdf.Anywhere.Windows**.

On Amazon ECS the Chromium engine can also run as a service of its own that the application calls - the `cloud/remote-engine/aws-ecs` folder of the samples sets it up; see "The remote engine" (installation-and-deployment.md).

### AWS free plan accounts

An account on the AWS free plan can start only the instance types the plan allows. Elastic Beanstalk and EC2 then refuse types such as `t3.large` (the environment never gets an instance); `m7i-flex.large` (2 vCPU, 8 GB) is allowed and suits ExpertPdf Anywhere.

## AWS Lambda

Online: https://docs.html-to-pdf.net/anywhere-library/html/AWS-Lambda.htm

ExpertPdf Anywhere runs in AWS Lambda as a function packaged in a **container image**, on x86_64 and on arm64. A zip deployment is not possible: the Chromium engine and its libraries are larger than the 250 MB a zip-based function can hold.

### The function and its image

Use the Lambda .NET base image and the packages for the function's architecture:

```xml
<ItemGroup>
  <PackageReference Include="Amazon.Lambda.Core" Version="3.3.0" />
  <PackageReference Include="Amazon.Lambda.APIGatewayEvents" Version="3.0.1" />
  <PackageReference Include="Amazon.Lambda.Serialization.SystemTextJson"
                    Version="3.0.1" />
  <PackageReference Include="ExpertPdf.Anywhere.Linux" Version="22.0.0" />
  <PackageReference Include="ExpertPdf.Anywhere.Fonts" Version="22.0.0" />
</ItemGroup>
```

```text
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish MyFunction.csproj -c Release -r linux-x64 \
    --self-contained false -o /app

FROM public.ecr.aws/lambda/dotnet:10
COPY --from=build /app ${LAMBDA_TASK_ROOT}
CMD ["MyFunction::MyFunction.Function::Handler"]
```

The function runs as Lambda's own non-root user; the native package makes the engine executable for every user when the image is built, so the Dockerfile needs no `chmod`. Lambda images have no fonts, which is why the fonts package is referenced.

A handler that converts the page given in the query string of a function URL or HTTP API request:

```csharp
public APIGatewayHttpApiV2ProxyResponse Handler(
    APIGatewayHttpApiV2ProxyRequest request, ILambdaContext context)
{
    PdfConverter pdfConverter = new PdfConverter();
    byte[] pdf = pdfConverter.GetPdfBytesFromUrl(
        request.QueryStringParameters["url"]);
    return new APIGatewayHttpApiV2ProxyResponse
    {
        StatusCode = 200,
        Headers = new Dictionary<string, string> {
            ["Content-Type"] = "application/pdf" },
        Body = Convert.ToBase64String(pdf),
        IsBase64Encoded = true
    };
}
```

```vbnet
Public Function Handler(request As APIGatewayHttpApiV2ProxyRequest,
                        context As ILambdaContext
) As APIGatewayHttpApiV2ProxyResponse
    Dim pdfConverter As New PdfConverter()
    Dim pdf As Byte() =
        pdfConverter.GetPdfBytesFromUrl(request.QueryStringParameters("url"))
    Return New APIGatewayHttpApiV2ProxyResponse With {
        .StatusCode = 200,
        .Headers = New Dictionary(Of String, String) From {
            {"Content-Type", "application/pdf"}},
        .Body = Convert.ToBase64String(pdf),
        .IsBase64Encoded = True
    }
End Function
```

### Step by step

You need the AWS CLI (`aws configure`) and Docker. The commands use region `eu-north-1` and account `123456789012`; replace both.

**1. Build the image.** `--provenance=false --sbom=false` matter: Lambda refuses the multi-manifest images Docker builds by default ("The image manifest, config or layer media type ... is not supported").

```text
docker build --platform linux/amd64 --provenance=false --sbom=false \
    -t expertpdf-lambda .
```

**2. Push it to Amazon ECR:**

```text
aws ecr create-repository --repository-name expertpdf-lambda
aws ecr get-login-password | docker login --username AWS \
    --password-stdin 123456789012.dkr.ecr.eu-north-1.amazonaws.com
docker tag expertpdf-lambda \
    123456789012.dkr.ecr.eu-north-1.amazonaws.com/expertpdf-lambda:1
docker push 123456789012.dkr.ecr.eu-north-1.amazonaws.com/expertpdf-lambda:1
```

**3. Create an execution role** - in the console, IAM > Roles > Create role > AWS service > Lambda, with the **AWSLambdaBasicExecutionRole** policy.

**4. Create the function** with 2048 MB of memory (Lambda gives processor time in proportion to memory) and a 120 second timeout:

```text
IMAGE=123456789012.dkr.ecr.eu-north-1.amazonaws.com/expertpdf-lambda:1
aws lambda create-function --function-name expertpdf-lambda \
    --package-type Image --memory-size 2048 --timeout 120 \
    --code ImageUri=$IMAGE \
    --role arn:aws:iam::123456789012:role/expertpdf-lambda-role
```

**5. Give it a URL** (`AWS_IAM` requires signed requests; `NONE` makes it public):

```text
aws lambda create-function-url-config --function-name expertpdf-lambda \
    --auth-type AWS_IAM
```

**arm64 (Graviton)**: reference **ExpertPdf.Anywhere.Linux.Arm64** instead of **ExpertPdf.Anywhere.Linux**, publish with `-r linux-arm64`, build with `--platform linux/arm64` and create the function with `--architectures arm64`. arm64 is cheaper per second and converts at a similar speed.

### Response size

A Lambda response is limited to 6 MB and the PDF travels base64-encoded in it, so about 4.4 MB of PDF fits. Store larger documents in an Amazon S3 bucket and return a presigned link instead; the sample does this when its `PDF_BUCKET` environment variable names a bucket.

### What to expect

Tested (eu-north-1, 2048 MB): every conversion succeeded on x86_64 and arm64, with every language rendered and the pages matching a Windows conversion.

Lambda replaces an environment after a few idle minutes, and the first conversion of a new environment takes longer, because Chromium starts with it. Provisioned concurrency keeps environments ready when that matters.

### Sample

The ExpertPdf Anywhere samples include this deployment, with a step-by-step README, in the `cloud/aws-lambda` folder.

## AWS Elastic Beanstalk

Online: https://docs.html-to-pdf.net/anywhere-library/html/AWS-Elastic-Beanstalk.htm

AWS Elastic Beanstalk runs a web application on EC2 instances it manages. ExpertPdf Anywhere runs on both of its .NET platforms: **Windows Server running IIS** and **Amazon Linux 2023 running .NET**.

### The source bundle

Elastic Beanstalk deploys a zip file, the source bundle, whose shape depends on the platform.

**Windows Server / IIS.** Publish the application for `win-x64` - self-contained, so it does not depend on the .NET versions the platform installs - zip the publish folder as `site.zip`, and put it in the bundle next to an `aws-windows-deployment-manifest.json` that installs it as the root site:

```text
{
  "manifestVersion": 1,
  "deployments": {
    "aspNetCoreWeb": [ {
      "name": "myapp",
      "parameters": {
        "appBundle": "site.zip",
        "iisPath": "/",
        "iisWebSite": "Default Web Site"
      }
    } ]
  }
}
```

**Amazon Linux 2023.** Publish the application (framework-dependent, for the ".NET 10" platform) with the fonts package, add a `Procfile` with the command that starts it - the platform's nginx forwards to port 5000 - and zip the folder:

```text
web: dotnet MyApp.dll --urls http://0.0.0.0:5000
```

Create the zip with forward slashes in the paths (Linux reads a backslash as part of a file name): `zip -r`, or the sample's `build-bundles.ps1` on Windows.

### Step by step

1. In the AWS console, open Elastic Beanstalk > Create application.
2. Platform: **.NET on Windows Server** (Windows Server 2022, IIS 10.0) or **.NET 10 running on 64bit Amazon Linux 2023**.
3. Application code: Upload your code, and choose the bundle.
4. Presets: Single instance for a test, High availability for production.
5. Service access: let the console create the service role and the EC2 instance profile if you have none.
6. Instance type: at least 2 vCPU and 8 GB - for example `m7i-flex.large`, `m7i.large` or `t3.large` (on a free plan account, see "Amazon Web Services" (installation-and-deployment.md)).
7. Create the environment, wait until its health is Ok, and open its domain.

With the CLI, upload the bundle to S3 and use `aws elasticbeanstalk create-application-version` and `create-environment`; the sample's README has the commands.

### What to expect

Every conversion succeeded on both platforms, and the Windows output is identical to a conversion on a local Windows computer. The instance keeps the engine loaded between conversions; the first conversion after a deployment also starts the engine.

### Sample

The ExpertPdf Anywhere samples include this deployment, with a step-by-step README, in the `cloud/aws-elastic-beanstalk` folder. Its `build-bundles.ps1` and `build-bundles.sh` publish the web sample into both bundles.

## Google Cloud Run

Online: https://docs.html-to-pdf.net/anywhere-library/html/Google-Cloud-Run.htm

Google Cloud Run runs container images and scales them with the traffic. ExpertPdf Anywhere runs there in a Linux image built from the standard `mcr.microsoft.com/dotnet/aspnet` image, as its non-root user, in both execution environments, and also as a **Cloud Run function**.

### The image

Reference the packages in the application:

```xml
<ItemGroup>
  <PackageReference Include="ExpertPdf.Anywhere.Linux" Version="22.0.0" />
  <PackageReference Include="ExpertPdf.Anywhere.Fonts" Version="22.0.0" />
</ItemGroup>
```

and build the image as in "Azure Container Apps" (installation-and-deployment.md) or "Docker" (installation-and-deployment.md); Cloud Run passes the port in the `PORT` environment variable (8080 by default).

### Step by step

**1. Set up the project** (once): create a project in the Google Cloud console, link a billing account, install the gcloud CLI, then:

```text
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
gcloud config set run/region europe-north1
gcloud services enable run.googleapis.com artifactregistry.googleapis.com
gcloud artifacts repositories create expertpdf \
    --repository-format=docker --location=europe-north1
gcloud auth configure-docker europe-north1-docker.pkg.dev
```

**2. Build and push the image:**

```text
docker build --platform linux/amd64 \
    -t europe-north1-docker.pkg.dev/YOUR_PROJECT_ID/expertpdf/myapp .
docker push europe-north1-docker.pkg.dev/YOUR_PROJECT_ID/expertpdf/myapp
```

**3. Deploy:**

```text
gcloud run deploy myapp \
    --image europe-north1-docker.pkg.dev/YOUR_PROJECT_ID/expertpdf/myapp \
    --cpu 2 --memory 4Gi --timeout 300 --concurrency 8 \
    --no-allow-unauthenticated
```

- `--cpu 2 --memory 4Gi` - with 2 GiB a heavy page can run the instance out of memory; Cloud Run then restarts it and the request fails with HTTP 503.
- `--concurrency 8` - the number of pages ExpertPdf Anywhere converts at once by default.
- `--no-allow-unauthenticated` - callers need an identity token; use `--allow-unauthenticated` only for a public service.
- `--execution-environment gen2` is optional: ExpertPdf Anywhere runs in the default (first generation) environment and in the second generation alike.

### Cloud Run functions

The same code runs as a Cloud Run function (`gcloud functions deploy --gen2 --runtime dotnet8`, with the **Google.Cloud.Functions.Hosting** package and an `IHttpFunction` class). Google builds the function from source, restoring the ExpertPdf Anywhere packages like any other, and runs it on Ubuntu as a non-root user. Use at least `--memory 2Gi` and `--cpu 2`.

### What to expect

Tested (europe-north1, 2 vCPU): every conversion succeeded in both execution environments and as a function, with every language rendered. The first conversion of a new instance also starts the engine. Cloud Run keeps an idle instance for a while, and `--min-instances 1` keeps one ready. A response can be up to 32 MB, so large PDFs are returned directly.

### Sample

The ExpertPdf Anywhere samples include this deployment, with a step-by-step README, in the `cloud/google-cloud-run` folder. The `cloud/remote-engine/google-cloud-run` folder runs the Chromium engine as a Cloud Run service of its own instead (see "The remote engine" (installation-and-deployment.md)).
