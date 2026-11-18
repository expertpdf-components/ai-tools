# Creating and editing PDF documents

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Overview

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfCreatorOverview.htm

ExpertPdf Anywhere creates PDF documents from scratch and changes existing ones with one document model, the classes in the `PdfDocument` namespace. The same model represents the result of an HTML to PDF conversion, so a converted document can be changed with the same classes before it is saved (see "Customizing a converted document" (pdf-creation-elements.md)).

You add pages and place content on them - text in any installed or bundled font, images, lines and shapes, HTML rendered by Chromium, the content of Word and RTF documents - then add bookmarks, links, attachments and templates repeated on the pages, and save the document as PDF, PDF/A, PDF/X, tagged and accessible PDF (PDF/UA-1 or PDF/UA-2) or a ZUGFeRD / Factur-X invoice, password protected or digitally signed. The library runs on Windows, Linux and macOS.

### Creating, loading and saving a document

- **A new document**: `new Document()`, or `Document.ctor(PdfStandardSubset)` for a PDF/A or PDF/X document. Pages are added with `Document.AddPage`.
- **An existing document**: the constructors that take a file path or a stream, with a password for an encrypted document. Elements can be added to the pages the document already has and to new pages.
- **Adding content**: create a page element (`TextElement`, `ImageElement`, `HtmlToPdfElement`, ...) and add it to a page or a template with `ElementsRenderer.AddElement(PageElement)`. See "Page elements" (pdf-creation-elements.md).
- **Saving**: `Document.Save(string)` writes a file or a stream, and `Document.Save` returns the bytes of the PDF.
- **Closing**: `Document` implements `IDisposable`. Create it in a `using` block, or call `Document.Close` when you no longer need it.

### Quick start

The code below creates a document with one page, adds a text and an HTML fragment rendered below it, and saves the document. Arial is drawn with the metric-compatible Liberation Sans font that the library bundles when the host does not have Arial.

```csharp
using ExpertPdf.Anywhere;
using ExpertPdf.Anywhere.Drawing;
using ExpertPdf.Anywhere.PdfDocument;

LicensingManager.LicenseKey = "your license key";

using (Document document = new Document())
{
    document.DocumentInformation.Title = "Hello";

    PdfPage page = document.AddPage();
    PdfFont font = document.AddFont(new Font("Arial", 16));
    page.AddElement(new TextElement(0, 0, "Hello from ExpertPdf Anywhere", font));

    // HTML content below the text
    HtmlToPdfElement html = new HtmlToPdfElement(0, 40,
        "<p>Text, <b>styles</b> and images from <i>HTML</i></p>", null);
    page.AddElement(html);

    document.Save("hello.pdf");
}
```

```vbnet
Imports ExpertPdf.Anywhere
Imports ExpertPdf.Anywhere.Drawing
Imports ExpertPdf.Anywhere.PdfDocument

LicensingManager.LicenseKey = "your license key"

Using document As New Document()
    document.DocumentInformation.Title = "Hello"

    Dim page As PdfPage = document.AddPage()
    Dim font As PdfFont = document.AddFont(New Font("Arial", 16))
    page.AddElement(New TextElement(0, 0, "Hello from ExpertPdf Anywhere", font))

    ' HTML content below the text
    Dim html As New HtmlToPdfElement(0, 40,
        "<p>Text, <b>styles</b> and images from <i>HTML</i></p>", Nothing)
    page.AddElement(html)

    document.Save("hello.pdf")
End Using
```

### What the document model offers

- Pages of any size and orientation, mixed in one document, inserted, removed and rotated. See "The Document class" (pdf-creation-elements.md) and "The PdfPage class" (pdf-creation-elements.md).
- Templates repeated on the pages: headers, footers, watermarks, page numbers. See "Templates" (pdf-creation-elements.md).
- HTML content rendered by Chromium in a page area, continued on the next pages when it does not fit. See "HTML to PDF element" (pdf-creation-elements.md).
- The content of a Word or RTF document in a page area. See "Word to PDF element" (office-to-pdf.md).
- The sheets of an Excel workbook or the slides of a PowerPoint presentation in a page area. See "Excel to PDF element" (office-to-pdf.md) and "PowerPoint to PDF element" (office-to-pdf.md).
- Text, fonts, images and shapes. See "Text element and fonts" (pdf-creation-elements.md), "Image element" (pdf-creation-elements.md) and "Shape elements" (pdf-creation-elements.md).
- Bookmarks, links, named destinations, page labels and a table of contents with page numbers. See "Bookmarks in a PDF document" (links-bookmarks-toc.md) and "The Document class" (pdf-creation-elements.md).
- Merging documents and extracting pages. See "Merge and split with Document" (edit-merge-split.md).
- PDF/A, PDF/X, tagged PDF and PDF/UA. See "PDF standards: PDF/A and PDF/X" (accessibility-and-compliance.md) and "Tagged PDF and PDF/UA" (accessibility-and-compliance.md).
- ZUGFeRD / Factur-X electronic invoices. See "ZUGFeRD / Factur-X electronic invoices" (accessibility-and-compliance.md).
- Passwords, permissions and digital signatures. See "Digital signature element" (security-and-signatures.md) and "Passwords, permissions and encryption" (security-and-signatures.md).

## Customizing a converted document

Online: https://docs.html-to-pdf.net/anywhere-library/html/PostConvertCustomizationOverview.htm

ExpertPdf Anywhere converts web pages to PDF, and it is also a **PDF library**: the document produced by a conversion can be changed before it is saved, existing PDF documents can be loaded and edited, several documents can be merged into one, and pages, texts, images and shapes can be added to a document.

The `PdfConverter` class offers a set of methods that return the result of the HTML to PDF conversion as a `Document` object instead of the bytes of a PDF file:

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

`PdfConverter.GetPdfDocumentObjectFromUrlAsync(string, CancellationToken)` and `PdfConverter.GetPdfDocumentObjectFromHtmlStringAsync(string, string, CancellationToken)` do the same without blocking the calling thread, and take a cancellation token.

The returned object is an instance of the `Document` class, which gives access to the pages of the PDF document. You can iterate through the document pages, add new pages to the document, append other PDF documents, or add new elements like texts and images to the document pages. After the changes, the document is saved to a file, to a stream or to a byte array with one of the *Save* methods of the `Document` class. The document implements `IDisposable`: dispose it, or call its *Close* method, when you no longer need it. The classes used to change the generated PDF document are in the `PdfDocument` namespace.

When the `Document` object is returned by one of the methods above, it already contains the PDF pages generated by the converter from the URL or from the HTML string. The pages are in the `Document.Pages` collection. New pages with the desired size, orientation and margins can be added to the collection and new elements can be added to any page in the collection.

The following sections describe the elements that can be added to a page of a PDF document. A `HtmlToPdfElement` object makes possible several conversions in the same PDF document, a `HtmlToImageElement` object embeds the image of an HTML document into the PDF document, and a `WordToPdfElement` object adds the content of a Word document. You can also add texts, images, shapes, digital signatures, bookmarks, templates, watermarks, file attachments and notes.

### Sample Code

In the code sample below, the converter records where the *IMG*, *H1* and *H2* elements of the HTML document were rendered in the PDF, and each of them is then highlighted with a green rectangle drawn on the PDF page:

```csharp
PdfConverter pdfConverter = new PdfConverter();

// the HTML elements whose position in the PDF we want: the images and the h1 / h2
// headings
pdfConverter.HtmlElementsMappingOptions.HtmlElementSelectors = new string[] { "img",
    "h1", "h2" };

using (Document pdfDocument = pdfConverter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/article"))
{
    HtmlElementMappingsCollection mappings =
        pdfConverter.HtmlElementsMappingOptions.HtmlElementsMappingResult;
    foreach (HtmlElementMapping elementMapping in mappings)
    {
        // an element can span several pages: one rectangle for each page where it
        // was rendered
        foreach (HtmlElementPdfRectangle location in elementMapping.PdfRectangles)
        {
            PdfPage page = pdfDocument.Pages[location.PageIndex];
            RectangleF rectangle = location.Rectangle;

            // draw a green frame around the element
            RectangleElement frame = new RectangleElement(rectangle.X, rectangle.Y,
                rectangle.Width, rectangle.Height);
            frame.ForeColor = Color.Green;
            page.AddElement(frame);
        }
    }
    pdfDocument.Save("elements-location.pdf");
}
```

```vbnet
Dim pdfConverter As New PdfConverter()

' the HTML elements whose position in the PDF we want: the images and the h1 / h2
' headings
pdfConverter.HtmlElementsMappingOptions.HtmlElementSelectors = New String() {"img",
    "h1", "h2"}

Using pdfDocument As Document = pdfConverter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/article")
    Dim mappings As HtmlElementMappingsCollection =
        pdfConverter.HtmlElementsMappingOptions.HtmlElementsMappingResult
    For Each elementMapping As HtmlElementMapping In mappings
        ' an element can span several pages: one rectangle for each page where it was
        ' rendered
        For Each location As HtmlElementPdfRectangle In elementMapping.PdfRectangles
            Dim page As PdfPage = pdfDocument.Pages(location.PageIndex)
            Dim rectangle As RectangleF = location.Rectangle

            ' draw a green frame around the element
            Dim frame As New RectangleElement(rectangle.X, rectangle.Y,
                rectangle.Width, rectangle.Height)
            frame.ForeColor = Color.Green
            page.AddElement(frame)
        Next
    Next
    pdfDocument.Save("elements-location.pdf")
End Using
```

## Coordinates and units

Online: https://docs.html-to-pdf.net/anywhere-library/html/CoordinatesSystemAndGraphicUnits.htm

The ExpertPdf Anywhere document model uses the point (1/72 inch) as its graphic unit. All the coordinates and sizes of the page elements, pages, margins and templates are specified in points. When the size of an element is known in pixels (for example the size of an image or of an HTML page), it can be converted to points with the static methods of the `UnitsConverter` class. A pixel is 1/96 inch, the resolution a browser uses to lay out a web page:

```csharp
public static float PointsToPixels(float points);
public static float PixelsToPoints(float pixels);
```

```vbnet
Public Shared Function PointsToPixels(points As Single) As Single
Public Shared Function PixelsToPoints(pixels As Single) As Single
```

The origin of the coordinates system is the top left corner of the drawable area of the page or template: on a page it is inside the page margins and below the document header template, if there is one. The positive X coordinates go to the right and the positive Y coordinates go down. The drawable area of a page or template is given by its `ElementsRenderer.ClientRectangle` property.

## The Document class

