# Frequently asked questions and troubleshooting

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Frequently asked questions

Online: https://docs.html-to-pdf.net/anywhere-library/html/FAQ.htm

### Which rendering engine does the HTML to PDF converter use?

Chromium, on every platform: it renders modern HTML, CSS, web fonts and JavaScript like the Chrome browser. There is no other engine to choose. By default the engine runs inside your application, which makes each conversion faster; `GlobalSettings.ChromiumEngineHostMode` can run it in a separate process for each conversion instead. See Rendering engine.

### A page built with a JavaScript framework (React, Angular, Vue...) is converted empty or incomplete.

The Chromium engine runs the scripts of the page as a browser does. When the page loads its content after the load event - from an API, after an animation - wait for it with `PdfConverter.WindowStatus`, `PdfConverter.ConversionDelay` or a manual start from JavaScript (see JavaScript and page loading).

### The conversion fails because the Chromium engine is not found.

The engine files must be in a Chromium-CEF-154.0.28 folder next to the application, or in the folder set with `PdfConverter.CefEnginePath`. The folder comes from the native package of your platform (ExpertPdf.Anywhere.Native.<rid>) or from a platform package (ExpertPdf.Anywhere.Windows, .Linux, .Linux.Arm64, .MacOS). A project references the native package of the platform it runs on; to build on one platform and publish for another, choose the package by the runtime identifier (see "Installation" (installation-and-deployment.md)). Check that the folder was published with the application.

### How do I create accessible documents, PDF/A documents or electronic invoices?

See Tagged PDF and accessibility, PDF standards and Electronic invoices.

### When I convert a HTML string to PDF, the external CSS files and images are not applied in the rendered PDF document.

When you convert a HTML string referencing external CSS files and images by relative URLs, the converter cannot determine the full URLs just looking at the string. Pass the full URL of the page the HTML string comes from as the base URL of the conversion (`PdfConverter.GetPdfBytesFromHtmlString(string, string)`): the string is then loaded as the page at that URL, so its relative URLs, web fonts and scripts resolve as they do when the page is opened in a browser.

This issue might also indicate an *authentication* or *permissions* problem on the server when accessing the external resources like images and CSS files: the HTML string is converted, but the images and CSS files are still requested from their URLs and they might not be accessible. Open the full URL of an image in a browser on the computer where the conversion runs to check that it can be loaded, then try to convert that URL with `PdfConverter.GetPdfBytesFromUrl(string)`.

Another situation where this problem occurs is a site with forms authentication (or any login stored in a cookie): the converter is a different client than the user's browser, so its requests are not authenticated. Send the authentication cookie with the requests of the converter, using the `PdfConverter.HttpCookies` collection. See also Server authentication.

### In the PDF document the images and fonts appear smaller than in the browser, or the content does not fill the width of the page. How can I control this?

The converter renders the HTML page in a virtual browser window whose width is given in pixels by `PdfConverter.PageWidth` (1024 pixels by default, also a parameter of the `PdfConverter` constructor). The page laid out at that width is then scaled to the width of the content area of the PDF page, which is given in points (1/72 inch): an A4 portrait page is 595 points wide, that is 793 pixels at 96 pixels per inch. So at the default width of 1024 pixels the content of an A4 page is shown smaller than in a browser window of the same width. `UnitsConverter` converts between pixels and points. See Controlling the HTML to PDF conversion.

- For larger text and images, set a smaller PageWidth - for example the width the page is designed for.
- For a page that needs more width, set a larger PageWidth, a landscape orientation (*PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Landscape*), a larger page size, or a custom page size (`PdfDocumentOptions.CustomPdfPageSize`).
- Content wider than the virtual window - a wide table, a long preformatted line - is cut at the right edge, as a browser cuts it when it prints. Set `PdfConverter.FitWideContent` to true to lay such a page out at its own width and scale it down to fit.
- `PdfConverter.PageHeight` is 0 by default: the whole page is converted. A height in pixels truncates the HTML document at that height.

### How can I convert a page of my own web application?

Give the converter the URL of the page: it loads it like a browser, with its CSS, images and scripts. When the page needs the login or the session of the current user, send their cookies with the request of the converter (see the next question). Alternatively, render the page to a HTML string in your application and convert the string with the URL of the page as the base URL, so that its relative URLs resolve. `ConverterUtils.GetHtmlFromURL(string)` downloads the HTML code of a page if you need to change it before the conversion.

### Is the session data of my web application available in the converted page?

The converter requests the page as a new client, without the cookies of the user's browser, so the page runs in a *new session* and without the user's login. Send the cookies of the current request with the requests of the converter, or pass the data the page needs in its query string. In an ASP.NET Core controller:

