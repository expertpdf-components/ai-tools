# Migrating from the classic ExpertPdf products

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Migrating to ExpertPdf Anywhere

Online: https://docs.html-to-pdf.net/anywhere-library/html/MigratingToAnywhere.htm

ExpertPdf Anywhere keeps the API of the ExpertPdf products, so moving an application to it is mostly a change of packages and of *using* directives. This topic lists everything else that changes. ExpertPdf Anywhere and the ExpertPdf products have different assemblies and namespaces, so they can be used side by side while an application moves over.

### Packages

Replace the ExpertPdf product packages (HtmlToPdf, PdfCreator, MergePdf, SplitPdf, PdfSecurity, PdfToText, PdfToImage, PdfImgExtract, RtfToPdf, with their x64 and engine packages) with one platform package - ExpertPdf.Anywhere.Windows, .Linux, .Linux.Arm64 or .MacOS - or with ExpertPdf.Anywhere and the native package of the runtime. See "Installation" (installation-and-deployment.md). There are no separate x64 packages: the library is AnyCPU and the native package is chosen by platform.

### Namespaces

| ExpertPdf products | ExpertPdf Anywhere |
|---|---|
| ExpertPdf.HtmlToPdf | ExpertPdf.Anywhere.HtmlToPdf |
| ExpertPdf.HtmlToPdf.PdfDocument, ExpertPdf.PdfCreator | ExpertPdf.Anywhere.PdfDocument |
| ExpertPdf.MergePdf, ExpertPdf.PDFSplit, ExpertPdf.PDFSecurity, ExpertPdf.RtfToPdf | ExpertPdf.Anywhere.MergePdf, .PDFSplit, .PDFSecurity, .RtfToPdf |
| ExpertPdf (PdfToTextConverter, PdfToImageConverter, PdfImgExtract) | ExpertPdf.Anywhere |
| System.Drawing (Color, Font, PointF, RectangleF, SizeF) | ExpertPdf.Anywhere.Drawing |

```csharp
// before:
//   using ExpertPdf.HtmlToPdf;
//   using ExpertPdf.HtmlToPdf.PdfDocument;
//   using System.Drawing;
// after:
using ExpertPdf.Anywhere.HtmlToPdf;
using ExpertPdf.Anywhere.PdfDocument;
using ExpertPdf.Anywhere.Drawing;
```

```vbnet
' before:
'   Imports ExpertPdf.HtmlToPdf
'   Imports ExpertPdf.HtmlToPdf.PdfDocument
'   Imports System.Drawing
' after:
Imports ExpertPdf.Anywhere.HtmlToPdf
Imports ExpertPdf.Anywhere.PdfDocument
Imports ExpertPdf.Anywhere.Drawing
```

A class name that exists in two namespaces (PdfDocumentOptions is in HtmlToPdf, MergePdf, RtfToPdf and WordToPdf) needs its namespace when a file imports both, as before.

### License keys

ExpertPdf Anywhere has its own license keys. An ExpertPdf Toolkit or ExpertComponents Toolkit Total license of version 22 or later unlocks it too; the keys of the separate ExpertPdf products do not. The key can be set once for the whole library with `LicensingManager.LicenseKey`, or on each object as before. A key that is not valid throws `LicenseException`. See "License key" (installation-and-deployment.md).

### The rendering engine

HTML is rendered by Chromium only; there is no WebKit, Blink or Internet Explorer engine. Code that sets *RenderingEngine = RenderingEngine.Chromium* keeps compiling (it is the only value). The settings that only the other engines read are not part of ExpertPdf Anywhere, so code that uses them stops compiling - remove those lines:

- The WebKit image pipeline: FitWidth, FitHeight, StretchToFit, AutoSizePdfPage, EmbedFonts and GenerateSelectablePdf on PdfDocumentOptions; FitWidth, FitHeight, StretchToFit, EmbedFonts and LicenseKey on HtmlToPdfArea; FitWidth, FitHeight, StretchToFit, EmbedFonts, Paginate and the ShowHeader/ShowFooter odd and even switches on HtmlToPdfElement. Chromium embeds every font it uses and fits the page to the PDF page width.
- The Internet Explorer and WebKit switches (InternetSecurityZone, the transparency and sampling switches, AvoidImageBreak, AvoidTextBreak and the like), and the engine launch settings of the Blink engine.
- The BeforeCreateNextPageEvent and BeforeRenderNextPageEvent events, raised only by the WebKit pagination.
- The proxy type (ProxyOptions.Type and NetworkProxyType): the engine takes the proxy host, port and credentials.
- The PDF/SiqQ members of PdfStandardSubset.
- RtfToPdf: the constructors with a page width in pixels, PageWidth, PageHeight, FitWidth, FitHeight, EmbedFonts and the RichTextBox drawing switches. The RTF is laid out on the PDF page itself, with its size, orientation and margins.

The Chromium engine runs inside the application by default; see "The Chromium engine" (installation-and-deployment.md). Several conversions run at the same time up to `PdfConverter.PdfConverterConcurrencyLevel` (8 by default).

### Images are bytes

ExpertPdf Anywhere does not use System.Drawing, which is Windows-only on .NET. Images go in and out as the bytes of an image file (PNG, JPEG, BMP or TIFF) with an `ImageFormat`: ImgConverter returns bytes, PdfToImageConverter returns *byte[]* and *byte[][]*, the extracted images are bytes, and ImageElement takes bytes or a file path. GIF output and RenderImageFormatConverter are not available.

```csharp
// before: System.Drawing.Image image = imgConverter.GetImageFromUrl(url);
byte[] png = new ExpertPdf.Anywhere.HtmlToPdf.ImgConverter()
    .GetImageBytesFromUrl("https://www.html-to-pdf.net", ImageFormat.Png);
File.WriteAllBytes("page.png", png);
```

```vbnet
' before: Dim image As System.Drawing.Image = imgConverter.GetImageFromUrl(url)
Dim png As Byte() = New ExpertPdf.Anywhere.HtmlToPdf.ImgConverter() _
    .GetImageBytesFromUrl("https://www.html-to-pdf.net", ImageFormat.Png)
File.WriteAllBytes("page.png", png)
```

The Color of ExpertPdf.Anywhere.Drawing has every named color of System.Drawing.Color, with the same names and values (*Color.Navy*, *Color.WhiteSmoke* and the others), and *Color.FromArgb* for any other color.

### Other differences

- Header and footer: the ShowOnFirstPage, ShowOnOddPages and ShowOnEvenPages switches are honoured.
- Encryption with a 128-bit key uses AES. EncryptionKeySize 256 is AES-256 (revision 6).
- A tagged conversion (Tagged, or PDF/A-3a) declares PDF/UA-1. PDF/UA-2 and the other PDF/A levels are available through AccessibilityStandard and PdfStandardSubset.
- Appending to a PDF/A document - with PDFMerge, the converter's append options or Document.AppendDocument - takes only documents that conform to the level; convert them first with Document.ConvertToPdfA.
- The evaluation mode stamps "Demo Version - ExpertPDF Software Components" on every page, with no page limit.

### What ExpertPdf Anywhere adds

Linux and macOS, Word to PDF, asynchronous conversions, the automatic table of contents, HTML forms as PDF form fields, PDF/UA-2 and PDF/A-4, form filling, redaction, page resizing, portfolios, signature validation, long-term validation and signing with external signers (hardware tokens, key services). See "Overview and namespaces" (installation-and-deployment.md).