Online: https://docs.html-to-pdf.net/anywhere-library/html/DocumentClass.htm

The `Document` class represents a PDF document in the ExpertPdf Anywhere document model. An empty document is created with the default constructor, and a document is created from an existing PDF document with the constructors that take the path of a .pdf file or a stream containing the PDF document. Either document can then be changed with the document model. The constructors with a password open a password protected PDF document; the password can be the user or the owner password. Here are the `Document` class constructors:

```csharp
public Document();
public Document(Stream pdfStream);
public Document(string pdfFileName);
public Document(Stream pdfStream, string pdfPassword);
public Document(string pdfFileName, string pdfPassword);
public Document(PdfStandardSubset pdfSubset);
public Document(ColorSpace colorSpace);
public Document(PdfStandardSubset pdfSubset, ColorSpace colorSpace);
```

```vbnet
Public Sub New()
Public Sub New(pdfStream As Stream)
Public Sub New(pdfFileName As String)
Public Sub New(pdfStream As Stream, pdfPassword As String)
Public Sub New(pdfFileName As String, pdfPassword As String)
Public Sub New(pdfSubset As PdfStandardSubset)
Public Sub New(colorSpace As ColorSpace)
Public Sub New(pdfSubset As PdfStandardSubset, colorSpace As ColorSpace)
```

The `Document` class gives access to the pages, the fonts, the bookmarks, the security settings, the templates, the document information and the viewer preferences. For example, a new page is added to the document with the `Document.AddPage` method, a new font with the `Document.AddFont(Font)` method and a new template with the `Document.AddTemplate(RectangleF)` method. The document is saved with the *Save* methods: to a file, to a stream, or to a byte array.

The class implements `IDisposable`: create the document in a `using` block, or call `Document.Close` when you no longer need it. A document created from a stream closes that stream when it is closed, unless `Document.DetachStream` was called first.

### Code Sample

The code below shows a typical use of the `Document` class: it creates a document, sets its properties, adds a page with a title, adds an HTML to PDF element below the title, and adds a text after the HTML content, on the page where that content ended.

```csharp
// a new PDF document with A4 pages and 20 point margins
using (Document document = new Document())
{
    document.DocumentInformation.Title = "Report";
    document.DocumentInformation.Author = "Example Inc.";

    PdfPage page = document.AddPage(PageSize.A4, new Margins(20, 20, 20, 20),
        PDFPageOrientation.Portrait);
    PdfFont titleFont = document.AddFont(new Font("Arial", 18, FontStyle.Bold));

    // a title, then HTML content right below it
    AddElementResult titleResult = page.AddElement(new TextElement(0, 0,
        "Monthly report", titleFont));

    float htmlTop = titleResult.EndPageBounds.Bottom + 10;
    HtmlToPdfElement html = new HtmlToPdfElement(0, htmlTop,
        "https://www.example.com/report-body");
    AddElementResult htmlResult = page.AddElement(html);

    // the HTML can span several pages: continue on the page where it ended
    PdfPage lastPage = document.Pages[htmlResult.EndPageIndex];
    lastPage.AddElement(new TextElement(0,
        htmlResult.EndPageBounds.Bottom + 10, "End of the report", titleFont));

    document.Save("report.pdf");
}
```

```vbnet
' a new PDF document with A4 pages and 20 point margins
Using document As New Document()
    document.DocumentInformation.Title = "Report"
    document.DocumentInformation.Author = "Example Inc."

    Dim page As PdfPage = document.AddPage(PageSize.A4, New Margins(20, 20, 20, 20),
        PDFPageOrientation.Portrait)
    Dim titleFont As PdfFont = document.AddFont(
        New Font("Arial", 18, FontStyle.Bold))

    ' a title, then HTML content right below it
    Dim titleResult As AddElementResult = page.AddElement(New TextElement(0, 0,
        "Monthly report", titleFont))

    Dim htmlTop As Single = titleResult.EndPageBounds.Bottom + 10
    Dim html As New HtmlToPdfElement(0, htmlTop,
        "https://www.example.com/report-body")
    Dim htmlResult As AddElementResult = page.AddElement(html)

    ' the HTML can span several pages: continue on the page where it ended
    Dim lastPage As PdfPage = document.Pages(htmlResult.EndPageIndex)
    lastPage.AddElement(New TextElement(0,
        htmlResult.EndPageBounds.Bottom + 10, "End of the report", titleFont))

    document.Save("report.pdf")
End Using
```

### PDF standards (PDF/A, PDF/X)

By default the library creates standard PDF documents that every PDF reader opens. Create the document with a `PdfStandardSubset` to make it conform to an ISO subset of PDF - for long term archiving (PDF/A), printing (PDF/X) or electronic invoices (PDF/A-3):

| PdfStandardSubset | Standard | Use |
|---|---|---|
| Full | PDF | The default: every PDF feature can be used. |
| Pdf_A_1b | PDF/A-1b (ISO 19005-1) | Long term archiving, for systems that require PDF/A-1. No transparency. |
| Pdf_A_1a | PDF/A-1a | PDF/A-1b with a tagged, accessible structure; the PDF/A-1 level that combines with PDF/UA-1. Needs a tagged document. |
| Pdf_A_2b | PDF/A-2b (ISO 19005-2) | Long term archiving; transparency and layers allowed. The usual choice for archiving. |
| Pdf_A_2u | PDF/A-2u | PDF/A-2b with Unicode text that can be reliably extracted and searched. |
| Pdf_A_2a | PDF/A-2a | PDF/A-2u with a tagged, accessible structure. Needs a tagged document. |
| Pdf_A_3b | PDF/A-3b (ISO 19005-3) | PDF/A-2b that can embed files of any type (associated files): electronic invoices, source data. |
| Pdf_A_3u | PDF/A-3u | PDF/A-3b with Unicode text that can be reliably extracted and searched. |
| Pdf_A_3a | PDF/A-3a | PDF/A-3u with a tagged, accessible structure. Needs a tagged document. |
| Pdf_A_4 | PDF/A-4 (ISO 19005-4) | Long term archiving on PDF 2.0; the level that combines with PDF/UA-2. |
| Pdf_A_4e, Pdf_A_4f | PDF/A-4e, PDF/A-4f | The PDF/A-4 variants for engineering documents (3D, rich media) and for embedded files of any kind. |
| Pdf_X_1a | PDF/X-1a (ISO 15930) | Graphics exchange for printing: CMYK colors, no transparency. |

A document created with a level A standard must be tagged: set `Document.Tagged` to true and tag its content (see Tagged PDF). The accessibility standard, PDF/UA-1 or PDF/UA-2, is declared separately with `Document.AccessibilityStandard`. A PDF/A or PDF/X document cannot be encrypted. An existing document is converted to PDF/A with `Document.ConvertToPdfA(PdfStandardSubset)`. See "PDF standards: PDF/A and PDF/X" (accessibility-and-compliance.md) for more about the standards.

```csharp
using (Document document = new Document(PdfStandardSubset.Pdf_A_2b))
{
    document.DocumentInformation.Title = "Annual report";
    // ... add pages and content ...
    document.Save("report-pdfa.pdf");
}
```

```vbnet
Using document As New Document(PdfStandardSubset.Pdf_A_2b)
    document.DocumentInformation.Title = "Annual report"
    ' ... add pages and content ...
    document.Save("report-pdfa.pdf")
End Using
```

A PDF/A-3 document can carry other files - the data behind a report, the source document - added with `AddAssociatedFile` and an `AttachmentRelationship`. For electronic invoices see ZUGFeRD / Factur-X invoices; for accessible documents see Tagged PDF.

```csharp
using (Document document = new Document(PdfStandardSubset.Pdf_A_3b))
{
    // ... add pages and content ...
    document.AddAssociatedFile("sales.csv", AttachmentRelationship.Data, "text/csv",
        "Sales figures");
    document.Save("sales-report.pdf");
}
```

```vbnet
Using document As New Document(PdfStandardSubset.Pdf_A_3b)
    ' ... add pages and content ...
    document.AddAssociatedFile("sales.csv", AttachmentRelationship.Data, "text/csv",
        "Sales figures")
    document.Save("sales-report.pdf")
End Using
```

### Page labels

Page labels are the page numbers a PDF viewer shows and accepts in its page box instead of the page positions - for example i, ii, iii for the front pages of a report and 1, 2, 3 for its body, or A-1, A-2 for an appendix. They do not change the pages. `AddPageLabelRange` starts a range at a page position: a `PageLabelStyle` (decimal, roman or letters, or no number), an optional prefix and the number of the first page of the range. A range ends where the next one starts; the pages before the first range are numbered 1, 2, 3. `Document.GetPageLabel(int)` returns the label of a page and `Document.ClearPageLabels` removes the ranges.

```csharp
using (Document document = new Document())
{
    PdfFont font = document.AddFont(new Font("Arial", 12));
    for (int i = 0; i < 9; i++)
    {
        PdfPage page = document.AddPage();
        page.AddElement(new TextElement(0, 0, "Page " + (i + 1), font));
    }

    // i, ii, iii for the first three pages, then 1, 2, 3..., and A-1, A-2... for the
    // appendix that starts on page 8
    document.AddPageLabelRange(0, PageLabelStyle.LowercaseRoman);
    document.AddPageLabelRange(3, PageLabelStyle.Decimal);
    document.AddPageLabelRange(7, PageLabelStyle.Decimal, "A-", 1);

    document.Save("labels.pdf");
}
```

```vbnet
Using document As New Document()
    Dim font As PdfFont = document.AddFont(New Font("Arial", 12))
    For i As Integer = 0 To 8
        Dim page As PdfPage = document.AddPage()
        page.AddElement(New TextElement(0, 0, "Page " & (i + 1), font))
    Next

    ' i, ii, iii for the first three pages, then 1, 2, 3..., and A-1, A-2... for the
    ' appendix that starts on page 8
    document.AddPageLabelRange(0, PageLabelStyle.LowercaseRoman)
    document.AddPageLabelRange(3, PageLabelStyle.Decimal)
    document.AddPageLabelRange(7, PageLabelStyle.Decimal, "A-", 1)

    document.Save("labels.pdf")
End Using
```

The ranges refer to the page positions when the document is saved: add them after the pages are added or removed. Page labels can be used in PDF/A documents.

### Named destinations

A named destination is a place in the document that a web page or another document can link to by name, with the name after # in the address of the document: *report.pdf#summary*. `Document.AddNamedDestination(string, ExplicitDestination)` adds one for an `ExplicitDestination` (a page, a point on the page and how the page is displayed). The name has up to 127 characters: ASCII letters, digits and the characters - _ . :

