# HTML to PDF conversion

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Overview

Online: https://docs.html-to-pdf.net/anywhere-library/html/HtmlToPdfOverview.htm

The HTML to PDF converter of ExpertPdf Anywhere converts web pages, HTML strings and HTML files to PDF documents and to images. It is part of the ExpertPdf.Anywhere library, which runs on .NET 8, .NET 9, .NET 10 and .NET 11 and on every framework that runs .NET Standard 2.0 libraries (.NET Framework 4.6.1 and later), on Windows (x64 and x86), Linux (x64 and ARM64) and macOS (Apple Silicon): ASP.NET Core and ASP.NET applications, console applications, services, desktop applications, containers and cloud functions. No printer driver is used. See Requirements.

The converter renders the HTML with Chromium, the engine of the Chrome browser - HTML5, CSS3, web fonts and JavaScript - and preserves the look of the page in the PDF, with selectable and searchable text, live links and embedded fonts. The engine ships in the NuGet packages, next to the application; it runs inside the application by default, or in a process of its own for each conversion. Report pages of your own application, with their charts, grids and styles, become PDF documents with a few lines of code.

### Quick start

Install the platform package of the machine that runs the application, for example **ExpertPdf.Anywhere.Windows** or **ExpertPdf.Anywhere.Linux** (see Installation), then:

```csharp
using ExpertPdf.Anywhere.HtmlToPdf;

PdfConverter converter = new PdfConverter();
converter.LicenseKey = "your license key";

// a web page to a PDF file
converter.SavePdfFromUrlToFile("https://www.example.com", "page.pdf");

// an HTML string to a PDF document in memory (to send it to a browser, store it,
// attach it to an email...)
byte[] pdfBytes = converter.GetPdfBytesFromHtmlString(
    "<h1>Hello</h1><p>From ExpertPdf</p>");
```

```vbnet
Imports ExpertPdf.Anywhere.HtmlToPdf

Dim converter As New PdfConverter()
converter.LicenseKey = "your license key"

' a web page to a PDF file
converter.SavePdfFromUrlToFile("https://www.example.com", "page.pdf")

' an HTML string to a PDF document in memory (to send it to a browser, store it,
' attach it to an email...)
Dim pdfBytes As Byte() = converter.GetPdfBytesFromHtmlString(
    "<h1>Hello</h1><p>From ExpertPdf</p>")
```

The default settings suit most documents. The converter has many options to control the result: the page size, orientation and margins, headers and footers, page breaks, bookmarks, security, the PDF standard and more - see Getting started and Features.

### What the converter does

- Converts a URL, an HTML string, an HTML file or an HTML stream to PDF bytes, a file, a stream, or a `Document` you can change before saving. See PdfConverter class.
- Converts the same sources to PNG, JPEG, BMP or TIFF images. See ImgConverter class.
- Asynchronous conversion methods with cancellation. See "Asynchronous conversions" (html-to-pdf.md).
- Headers and footers with text, images, HTML, Word documents and page numbers. See Headers and footers.
- Bookmarks, internal links and a table of contents with page numbers built from the headings of the page. See Automatic bookmarks.
- PDF/A, PDF/X-1a, tagged PDF, PDF/UA-1 and PDF/UA-2. See PDF standards and Tagged PDF and accessibility.
- ZUGFeRD / Factur-X electronic invoices. See Electronic invoices.
- HTML forms converted to fillable PDF form fields - see "HTML forms to PDF form fields" (html-to-pdf.md).
- The position in the PDF of any HTML element. See HTML elements mapping.
- HTML added anywhere in a PDF document, next to text, images and shapes. See HTML to PDF element.

## Getting started

Online: https://docs.html-to-pdf.net/anywhere-library/html/ConverterAPIOverview.htm

The converter is in the `HtmlToPdf` namespace. Its main classes:

- `PdfConverter` - converts a URL, an HTML string, an HTML file or stream to PDF: to bytes, a file, a stream, or a `Document` you can change before saving. See PdfConverter class.
- `ImgConverter` - converts the same sources to images, returned as the bytes of an image file. See ImgConverter class.
- The option objects of the converter: `PdfConverter.PdfDocumentOptions` (page size, orientation, margins, compression, header and footer switches), `PdfConverter.PdfHeaderOptions`, `PdfConverter.PdfFooterOptions`, `PdfConverter.PdfSecurityOptions`, `PdfConverter.PdfDocumentInfo`, `PdfConverter.PdfViewerPreferences`.

The HTML is rendered by Chromium, the only rendering engine of the library. Settings that apply to the whole process - whether the engine runs inside the application (the default) or in a process of its own for each conversion, and a log file - are on `GlobalSettings`.

### A conversion with the usual options

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.LicenseKey = "your license key";

// the page
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4;
pdfConverter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Portrait;
pdfConverter.PdfDocumentOptions.LeftMargin = 20;
pdfConverter.PdfDocumentOptions.RightMargin = 20;
pdfConverter.PdfDocumentOptions.TopMargin = 20;
pdfConverter.PdfDocumentOptions.BottomMargin = 20;

// a footer with the page numbers
pdfConverter.PdfDocumentOptions.ShowFooter = true;
pdfConverter.PdfFooterOptions.FooterText = "Quarterly report";
pdfConverter.PdfFooterOptions.ShowPageNumber = true;
pdfConverter.PdfFooterOptions.PageNumberText = "Page";

// the document information
pdfConverter.PdfDocumentInfo.Title = "Quarterly report";
pdfConverter.PdfDocumentInfo.AuthorName = "Example Inc.";