```csharp
using ExpertPdf.Anywhere.HtmlToPdf;

public IActionResult Report()
{
    PdfConverter converter = new PdfConverter();

    // the session and authentication cookies of the current user
    foreach (KeyValuePair<string, string> cookie in Request.Cookies)
        converter.HttpCookies.Add(cookie.Key, cookie.Value);

    string url = Url.Action("ReportPage", "Home", null, Request.Scheme);
    byte[] pdf = converter.GetPdfBytesFromUrl(url);
    return File(pdf, "application/pdf", "report.pdf");
}
```

```vbnet
Imports ExpertPdf.Anywhere.HtmlToPdf

Public Function Report() As IActionResult
    Dim converter As New PdfConverter()

    ' the session and authentication cookies of the current user
    For Each cookie As KeyValuePair(Of String, String) In Request.Cookies
        converter.HttpCookies.Add(cookie.Key, cookie.Value)
    Next

    Dim url As String = Me.Url.Action("ReportPage", "Home", Nothing, Request.Scheme)
    Dim pdf As Byte() = converter.GetPdfBytesFromUrl(url)
    Return File(pdf, "application/pdf", "report.pdf")
End Function
```

### Which fonts does the converter use, and are they embedded in the PDF document?

The Chromium engine embeds every font it uses in the PDF document, as a subset with the characters of the document, so the document looks the same on every computer. It uses:

- the fonts installed on the computer;
- the web fonts of the page (@font-face, TTF, OTF, WOFF, WOFF2);
- the font files in a *fonts* folder next to the application, on every platform: add the files to your project with copy to the output folder;
- on Linux and macOS, the Noto fonts of the ExpertPdf.Anywhere.Fonts package, for every script.

The fonts the library draws itself - system fonts used by the text elements of the document model, the fonts of Word and RTF documents - come from the installed fonts and the same fonts folder, and are embedded too.

### Text in Chinese, Japanese, Arabic or another script appears as empty boxes on Linux.

The host has no font for that script. Docker images, AWS Lambda and Azure App Service on Linux come with few or no fonts, and the fonts bundled with the library (Liberation) cover Latin text only. Add the ExpertPdf.Anywhere.Fonts package, which copies Google's Noto fonts for every script next to the application when it is built for Linux or macOS, or install fonts in the image (for example the fonts-noto-core and fonts-noto-cjk packages on Debian and Ubuntu).

### I installed a font on the server but the converter still uses another font.

On Windows, install the font for all users: a font installed only for the logged in user is not seen by a service or a web application running under another account. On Linux, put the font files in /usr/share/fonts or /usr/local/share/fonts, or in the *fonts* folder next to the application. If the application was running when the font was installed, restart it.

Check also the name the page asks for: the CSS font-family must match the family name of the font, and a family that is not found is replaced by the default font of its generic family (serif, sans-serif, monospace).

### Some images are cut off between PDF pages. Is there any option to avoid this?

Give the image, or the element that holds it, the *page-break-inside: avoid* (or *break-inside: avoid*) CSS style: the engine then moves it to the next page when it does not fit on the current one. See Page breaks.

### Do I need to install system packages to run the converter on Linux or in Docker?

No. The Linux native packages bring the Chromium engine with the libraries it needs, so a plain .NET image converts without apt-get or dnf packages and with no X server. The host needs glibc 2.30 or later (Ubuntu 20.04, Debian 11, RHEL 9, Amazon Linux 2023 and later; the official .NET images); on Alpine Linux (musl) use the remote engine ("The remote engine" (installation-and-deployment.md)). Fonts are the one thing to add on a host without them (see the question above). See "Requirements" (installation-and-deployment.md).

### Where can I deploy an application that uses ExpertPdf Anywhere?

Anywhere the application can run native code: Windows and Linux servers and virtual machines, Windows Server Core and Linux containers, Kubernetes, Azure App Service (Windows and Linux, Basic plan or higher), Azure Functions on a Dedicated (App Service) plan, Azure Container Apps, AWS Lambda (as a container image: the engine is too large for a zip deployment), AWS Elastic Beanstalk and Google Cloud Run. The Azure Functions Consumption and Flex Consumption plans cannot run the engine. On Azure App Service for Windows, do not use Run From Package (WEBSITE_RUN_FROM_PACKAGE): the engine cannot be started from the mounted package; Zip Deploy and Web Deploy work.

### Conversion failure. Exception of type 'System.OutOfMemoryException' was thrown.

This error might occur when large web pages, containing many or very large images, are converted. A conversion needs memory in proportion to the size of the page, and an image takes the memory of its full resolution even when the page shows it in a small box.

Run the application in a 64-bit process, which can use much more memory than a 32-bit one; use images sized for the web; and limit the number of conversions that run at the same time (see "Multithreading" (html-to-pdf.md)).