```csharp
// the summary starts 200 points down the second page
document.AddNamedDestination("summary", new ExplicitDestination(document.Pages[1],
    new PointF(0, 200)));
```

```vbnet
' the summary starts 200 points down the second page
document.AddNamedDestination("summary", New ExplicitDestination(document.Pages(1),
    New PointF(0, 200)))
```

### Table of contents

A `TableOfContents` lists headings with the numbers of the pages they are on, each entry a link to its heading. `Document.InsertTableOfContents(TableOfContents, int)` inserts it at a page position, on as many pages as the entries need, and returns the number of pages inserted. Add the entries after the content: `TableOfContents.AddEntries(Document, HtmlElementMappingsCollection)` takes the headings an HTML conversion mapped (HtmlElementsMappingOptions with the "h1", "h2"... selectors; h1 is level 1, h2 level 2), and `TableOfContents.AddEntry(string, int, PdfPage, float)` adds an entry of your own.

The page numbers are the page labels when the document has page label ranges. Ranges that start at or after the position where the table is inserted move with their pages, so the content can be numbered first and the table inserted before it. The title, fonts, colors, indent and dotted leaders are properties of the table of contents. In a tagged document the table is tagged as a TOC element.

```csharp
using (Document document = new Document())
{
    PdfPage page = document.AddPage();

    // a long HTML document; the converter maps where its headings are
    HtmlToPdfElement html = new HtmlToPdfElement("https://www.example.com/manual");
    html.HtmlElementsMappingOptions.HtmlElementSelectors = new string[] { "h1", "h2",
        "h3" };
    page.AddElement(html);

    TableOfContents toc = new TableOfContents();
    toc.Title = "Contents";
    toc.AddEntries(document,
        html.HtmlElementsMappingOptions.HtmlElementsMappingResult);

    // the content pages are numbered 1, 2, 3...; the table pages before them
    // i, ii...
    document.AddPageLabelRange(0, PageLabelStyle.Decimal);
    int tocPages = document.InsertTableOfContents(toc, 0);
    document.AddPageLabelRange(0, PageLabelStyle.LowercaseRoman);

    document.Save("manual.pdf");
}
```

```vbnet
Using document As New Document()
    Dim page As PdfPage = document.AddPage()

    ' a long HTML document; the converter maps where its headings are
    Dim html As New HtmlToPdfElement("https://www.example.com/manual")
    html.HtmlElementsMappingOptions.HtmlElementSelectors = New String() {"h1", "h2",
        "h3"}
    page.AddElement(html)

    Dim toc As New TableOfContents()
    toc.Title = "Contents"
    toc.AddEntries(document,
        html.HtmlElementsMappingOptions.HtmlElementsMappingResult)

    ' the content pages are numbered 1, 2, 3...; the table pages before them
    ' i, ii...
    document.AddPageLabelRange(0, PageLabelStyle.Decimal)
    Dim tocPages As Integer = document.InsertTableOfContents(toc, 0)
    document.AddPageLabelRange(0, PageLabelStyle.LowercaseRoman)

    document.Save("manual.pdf")
End Using
```

### Select Color Space (RGB, CMYK, Grayscale)

By default, the document model uses the **RGB** color space to draw graphics and images. The `Document` class constructors with a parameter of `ColorSpace` type create a document that draws its graphics in the **CMYK** or **Grayscale** color space instead.

## PDF renderers

Online: https://docs.html-to-pdf.net/anywhere-library/html/PDFRenderers.htm

A *PDF renderer* is an object capable of rendering page elements into the PDF document. The document model defines two types of renderers derived from the `ElementsRenderer` abstract class: the `PdfPage` class, which renders elements in a PDF page, and the `Template` class, which renders elements in a template area repeated on the pages of the PDF document.

Both renderers accept the same page elements: HTML to PDF and HTML to image elements, Word elements, text, images, shapes, and the interactive elements (links, text notes, file attachments). An element is added with the `ElementsRenderer.AddElement(PageElement)` method, or without blocking with `ElementsRenderer.AddElementAsync(PageElement)`.

An interactive element added to a template is created on each page the template is drawn on, at the same position, so a link in a header works on every page. Two elements are created only once: a digital signature signs the document once and is placed on the first page the template is drawn on, and a file attachment without a rectangle is attached to the document once.

## The PdfPage class

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfPageClass.htm

The `PdfPage` class represents a page in the PDF document. A `PdfPage` object is created with the `Document.AddPage` and `Document.InsertPage(int, PageSize, Margins, PDFPageOrientation)` methods of the `Document` class. When the `Document.AddPage` method is called without parameters, a page with the default margins, size and orientation is created. The margins are inherited from the `Document.Margins` property. The default orientation is *Portrait* for the first page of the document, and the orientation of the previous page otherwise. The default size is *A4* for the first page, and the size of the previous page otherwise. Each of these settings can be given to the other overloads of the `Document.AddPage` method. A page can have its own size and orientation, so a document can mix page sizes.

A page renders the page elements added to it with `PdfPage.AddElement(PageElement)`. Its `PdfPage.RotationAngle` property rotates the page when it is displayed, `PdfPage.ShowHeaderTemplate` and `PdfPage.ShowFooterTemplate` hide the document header or footer on that page, and `PdfPage.CustomHeaderTemplate` and `PdfPage.CustomFooterTemplate` replace them on that page only.

### Sample Code

The sample code below opens an existing PDF document, adds a new page (size Letter, 5 points margins) and saves the document back to the file:

```csharp
using (Document existingDoc = new Document(pdfFilePath))
{
    PdfPage newPage = existingDoc.AddPage(PageSize.Letter, new Margins(5));
    existingDoc.Save(pdfFilePath);
}
```

```vbnet
Using existingDoc As New Document(pdfFilePath)
    Dim newPage As PdfPage = existingDoc.AddPage(PageSize.Letter, New Margins(5))
    existingDoc.Save(pdfFilePath)
End Using
```

## Templates

Online: https://docs.html-to-pdf.net/anywhere-library/html/TemplateClass.htm

The `Template` class represents content repeated on the pages of the PDF document. Templates are used for headers and footers, watermarks, page numbers or any other content that has to be repeated on the pages of the document.

A `Template` renders the same page elements as a `PdfPage`: HTML to PDF elements, texts, images and shapes, and the interactive elements, which are created on each page the template is drawn on (see "PDF renderers" (pdf-creation-elements.md)). A `Template` object is created with the `Document.AddTemplate(RectangleF)` methods of the `Document` class.

There are two kinds of templates: **predefined templates** like the header and the footer, and **custom templates**. Both are instances of the `Template` class, but the space of the predefined templates is taken from the drawable area of the PDF pages.

`Template.DrawOnFirstPage`, `Template.DrawOnOddPages` and `Template.DrawOnEvenPages` select the pages a template is drawn on; a predefined template keeps its space on the pages it is not drawn on. `Template.BringToFront` (the default) draws the template over the page content and `Template.SendToBackground` draws it behind. On a single page, `PdfPage.CustomHeaderTemplate` and `PdfPage.CustomFooterTemplate` replace the document header and footer.

### Predefined Templates

The predefined templates are the document header, footer, left bar template and right bar template (`Document.HeaderTemplate`, `Document.FooterTemplate`, `Document.LeftTemplate` and `Document.RightTemplate`). The predefined templates are automatically docked to the corresponding side of the PDF page.

For example, the predefined header is automatically docked to the top of the PDF page, which means the header location is the top left corner and the width of the header template is the width of the page. Also if a predefined header was set, the available client area in PDF page will start right under the header. Similarly, if a predefined footer was set, it will be docked to the bottom of page and the available client area in PDF will end right above the footer template. It is recommended to set the predefined templates before starting to add elements to the document.

Below is a sample code to add a footer to the PDF document. Another special element in the sample code below is the text element used to add page numbering. In a text element added to a template, the **&p;** token is replaced with the current page number and the **&P;** token with the total number of pages of the PDF document. `Template.PageNumberingStartIndex` and `Template.PageNumberingPageCountIncrement` offset the two numbers.

```csharp
private void AddHtmlFooter(Document document, PdfFont pageNumberFont,
    string footerHtmlUrl)
{
    // a template 60 points high, repeated at the bottom of every page
    document.FooterTemplate = document.AddTemplate(
        document.Pages[0].ClientRectangle.Width, 60);

    // HTML content in the footer
    HtmlToPdfElement footerHtml = new HtmlToPdfElement(footerHtmlUrl);
    document.FooterTemplate.AddElement(footerHtml);

    // the page number: &p; is the current page, &P; the number of pages
    TextElement pageNumber = new TextElement(
        document.FooterTemplate.ClientRectangle.Width - 120, 30,
        "Page &p; of &P;", pageNumberFont);
    document.FooterTemplate.AddElement(pageNumber);
}
```

```vbnet
Private Sub AddHtmlFooter(document As Document, pageNumberFont As PdfFont,
    footerHtmlUrl As String)
    ' a template 60 points high, repeated at the bottom of every page
    document.FooterTemplate = document.AddTemplate(document.Pages(
        0).ClientRectangle.Width, 60)

    ' HTML content in the footer
    Dim footerHtml As New HtmlToPdfElement(footerHtmlUrl)
    document.FooterTemplate.AddElement(footerHtml)

    ' the page number: &p; is the current page, &P; the number of pages
    Dim pageNumber As New TextElement(
        document.FooterTemplate.ClientRectangle.Width - 120, 30,
        "Page &p; of &P;", pageNumberFont)
    document.FooterTemplate.AddElement(pageNumber)
End Sub
```

### Custom Templates

The custom templates can be used to add watermarks or any other content that must be repeated in the same position on each page. In the code sample below, a watermark containing a text and an image is added to an existing document:

```csharp
// open an existing document
using (Document document = new Document("contract.pdf"))
{
    PdfPage firstPage = document.Pages[0];

    // a watermark repeated on every page: a template in the middle of the page
    // with a text and an image
    float width = 220, height = 80;
    float x = (firstPage.ClientRectangle.Width - width) / 2;
    float y = (firstPage.ClientRectangle.Height - height) / 2;
    Template watermark = document.AddTemplate(new RectangleF(x, y, width, height));

    PdfFont font = document.AddFont(StdFontBaseFamily.HelveticaBold);
    font.Size = 24;
    TextElement text = new TextElement(0, 0, "CONFIDENTIAL", font);
    text.ForeColor = Color.Red;
    // mostly transparent: the page content stays readable
    text.Transparency = 30;
    watermark.AddElement(text);

    ImageElement logo = new ImageElement(0, 40, 60, 30, "logo.png");
    logo.Transparency = 30;
    watermark.AddElement(logo);

    document.Save("contract-watermarked.pdf");
}
```