pdfConverter.SavePdfFromUrlToFile("https://www.example.com/report", "report.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.LicenseKey = "your license key"

' the page
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4
pdfConverter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Portrait
pdfConverter.PdfDocumentOptions.LeftMargin = 20
pdfConverter.PdfDocumentOptions.RightMargin = 20
pdfConverter.PdfDocumentOptions.TopMargin = 20
pdfConverter.PdfDocumentOptions.BottomMargin = 20

' a footer with the page numbers
pdfConverter.PdfDocumentOptions.ShowFooter = True
pdfConverter.PdfFooterOptions.FooterText = "Quarterly report"
pdfConverter.PdfFooterOptions.ShowPageNumber = True
pdfConverter.PdfFooterOptions.PageNumberText = "Page"

' the document information
pdfConverter.PdfDocumentInfo.Title = "Quarterly report"
pdfConverter.PdfDocumentInfo.AuthorName = "Example Inc."

pdfConverter.SavePdfFromUrlToFile("https://www.example.com/report", "report.pdf")
```

### Sending the PDF to the browser

In an ASP.NET Core controller or Razor Page, return the bytes as a file:

```csharp
public IActionResult DownloadReport()
{
    PdfConverter pdfConverter = new PdfConverter();
    byte[] pdfBytes = pdfConverter.GetPdfBytesFromUrl(
        "https://www.example.com/report");

    // "inline" shows the document in the browser; File(bytes, type, name) downloads
    // it
    Response.Headers["Content-Disposition"] = "inline; filename=report.pdf";
    return File(pdfBytes, "application/pdf");
}
```

```vbnet
Public Function DownloadReport() As IActionResult
    Dim pdfConverter As New PdfConverter()
    Dim pdfBytes As Byte() = pdfConverter.GetPdfBytesFromUrl(
        "https://www.example.com/report")

    ' "inline" shows the document in the browser; File(bytes, type, name) downloads
    ' it
    Response.Headers("Content-Disposition") = "inline; filename=report.pdf"
    Return File(pdfBytes, "application/pdf")
End Function
```

In an ASP.NET Core application the conversion can run without blocking a request thread with the asynchronous methods, for example `PdfConverter.GetPdfBytesFromUrlAsync(string, CancellationToken)` (see PdfConverter class).

To convert a page of your own application with the data of the current user, convert its HTML: render the view to a string and call GetPdfBytesFromHtmlString with the base URL of the site (see the FAQ), or pass the authentication cookie of the user with `PdfConverter.HttpCookies`.

## The PdfConverter class

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfConverterClass.htm

`PdfConverter` defines a set of methods to render a PDF document from a URL, an HTML string, an HTML file or an HTML stream. The result can be an array of bytes, a file, a stream or a `Document` object. The generated PDF document can be further changed, saved to a file, stored in a database or sent as the HTTP response to a browser.

### PdfConverter Save/Render Methods

The methods below convert the URL given as parameter to a PDF document returned as an array of bytes. The URL can also be the full path of an HTML file on the local file system. The bytes can be sent to a browser, saved to a file or a database, or sent by email as an attachment. The *internalLinksDocUrl* parameter is the full URL of the document the links with anchors (#name) point to; the links to that document become links inside the PDF.

```csharp
public byte[] GetPdfBytesFromUrl(string url);
public byte[] GetPdfBytesFromUrl(string url, string internalLinksDocUrl);
```

```vbnet
Public Function GetPdfBytesFromUrl(url As String) As Byte()
Public Function GetPdfBytesFromUrl(url As String,
    internalLinksDocUrl As String) As Byte()
```

To convert an HTML string to PDF use one of the methods below. The first one renders the HTML string as it is. The second one has the additional parameter *urlBase*, the full URL of the page the HTML string comes from. The converter uses it to resolve the images, CSS and JavaScript files referenced by relative URLs; without it those resources are not loaded. With an http or https base URL the HTML string is loaded as the page at that URL, so web fonts, scripts and cookies of that site work as they do when the URL itself is converted.

```csharp
public byte[] GetPdfBytesFromHtmlString(string htmlString);
public byte[] GetPdfBytesFromHtmlString(string htmlString, string urlBase);
```

```vbnet
Public Function GetPdfBytesFromHtmlString(htmlString As String) As Byte()
Public Function GetPdfBytesFromHtmlString(htmlString As String,
    urlBase As String) As Byte()
```

The corresponding methods that save the PDF document to a file are listed below.

```csharp
public void SavePdfFromUrlToFile(string url, string outFile);
public void SavePdfFromHtmlStringToFile(string htmlString, string outFile);
public void SavePdfFromHtmlStringToFile(string htmlString, string outFile,
    string urlBase);
```

```vbnet
Public Sub SavePdfFromUrlToFile(url As String, outFile As String)
Public Sub SavePdfFromHtmlStringToFile(htmlString As String, outFile As String)
Public Sub SavePdfFromHtmlStringToFile(htmlString As String, outFile As String,
    urlBase As String)
```

The methods with the *ToStream* suffix write the PDF document to a stream you provide - a file stream, the response stream of a web request - instead of returning the bytes:

```csharp
public void SavePdfFromUrlToStream(string url, Stream outPdfStream);
public void SavePdfFromUrlToStream(string url, string internalLinksDocUrl,
    Stream outPdfStream);
public void SavePdfFromHtmlFileToStream(string htmlFilePath, Stream outPdfStream);
public void SavePdfFromHtmlFileToStream(string htmlFilePath,
    string internalLinksDocUrl, Stream outPdfStream);
public void SavePdfFromHtmlStringToStream(string htmlString, Stream outPdfStream);
public void SavePdfFromHtmlStringToStream(string htmlString, string urlBase,
    Stream outPdfStream);
public void SavePdfFromHtmlStringToStream(string htmlString, string urlBase,
    string internalLinksDocUrl, Stream outPdfStream);
public void SavePdfFromHtmlStreamToStream(System.IO.Stream htmlStream,
    Encoding streamEncoding, Stream outPdfStream);
public void SavePdfFromHtmlStreamToStream(System.IO.Stream htmlStream,
    Encoding streamEncoding, string urlBase, Stream outPdfStream);
public void SavePdfFromHtmlStreamToStream(System.IO.Stream htmlStream,
    Encoding streamEncoding, string urlBase, string internalLinksDocUrl,
        Stream outPdfStream);
```

```vbnet
Public Sub SavePdfFromUrlToStream(url As String, outPdfStream As Stream)
Public Sub SavePdfFromUrlToStream(url As String, internalLinksDocUrl As String,
    outPdfStream As Stream)
Public Sub SavePdfFromHtmlFileToStream(htmlFilePath As String,
    outPdfStream As Stream)
Public Sub SavePdfFromHtmlFileToStream(htmlFilePath As String,
    internalLinksDocUrl As String, outPdfStream As Stream)
Public Sub SavePdfFromHtmlStringToStream(htmlString As String,
    outPdfStream As Stream)
Public Sub SavePdfFromHtmlStringToStream(htmlString As String, urlBase As String,
    outPdfStream As Stream)
Public Sub SavePdfFromHtmlStringToStream(htmlString As String, urlBase As String,
    internalLinksDocUrl As String, outPdfStream As Stream)
Public Sub SavePdfFromHtmlStreamToStream(htmlStream As System.IO.Stream,
    streamEncoding As Encoding, outPdfStream As Stream)
Public Sub SavePdfFromHtmlStreamToStream(htmlStream As System.IO.Stream,
    streamEncoding As Encoding, urlBase As String, outPdfStream As Stream)
Public Sub SavePdfFromHtmlStreamToStream(htmlStream As System.IO.Stream,
    streamEncoding As Encoding, urlBase As String, internalLinksDocUrl As String,
        outPdfStream As Stream)
```

A set of methods returns a `Document` object that can be changed before it is saved - new pages, text, images, HTML, a digital signature - as described in Post Convert Customization Overview. The `Document` class and the elements that can be added to it are in the `PdfDocument` namespace. Close the document when you are done with it (it is *IDisposable*, so a *using* statement does the same).

```csharp
public Document GetPdfDocumentObjectFromUrl(string url);
public Document GetPdfDocumentObjectFromUrl(string url, string internalLinksDocUrl);
public Document GetPdfDocumentObjectFromHtmlFile(string htmlFilePath);
public Document GetPdfDocumentObjectFromHtmlFile(string htmlFilePath,
    string internalLinksDocUrl);
public Document GetPdfDocumentObjectFromHtmlString(string htmlString);
public Document GetPdfDocumentObjectFromHtmlString(string htmlString,
    string urlBase);
public Document GetPdfDocumentObjectFromHtmlString(string htmlString,
    string urlBase, string internalLinksDocUrl);
public Document GetPdfDocumentObjectFromHtmlStream(System.IO.Stream htmlStream,
    Encoding streamEncoding);
public Document GetPdfDocumentObjectFromHtmlStream(System.IO.Stream htmlStream,
    Encoding streamEncoding, string urlBase);
public Document GetPdfDocumentObjectFromHtmlStream(System.IO.Stream htmlStream,
    Encoding streamEncoding, string urlBase, string internalLinksDocUrl);
```

```vbnet
Public Function GetPdfDocumentObjectFromUrl(url As String) As Document
Public Function GetPdfDocumentObjectFromUrl(url As String,
    internalLinksDocUrl As String) As Document
Public Function GetPdfDocumentObjectFromHtmlFile(htmlFilePath As String) As Document
Public Function GetPdfDocumentObjectFromHtmlFile(htmlFilePath As String,
    internalLinksDocUrl As String) As Document
Public Function GetPdfDocumentObjectFromHtmlString(htmlString As String) As Document
Public Function GetPdfDocumentObjectFromHtmlString(htmlString As String,
    urlBase As String) As Document
Public Function GetPdfDocumentObjectFromHtmlString(htmlString As String,
    urlBase As String, internalLinksDocUrl As String) As Document
Public Function GetPdfDocumentObjectFromHtmlStream(htmlStream As System.IO.Stream,
    streamEncoding As Encoding) As Document
Public Function GetPdfDocumentObjectFromHtmlStream(htmlStream As System.IO.Stream,
    streamEncoding As Encoding, urlBase As String) As Document
Public Function GetPdfDocumentObjectFromHtmlStream(htmlStream As System.IO.Stream,
    streamEncoding As Encoding, urlBase As String,
        internalLinksDocUrl As String) As Document
```

The methods with *WithTempFile* in their name, for example `PdfConverter.GetPdfBytesFromHtmlStringWithTempFile(string)`, first save the HTML to a temporary file and convert that file.

### Asynchronous methods

Each of the methods below returns a *Task*, and no thread waits while the page loads and renders, so the calling thread - a web request, a user interface - is free during the conversion. Cancelling the optional *CancellationToken* stops the conversion and completes the task as canceled. Do not change the converter, or start another conversion with it, before the task completes; use one converter for each conversion that runs at the same time.

```csharp
public Task<byte[]> GetPdfBytesFromUrlAsync(string url,
    CancellationToken cancellationToken = default(CancellationToken));
public Task<byte[]> GetPdfBytesFromHtmlFileAsync(string htmlFilePath,
    CancellationToken cancellationToken = default(CancellationToken));
public Task<byte[]> GetPdfBytesFromHtmlStringAsync(string htmlString,
    CancellationToken cancellationToken = default(CancellationToken));
public Task<byte[]> GetPdfBytesFromHtmlStringAsync(string htmlString,
    string urlBase, CancellationToken cancellationToken = default(
        CancellationToken));
public Task SavePdfFromUrlToFileAsync(string url, string outFile,
    CancellationToken cancellationToken = default(CancellationToken));
public Task SavePdfFromHtmlStringToFileAsync(string htmlString, string outFile,
    CancellationToken cancellationToken = default(CancellationToken));
public Task SavePdfFromHtmlStringToFileAsync(string htmlString, string outFile,
    string urlBase, CancellationToken cancellationToken = default(
        CancellationToken));
public Task SavePdfFromUrlToStreamAsync(string url, Stream outPdfStream,
    CancellationToken cancellationToken = default(CancellationToken));
public Task SavePdfFromHtmlStringToStreamAsync(string htmlString, string urlBase,
    Stream outPdfStream, CancellationToken cancellationToken = default(
        CancellationToken));
public Task<Document> GetPdfDocumentObjectFromUrlAsync(string url,
    CancellationToken cancellationToken = default(CancellationToken));
public Task<Document> GetPdfDocumentObjectFromHtmlStringAsync(string htmlString,
    string urlBase, CancellationToken cancellationToken = default(
        CancellationToken));
```

```vbnet
Public Function GetPdfBytesFromUrlAsync(url As String,
    Optional cancellationToken As CancellationToken = Nothing) As Task(Of Byte())
Public Function GetPdfBytesFromHtmlFileAsync(htmlFilePath As String,
    Optional cancellationToken As CancellationToken = Nothing) As Task(Of Byte())
Public Function GetPdfBytesFromHtmlStringAsync(htmlString As String,
    Optional cancellationToken As CancellationToken = Nothing) As Task(Of Byte())
Public Function GetPdfBytesFromHtmlStringAsync(htmlString As String,
    urlBase As String,
        Optional cancellationToken As CancellationToken = Nothing) As Task(Of Byte())
Public Function SavePdfFromUrlToFileAsync(url As String, outFile As String,
    Optional cancellationToken As CancellationToken = Nothing) As Task
Public Function SavePdfFromHtmlStringToFileAsync(htmlString As String,
    outFile As String,
        Optional cancellationToken As CancellationToken = Nothing) As Task
Public Function SavePdfFromHtmlStringToFileAsync(htmlString As String,
    outFile As String, urlBase As String,
        Optional cancellationToken As CancellationToken = Nothing) As Task
Public Function SavePdfFromUrlToStreamAsync(url As String, outPdfStream As Stream,
    Optional cancellationToken As CancellationToken = Nothing) As Task
Public Function SavePdfFromHtmlStringToStreamAsync(htmlString As String,
    urlBase As String, outPdfStream As Stream,
        Optional cancellationToken As CancellationToken = Nothing) As Task
Public Function GetPdfDocumentObjectFromUrlAsync(url As String,
    Optional cancellationToken As CancellationToken = Nothing) As Task(Of Document)
Public Function GetPdfDocumentObjectFromHtmlStringAsync(htmlString As String,
    urlBase As String,
        Optional cancellationToken As CancellationToken = Nothing) As Task(
            Of Document)
```

The image converter has the same pattern: `ImgConverter.GetImageBytesFromUrlAsync(string, ImageFormat, CancellationToken)`, GetImageBytesFromHtmlStringAsync and SaveImageFromUrlToFileAsync.

```csharp
using System.Threading;
using System.Threading.Tasks;

public async Task<byte[]> CreateReportAsync(string url,
    CancellationToken cancellationToken)
{
    PdfConverter converter = new PdfConverter();
    return await converter.GetPdfBytesFromUrlAsync(url, cancellationToken);
}
```

```vbnet
Public Async Function CreateReportAsync(url As String,
    cancellationToken As System.Threading.CancellationToken) _
    As System.Threading.Tasks.Task(Of Byte())
    Dim converter As New PdfConverter()
    Return Await converter.GetPdfBytesFromUrlAsync(url, cancellationToken)
End Function
```

### PdfConverter Configuration Properties

The conversion and the generated PDF document can be configured in many ways: the page size (A4, A3, Letter...), orientation and margins, compression, encryption and passwords, the document information (author, title, subject...), headers and footers with page numbers, and more. The main properties of the converter are described below.

The `PdfConverter.LicenseKey` property takes the license key you received after purchase. When it is not set, the converter runs in evaluation mode and a demo watermark is drawn on the generated document.

```csharp
public string LicenseKey { get; set; }
```

```vbnet
Public Property LicenseKey() As String
```

The `PdfConverter.PageWidth` and `PdfConverter.PageHeight` properties set the width and height, in pixels, of the browser window the page is rendered in - the same effect as resizing a browser window to those dimensions. The page is laid out at that width and then scaled to the width of the PDF page between the margins.

The default *PageWidth* is *1024 pixels*; 0 or a negative value also means 1024. The default *PageHeight* is *0*, and the whole page is converted, on as many PDF pages as it needs. A positive height sets the height of the browser window, which pages sized to the window (for example with CSS vh units) use; with `PdfConverter.ClipHtmlView` set to true the content below that height is left out of the PDF. Both values can also be passed to the `PdfConverter` constructor.

```csharp
public int PageWidth { get; set; }
public int PageHeight { get; set; }
```

```vbnet
Public Property PageWidth() As Integer
Public Property PageHeight() As Integer
```

The `PdfConverter.PdfDocumentOptions` property controls the generated PDF document: the margins, the page size and orientation, the compression and JPEG compression level, live links and internal links, single page conversion, documents to prepend or append, and whether the header and footer are shown. It exposes an object of type `PdfDocumentOptions`, created by the `PdfConverter` constructor: you set the properties of that object rather than the property itself. The same holds for the other option objects below.

```csharp
public PdfDocumentOptions PdfDocumentOptions { get; }
```

```vbnet
Public ReadOnly Property PdfDocumentOptions() As PdfDocumentOptions
```

The `PdfConverter.PdfSecurityOptions` property sets the user and owner passwords and the permissions of the generated document - printing, copying, editing and so on. It exposes an object of type `PdfSecurityOptions`. See Security.

```csharp
public PdfSecurityOptions PdfSecurityOptions { get; }
```

```vbnet
Public ReadOnly Property PdfSecurityOptions() As PdfSecurityOptions
```

The `PdfConverter.PdfDocumentInfo` property sets the description of the generated document - title, author, subject, keywords, creation date. It exposes an object of type `PdfDocumentInfo`. See Document information.

```csharp
public PdfDocumentInfo PdfDocumentInfo { get; }
```

```vbnet
Public ReadOnly Property PdfDocumentInfo() As PdfDocumentInfo
```

The `PdfConverter.PdfHeaderOptions` and `PdfConverter.PdfFooterOptions` properties set the content and the look of the header and the footer. The header and the footer are shown only when the `PdfDocumentOptions.ShowHeader` and `PdfDocumentOptions.ShowFooter` properties of `PdfConverter.PdfDocumentOptions` are true. They expose objects of type `PdfHeaderOptions` and `PdfFooterOptions`. See Headers and footers.

```csharp
public PdfHeaderOptions PdfHeaderOptions { get; }
public PdfFooterOptions PdfFooterOptions { get; }
```

```vbnet
Public ReadOnly Property PdfHeaderOptions() As PdfHeaderOptions
Public ReadOnly Property PdfFooterOptions() As PdfFooterOptions
```

Other properties used often:

- `PdfConverter.ConversionDelay`, `PdfConverter.NavigationTimeout` and `PdfConverter.WindowStatus` - when the page is ready to be converted. See JavaScript and page loading.
- `PdfConverter.HttpCookies`, `PdfConverter.HttpHeaders`, `PdfConverter.AuthenticationOptions` and `PdfConverter.LoginOptions` - access to pages that need a login. See Server authentication.
- `PdfConverter.PdfStandardSubset`, `PdfConverter.Tagged` and `PdfConverter.AccessibilityStandard` - PDF/A, PDF/X-1a, tagged PDF, PDF/UA-1 and PDF/UA-2. See PDF standards and Tagged PDF.
- `PdfConverter.PdfBookmarkOptions` and `PdfConverter.TableOfContentsOptions` - bookmarks and a table of contents built from the headings of the page. See Automatic bookmarks.
- `PdfConverter.ConversionSummary` - the pages and the positions of the content after a conversion. See Conversion summary.
- `PdfConverter.PdfConverterConcurrencyLevel` - how many conversions run at the same time in the process (8 by default). See Multithreading.

## Features

Online: https://docs.html-to-pdf.net/anywhere-library/html/Features.htm

The topics of this section describe the main features of the converter, with code for each one.

### Rendering

- Rendering engine - Chromium (HTML5, CSS3, web fonts, JavaScript), inside the application or in a process of its own.
- JavaScript and page loading - scripts, waiting for the page, injected CSS and JavaScript.
- Server authentication - HTTP authentication, login forms and cookies.
- Excluding elements, converting a part of the page.
- Fine control of the conversion - browser window size, content scaling, request data, page ranges, timeouts.
- Multithreading - many conversions in parallel.
- Asynchronous conversion with cancellation - see "Asynchronous conversions" (html-to-pdf.md).
- Start the conversion from JavaScript.
- HTML to image - PNG, JPEG, BMP and TIFF, see ImgConverter class.

### Pages and layout

- Page size, orientation and margins.
- Headers and footers with text, images, HTML, Word documents and page numbers.
- Page breaks and keep together.
- Repeated table headers.

### Navigation

- Automatic bookmarks, flat or nested.
- Automatic table of contents with page numbers and links, built from the headings of the page - see "Automatic table of contents" (links-bookmarks-toc.md).
- Internal links and live web links.
- HTML elements location in the PDF.

### The PDF document

- PDF standards - PDF/A-1a, 1b, 2a, 2b, 2u, 3a, 3b, 3u, 4, 4e, 4f, PDF/X-1a, associated files.
- Tagged PDF and accessibility - PDF/UA-1 and PDF/UA-2 (`PdfConverter.AccessibilityStandard`).
- HTML forms to fillable PDF form fields - see "HTML forms to PDF form fields" (html-to-pdf.md).
- ZUGFeRD / Factur-X electronic invoices.
- Security - passwords and permissions.
- Document information - title, author, keywords.
- Image compression and color space.
- Merging with existing PDF documents.
- Changing the generated document - add text, images, HTML, signatures.
- Conversion summary - the pages and positions of the result.

## Custom page size

Online: https://docs.html-to-pdf.net/anywhere-library/html/CustomPdfPage.htm

The converter can produce PDF documents with pages of any size. The page size is set with the `PdfDocumentOptions.PdfPageSize` property, of type `PdfPageSize`: a standard size like *A4* (the default), *A3* or *Letter*, or *Custom*. With *Custom*, the page size is given by the `PdfDocumentOptions.CustomPdfPageSize` property.

The code below sets the converter to produce pages 200 points wide and 300 points high. A point is 1/72 inch.

```csharp
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.Custom;
pdfConverter.PdfDocumentOptions.CustomPdfPageSize = new SizeF(200, 300);
```

```vbnet
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.Custom
pdfConverter.PdfDocumentOptions.CustomPdfPageSize = New SizeF(200, 300)
```

The custom size is 595 x 842 points by default, the size of an A4 portrait page. `PdfDocumentOptions.PdfPageOrientation` set to *Landscape* turns the page so that its longer side is horizontal. The margins are set with the LeftMargin, RightMargin, TopMargin and BottomMargin properties of the same object, in points.

`SizeF` is in the `Drawing` namespace.

## Document description

Online: https://docs.html-to-pdf.net/anywhere-library/html/DocumentDescription.htm

The document properties - author, title, subject, keywords, creation date - are set with the `PdfConverter.PdfDocumentInfo` property of the `PdfConverter` object. PDF readers show them in the document properties, and search tools index them.

```csharp
pdfConverter.PdfDocumentInfo.AuthorName = "ExpertPdf";
pdfConverter.PdfDocumentInfo.Title = "PDF Document Info";
pdfConverter.PdfDocumentInfo.Subject = "HTML to PDF Converter";
pdfConverter.PdfDocumentInfo.Keywords = "HTML, PDF, Converter";
pdfConverter.PdfDocumentInfo.CreatedDate = DateTime.Now;
```

```vbnet
pdfConverter.PdfDocumentInfo.AuthorName = "ExpertPdf"
pdfConverter.PdfDocumentInfo.Title = "PDF Document Info"
pdfConverter.PdfDocumentInfo.Subject = "HTML to PDF Converter"
pdfConverter.PdfDocumentInfo.Keywords = "HTML, PDF, Converter"
pdfConverter.PdfDocumentInfo.CreatedDate = DateTime.Now
```

The title, description and keywords of the HTML page itself are available after the conversion in `PdfConverter.HtmlDocumentInfo`, for example to copy the page title into the document properties of a document converted with `PdfConverter.GetPdfDocumentObjectFromUrl(string)`.

## Page breaks and keeping content together

Online: https://docs.html-to-pdf.net/anywhere-library/html/PageBreaks.htm

The Chromium engine paginates the HTML the way the Chrome browser does when it prints a page, and it honors the CSS properties that control the page breaks:

- page-break-before: always (or break-before: page)
- page-break-after: always (or break-after: page)
- page-break-inside: avoid (or break-inside: avoid)

For example, with the *page-break-after: always* style applied to an HTML element (an image, a paragraph, a section) you instruct the converter to start a new page right after that element.

A line of text is never cut between two pages: the engine moves it to the next page. An image or a block can be split across a page boundary; give it the *page-break-inside: avoid* style to keep it whole on one page.

The *page-break-inside: avoid* style is also how you keep a group of HTML elements together on the same page - a table row, a table, a div with a heading and its paragraph. This is very useful for reports. In the example below the table contains a large number of rows, each with an image on the left and a text on the right, and no row is split between two pages.

```xml
<table>
    <tr style="page-break-inside: avoid">
        <td>
            <img width="100" height="100" src="img1.jpg">
        </td>
        <td>
            My text 1
        </td>
    </tr>

    <tr style="page-break-inside: avoid">
        <td>
            <img width="100" height="100" src="img2.jpg">
        </td>
        <td>
            My text 2
        </td>
    </tr>
</table>
```

The styles can be in the page itself or added at conversion time with `PdfConverter.CustomCSS`, for example to start every chapter of a page you cannot change on a new page:

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.CustomCSS =
    "h1 { page-break-before: always; } img { break-inside: avoid; }";
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/book", "book.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.CustomCSS =
    "h1 { page-break-before: always; } img { break-inside: avoid; }"
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/book", "book.pdf")
```

## Repeating table headers

Online: https://docs.html-to-pdf.net/anywhere-library/html/RepeatHTMLTableHead.htm

When an HTML table spans several PDF pages, the converter repeats the head of the table at the top of each page the table continues on. This works for any table whose head is in a **thead** element, the way the Chrome browser repeats it when it prints a page; the **tfoot** element of a table is repeated at the bottom of each page in the same way.

The repetition comes from the **display: table-header-group** style, which is the default style of a thead element. If the page gives its table heads another display style, restore it:

```xml
thead { display: table-header-group; }
tfoot { display: table-footer-group; }
```

This is useful for long tables, so the reader of the document always knows which column the data on a page belongs to. The repeated head can contain anything a table head can - text, images or any other HTML. Combine it with *page-break-inside: avoid* on the rows (see "Page breaks and keeping content together" (html-to-pdf.md)) so that no row is split between two pages.

## JavaScript and page loading

Online: https://docs.html-to-pdf.net/anywhere-library/html/ClientScripts.htm

Pages built with JavaScript - charts, maps, single page applications, content loaded on demand - must finish running their scripts before the converter captures them. These properties of `PdfConverter` control JavaScript and decide when the page is ready. The page runs in Chromium, the engine of the Chrome browser, with the JavaScript of a current browser.

### Enabling JavaScript

- `PdfConverter.ScriptsEnabled` - JavaScript runs in the converted page (true by default).
- `PdfConverter.WebGlEnabled` - WebGL for pages drawn with it (3D charts, maps). Off by default, which makes conversions faster.

### Waiting for the page

The conversion starts when the page has loaded. For content that appears later, use one of these:

| Property | Use |
|---|---|
| `PdfConverter.ConversionDelay` | Wait a fixed number of seconds after the page loads. |
| `PdfConverter.WindowStatus` | Wait until the page sets window.status to the given text - the page says when it is ready. |
| `PdfConverter.StartupMode` = Manual | Wait until the page calls ExpertPdfJSObj.startConversion(). See Start conversion from JavaScript. |
| `PdfConverter.LazyImagesLoadingEnabled`, `PdfConverter.LazyImagesLoadingDelay` | Give images that load when they scroll into view the time to load. |
| `PdfConverter.NavigationTimeout` | The maximum time to load the page (60 seconds by default). |

```csharp
PdfConverter converter = new PdfConverter();

// the page runs: window.status = "ready"; when its charts are drawn
converter.WindowStatus = "ready";
converter.NavigationTimeout = 120;

converter.SavePdfFromUrlToFile("https://www.example.com/dashboard", "dashboard.pdf");
```

```vbnet
Dim converter As New PdfConverter()

' the page runs: window.status = "ready"; when its charts are drawn
converter.WindowStatus = "ready"
converter.NavigationTimeout = 120

converter.SavePdfFromUrlToFile("https://www.example.com/dashboard", "dashboard.pdf")
```

### Changing the page before the conversion

- `PdfConverter.CustomCSS` - CSS added to the page, for example to hide a cookie banner or a menu.
- `PdfConverter.PostLoadingScript` - JavaScript that runs after the page has loaded; `PdfConverter.PostLoadingScriptDelayAfter` waits after it.
- `PdfConverter.StartupScript` - JavaScript injected into the page that runs before the scripts of the page.
- `PdfConverter.ClickElementsSelectors` - elements clicked before the conversion, for example the "close" button of a banner; `PdfConverter.ClickElementsDelayBefore` and `PdfConverter.ClickElementsDelayAfter` wait around the clicks.

```csharp
PdfConverter converter = new PdfConverter();
converter.CustomCSS = "#cookie-banner, nav { display: none !important; }";
converter.PostLoadingScript =
    "document.querySelectorAll('details').forEach(function (d) { d.open = true; });";
converter.SavePdfFromUrlToFile("https://www.example.com/article", "article.pdf");
```

```vbnet
Dim converter As New PdfConverter()
converter.CustomCSS = "#cookie-banner, nav { display: none !important; }"
converter.PostLoadingScript =
    "document.querySelectorAll('details').forEach(function (d) { d.open = true; });"
converter.SavePdfFromUrlToFile("https://www.example.com/article", "article.pdf")
```

## Starting the conversion from JavaScript

Online: https://docs.html-to-pdf.net/anywhere-library/html/StartConversionFromJs.htm

The converter exposes a global JavaScript object called *ExpertPdfJSObj* in the page it converts. With it a script can tell whether it runs inside the converter (check that *typeof ExpertPdfJSObj == "object"*) and read the version of the converter with *ExpertPdfJSObj.getAppVersion()*.

Its main use is to start the conversion from the page itself, when the page knows better than a fixed delay when it is ready - after its data has arrived and its charts are drawn, for example. To do that:

1. Set the `PdfConverter.StartupMode` property of the `PdfConverter` object to `StartupMode.Manual`.
2. Call *ExpertPdfJSObj.startConversion()* from the page's JavaScript when the page is ready.

Important:

- If `PdfConverter.StartupMode` is set to `StartupMode.Manual` and the page never calls *ExpertPdfJSObj.startConversion()*, the conversion fails with a timeout after `PdfConverter.NavigationTimeout` seconds.
- With the default value, `StartupMode.Automatic`, the conversion starts when the page has loaded (and `PdfConverter.ConversionDelay` has elapsed), without waiting for any JavaScript call.

The image converter and the HTML elements of a `Document` have the same *StartupMode* property.

### Sample Code

Below is the HTML of a test page that counts three seconds after it loads and then starts the conversion. Converted in `StartupMode.Manual` mode the PDF shows the timer at 3; converted in `StartupMode.Automatic` mode it shows the timer at 1, because the conversion does not wait.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Start the conversion from JavaScript</title>
</head>
<body>
    <h1>Start the conversion from JavaScript</h1>
    <p>
        The page increments a timer every second until it reaches 3,
        then starts the conversion.
    </p>
    <p>
        Converter version: <span id="version"></span><br />
        Timer: <span id="timer"></span>
    </p>

    <script>
        var timer = 0;
        var inConverter = typeof ExpertPdfJSObj == "object";

        document.getElementById("version").textContent =
            inConverter ? ExpertPdfJSObj.getAppVersion() : "Not in the converter.";

        function incrementTimer() {
            timer = timer + 1;
            document.getElementById("timer").textContent = timer;

            if (timer == 3) {
                // 3 seconds elapsed - start the conversion
                if (inConverter) {
                    ExpertPdfJSObj.startConversion();
                }
            }
            else {
                // wait another second
                setTimeout(incrementTimer, 1000);
            }
        }

        incrementTimer();
    </script>
</body>
</html>
```

The code that converts the page above in manual mode. The *url* variable is the address of the page.

```csharp
// instantiate a html to pdf converter object
PdfConverter converter = new PdfConverter();

// wait for the page to call ExpertPdfJSObj.startConversion()
converter.StartupMode = StartupMode.Manual;

// fail if the call does not come within 10 seconds
converter.NavigationTimeout = 10;

// create a new pdf document converting an url
using (Document doc = converter.GetPdfDocumentObjectFromUrl(url))
{
    // save pdf document
    doc.Save("Sample.pdf");
}
```

```vbnet
' instantiate a html to pdf converter object
Dim converter As New PdfConverter()

' wait for the page to call ExpertPdfJSObj.startConversion()
converter.StartupMode = StartupMode.Manual

' fail if the call does not come within 10 seconds
converter.NavigationTimeout = 10

' create a new pdf document converting an url
Using doc As Document = converter.GetPdfDocumentObjectFromUrl(url)
    ' save pdf document
    doc.Save("Sample.pdf")
End Using
```

## Fine control of the conversion

Online: https://docs.html-to-pdf.net/anywhere-library/html/ControlHTMLToPDFConversion.htm

The converter offers a fine control of the conversion. The default settings suit most pages, and the properties described below change the result when more control is needed.

### PDF Document Page Orientation

The PDF pages can be in *Portrait* or *Landscape* orientation. By default the page is A4, Portrait. To change the orientation set the `PdfDocumentOptions.PdfPageOrientation` property of the converter; a page you add to a document yourself takes its orientation when you create it.

```csharp
// set the PDF page orientation
pdfConverter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Landscape;
```

```vbnet
' set the PDF page orientation
pdfConverter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Landscape
```

### PDF Document Page Size

The PDF page size can be a standard size like A4, A3 or Letter, or a custom size. The width and height of a custom size are specified in points (1 point is 1/72 inch). See Page size.

```csharp
// set the PDF page size to standard A4
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4;

// set the PDF page size to a custom value
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.Custom;
pdfConverter.PdfDocumentOptions.CustomPdfPageSize = new SizeF(widthInPoints,
    heightInPoints);
```

```vbnet
' set the PDF page size to standard A4
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4

' set the PDF page size to a custom value
pdfConverter.PdfDocumentOptions.PdfPageSize = PdfPageSize.Custom
pdfConverter.PdfDocumentOptions.CustomPdfPageSize = New SizeF(widthInPoints,
    heightInPoints)
```

### The Browser Window of the Converter

The converter renders the HTML page in a browser window of its own, the way a browser renders it on the screen. This window does not depend on the display of the computer: it has a resolution of 96 pixels per inch on every platform, so 96 pixels are 72 points. Web pages measure their elements in pixels, and this is why the window is specified in pixels; it is the only dimension of the converter given in pixels, all the others are in points (1 point is 1/72 inch). The `UnitsConverter` class converts pixels to points and points to pixels.

The width and height of the window are set with the `PdfConverter.PageWidth` and `PdfConverter.PageHeight` properties, or passed to the `PdfConverter` constructor.

The default width is *1024 pixels* (0 or a negative value also means 1024), which suits most web pages; a page designed for a wider screen may need more. The default height is 0, and the whole page is converted, on as many PDF pages as it needs. A positive height sets the height of the window, which pages sized to the window (for example with CSS vh units) use; with `PdfConverter.ClipHtmlView` set to true the content below that height is left out of the PDF.

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.PageWidth = 1280; // a page designed for a wide screen
pdfConverter.PageHeight = 0; // convert the whole page
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.PageWidth = 1280 ' a page designed for a wide screen
pdfConverter.PageHeight = 0 ' convert the whole page
```

### HTML Content Resizing in PDF Page

The page is laid out at the width of the browser window and then scaled to the width of the PDF page between the margins. The PDF pages have a fixed size in points: an *A4 portrait page* is *595 points* wide and *842 points* high, which is *793 x 1122 pixels* at 96 pixels per inch.

At the default width of 1024 pixels, the content of a page is therefore scaled down to fit an A4 page, which is why the fonts and images in the PDF look smaller than in the browser. A window narrower than the page scales the content up. To get the content at its real size, set `PdfConverter.PageWidth` to the width between the margins in pixels - 793 pixels for an A4 portrait page with no margins:

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.PdfDocumentOptions.LeftMargin = 36;
pdfConverter.PdfDocumentOptions.RightMargin = 36;

// the width between the margins of an A4 page, in pixels: the content at real size
pdfConverter.PageWidth = (int)UnitsConverter.PointsToPixels(595 - 36 - 36);
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.PdfDocumentOptions.LeftMargin = 36
pdfConverter.PdfDocumentOptions.RightMargin = 36

' the width between the margins of an A4 page, in pixels: the content at real size
pdfConverter.PageWidth = CInt(UnitsConverter.PointsToPixels(595 - 36 - 36))
```

Content wider than the window - a wide table, a long line of preformatted text - is cut at the right edge, as a browser cuts it when it prints. With `PdfConverter.FitWideContent` set to true, a page that lays out wider than the window is laid out again at its own width (at most four times the window width) and the whole page is scaled down to fit, so the wide content appears complete, smaller. A wider page - Landscape orientation, a larger standard size like A3, or a custom size - gives the content more room at the same scale.

### Single PDF Page Conversion

With the `PdfDocumentOptions.SinglePage` property set to *True*, the whole HTML content is placed on a single PDF page. The page keeps the width of the specified page size and gets the height needed to show the whole content, with the header and the footer.

```csharp
pdfConverter.PdfDocumentOptions.SinglePage = true;
```

```vbnet
pdfConverter.PdfDocumentOptions.SinglePage = True
```

### Request data, page ranges and content height

- `PdfConverter.HttpHeaders` and `PdfConverter.HttpCookies` - headers and cookies sent with the requests of the page and of its resources (for example an authentication cookie).
- `PdfConverter.HttpPostParameters` - form data: when the collection is not empty the page is requested with POST.
- `PdfConverter.PageRanges` - keep only some pages of the result, for example "1-3,5".
- `PdfConverter.AllowContentHeightResize` - set it to false when the content is split on more pages than expected.
- `PdfConverter.EnableOfflineMode` - block every network request, to convert HTML that must not load anything from the network.

```csharp
PdfConverter converter = new PdfConverter();

// the report page expects the parameters of its search form
converter.HttpPostParameters.Add("year", "2026");
converter.HttpPostParameters.Add("region", "EMEA");
converter.HttpHeaders.Add("Accept-Language", "en-US");

// the first three pages only
converter.PageRanges = "1-3";

converter.SavePdfFromUrlToFile("https://www.example.com/report", "report.pdf");
```

```vbnet
Dim converter As New PdfConverter()

' the report page expects the parameters of its search form
converter.HttpPostParameters.Add("year", "2026")
converter.HttpPostParameters.Add("region", "EMEA")
converter.HttpHeaders.Add("Accept-Language", "en-US")

' the first three pages only
converter.PageRanges = "1-3"

converter.SavePdfFromUrlToFile("https://www.example.com/report", "report.pdf")
```

### Timeouts

- `PdfConverter.NavigationTimeout` - how long the page may take to load, in seconds (default 60).
- `PdfConverter.RenderOnTimeout` - when the page has not finished loading at the navigation timeout (an image or a script that never answers), convert what is loaded instead of failing. `ImgConverter.RenderOnTimeout` does the same for HTML to image conversions.
- `PdfConverter.MaxConversionTime` - the time allowed for producing the PDF once the page has loaded, in seconds (default 60); when it is exceeded the conversion fails with a print timeout error. A conversion takes at most NavigationTimeout + MaxConversionTime + 30 seconds, so a page that never finishes - a script in an endless loop - fails in a known time instead of keeping the application waiting.

HtmlToPdfArea, HtmlToPdfElement, HtmlToImageElement and ImgConverter have the same properties.

```csharp
PdfConverter converter = new PdfConverter();
converter.NavigationTimeout = 30;
// convert the page even if an advertisement never finishes loading
converter.RenderOnTimeout = true;
// give up on a page that takes more than 20 seconds to convert once loaded
converter.MaxConversionTime = 20;
converter.SavePdfFromUrlToFile("https://www.example.com/report", "report.pdf");
```

```vbnet
Dim converter As New PdfConverter()
converter.NavigationTimeout = 30
' convert the page even if an advertisement never finishes loading
converter.RenderOnTimeout = True
' give up on a page that takes more than 20 seconds to convert once loaded
converter.MaxConversionTime = 20
converter.SavePdfFromUrlToFile("https://www.example.com/report", "report.pdf")
```

### Where the last page ends

The library decides where the content ends: `PdfConverter.ManagedContentTrimming` (default true) draws the page the content ends on only down to where the content ends, on a document of any length. A page does not end with a blank page (for example when its bottom margin or padding does not fit on the last page), and `ConversionSummary.LastPageRectangle` ends right below the content, where more content can be added to the document. `PdfConverter.ManagedContentTrimmingMode` chooses how the end is found: Ink (the default) measures the painted content, so a shadow or a positioned decoration below the last block is kept; Marker ends where the page layout ends.

With ManagedContentTrimming set to false, the engine shortens the page only when the whole content fits on one page. AllowContentHeightResize set to false turns off both. HTML header and footer areas (HtmlToPdfArea) and HtmlToPdfElement have the same two properties.

```csharp
PdfConverter converter = new PdfConverter();
converter.ManagedContentTrimmingMode = ManagedContentTrimmingMode.Marker;
Document document = converter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/invoice");

// add a note right below the converted content
RectangleF last = converter.ConversionSummary.LastPageRectangle;
PdfPage lastPage = document.Pages[converter.ConversionSummary.LastPageIndex];
lastPage.AddElement(new TextElement(0, last.Bottom + 10, "Thank you for your order.",
    document.AddFont(new Font("Arial", 10), true)));
document.Save("invoice.pdf");
document.Close();
```

```vbnet
Dim converter As New PdfConverter()
converter.ManagedContentTrimmingMode = ManagedContentTrimmingMode.Marker
Dim document As Document = converter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/invoice")

' add a note right below the converted content
Dim last As RectangleF = converter.ConversionSummary.LastPageRectangle
Dim lastPage As PdfPage = document.Pages(converter.ConversionSummary.LastPageIndex)
lastPage.AddElement(New TextElement(0, last.Bottom + 10, "Thank you for your order.",
    document.AddFont(New Font("Arial", 10), True)))
document.Save("invoice.pdf")
document.Close()
```

## Conversion summary

Online: https://docs.html-to-pdf.net/anywhere-library/html/ConversionSummary.htm

The `PdfConverter.ConversionSummary` property of the `PdfConverter` class returns an object filled in by each successful call of the conversion methods described in "The PdfConverter class" (html-to-pdf.md). Its data describes the conversion that has just finished and helps to change the generated document further, as described in "Customizing the converted document" (html-to-pdf.md).

The main properties of the `ConversionSummary` object are `ConversionSummary.PdfPageCount`, the number of pages rendered by the converter, and `ConversionSummary.RenderedPagesRectangles`, the rectangle of the content rendered on each page.

To add more content after the converted page, use `ConversionSummary.LastPageIndex` and `ConversionSummary.LastPageRectangle`: the zero based index of the last rendered page and the rectangle of the content on that page. The rectangle ends where the content of the page ends (see Where the last page ends). `ConversionSummary.ConsoleLog` holds what the scripts of the page wrote to the browser console.

### Sample Code

The code below converts a URL, then adds a second web page right below the content of the first one:

```csharp
PdfConverter pdfConverter = new PdfConverter();

// convert the first URL to a Document object
Document pdfDocument = pdfConverter.GetPdfDocumentObjectFromUrl(url);
try
{
    ConversionSummary conversionSummary = pdfConverter.ConversionSummary;

    // the PDF page where the conversion ended
    PdfPage lastPage = pdfDocument.Pages[conversionSummary.LastPageIndex];

    // the rectangle of the content on that page
    RectangleF lastRectangle = conversionSummary.LastPageRectangle;

    // add the second URL right below the content of the first conversion
    HtmlToPdfElement htmlToPdfUrl2 = new HtmlToPdfElement(lastRectangle.Left,
        lastRectangle.Bottom, url2);
    lastPage.AddElement(htmlToPdfUrl2);

    pdfDocument.Save(pdfFilePath);
}
finally
{
    pdfDocument.Close();
}
```

```vbnet
Dim pdfConverter As New PdfConverter()

' convert the first URL to a Document object
Dim pdfDocument As Document = pdfConverter.GetPdfDocumentObjectFromUrl(url)
Try
    Dim conversionSummary As ConversionSummary = pdfConverter.ConversionSummary

    ' the PDF page where the conversion ended
    Dim lastPage As PdfPage = pdfDocument.Pages(conversionSummary.LastPageIndex)

    ' the rectangle of the content on that page
    Dim lastRectangle As RectangleF = conversionSummary.LastPageRectangle

    ' add the second URL right below the content of the first conversion
    Dim htmlToPdfUrl2 As New HtmlToPdfElement(lastRectangle.Left,
        lastRectangle.Bottom, url2)
    lastPage.AddElement(htmlToPdfUrl2)

    pdfDocument.Save(pdfFilePath)
Finally
    pdfDocument.Close()
End Try
```

The second page continues on new pages when it does not fit on the last page (see HTML to PDF element).

## JPEG compression

Online: https://docs.html-to-pdf.net/anywhere-library/html/JpegCompression.htm

The converter compresses the images of the generated PDF with the JPEG algorithm to reduce the size of the document. JPEG compression lowers the quality of the images: the higher the compression level, the smaller the images and the lower their quality.

The `PdfDocumentOptions.JpegCompressionLevel` property sets the compression level on a scale from 0 to 100. At 0 the compression is the lowest and the image quality the best. The default level is 10, a good balance between the quality of the images and the size of the document.

For the best image quality, turn the JPEG compression off by setting `PdfDocumentOptions.JpegCompressionEnabled` to *false*. The converter then does not re-encode the images.

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.PdfDocumentOptions.JpegCompressionEnabled = true;
pdfConverter.PdfDocumentOptions.JpegCompressionLevel = 20;
pdfConverter.SavePdfFromUrlToFile("https://www.example.com", "compressed.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.PdfDocumentOptions.JpegCompressionEnabled = True
pdfConverter.PdfDocumentOptions.JpegCompressionLevel = 20
pdfConverter.SavePdfFromUrlToFile("https://www.example.com", "compressed.pdf")
```

Pages with large photos can also be made smaller with `PdfDocumentOptions.ImagesMaxResolution`, which resamples an image drawn at a higher resolution than the one you set (for example 150 pixels per inch for screen reading, 300 for print). The compression of the rest of the document - page content and fonts - is set with `PdfDocumentOptions.PdfCompressionLevel`.

## Color space

Online: https://docs.html-to-pdf.net/anywhere-library/html/ColorSpace.htm

The `PdfConverter.ColorSpace` property sets the color space - **RGB** (the default), **CMYK** or **Gray** - of the graphics the library draws in the generated document: the text, lines and backgrounds of the headers and footers, and the elements added later to the `Document` returned by the converter. The content rendered from the HTML page keeps the RGB colors of the page.

```csharp
pdfConverter.ColorSpace = ColorSpace.CMYK;
```

```vbnet
pdfConverter.ColorSpace = ColorSpace.CMYK
```

For a document whose whole content is in CMYK, for printing, produce it as PDF/X-1a: with `PdfConverter.PdfStandardSubset` set to `PdfStandardSubset.Pdf_X_1a`, the colors of the HTML content are converted to CMYK too. See PDF standards.

```csharp
pdfConverter.ColorSpace = ColorSpace.CMYK;
pdfConverter.PdfStandardSubset = PdfStandardSubset.Pdf_X_1a;
```

```vbnet
pdfConverter.ColorSpace = ColorSpace.CMYK
pdfConverter.PdfStandardSubset = PdfStandardSubset.Pdf_X_1a
```

## HTML forms to PDF form fields

Online: https://docs.html-to-pdf.net/anywhere-library/html/HtmlFormsToPdfFields.htm

With `PdfConverter.CreateFormFields` set, the HTML form controls of the page become fillable PDF form fields at the same place: text boxes (inputs, password boxes, text areas), check boxes, radio button groups, drop-down lists and list boxes. The page's own borders and styling stay visible around the fields.

### Converting a form

```csharp
using ExpertPdf.Anywhere.HtmlToPdf;

string html = "<form><label>Name <input name='name'></label>" +
    "<label><input type='checkbox' name='agree'> I agree</label></form>";

PdfConverter converter = new PdfConverter();
// converter.LicenseKey = "your license key";
converter.CreateFormFields = true;
converter.SavePdfFromHtmlStringToFile(html, "form.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere.HtmlToPdf

Dim html As String = "<form><label>Name <input name='name'></label>" &
    "<label><input type='checkbox' name='agree'> I agree</label></form>"

Dim converter As New PdfConverter()
' converter.LicenseKey = "your license key"
converter.CreateFormFields = True
converter.SavePdfFromHtmlStringToFile(html, "form.pdf")
```

### How the fields are made

- A field is named after the control's name or id, and gets the control's value, checked state, options and selection, maximum length and read-only state. The label of the control becomes the field's description (its tooltip).
- Radio buttons with the same name in a form become one radio button group.
- The option needs JavaScript, which is enabled by default.
- The fields work in PDF/A documents, and in tagged (PDF/UA) documents they are part of the logical structure, read where the control stood.
- HtmlToPdfElement has the same option, for a form added to a page of a document.

To fill the fields of an existing PDF document from code, see "Filling PDF forms" (forms.md).

## Prepending and appending documents

Online: https://docs.html-to-pdf.net/anywhere-library/html/MergeCapabilities.htm

The HTML to PDF converter can add existing PDF documents before and after the conversion result: a cover page in front of a converted report, terms and conditions after an offer. The documents are set with properties of the `PdfDocumentOptions` class, as files or as streams, before the conversion method is called. Their pages are merged as they are, with their own size and links, and the header and footer of the converter are not drawn on them. A document that cannot be read fails the conversion.

The properties are:

```csharp
public string AppendPDFFile { get; set; }
public string[] AppendPDFFileArray { get; set; }
public Stream AppendPDFStream { get; set; }
public Stream[] AppendPDFStreamArray { get; set; }
public string PrependPDFFile { get; set; }
public string[] PrependPDFFileArray { get; set; }
public Stream PrependPDFStream { get; set; }
public Stream[] PrependPDFStreamArray { get; set; }
```

```vbnet
Public Property AppendPDFFile() As String
Public Property AppendPDFFileArray() As String()
Public Property AppendPDFStream() As Stream
Public Property AppendPDFStreamArray() As Stream()
Public Property PrependPDFFile() As String
Public Property PrependPDFFileArray() As String()
Public Property PrependPDFStream() As Stream
Public Property PrependPDFStreamArray() As Stream()
```

### Sample Code

```csharp
PdfConverter pdfConverter = new PdfConverter();

// existing documents appended after the converted page: a file, and a document from
// memory
pdfConverter.PdfDocumentOptions.AppendPDFFile = "terms-and-conditions.pdf";
byte[] attachmentBytes = File.ReadAllBytes("price-list.pdf");
pdfConverter.PdfDocumentOptions.AppendPDFStreamArray =
    new Stream[] { new MemoryStream(attachmentBytes) };

// and a cover page placed before it
pdfConverter.PdfDocumentOptions.PrependPDFFile = "cover.pdf";

pdfConverter.SavePdfFromUrlToFile("https://www.example.com/offer", "offer.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()

' existing documents appended after the converted page: a file, and a document from
' memory
pdfConverter.PdfDocumentOptions.AppendPDFFile = "terms-and-conditions.pdf"
Dim attachmentBytes As Byte() = File.ReadAllBytes("price-list.pdf")
pdfConverter.PdfDocumentOptions.AppendPDFStreamArray =
    New Stream() {New MemoryStream(attachmentBytes)}

' and a cover page placed before it
pdfConverter.PdfDocumentOptions.PrependPDFFile = "cover.pdf"

pdfConverter.SavePdfFromUrlToFile("https://www.example.com/offer", "offer.pdf")
```

### Merging into a PDF/A document

When the converter produces a PDF/A document (see "PDF standards: PDF/A and PDF/X" (accessibility-and-compliance.md)), the documents you add before or after the conversion result must conform to the same level: the same PDF/A part, with at least the same conformance (a PDF/A-2b result takes PDF/A-2b, PDF/A-2u and PDF/A-2a documents). Their pages are added as they are, so the conversion refuses a document that does not conform, with an `HtmlConvertException`. Convert such a document first: load it into a `Document`, call `Document.ConvertToPdfA(PdfStandardSubset)` and save it. `Document.AppendDocument(Document)` on a PDF/A document applies the same rule.

```csharp
// make an existing document PDF/A-2b before it is appended to a PDF/A-2b conversion
using (Document terms = new Document("terms-and-conditions.pdf"))
{
    terms.ConvertToPdfA(PdfStandardSubset.Pdf_A_2b);
    terms.Save("terms-pdfa.pdf");
}

PdfConverter pdfConverter = new PdfConverter();
pdfConverter.PdfStandardSubset = PdfStandardSubset.Pdf_A_2b;
pdfConverter.PdfDocumentOptions.AppendPDFFile = "terms-pdfa.pdf";
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/offer", "offer-pdfa.pdf");
```

```vbnet
' make an existing document PDF/A-2b before it is appended to a PDF/A-2b conversion
Using terms As New Document("terms-and-conditions.pdf")
    terms.ConvertToPdfA(PdfStandardSubset.Pdf_A_2b)
    terms.Save("terms-pdfa.pdf")
End Using

Dim pdfConverter As New PdfConverter()
pdfConverter.PdfStandardSubset = PdfStandardSubset.Pdf_A_2b
pdfConverter.PdfDocumentOptions.AppendPDFFile = "terms-pdfa.pdf"
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/offer", "offer-pdfa.pdf")
```

### More control over the merge

To insert pages anywhere in the result, convert to a `Document` object and use `Document.AppendDocument(Document)` or the pages collection (see "Customizing the converted document" (html-to-pdf.md)). To merge existing PDF files without a conversion, use the PDF merge tool described in "Merging PDF documents" (edit-merge-split.md).

## Customizing the converted document

Online: https://docs.html-to-pdf.net/anywhere-library/html/PostConvertCustomization.htm

The converter offers a set of render methods described in Advanced Post Convert Customization section, returning the internal `Document` object created by the converter. This `Document` object offers access to the collection of pages of PDF document. You can iterate through the document pages, add new pages to the document, append external PDF documents or add new elements like text and images to the document pages. After modification, the document can be saved into a file or to a stream using one of the *Save* methods of the `Document` class.

The document keeps the resources of the conversion until it is closed: call its `Document.Close` method after saving it, or create it in a *using* statement, since the class implements *IDisposable*.

The section Advanced Post Convert Customization contains a detailed description of the classes and methods available in the `PdfDocument` namespace.

### Sample Code

Below there is a complete example of how to convert many HTML documents into the same PDF document using post convert customization of the `Document` object. Each additional HTML to PDF conversion is represented by a `HtmlToPdfElement` object that can be added in any position in a PDF page. The first conversion is performed by the call to *pdfConverter.GetPdfDocumentObjectFromUrl*, the next two conversions (the conversion of another URL and the conversion of a HTML string) are achieved by adding two `HtmlToPdfElement` objects to the document pages:

```csharp
PdfConverter pdfConverter = new PdfConverter();

// convert the first page into a Document object that can be changed before it is
// saved
Document pdfDocument = pdfConverter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/part1");
try
{
    // where the first conversion ended: the last page and the rectangle of its
    // content
    ConversionSummary summary = pdfConverter.ConversionSummary;
    PdfPage lastPage = pdfDocument.Pages[summary.LastPageIndex];
    RectangleF lastRectangle = summary.LastPageRectangle;

    // the second page continues right below the first one (use
    // pdfDocument.Pages.AddNewPage() to start on a new page)
    HtmlToPdfElement part2 = new HtmlToPdfElement(lastRectangle.Left,
        lastRectangle.Bottom, "https://www.example.com/part2");
    AddElementResult result = lastPage.AddElement(part2);

    // and a line of HTML after everything rendered so far
    PdfPage endPage = pdfDocument.Pages[result.EndPageIndex];
    endPage.AddElement(new HtmlToPdfElement(result.EndPageBounds.Left,
        result.EndPageBounds.Bottom,
        "<p><i>End of the report</i></p>", null));

    pdfDocument.Save("report.pdf");
}
finally
{
    pdfDocument.Close();
}
```

```vbnet
Dim pdfConverter As New PdfConverter()

' convert the first page into a Document object that can be changed before it is
' saved
Dim pdfDocument As Document = pdfConverter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/part1")
Try
    ' where the first conversion ended: the last page and the rectangle of its
    ' content
    Dim summary As ConversionSummary = pdfConverter.ConversionSummary
    Dim lastPage As PdfPage = pdfDocument.Pages(summary.LastPageIndex)
    Dim lastRectangle As RectangleF = summary.LastPageRectangle

    ' the second page continues right below the first one (use
    ' pdfDocument.Pages.AddNewPage() to start on a new page)
    Dim part2 As New HtmlToPdfElement(lastRectangle.Left, lastRectangle.Bottom,
        "https://www.example.com/part2")
    Dim result As AddElementResult = lastPage.AddElement(part2)

    ' and a line of HTML after everything rendered so far
    Dim endPage As PdfPage = pdfDocument.Pages(result.EndPageIndex)
    endPage.AddElement(New HtmlToPdfElement(result.EndPageBounds.Left,
        result.EndPageBounds.Bottom,
        "<p><i>End of the report</i></p>", Nothing))

    pdfDocument.Save("report.pdf")
Finally
    pdfDocument.Close()
End Try
```

## Asynchronous conversions

Online: https://docs.html-to-pdf.net/anywhere-library/html/AsyncConversion.htm

The HTML to PDF converter has asynchronous versions of its conversion methods - GetPdfBytesFromUrlAsync, GetPdfBytesFromHtmlStringAsync, GetPdfDocumentObjectFromUrlAsync, SavePdfFromUrlToFileAsync and the others - that return a *Task* and take an optional *CancellationToken*. No thread waits while the page loads and renders, so a web application keeps its threads free for other requests, and several conversions can be awaited together.

### Awaiting a conversion

```csharp
using ExpertPdf.Anywhere.HtmlToPdf;

PdfConverter converter = new PdfConverter();
// converter.LicenseKey = "your license key";
byte[] pdf = await converter.GetPdfBytesFromUrlAsync("https://www.html-to-pdf.net");
File.WriteAllBytes("page.pdf", pdf);
```

```vbnet
Imports ExpertPdf.Anywhere.HtmlToPdf

Dim converter As New PdfConverter()
' converter.LicenseKey = "your license key"
Dim pdf As Byte() = Await converter.GetPdfBytesFromUrlAsync(
    "https://www.html-to-pdf.net")
File.WriteAllBytes("page.pdf", pdf)
```

In an ASP.NET Core action, pass the request's cancellation token: when the browser goes away, the conversion stops.

```csharp
using System.Threading.Tasks;

public async Task<IActionResult> Report(System.Threading.CancellationToken token)
{
    PdfConverter converter = new PdfConverter();
    byte[] pdf = await converter.GetPdfBytesFromUrlAsync(
        "https://www.html-to-pdf.net", token);
    return File(pdf, "application/pdf", "report.pdf");
}
```

```vbnet
Imports System.Threading.Tasks

Public Async Function Report(token As System.Threading.CancellationToken) _
    As Task(Of IActionResult)
    Dim converter As New PdfConverter()
    Dim pdf As Byte() = Await converter.GetPdfBytesFromUrlAsync(
        "https://www.html-to-pdf.net", token)
    Return File(pdf, "application/pdf", "report.pdf")
End Function
```

### Several conversions at the same time

Start the conversions, then await them together: the pages load and render at the same time. Use one converter object per conversion. The number of conversions that run at once is limited by `PdfConverter.PdfConverterConcurrencyLevel`; the others wait their turn.

```csharp
string[] urls = { "https://www.html-to-pdf.net", "https://www.example.com" };
Document[] documents = await System.Threading.Tasks.Task.WhenAll(
    System.Linq.Enumerable.Select(urls,
        address => new PdfConverter().GetPdfDocumentObjectFromUrlAsync(address)));

Document merged = new Document();
foreach (Document document in documents)
    merged.AppendDocument(document);
merged.Save("pages.pdf");
merged.Close();
foreach (Document document in documents)
    document.Close();
```

```vbnet
Dim urls As String() = {"https://www.html-to-pdf.net", "https://www.example.com"}
Dim documents As Document() = Await System.Threading.Tasks.Task.WhenAll(
    System.Linq.Enumerable.Select(urls,
        Function(address) New PdfConverter().GetPdfDocumentObjectFromUrlAsync(
            address)))

Dim merged As New Document()
For Each document As Document In documents
    merged.AppendDocument(document)
Next
merged.Save("pages.pdf")
merged.Close()
For Each document As Document In documents
    document.Close()
Next
```

One converter object runs one conversion at a time. Elements added to a document can be rendered asynchronously too: the *AddElementAsync* method of pages and templates renders an HTML or image element at once and places it after the elements added before it.

## Multithreading

Online: https://docs.html-to-pdf.net/anywhere-library/html/MultithreadingSupport.htm

Most applications that create PDF documents convert for many users at once: an ASP.NET Core application handles each request on a thread of its own, and a background service often processes a queue with several workers. ExpertPdf Anywhere is built for that. Conversions running at the same time on different threads are independent of each other, and the memory and resources used by a conversion are released when it ends, so the library can run for a long time in services under a heavy load.

The rule to follow is **one converter per conversion**: create a new `PdfConverter` for each conversion and do not share an instance between threads. The same holds for a `Document`: documents built at the same time on different threads are safe, one document used by two threads at once is not.

### The number of simultaneous conversions

The static property `PdfConverter.PdfConverterConcurrencyLevel` sets how many HTML conversions run at the same time in the application. Conversions started beyond that number wait, in the order they were started, until one ends. The default value is 8; zero or a negative value removes the limit. Set it at start-up, before the first conversion.

Each conversion in flight has a Chromium browser of its own, which needs memory in proportion to the page it renders and processor time. Choose the limit from the processor cores and the memory of the server. The Chromium engine runs inside the application by default or in separate processes (see "The Chromium engine" (installation-and-deployment.md)); the limit applies to both.

### Sample Code

Several threads converting at the same time, each with its own converter:

```csharp
// at most 4 conversions at the same time in this application
PdfConverter.PdfConverterConcurrencyLevel = 4;

string[] urls =
{
    "https://www.example.com/report1",
    "https://www.example.com/report2",
    "https://www.example.com/report3"
};

System.Threading.Tasks.Parallel.For(0, urls.Length, i =>
{
    // a new converter for every conversion: converters are not shared between
    // threads
    PdfConverter pdfConverter = new PdfConverter();
    pdfConverter.SavePdfFromUrlToFile(urls[i], "report" + (i + 1) + ".pdf");
});
```

```vbnet
' at most 4 conversions at the same time in this application
PdfConverter.PdfConverterConcurrencyLevel = 4

Dim urls As String() =
{
    "https://www.example.com/report1",
    "https://www.example.com/report2",
    "https://www.example.com/report3"
}

System.Threading.Tasks.Parallel.For(0, urls.Length,
    Sub(i)
        ' a new converter for every conversion: converters are not shared between
        ' threads
        Dim pdfConverter As New PdfConverter()
        pdfConverter.SavePdfFromUrlToFile(urls(i), "report" & (i + 1) & ".pdf")
    End Sub)
```

In an application that already uses *async* code, such as a web application, the asynchronous methods of the converter (*GetPdfBytesFromUrlAsync*, *SavePdfFromUrlToFileAsync* and the others ending in Async) convert without keeping a thread waiting while Chromium works, and take a cancellation token:

```csharp
PdfConverter pdfConverter = new PdfConverter();
byte[] pdf = await pdfConverter.GetPdfBytesFromUrlAsync(
    "https://www.example.com", cancellationToken);
```

```vbnet
Dim pdfConverter As New PdfConverter()
Dim pdf As Byte() = Await pdfConverter.GetPdfBytesFromUrlAsync(
    "https://www.example.com", cancellationToken)
```

The samples include a console application, *MultithreadedPerformance*, that runs many conversions on several threads and reports the throughput, to measure the converter on your own pages and server.