```vbnet
' open an existing document
Using document As New Document("contract.pdf")
    Dim firstPage As PdfPage = document.Pages(0)

    ' a watermark repeated on every page: a template in the middle of the page
    ' with a text and an image
    Dim width As Single = 220, height As Single = 80
    Dim x As Single = (firstPage.ClientRectangle.Width - width) / 2
    Dim y As Single = (firstPage.ClientRectangle.Height - height) / 2
    Dim watermark As Template = document.AddTemplate(
        New RectangleF(x, y, width, height))

    Dim font As PdfFont = document.AddFont(StdFontBaseFamily.HelveticaBold)
    font.Size = 24
    Dim text As New TextElement(0, 0, "CONFIDENTIAL", font)
    text.ForeColor = Color.Red
    ' mostly transparent: the page content stays readable
    text.Transparency = 30
    watermark.AddElement(text)

    Dim logo As New ImageElement(0, 40, 60, 30, "logo.png")
    logo.Transparency = 30
    watermark.AddElement(logo)

    document.Save("contract-watermarked.pdf")
End Using
```

## Page elements

Online: https://docs.html-to-pdf.net/anywhere-library/html/PDFPageElements.htm

A page element represents anything that can be added to a renderer (a page or a template), and it derives from the `PageElement` abstract class. The document model defines two types of page elements: graphic elements, which derive from the `PageGraphicElement` class, and interactive page elements, which derive directly from the `PageElement` class.

The graphic elements have common properties like fore color, back color, transparency and rotation. Examples of graphic elements are the HTML to PDF element, the Word, Excel and PowerPoint to PDF elements, the text element, the image element and the shape elements. Examples of interactive page elements are the internal link element, the URL link element, the text note element and the file attachment element.

In a tagged document, the `PageElement.TagType`, `PageElement.AlternateText`, `PageElement.ActualText` and `PageElement.Artifact` properties of an element say what the element is for assistive technology (see "Tagged PDF and PDF/UA" (accessibility-and-compliance.md)).

### Page Graphic Elements

The document model defines the following graphic elements that can be added to a page:

- "HTML to PDF element" (pdf-creation-elements.md)
- "HTML to image element" (pdf-creation-elements.md)
- "Word to PDF element" (office-to-pdf.md) - the content of a Word document (docx, doc, rtf and the other formats the `WordToPdfElement` reads), RTF included.
- "Excel to PDF element" (office-to-pdf.md) - the sheets of an Excel workbook (xlsx, xls, csv and the other formats the Excel to PDF converter reads).
- "PowerPoint to PDF element" (office-to-pdf.md) - the slides of a PowerPoint presentation.
- "Text element and fonts" (pdf-creation-elements.md)
- "Image element" (pdf-creation-elements.md)
- "Shape elements" (pdf-creation-elements.md)

The page graphic elements derive from the `PageGraphicElement` class. This class defines the following properties and methods that change the aspect of the element:

- `PageGraphicElement.ForeColor` - the color used to draw a shape, for example the color of a line when drawing a line element or the text color when drawing a text.
- `PageGraphicElement.BackColor` - the background color of a graphic element, for example the fill color of a rectangle element.
- `PageGraphicElement.Gradient` - a gradient color used to fill a shape like a rectangle element.
- `PageGraphicElement.Rotate(float)` - rotates the coordinates system clockwise by the specified angle before the element is rendered. `PageGraphicElement.Translate(float, float)`, `PageGraphicElement.Scale(float, float)` and `PageGraphicElement.Skew(float, float)` transform the coordinates system the same way.
- `PageGraphicElement.Transparency` - the element transparency, a value between 0 and 100: 0 means completely transparent and 100 (the default) means completely opaque.
- `PageGraphicElement.LineStyle` - the line width, dash style, line join style and line cap style of the elements that draw lines, like the rectangle, ellipse and line elements.
- `PageGraphicElement.ColorSpace` - the color space (RGB, CMYK or grayscale) the element colors are written in.

### Page Interactive Elements

The page interactive elements derive directly from the `PageElement` class. They add interactive features to the PDF document like internal and URL links, file attachments and links, popup text notes and digital signatures.

The document model defines the following interactive elements: `DigitalSignatureElement`, `LinkUrlElement`, `InternalLinkElement`, `SoundLinkElement`, `FileLinkElement`, `FileAttachmentElement` and `TextNoteElement`. They can be added to a page or to a template; on a template they are created on each page the template is drawn on.

## HTML to PDF element

Online: https://docs.html-to-pdf.net/anywhere-library/html/HTMLToPDFConverterElement.htm

The `HtmlToPdfElement` graphic element renders a web page or an HTML string into a page or a template of a PDF document, with the Chromium engine. It lets you choose the position and the size of the content rendered from HTML, and add many HTML to PDF conversions to the same document.

When the rendered content spans several pages, the `AddElementResult` object returned after the element is added to a page or template tells the index of the last page and the rectangle the content occupies on it, and `HtmlToPdfElement.RenderedPagesRectangles` gives the rectangle drawn on each page.

The `HtmlToPdfElement` offers many constructors that call the following two constructors with default values for converting a URL or an HTML string to PDF:

```csharp
public HtmlToPdfElement(float x, float y, float width, float height,
    string urlToConvert, int htmlViewerWidth, int htmlViewerHeight);

public HtmlToPdfElement(float x, float y, float width, float height,
    string htmlStringToConvert, string htmlStringBaseURL,
    int htmlViewerWidth, int htmlViewerHeight);
```

```vbnet
Public Sub New(x As Single, y As Single, width As Single, height As Single,
    urlToConvert As String, htmlViewerWidth As Integer,
    htmlViewerHeight As Integer)

Public Sub New(x As Single, y As Single, width As Single, height As Single,
    htmlStringToConvert As String, htmlStringBaseURL As String,
    htmlViewerWidth As Integer, htmlViewerHeight As Integer)
```

The first constructor creates a *URL to PDF* element at the specified x and y coordinates with the specified *width* and *height*. The width and height in pixels of the browser window the page is laid out in are given by the *htmlViewerWidth* and *htmlViewerHeight* parameters.

- *x* - The x position in points where the rendered content is placed.
- *y* - The y position in points where the rendered content is placed.
- *width* - The destination width in points for the rendered content. If the width is negative, the destination width is the width available in the page or template.
- *height* - The destination height in points for the rendered content. If the height is negative, the height is determined automatically so all the content is rendered.
- *urlToConvert* - The URL to convert to PDF.
- *htmlViewerWidth* - The browser window width in pixels. The default value is 1024 pixels. The effect of this parameter is the same as viewing the HTML page in a browser window with the specified width. Content wider than the window is cut at its right edge, unless `HtmlToPdfElement.FitWideContent` is set.
- *htmlViewerHeight* - The browser window height in pixels. A negative value, the default, lets the height be determined from the content. With `HtmlToPdfElement.ClipHtmlView` set, a positive height is a fixed window height and the content below it is left out of the PDF.

The second constructor creates an *HTML string to PDF* element at the specified x and y coordinates with the specified *width* and *height*.

- *htmlStringToConvert* - The HTML string converted to PDF.
- *htmlStringBaseURL* - The full URL of the page the string was taken from, used to resolve the images and CSS files referenced by a relative URL in the HTML string. When it is an http or https URL, the string is loaded as the page at that URL, so its scripts, fonts and cookies work as they do for that page. When this parameter is null, no base URL is used.

Below is a sample code that adds a web page and, after it, an HTML string to a PDF page with `HtmlToPdfElement` objects:

```csharp
using (Document document = new Document())
{
    PdfPage page = document.AddPage();

    // a web page rendered at the top left corner of the page, with the width of the
    // page and the height it needs; the content continues on new pages when it is
    // longer than a page
    HtmlToPdfElement webPage = new HtmlToPdfElement(0, 0,
        "https://www.example.com/article");
    AddElementResult result = page.AddElement(webPage);

    // an HTML string after it, with a base URL for its relative images and style
    // sheets
    HtmlToPdfElement note = new HtmlToPdfElement(0, result.EndPageBounds.Bottom + 20,
        "<p style='color:gray'>Printed from <img src='logo.png' height='16'>" +
            " example.com</p>", "https://www.example.com/");
    document.Pages[result.EndPageIndex].AddElement(note);

    document.Save("article.pdf");
}
```

```vbnet
Using document As New Document()
    Dim page As PdfPage = document.AddPage()

    ' a web page rendered at the top left corner of the page, with the width of the
    ' page and the height it needs; the content continues on new pages when it is
    ' longer than a page
    Dim webPage As New HtmlToPdfElement(0, 0, "https://www.example.com/article")
    Dim result As AddElementResult = page.AddElement(webPage)

    ' an HTML string after it, with a base URL for its relative images and style
    ' sheets
    Dim note As New HtmlToPdfElement(0, result.EndPageBounds.Bottom + 20,
        "<p style='color:gray'>Printed from <img src='logo.png' height='16'>" &
            " example.com</p>", "https://www.example.com/")
    document.Pages(result.EndPageIndex).AddElement(note)

    document.Save("article.pdf")
End Using
```

`ElementsRenderer.AddElementAsync(PageElement)` starts the render of an HTML element at once and places it after the elements added before it, so several HTML elements added that way render at the same time.

### Content that continues on the next pages

An HtmlToPdfElement continues on the next pages when its content does not fit in the room left on the page - when it has no height of its own, or a height taller than that room - wherever it starts on the page, and its last page is drawn only down to where the content ends. A height taller than the room left stops the element once that much height is drawn. AddElement returns the page and the rectangle where the element ended, to place the next element right below it. This is `HtmlToPdfElement.ManagedContentTrimming` (default true); set it to false to draw only what fits in the room left on the page. `HtmlToPdfElement.ManagedContentTrimmingMode` chooses how the end of the content is found (Ink, the default, or Marker). A fixed window height (ClipHtmlView with an HtmlViewerHeight) keeps the element on one page.

The element continues on the page that follows when the document already has one, and adds a page otherwise. An added page has the size, orientation, margins and rotation of the page it continues. In a document loaded from an existing PDF file, the element is drawn only on the page it is added to.

`HtmlToPdfElement.MaxConversionTime` limits the time the conversion of the element may take once its page is loaded (default 60 seconds); a page that never finishes fails with a timeout error when the element is added. `HtmlToPdfElement.RenderOnTimeout` converts what is loaded at the navigation timeout instead of failing.

```csharp
using (Document document = new Document())
{
    PdfPage page = document.AddPage();
    PdfFont font = document.AddFont(new Font("Arial", 14));
    page.AddElement(new TextElement(0, 0, "Monthly report", font));

    // starts 300 points down the first page and continues on the next pages
    HtmlToPdfElement html = new HtmlToPdfElement(0, 300,
        "https://www.example.com/report");
    AddElementResult result = page.AddElement(html);

    // a text right below the end of the HTML content
    PdfPage endPage = document.Pages[result.EndPageIndex];
    endPage.AddElement(new TextElement(0, result.EndPageBounds.Bottom + 10,
        "End of report", font));
    document.Save("report.pdf");
}
```

```vbnet
Using document As New Document()
    Dim page As PdfPage = document.AddPage()
    Dim font As PdfFont = document.AddFont(New Font("Arial", 14))
    page.AddElement(New TextElement(0, 0, "Monthly report", font))

    ' starts 300 points down the first page and continues on the next pages
    Dim html As New HtmlToPdfElement(0, 300, "https://www.example.com/report")
    Dim result As AddElementResult = page.AddElement(html)

    ' a text right below the end of the HTML content
    Dim endPage As PdfPage = document.Pages(result.EndPageIndex)
    endPage.AddElement(New TextElement(0, result.EndPageBounds.Bottom + 10,
        "End of report", font))
    document.Save("report.pdf")
End Using
```

The mapped rectangles of the HTML elements mapping (see below) are on the document pages, ready for a table of contents.

### Page Breaks, Keep Together in HtmlToPdfElement

The converter supports the CSS properties that control the page breaks: *break-before: page*, *break-after: page* and *break-inside: avoid*, and the older *page-break-before: always*, *page-break-after: always* and *page-break-inside: avoid*. For example, with the *page-break-after: always* style applied to an HTML element (image, text, etc), you instruct the converter to insert a page break right after that element is rendered. A line of text is never cut between two pages.

A very useful feature when creating PDF reports is *keep together*, done with the *page-break-inside: avoid* style. It instructs the converter to avoid breaking the content of a group of HTML elements you want to keep together on the same page - a table, a table row, a div element, an image.

Below is an example of using the *page-break-inside: avoid* style. The table contains a large number of rows, each row containing an image on the left and a text on the right, and a row must not span two pages.

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

### Live HTTP Links in HtmlToPdfElement

The converter converts the HTTP links of the HTML document into links in the PDF document. This works on links containing text, images or any other content the HTML code allows, and it is the default behavior of the converter. To get no active links in the generated PDF document, set the `HtmlToPdfElement.LiveUrlsEnabled` property to *False*.

### Rendering engine and JavaScript

The element is rendered by Chromium, the engine of the Chrome browser, with HTML5, CSS3, web fonts and JavaScript. `HtmlToPdfElement.RenderingEngine` has the single value Chromium. The engine is deployed by the native engine package of the platform (or a platform package such as ExpertPdf.Anywhere.Linux) in a folder beside the application; `HtmlToPdfElement.CefEnginePath` points to the engine folder when it is somewhere else. The engine runs inside the application by default (`GlobalSettings.ChromiumEngineHostMode`); `HtmlToPdfElement.ForceOutOfProcess` runs one element in an engine process of its own.

`HtmlToPdfElement.ScriptsEnabled` lets the JavaScript of the HTML run (true by default), and `HtmlToPdfElement.ConversionDelay` waits for the scripts a number of seconds after the page is loaded. A page that draws with WebGL needs `HtmlToPdfElement.WebGlEnabled` set to true.

### Server Authentication in HtmlToPdfElement

When the web server asks for a user name and a password (HTTP authentication, for example Basic authentication), set them in the `HtmlToPdfElement.AuthenticationOptions` property of the `HtmlToPdfElement` object. A site that signs its users in with a login form is handled with `HtmlToPdfElement.LoginOptions`, and cookies and HTTP headers are sent with `HtmlToPdfElement.HttpCookies` and `HtmlToPdfElement.HttpHeaders` (see "Server authentication, cookies and HTTP headers" (web-elements-and-auth.md)).

Below you can find sample code for setting the user name and password for authentication when converting HTML to PDF using an `HtmlToPdfElement` object:

```csharp
htmlToPdfElement.AuthenticationOptions.Username = username;
htmlToPdfElement.AuthenticationOptions.Password = password;
```

```vbnet
htmlToPdfElement.AuthenticationOptions.Username = username
htmlToPdfElement.AuthenticationOptions.Password = password
```

### Bookmarks in HtmlToPdfElement

The converter can create bookmarks in the generated PDF document for the HTML elements you select. The bookmarks are controlled by the `HtmlToPdfElement.PdfBookmarkOptions` property and are created when a list of CSS selectors is set in the `PdfBookmarkOptions.HtmlElementSelectors` property: "h1" selects all the h1 elements, ".myclass" the elements with the CSS class myclass and "#myid" the element with the id myid.

For example, to create bookmarks for the H1 and H2 elements, and only for the H2 elements with the CSS class bookmark, use the following line of code:

```csharp
htmlToPdfElement.PdfBookmarkOptions.HtmlElementSelectors =
    new string[] { "h1", "h2.bookmark" };
```

```vbnet
htmlToPdfElement.PdfBookmarkOptions.HtmlElementSelectors =
    New String() {"h1", "h2.bookmark"}
```

With `PdfBookmarkOptions.HierarchicalBookmarks` set, the bookmarks are nested by the position of their selector in the list (the first selector gives the top level), and `PdfBookmarkOptions.MaxTitleLength` limits the length of a bookmark title.

### Internal Links in HtmlToPdfElement

The converter converts the HTML links with anchors found in the HTML document to internal links in the PDF. This feature can be used to create a table of contents in the generated PDF document.

An HTML link with an anchor consists of two HTML elements: a link defined by an *<a href="#target">Internal Link</a>* tag and the target of the link, an element with the id *target* or an *<a name="target">Link Target</a>* tag. The converter generates an internal link in the PDF from *"Internal Link"* to *"Link Target"*.

The generation of internal links can be disabled with **htmlToPdfElement.InternalLinksEnabled = false;**.

An internal link is recognized when it refers to the converted document. When the links of a page name that page by another URL than the one converted - for example a site whose start page is served for its root address and links to itself by its file name - pass the URL the links use in the *internalLinksDocUrl* parameter of the `HtmlToPdfElement` constructors. When converting an HTML string to PDF, pass the *baseUrl* and, when needed, the *internalLinksDocUrl* parameters.

### JPEG Compression of Images in PDF

The `Document` compresses images with the JPEG compression algorithm to reduce the size of the PDF document: the images added with an image element, and all the images of a document loaded from an existing PDF when it is saved. The JPEG compression reduces the quality of the images; when the JPEG compression level is increased, the quality of the images in the PDF decreases. An image element keeps transparent images and very small images lossless.

The `Document.JpegCompressionLevel` property sets the level used for JPEG compression on a scale from 0 to 100. When the compression level is 0 the compression is the lowest and the image quality is the best. The default JPEG compression level is 10, which offers a good balance between the quality of the images and the size of the PDF document.

For the best image quality, disable the JPEG compression of the images by setting `Document.JpegCompressionEnabled` to *False*. `Document.ImagesMaxResolution` resamples images that have more pixels than their size on the page needs.

### Retrieve HTML Elements Mapping to PDF from HtmlToPdfElement

The converter can tell the position in the generated PDF document of any HTML element. Knowing the position in the PDF document of an element from the HTML document allows you to create bookmarks for elements from the HTML document, create internal links between HTML elements, place texts or images over the HTML elements, or place a digital signature over a certain element.

This feature is accessed with the `HtmlToPdfElement.HtmlElementsMappingOptions` property of the `HtmlToPdfElement` object. Its `HtmlElementsMappingOptions.HtmlElementSelectors` property takes the CSS selectors of the HTML elements whose position you want: "h1" selects all the h1 elements, ".myclass" the elements with the CSS class myclass and "#myid" the element with the id myid. Set it before the element is added to a page.

The HTML elements mapping is returned in the `HtmlElementsMappingOptions.HtmlElementsMappingResult` property, a collection of `HtmlElementMapping` objects. Each one gives the PDF page index and the rectangle inside that page for each page where the element was rendered, and the element id, tag name, CSS class name, text and outer HTML code.

In the code sample below, all the *H1* and *IMG* elements and the elements with the ids *MyID1* and *MyID2* are highlighted with a red rectangle in the generated PDF:

```csharp
// the HTML elements for which you want to retrieve the position
htmlToPdfElement.HtmlElementsMappingOptions.HtmlElementSelectors =
    new string[] { "img", "h1", "#MyID1", "#MyID2" };

// add the element to a PDF page
page.AddElement(htmlToPdfElement);

// iterate over the HTML elements mappings and draw a red rectangle around the
// element in PDF
HtmlElementMappingsCollection mappings =
    htmlToPdfElement.HtmlElementsMappingOptions.HtmlElementsMappingResult;
foreach (HtmlElementMapping elementMapping in mappings)
{
    // an HTML element can span several PDF pages
    foreach (HtmlElementPdfRectangle pdfRectangle in elementMapping.PdfRectangles)
    {
        // the PDF page and the rectangle where the HTML element was rendered
        PdfPage elementPdfPage = document.Pages[pdfRectangle.PageIndex];
        RectangleF elementPdfRectangle = pdfRectangle.Rectangle;

        RectangleElement elementHighlightRectangle = new RectangleElement(
            elementPdfRectangle.X, elementPdfRectangle.Y,
            elementPdfRectangle.Width, elementPdfRectangle.Height);
        elementHighlightRectangle.ForeColor = Color.Red;

        elementPdfPage.AddElement(elementHighlightRectangle);
    }
}
```

```vbnet
' the HTML elements for which you want to retrieve the position
htmlToPdfElement.HtmlElementsMappingOptions.HtmlElementSelectors =
    New String() {"img", "h1", "#MyID1", "#MyID2"}

' add the element to a PDF page
page.AddElement(htmlToPdfElement)

' iterate over the HTML elements mappings and draw a red rectangle around the
' element in PDF
Dim mappings As HtmlElementMappingsCollection =
    htmlToPdfElement.HtmlElementsMappingOptions.HtmlElementsMappingResult
For Each elementMapping As HtmlElementMapping In mappings
    ' an HTML element can span several PDF pages
    For Each pdfRectangle As HtmlElementPdfRectangle In elementMapping.PdfRectangles
        ' the PDF page and the rectangle where the HTML element was rendered
        Dim elementPdfPage As PdfPage = document.Pages(pdfRectangle.PageIndex)
        Dim elementPdfRectangle As RectangleF = pdfRectangle.Rectangle

        Dim elementHighlightRectangle As New RectangleElement(
            elementPdfRectangle.X, elementPdfRectangle.Y,
            elementPdfRectangle.Width, elementPdfRectangle.Height)
        elementHighlightRectangle.ForeColor = Color.Red

        elementPdfPage.AddElement(elementHighlightRectangle)
    Next
Next
```

## HTML to image element

Online: https://docs.html-to-pdf.net/anywhere-library/html/HTMLToImageConverterElement.htm

The `HtmlToImageElement` graphic element renders a web page or an HTML string to an image with the Chromium engine and draws that image into a page or a template of a PDF document. Where an HTML to PDF element produces selectable text, links and vector graphics, this element produces a picture of the page. You can choose the position and the size of the image in the PDF page and add many HTML to image conversions to the same document.

When the image spans several pages, the `AddElementResult` object returned after the element is added to a page or template tells the index of the last page and the rectangle the image occupies on it.

The `HtmlToImageElement` offers many constructors that call the following two constructors with default values for converting a URL or an HTML string to an image:

```csharp
public HtmlToImageElement(float x, float y, float width, float height,
    string urlToConvert, int htmlViewerWidth, int htmlViewerHeight);

public HtmlToImageElement(float x, float y, float width, float height,
    string htmlStringToConvert, string htmlStringBaseURL,
    int htmlViewerWidth, int htmlViewerHeight);
```

```vbnet
Public Sub New(x As Single, y As Single, width As Single, height As Single,
    urlToConvert As String, htmlViewerWidth As Integer,
    htmlViewerHeight As Integer)

Public Sub New(x As Single, y As Single, width As Single, height As Single,
    htmlStringToConvert As String, htmlStringBaseURL As String,
    htmlViewerWidth As Integer, htmlViewerHeight As Integer)
```

The first constructor creates a *URL to image* element at the specified x and y coordinates with the specified *width* and *height*. The width and height in pixels of the browser window the page is laid out in are given by the *htmlViewerWidth* and *htmlViewerHeight* parameters.

- *x* - The x position in points where the image is placed.
- *y* - The y position in points where the image is placed.
- *width* - The destination width in points for the image. If the width is negative, the destination width is the width available in the page or template.
- *height* - The destination height in points for the image. If the height is negative, the height is determined automatically from the width and the proportions of the image.
- *urlToConvert* - The URL to convert.
- *htmlViewerWidth* - The browser window width in pixels. The default value is 1024 pixels. The effect of this parameter is the same as viewing the HTML page in a browser window with the specified width.
- *htmlViewerHeight* - The browser window height in pixels. A negative value, the default, captures the whole page; a positive value captures only that height of the page.

The second constructor creates an *HTML string to image* element at the specified x and y coordinates with the specified *width* and *height*.

- *htmlStringToConvert* - The HTML string to convert.
- *htmlStringBaseURL* - The full URL of the page the string was taken from, used to resolve the images and CSS files referenced by a relative URL in the HTML string. When this parameter is null, no base URL is used.

`HtmlToImageElement.FitWidth` (true by default) shrinks the image to the available width when it is wider, `HtmlToImageElement.StretchToFit` also enlarges a narrower image to that width, and `HtmlToImageElement.FitHeight` shrinks the image to the available height. A tall image continues on the next pages while `HtmlToImageElement.Paginate` is true (the default). `HtmlToImageElement.GetImage` returns the image as the bytes of a PNG file.

### Sample Code

The code below draws the image of an HTML fragment, 300 points wide, at the top of a new page:

```csharp
using (Document document = new Document())
{
    PdfPage page = document.AddPage();

    // the HTML is laid out in a 600 pixels wide window and drawn 300 points wide
    HtmlToImageElement card = new HtmlToImageElement(0, 0, 300,
        "<div style='padding:20px; background:#eef; font-size:24px'>" +
            "Account summary</div>", null, 600);
    page.AddElement(card);

    document.Save("card.pdf");
}
```

```vbnet
Using document As New Document()
    Dim page As PdfPage = document.AddPage()

    ' the HTML is laid out in a 600 pixels wide window and drawn 300 points wide
    Dim card As New HtmlToImageElement(0, 0, 300,
        "<div style='padding:20px; background:#eef; font-size:24px'>" &
            "Account summary</div>", Nothing, 600)
    page.AddElement(card)

    document.Save("card.pdf")
End Using
```

## Text element and fonts

Online: https://docs.html-to-pdf.net/anywhere-library/html/TextElementAndFonts.htm

The text element adds text to a PDF document and is implemented by the `TextElement` class. The `TextElement` offers many constructors that call the following constructor with default values:

```csharp
public TextElement(float x, float y, float width, float height, string text,
    PdfFont font, PdfColor textColor);
```

```vbnet
Public Sub New(x As Single, y As Single, width As Single, height As Single,
    text As String, font As PdfFont, textColor As PdfColor)
```

The constructor creates a text element rendered in the specified rectangle with the specified font and color. The text is wrapped to the width of the rectangle.

If the text pagination is not allowed (the `TextElement.Paginate` property of the `TextElement` is *False*), the text is written on the current page, and the rendered text height is the smaller of the specified height and the height available on the page. The text that did not fit and the text bounds inside the current page are returned in the `AddTextElementResult` object when the element is added to a renderer.

If the text pagination is allowed (the `TextElement.Paginate` property is *True*, the default) and the text needs pagination (no height is specified, or the specified height is bigger than the space available on the page), the height is ignored and the text is rendered to its end on as many pages as it needs. The text bounds inside the last page and the last page index are returned in the `AddTextElementResult` object when the element is added to a renderer.

If the text pagination is allowed but the text does not need pagination (the specified height is less than the space available on the page), the rendered text is truncated to fit the specified height, and the text that did not fit is returned in `AddTextElementResult.RemainingText`. On a page of a document loaded from an existing PDF, the text is drawn on that page only.

Constructor parameters:

- *x* - The x coordinate where the text starts.
- *y* - The y coordinate where the text starts.
- *width* - The width of the destination rectangle.
- *height* - The height of the destination rectangle.
- *text* - The text to be rendered.
- *font* - The text font.
- *textColor* - The text color.

Other properties of the element set the alignment (`TextElement.TextAlign`, `TextElement.VerticalTextAlign`), the spacing (`TextElement.LineSpacing`, `TextElement.CharacterSpacing`, `TextElement.WordSpacing`), the direction of the text (`TextElement.TextAngle`, `TextElement.RightToLeftLanguage`) and a clipping rectangle (`TextElement.ClipRectangle`). In a text element added to a template, &p; is replaced with the page number and &P; with the number of pages (see "Templates" (pdf-creation-elements.md)).

### Fonts

A text element draws with a `PdfFont` added to the document with one of the `Document.AddFont(Font)` overloads (or the same methods of `Document.Fonts`):

- **An installed font** - `Document.AddFont(Font)` with an `Font`: a family name, a size in points and a `FontStyle`. The font is subset-embedded in the PDF, so the document shows the same text on every viewer and Unicode text of any script the font covers is written correctly. `Document.AddFont(Font, bool)` accepts only true for its embed flag.
- **A font file** - `Document.AddFont(string)` with the path of a TrueType or OpenType file, embedded the same way. The font does not have to be installed.
- **A standard PDF font** - `Document.AddFont(StdFontBaseFamily)` with one of the 14 fonts every PDF viewer has (Helvetica, Times, Courier, Symbol, ZapfDingbats). They are not embedded, which keeps the document small, and they cover Western European text. In a PDF/A or PDF/X document, which requires embedded fonts, Helvetica, Times and Courier are drawn with the bundled Liberation fonts of the same metrics, embedded; Symbol and ZapfDingbats cannot be used there.
- **A standard CJK font** - `Document.AddFont(StandardCJKFont)` for Chinese, Japanese and Korean text, not embedded: the PDF viewer supplies the glyphs. For a PDF/A document use an installed CJK font or a CJK font file instead.

The `PdfFont.Size`, `PdfFont.IsUnderline` and `PdfFont.IsStrikeout` properties can be changed after the font is created, and `PdfFont.GetTextWidth(string)` measures a text.

### Fonts on Windows, Linux and macOS

An installed font is looked up among the fonts of the computer the application runs on, so the families available depend on the platform: Arial or SimSun are installed on Windows and usually not on Linux. The library bundles the Liberation Sans, Liberation Serif and Liberation Mono fonts, which have the metrics of Arial, Times New Roman and Courier New: when one of these three families (or Helvetica, Times, Courier) is not installed, its Liberation twin is used, so text in these fonts is laid out the same on every platform. Any other family that is not installed fails with a `PdfDocumentException`.

The Liberation fonts cover Latin, Greek and Cyrillic text. For other scripts on Linux and macOS servers without fonts, add the **ExpertPdf.Anywhere.Fonts** package (the Noto fonts for every script, CJK included) or install fonts on the server. Font files placed in a *fonts* folder beside the application are also available by their family names, on every platform. `SystemFonts.InstalledFamilies` lists the families the library can use and `SystemFonts.IsFamilyInstalled(string)` checks one.

### Sample Code

Below there is a sample code showing how to add text and fonts to a PDF document:

```csharp
// add a page to the PDF document
PdfPage firstPage = document.AddPage();

// Times New Roman fonts of 10 points in four styles (Liberation Serif where Times
// New Roman is not installed)
Font ttfFont = new Font("Times New Roman", 10);
Font ttfFontItalic = new Font("Times New Roman", 10, FontStyle.Italic);
Font ttfFontBold = new Font("Times New Roman", 10, FontStyle.Bold);
Font ttfFontBoldItalic = new Font("Times New Roman", 10,
    FontStyle.Bold | FontStyle.Italic);

// a CJK font: SimSun on Windows; Noto Sans CJK SC comes with the
// ExpertPdf.Anywhere.Fonts package on Linux and macOS
string cjkFamily = SystemFonts.IsFamilyInstalled("SimSun") ? "SimSun"
    : "Noto Sans CJK SC";
Font ttfCJKFont = new Font(cjkFamily, 10);

// the PDF fonts, embedded in the document
PdfFont newTimesFont = document.AddFont(ttfFont);
PdfFont newTimesFontItalic = document.AddFont(ttfFontItalic);
PdfFont newTimesFontBold = document.AddFont(ttfFontBold);
PdfFont newTimesFontBoldItalic = document.AddFont(ttfFontBoldItalic);
PdfFont cjkEmbeddedFont = document.AddFont(ttfCJKFont);

// standard Times Type 1 fonts, not embedded
PdfFont stdTimesFont = document.AddFont(StdFontBaseFamily.TimesRoman);
PdfFont stdTimesFontItalic = document.AddFont(StdFontBaseFamily.TimesItalic);
PdfFont stdTimesFontBoldItalic = document.AddFont(
    StdFontBaseFamily.TimesBoldItalic);

// standard CJK fonts, not embedded
PdfFont cjkJapaneseStandardFont = document.AddFont(
    StandardCJKFont.HeiseiKakuGothicW5);
PdfFont cjkChineseTraditionalStandardFont = document.AddFont(
    StandardCJKFont.MonotypeHeiMedium);

// add text elements to the page

TextElement trueTypeText = new TextElement(0, 10, "True Type Fonts Demo:",
    newTimesFontBold);
AddElementResult addResult = firstPage.AddElement(trueTypeText);

// the result of adding the element gives the rendered size in the PDF page
TextElement textElement1 = new TextElement(20, addResult.EndPageBounds.Bottom
    + 10, "Hello World !!!!", newTimesFont);
addResult = firstPage.AddElement(textElement1);

// another element 5 points below the text above, in a bigger font
newTimesFontItalic.Size = 15;
TextElement textElement2 = new TextElement(20, addResult.EndPageBounds.Bottom
    + 5, "Hello World !!!!", newTimesFontItalic);
textElement2.ForeColor = Color.Green;
addResult = firstPage.AddElement(textElement2);

newTimesFontBoldItalic.Size = 20;
TextElement textElement3 = new TextElement(20, addResult.EndPageBounds.Bottom
    + 5, "Hello World !!!!", newTimesFontBoldItalic);
textElement3.ForeColor = Color.Blue;
addResult = firstPage.AddElement(textElement3);

TextElement stdTypeText = new TextElement(0, addResult.EndPageBounds.Bottom
    + 10, "Standard PDF Fonts Demo:", newTimesFontBold);
addResult = firstPage.AddElement(stdTypeText);

TextElement textElement4 = new TextElement(20, addResult.EndPageBounds.Bottom
    + 10, "Hello World !!!!", stdTimesFont);
addResult = firstPage.AddElement(textElement4);

stdTimesFontItalic.Size = 15;
TextElement textElement5 = new TextElement(20, addResult.EndPageBounds.Bottom
    + 5, "Hello World !!!!", stdTimesFontItalic);
textElement5.ForeColor = Color.Green;
addResult = firstPage.AddElement(textElement5);

stdTimesFontBoldItalic.Size = 20;
TextElement textElement6 = new TextElement(20, addResult.EndPageBounds.Bottom
    + 5, "Hello World !!!!", stdTimesFontBoldItalic);
textElement6.ForeColor = Color.Blue;
addResult = firstPage.AddElement(textElement6);

// Unicode text with the embedded fonts
TextElement unicodeText = new TextElement(0, addResult.EndPageBounds.Bottom
    + 10, "Unicode Text Demo:", newTimesFontBold);
addResult = firstPage.AddElement(unicodeText);

// Russian text
newTimesFont.Size = 15;
TextElement textElement8 = new TextElement(20, addResult.EndPageBounds.Bottom
    + 5, "Появление на свет!!", newTimesFont);
addResult = firstPage.AddElement(textElement8);

// Japanese text
TextElement textElementJapanese = new TextElement(20,
    addResult.EndPageBounds.Bottom + 5, "こんにちは世界！", cjkEmbeddedFont);
addResult = firstPage.AddElement(textElementJapanese);

// Chinese text
TextElement textElementChinese = new TextElement(20,
    addResult.EndPageBounds.Bottom + 5, "你好世界！", cjkEmbeddedFont);
addResult = firstPage.AddElement(textElementChinese);

TextElement stdCJKText = new TextElement(0, addResult.EndPageBounds.Bottom
    + 10, "Standard CJK Fonts Demo:", newTimesFontBold);
addResult = firstPage.AddElement(stdCJKText);

// Hello World in Japanese
TextElement textElement10 = new TextElement(20, addResult.EndPageBounds.Bottom
    + 20, "こんにちは世界！", cjkJapaneseStandardFont);
addResult = firstPage.AddElement(textElement10);

// Hello World in Chinese Traditional
TextElement textElement11 = new TextElement(20, addResult.EndPageBounds.Bottom
    + 5, "你好世界！", cjkChineseTraditionalStandardFont);
addResult = firstPage.AddElement(textElement11);
```

```vbnet
' add a page to the PDF document
Dim firstPage As PdfPage = document.AddPage()

' Times New Roman fonts of 10 points in four styles (Liberation Serif where Times
' New Roman is not installed)
Dim ttfFont As New Font("Times New Roman", 10)
Dim ttfFontItalic As New Font("Times New Roman", 10, FontStyle.Italic)
Dim ttfFontBold As New Font("Times New Roman", 10, FontStyle.Bold)
Dim ttfFontBoldItalic As New Font("Times New Roman", 10,
    FontStyle.Bold Or FontStyle.Italic)

' a CJK font: SimSun on Windows; Noto Sans CJK SC comes with the
' ExpertPdf.Anywhere.Fonts package on Linux and macOS
Dim cjkFamily As String = If(SystemFonts.IsFamilyInstalled("SimSun"), "SimSun",
    "Noto Sans CJK SC")
Dim ttfCJKFont As New Font(cjkFamily, 10)

' the PDF fonts, embedded in the document
Dim newTimesFont As PdfFont = document.AddFont(ttfFont)
Dim newTimesFontItalic As PdfFont = document.AddFont(ttfFontItalic)
Dim newTimesFontBold As PdfFont = document.AddFont(ttfFontBold)
Dim newTimesFontBoldItalic As PdfFont = document.AddFont(ttfFontBoldItalic)
Dim cjkEmbeddedFont As PdfFont = document.AddFont(ttfCJKFont)

' standard Times Type 1 fonts, not embedded
Dim stdTimesFont As PdfFont = document.AddFont(StdFontBaseFamily.TimesRoman)
Dim stdTimesFontItalic As PdfFont = document.AddFont(StdFontBaseFamily.TimesItalic)
Dim stdTimesFontBoldItalic As PdfFont = document.AddFont(
    StdFontBaseFamily.TimesBoldItalic)

' standard CJK fonts, not embedded
Dim cjkJapaneseStandardFont As PdfFont = document.AddFont(
    StandardCJKFont.HeiseiKakuGothicW5)
Dim cjkChineseTraditionalStandardFont As PdfFont = document.AddFont(
    StandardCJKFont.MonotypeHeiMedium)

' add text elements to the page

Dim trueTypeText As New TextElement(0, 10, "True Type Fonts Demo:", newTimesFontBold)
Dim addResult As AddElementResult = firstPage.AddElement(trueTypeText)

' the result of adding the element gives the rendered size in the PDF page
Dim textElement1 As New TextElement(20, addResult.EndPageBounds.Bottom + 10,
    "Hello World !!!!", newTimesFont)
addResult = firstPage.AddElement(textElement1)

' another element 5 points below the text above, in a bigger font
newTimesFontItalic.Size = 15
Dim textElement2 As New TextElement(20, addResult.EndPageBounds.Bottom + 5,
    "Hello World !!!!", newTimesFontItalic)
textElement2.ForeColor = Color.Green
addResult = firstPage.AddElement(textElement2)

newTimesFontBoldItalic.Size = 20
Dim textElement3 As New TextElement(20, addResult.EndPageBounds.Bottom + 5,
    "Hello World !!!!", newTimesFontBoldItalic)
textElement3.ForeColor = Color.Blue
addResult = firstPage.AddElement(textElement3)

Dim stdTypeText As New TextElement(0, addResult.EndPageBounds.Bottom + 10,
    "Standard PDF Fonts Demo:", newTimesFontBold)
addResult = firstPage.AddElement(stdTypeText)

Dim textElement4 As New TextElement(20, addResult.EndPageBounds.Bottom + 10,
    "Hello World !!!!", stdTimesFont)
addResult = firstPage.AddElement(textElement4)

stdTimesFontItalic.Size = 15
Dim textElement5 As New TextElement(20, addResult.EndPageBounds.Bottom + 5,
    "Hello World !!!!", stdTimesFontItalic)
textElement5.ForeColor = Color.Green
addResult = firstPage.AddElement(textElement5)

stdTimesFontBoldItalic.Size = 20
Dim textElement6 As New TextElement(20, addResult.EndPageBounds.Bottom + 5,
    "Hello World !!!!", stdTimesFontBoldItalic)
textElement6.ForeColor = Color.Blue
addResult = firstPage.AddElement(textElement6)

' Unicode text with the embedded fonts
Dim unicodeText As New TextElement(0, addResult.EndPageBounds.Bottom + 10,
    "Unicode Text Demo:", newTimesFontBold)
addResult = firstPage.AddElement(unicodeText)

' Russian text
newTimesFont.Size = 15
Dim textElement8 As New TextElement(20, addResult.EndPageBounds.Bottom + 5,
    "Появление на свет!!", newTimesFont)
addResult = firstPage.AddElement(textElement8)

' Japanese text
Dim textElementJapanese As New TextElement(20, addResult.EndPageBounds.Bottom + 5,
    "こんにちは世界！", cjkEmbeddedFont)
addResult = firstPage.AddElement(textElementJapanese)

' Chinese text
Dim textElementChinese As New TextElement(20, addResult.EndPageBounds.Bottom + 5,
    "你好世界！", cjkEmbeddedFont)
addResult = firstPage.AddElement(textElementChinese)

Dim stdCJKText As New TextElement(0, addResult.EndPageBounds.Bottom + 10,
    "Standard CJK Fonts Demo:", newTimesFontBold)
addResult = firstPage.AddElement(stdCJKText)

' Hello World in Japanese
Dim textElement10 As New TextElement(20, addResult.EndPageBounds.Bottom + 20,
    "こんにちは世界！", cjkJapaneseStandardFont)
addResult = firstPage.AddElement(textElement10)

' Hello World in Chinese Traditional
Dim textElement11 As New TextElement(20, addResult.EndPageBounds.Bottom + 5,
    "你好世界！", cjkChineseTraditionalStandardFont)
addResult = firstPage.AddElement(textElement11)
```

## Image element

Online: https://docs.html-to-pdf.net/anywhere-library/html/ImageElement.htm

The image element adds images to a PDF document and is implemented by the `ImageElement` class. The image is given as the path of an image file or as the bytes of an image file (a `byte[]`), in one of the PNG, JPEG, GIF, BMP, TIFF and WebP formats. The `ImageElement` offers many constructors that call the following two constructors with default values:

```csharp
public ImageElement(float x, float y, float destWidth, float destHeight,
    string filePath);
public ImageElement(float x, float y, float destWidth, float destHeight,
    byte[] imageObj);
```

```vbnet
Public Sub New(x As Single, y As Single, destWidth As Single, destHeight As Single,
    filePath As String)
Public Sub New(x As Single, y As Single, destWidth As Single, destHeight As Single,
    imageObj As Byte())
```

The first constructor creates an `ImageElement` from the specified file, rendered at the position *(x,y)* with the *(destWidth,destHeight)* size.

The second constructor creates an `ImageElement` from the bytes of an image file, rendered at the position *(x,y)* with the *(destWidth,destHeight)* size. The bytes can come from a file, a database, a web request or an image produced by another part of the library, like the HTML to image converter.

- *x* - The X location where this element is rendered.
- *y* - The Y location where this element is rendered.
- *destWidth* - The destination rectangle width.
- *destHeight* - The destination rectangle height.
- *filePath* - The image file path.
- *imageObj* - The bytes of the image file.

The constructors without a width use the width available in the page, and the constructors without a height determine the height from the width so the image keeps its aspect ratio. An image taller than the room left on the page continues on the next pages while `ImageElement.Paginate` is true. The frames of a multi-page TIFF or of an animated GIF or WebP are each rendered on a page of their own while `ImageElement.RenderMultiFrame` is true, and `ImageElement.RenderTransparentImage` keeps or removes the transparency of the image. The images are JPEG-compressed as set by the `Document.JpegCompressionEnabled` and `Document.JpegCompressionLevel` properties of the document; transparent and very small images are kept lossless.

### Sample Code

Below there is a sample code showing how to add images to a PDF document:

```csharp
// add a page to the PDF document
PdfPage firstPage = document.AddPage();

string imagePath = Path.Combine("Images", "logo.png");

// display the image in the available space in the page, with the height determined
// automatically to keep the aspect ratio
ImageElement imageElement1 = new ImageElement(0, 0, imagePath);
AddElementResult addResult = firstPage.AddElement(imageElement1);

// display the image with the specified width and the height determined
// automatically; the image is placed below the previous image, whose bounds inside
// the page are taken from the AddElementResult object
ImageElement imageElement2 = new ImageElement(0, addResult.EndPageBounds.Bottom + 10,
    100, imagePath);
addResult = firstPage.AddElement(imageElement2);

// display the image from bytes, with the specified width and height; the image does
// not keep its aspect ratio
byte[] imageBytes = File.ReadAllBytes(imagePath);
ImageElement imageElement3 = new ImageElement(addResult.EndPageBounds.Right + 10,
    addResult.EndPageBounds.Top, 200, 100, imageBytes);
addResult = firstPage.AddElement(imageElement3);
```

```vbnet
' add a page to the PDF document
Dim firstPage As PdfPage = document.AddPage()

Dim imagePath As String = Path.Combine("Images", "logo.png")

' display the image in the available space in the page, with the height determined
' automatically to keep the aspect ratio
Dim imageElement1 As New ImageElement(0, 0, imagePath)
Dim addResult As AddElementResult = firstPage.AddElement(imageElement1)

' display the image with the specified width and the height determined
' automatically; the image is placed below the previous image, whose bounds inside
' the page are taken from the AddElementResult object
Dim imageElement2 As New ImageElement(0, addResult.EndPageBounds.Bottom + 10,
    100, imagePath)
addResult = firstPage.AddElement(imageElement2)

' display the image from bytes, with the specified width and height; the image does
' not keep its aspect ratio
Dim imageBytes As Byte() = File.ReadAllBytes(imagePath)
Dim imageElement3 As New ImageElement(addResult.EndPageBounds.Right + 10,
    addResult.EndPageBounds.Top, 200, 100, imageBytes)
addResult = firstPage.AddElement(imageElement3)
```

## Shape elements

Online: https://docs.html-to-pdf.net/anywhere-library/html/ShapeElements.htm

The document model defines the following shape elements:

1. `LineElement`
2. `RectangleElement`
3. `EllipseElement`
4. `CircleElement`
5. `EllipseArcElement`
6. `EllipseSliceElement`
7. `BezierCurveElement`
8. `PolygonElement`

The outline of a shape is drawn with the `PageGraphicElement.ForeColor` color and the `PageGraphicElement.LineStyle` line style, and a closed shape is filled with the `PageGraphicElement.BackColor` color or the `PageGraphicElement.Gradient` gradient. The colors are `PdfColor` objects: an `Color` converts to one implicitly, and the `PdfColor` constructors also take RGB, CMYK or gray values.

### Sample Code

Below is a sample code showing how to add shapes to a PDF document:

```csharp
// add a page to the PDF document
PdfPage firstPage = document.AddPage();

// draw rectangle
RectangleElement rectangle1 = new RectangleElement(10, 10, 150, 100);
rectangle1.ForeColor = Color.Blue;
rectangle1.LineStyle.LineWidth = 5;
rectangle1.LineStyle.LineJoinStyle = LineJoinStyle.RoundJoin;
firstPage.AddElement(rectangle1);

// draw colored rectangle
RectangleElement rectangle2 = new RectangleElement(200, 10, 150, 100);
rectangle2.ForeColor = Color.Blue;
rectangle2.BackColor = Color.Green;
firstPage.AddElement(rectangle2);

// draw gradient colored rectangle
RectangleElement rectangle3 = new RectangleElement(400, 25, 100, 50);
rectangle3.ForeColor = Color.Blue;
rectangle3.Gradient = new GradientColor(GradientDirection.Vertical, Color.Green,
    Color.Blue);
firstPage.AddElement(rectangle3);

// draw ellipse
EllipseElement ellipse1 = new EllipseElement(75, 200, 70, 50);
ellipse1.ForeColor = Color.Blue;
ellipse1.LineStyle.LineDashStyle = LineDashStyle.Dash;
firstPage.AddElement(ellipse1);

// draw filled ellipse
EllipseElement ellipse2 = new EllipseElement(275, 200, 70, 50);
ellipse2.ForeColor = Color.Blue;
ellipse2.BackColor = Color.Green;
firstPage.AddElement(ellipse2);

// draw circle filled with a gradient
CircleElement circle1 = new CircleElement(450, 200, 40);
circle1.ForeColor = Color.Blue;
circle1.Gradient = new GradientColor(GradientDirection.Vertical, Color.Green,
    Color.Blue);
firstPage.AddElement(circle1);

// draw Bezier curves
BezierCurveElement bezierCurve1 = new BezierCurveElement(10, 350, 100, 300, 200,
    400, 300, 350);
bezierCurve1.ForeColor = Color.Blue;
bezierCurve1.LineStyle.LineWidth = 3;
bezierCurve1.LineStyle.LineJoinStyle = LineJoinStyle.RoundJoin;
firstPage.AddElement(bezierCurve1);

BezierCurveElement bezierCurve2 = new BezierCurveElement(10, 350, 100, 400, 200,
    300, 300, 350);
bezierCurve2.ForeColor = Color.Green;
bezierCurve2.LineStyle.LineWidth = 3;
bezierCurve2.LineStyle.LineJoinStyle = LineJoinStyle.RoundJoin;
firstPage.AddElement(bezierCurve2);

// draw a filled triangle
PolygonElement triangle = new PolygonElement(new PointF[] {
    new PointF(400, 300), new PointF(500, 300), new PointF(450, 400) });
triangle.ForeColor = Color.Blue;
triangle.BackColor = Color.FromArgb(255, 200, 0);
firstPage.AddElement(triangle);
```

```vbnet
' add a page to the PDF document
Dim firstPage As PdfPage = document.AddPage()

' draw rectangle
Dim rectangle1 As New RectangleElement(10, 10, 150, 100)
rectangle1.ForeColor = Color.Blue
rectangle1.LineStyle.LineWidth = 5
rectangle1.LineStyle.LineJoinStyle = LineJoinStyle.RoundJoin
firstPage.AddElement(rectangle1)

' draw colored rectangle
Dim rectangle2 As New RectangleElement(200, 10, 150, 100)
rectangle2.ForeColor = Color.Blue
rectangle2.BackColor = Color.Green
firstPage.AddElement(rectangle2)

' draw gradient colored rectangle
Dim rectangle3 As New RectangleElement(400, 25, 100, 50)
rectangle3.ForeColor = Color.Blue
rectangle3.Gradient = New GradientColor(GradientDirection.Vertical, Color.Green,
    Color.Blue)
firstPage.AddElement(rectangle3)

' draw ellipse
Dim ellipse1 As New EllipseElement(75, 200, 70, 50)
ellipse1.ForeColor = Color.Blue
ellipse1.LineStyle.LineDashStyle = LineDashStyle.Dash
firstPage.AddElement(ellipse1)

' draw filled ellipse
Dim ellipse2 As New EllipseElement(275, 200, 70, 50)
ellipse2.ForeColor = Color.Blue
ellipse2.BackColor = Color.Green
firstPage.AddElement(ellipse2)

' draw circle filled with a gradient
Dim circle1 As New CircleElement(450, 200, 40)
circle1.ForeColor = Color.Blue
circle1.Gradient = New GradientColor(GradientDirection.Vertical, Color.Green,
    Color.Blue)
firstPage.AddElement(circle1)

' draw Bezier curves
Dim bezierCurve1 As New BezierCurveElement(10, 350, 100, 300, 200, 400,
    300, 350)
bezierCurve1.ForeColor = Color.Blue
bezierCurve1.LineStyle.LineWidth = 3
bezierCurve1.LineStyle.LineJoinStyle = LineJoinStyle.RoundJoin
firstPage.AddElement(bezierCurve1)

Dim bezierCurve2 As New BezierCurveElement(10, 350, 100, 400, 200, 300,
    300, 350)
bezierCurve2.ForeColor = Color.Green
bezierCurve2.LineStyle.LineWidth = 3
bezierCurve2.LineStyle.LineJoinStyle = LineJoinStyle.RoundJoin
firstPage.AddElement(bezierCurve2)

' draw a filled triangle
Dim triangle As New PolygonElement(New PointF() {
    New PointF(400, 300), New PointF(500, 300), New PointF(450, 400)})
triangle.ForeColor = Color.Blue
triangle.BackColor = Color.FromArgb(255, 200, 0)
firstPage.AddElement(triangle)
```
