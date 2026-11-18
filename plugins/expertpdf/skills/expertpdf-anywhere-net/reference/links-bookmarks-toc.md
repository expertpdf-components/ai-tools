# Links, bookmarks and table of contents

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Live HTTP links

Online: https://docs.html-to-pdf.net/anywhere-library/html/LiveLinks.htm

The converter turns every HTTP link of the HTML document into a link in the PDF document. This works for links on text, on images or on any other HTML content. It is the default behavior. If you do not want active links in the generated PDF document, set `PdfDocumentOptions.LiveUrlsEnabled` to *false*: the links are still drawn as the page shows them, but clicking them does nothing.

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.PdfDocumentOptions.LiveUrlsEnabled = false;
pdfConverter.SavePdfFromUrlToFile("https://www.example.com", "no-links.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.PdfDocumentOptions.LiveUrlsEnabled = False
pdfConverter.SavePdfFromUrlToFile("https://www.example.com", "no-links.pdf")
```

The HTML elements added to a `Document` have a property of the same name, `HtmlToPdfElement.LiveUrlsEnabled`. Links inside the same document are controlled separately; see "Internal links" (links-bookmarks-toc.md).

## Internal links

Online: https://docs.html-to-pdf.net/anywhere-library/html/InternalLinks.htm

The converter turns the HTML links that point to an anchor in the same document into internal links in the PDF: clicking one in a PDF viewer jumps to the place the anchor was rendered, on whatever page that is. This makes it easy to give the generated document a table of contents.

An internal link consists of two HTML elements: the link, written as *<a href="#target">Internal Link</a>*, and its target, an element whose id is the anchor name, such as *<h2 id="target">Link Target</h2>*, or a named anchor, *<a name="target">Link Target</a>*. When the converter finds this construction it creates an internal link in the PDF from *"Internal Link"* to *"Link Target"*.

Internal links are created by default. Set `PdfDocumentOptions.InternalLinksEnabled` to *false* to leave them out:

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.PdfDocumentOptions.InternalLinksEnabled = false;
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.PdfDocumentOptions.InternalLinksEnabled = False
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf")
```

The Chromium engine resolves every anchor against the document it renders, so internal links work the same way for a URL, an HTML file and an HTML string. When you convert an HTML string, pass the *urlBase* parameter so that the images, style sheets and other links of the string resolve as well. The conversion methods that take an *internalLinksDocUrl* parameter accept it for compatibility; the value is not needed.

To have the converter build a table of contents from the headings of the page, with page numbers, use `PdfConverter.TableOfContentsOptions`. The same option on the HTML elements of a `Document` is `HtmlToPdfElement.InternalLinksEnabled`.

## Automatic bookmarks

Online: https://docs.html-to-pdf.net/anywhere-library/html/Bookmarks.htm

The converter can create the bookmarks (the outline shown next to the document in PDF readers) from the headings or other elements of the HTML page. Each bookmark takes the text of its element and goes to the place where the element is in the PDF.

### Automatic bookmarks

List the elements to bookmark as CSS selectors in `PdfBookmarkOptions.HtmlElementSelectors` (property `PdfConverter.PdfBookmarkOptions`). Without selectors, no bookmarks are created. "h1" selects all h1 elements, "*.chapter" the elements with the CSS class chapter, "*#summary" the element with the id summary. Only the elements that have a text are bookmarked.

```csharp
PdfConverter converter = new PdfConverter();

// a bookmark for every h1 and h2 heading of the page
converter.PdfBookmarkOptions.HtmlElementSelectors = new string[] { "h1", "h2" };

converter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf");
```

```vbnet
Dim converter As New PdfConverter()

' a bookmark for every h1 and h2 heading of the page
converter.PdfBookmarkOptions.HtmlElementSelectors = New String() {"h1", "h2"}

converter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf")
```

By default every bookmarked element is a top-level entry. With `PdfBookmarkOptions.HierarchicalBookmarks` set to true the bookmarks are nested by the order of the selectors: the elements matched by the first selector are the top-level entries, the elements matched by the second one nest under the closest earlier entry of a lower level, and so on.

```csharp
PdfConverter converter = new PdfConverter();

// chapters (h1) with their sections (h2) and subsections (h3) nested under them
converter.PdfBookmarkOptions.HtmlElementSelectors =
    new string[] { "h1", "h2", "h3" };
converter.PdfBookmarkOptions.HierarchicalBookmarks = true;

converter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf");
```

```vbnet
Dim converter As New PdfConverter()

' chapters (h1) with their sections (h2) and subsections (h3) nested under them
converter.PdfBookmarkOptions.HtmlElementSelectors =
    New String() {"h1", "h2", "h3"}
converter.PdfBookmarkOptions.HierarchicalBookmarks = True

converter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf")
```

`PdfBookmarkOptions.MaxTitleLength` limits the length of the bookmark titles. HTML added to a document with the HtmlToPdfElement creates bookmarks the same way.

A table of contents printed in the document, with the page number of each heading and a link to it, is built from the headings of the page with `PdfConverter.TableOfContentsOptions` - see "Automatic table of contents" (links-bookmarks-toc.md).

To build a bookmark tree of your own - titles that are not in the page, an order of your own - convert to a `Document` and add the bookmarks yourself, using the positions of the elements (see HTML elements mapping and Bookmarks).

## Automatic table of contents

Online: https://docs.html-to-pdf.net/anywhere-library/html/AutomaticTableOfContents.htm

The HTML to PDF converter can give a converted document a table of contents with the numbers of the PDF pages the entries are on. Set it up with `PdfConverter.TableOfContentsOptions`; its `HtmlTableOfContentsOptions.Mode` chooses one of three ways.

### Numbering the page's own table of contents

With HtmlTableOfContentsMode.FillExisting, the page already has a table of contents made of links to anchors in the page. Name the element that holds it with `HtmlTableOfContentsOptions.ContainerSelector`: each link in it gets a leader and the number of the page its target is on.

```csharp
using ExpertPdf.Anywhere.HtmlToPdf;

PdfConverter converter = new PdfConverter();
converter.TableOfContentsOptions.Mode = HtmlTableOfContentsMode.FillExisting;
converter.TableOfContentsOptions.ContainerSelector = "#toc";
converter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere.HtmlToPdf

Dim converter As New PdfConverter()
converter.TableOfContentsOptions.Mode = HtmlTableOfContentsMode.FillExisting
converter.TableOfContentsOptions.ContainerSelector = "#toc"
converter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf")
```

### A table of contents written into the page

With HtmlTableOfContentsMode.InsertHtml, the converter writes a table of contents into the page from the headings matched by `HtmlTableOfContentsOptions.HeadingSelectors`, then numbers it. It is HTML, styled by the page's CSS: a *nav.expertpdf-toc* element with a *h2.expertpdf-toc-title* title and nested lists whose items have the classes *expertpdf-toc-entry* and *expertpdf-toc-level-N*. `HtmlTableOfContentsOptions.PlaceholderSelector` names an element to put it in, and `HtmlTableOfContentsOptions.PageBreak` gives it a page of its own.

```csharp
PdfConverter converter = new PdfConverter();
HtmlTableOfContentsOptions toc = converter.TableOfContentsOptions;
toc.Mode = HtmlTableOfContentsMode.InsertHtml;
toc.HeadingSelectors = new string[] { "h1", "h2" };
toc.Title = "Contents";
converter.CustomCSS =
    "nav.expertpdf-toc li.expertpdf-toc-level-1 { font-weight: bold; }";
byte[] pdf = converter.GetPdfBytesFromUrl("https://www.example.com/manual");
```

```vbnet
Dim converter As New PdfConverter()
Dim toc As HtmlTableOfContentsOptions = converter.TableOfContentsOptions
toc.Mode = HtmlTableOfContentsMode.InsertHtml
toc.HeadingSelectors = New String() {"h1", "h2"}
toc.Title = "Contents"
converter.CustomCSS =
    "nav.expertpdf-toc li.expertpdf-toc-level-1 { font-weight: bold; }"
Dim pdf As Byte() = converter.GetPdfBytesFromUrl("https://www.example.com/manual")
```

### Table of contents pages

With HtmlTableOfContentsMode.GeneratePages, the table of contents is drawn on PDF pages of its own, inserted at the start, after the first page or at the end (`HtmlTableOfContentsOptions.PagePosition`). The title, the fonts, the colors, the leaders, numbered entries and a style per level are options; the header and footer of the conversion appear on those pages too.

```csharp
PdfConverter converter = new PdfConverter();
HtmlTableOfContentsOptions toc = converter.TableOfContentsOptions;
toc.Mode = HtmlTableOfContentsMode.GeneratePages;
toc.HeadingSelectors = new string[] { "h1", "h2" };
toc.PagePosition = HtmlTableOfContentsPagePosition.Start;
toc.Title = "Contents";
toc.TitleAlign = HorizontalTextAlign.Center;
toc.NumberEntries = true;
converter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf");
```

```vbnet
Dim converter As New PdfConverter()
Dim toc As HtmlTableOfContentsOptions = converter.TableOfContentsOptions
toc.Mode = HtmlTableOfContentsMode.GeneratePages
toc.HeadingSelectors = New String() {"h1", "h2"}
toc.PagePosition = HtmlTableOfContentsPagePosition.Start
toc.Title = "Contents"
toc.TitleAlign = HorizontalTextAlign.Center
toc.NumberEntries = True
converter.SavePdfFromUrlToFile("https://www.example.com/manual", "manual.pdf")
```

The entries of every mode are links to their headings. The numbers start at the first page number of the footer (or of the header) when page numbering is offset, and right-to-left pages get their numbers on the left. In a tagged document the table of contents is part of the logical structure.

## Bookmarks in a PDF document

Online: https://docs.html-to-pdf.net/anywhere-library/html/DocumentBookmarks.htm

The document model adds bookmarks (the outline a PDF viewer shows beside the pages) to a PDF document. The `Document.AddBookmark(string, ExplicitDestination)` methods of the `Document` class add root bookmarks and, with a parent bookmark, child bookmarks. Each bookmark leads to an `ExplicitDestination`: a page, a point on the page, and how the page is displayed (`DestinationViewMode`, zoom).

The `Bookmark` returned by the method has a `Bookmark.Color` and a `Bookmark.Style` (normal, bold, italic), and its children are in `Bookmark.DescendantBookmarks`. All the bookmarks of the document, including those of a loaded document and those an HTML conversion created, are in the `Document.Bookmarks` collection. Bookmarks can also be created automatically from the headings of an HTML document (see "HTML to PDF element" (pdf-creation-elements.md) and "Automatic bookmarks" (links-bookmarks-toc.md)).

### Code Sample

The code sample below, an ASP.NET Core controller action, creates a document with two pages, root bookmarks for the pages and child bookmarks for the images on the first page, and returns the PDF to the browser:

```csharp
public IActionResult CreatePdf()
{
    using (Document document = new Document())
    {
        // add a page to the PDF document
        PdfPage firstPage = document.AddPage();

        // add a root bookmark to the first page
        Bookmark firstPageBookmark = document.AddBookmark("First Page",
            new ExplicitDestination(firstPage));
        firstPageBookmark.Style = PdfBookmarkStyle.Bold;

        string imagesPath = Path.Combine(AppContext.BaseDirectory, "Images");

        // display an image in the available space in the page, with the height
        // determined automatically to keep the aspect ratio
        ImageElement imageElement1 = new ImageElement(0, 0,
            Path.Combine(imagesPath, "html-to-pdf.png"));
        AddElementResult addResult = firstPage.AddElement(imageElement1);

        // add a child bookmark to this image
        document.AddBookmark("HTML to PDF Converter", new ExplicitDestination(
            firstPage, new PointF(0, 0)), firstPageBookmark);

        // a second image below the first one
        float y = addResult.EndPageBounds.Bottom + 20;
        ImageElement imageElement2 = new ImageElement(0, y,
            Path.Combine(imagesPath, "word-to-pdf.png"));
        addResult = firstPage.AddElement(imageElement2);

        // add a child bookmark to this image
        Bookmark wordBookmark = document.AddBookmark("Word to PDF Converter",
            new ExplicitDestination(firstPage, new PointF(0, y)), firstPageBookmark);
        wordBookmark.Color = Color.Blue;

        // add a new page to the document
        PdfPage secondPage = document.AddPage();

        // add a root bookmark to the second page and set a 150% zoom when visiting
        // this bookmark
        ExplicitDestination secondPageDestination = new ExplicitDestination(
            secondPage);
        secondPageDestination.ZoomPercentage = 150;
        Bookmark secondPageBookmark = document.AddBookmark("Second Page",
            secondPageDestination);

        ImageElement imageElement3 = new ImageElement(0, 0,
            Path.Combine(imagesPath, "pdf-library.png"));
        secondPage.AddElement(imageElement3);

        // send the PDF document to the browser
        byte[] pdf = document.Save();
        return File(pdf, "application/pdf", "Bookmarks.pdf");
    }
}
```

```vbnet
Public Function CreatePdf() As IActionResult
    Using document As New Document()
        ' add a page to the PDF document
        Dim firstPage As PdfPage = document.AddPage()

        ' add a root bookmark to the first page
        Dim firstPageBookmark As Bookmark = document.AddBookmark("First Page",
            New ExplicitDestination(firstPage))
        firstPageBookmark.Style = PdfBookmarkStyle.Bold

        Dim imagesPath As String = Path.Combine(AppContext.BaseDirectory, "Images")

        ' display an image in the available space in the page, with the height
        ' determined automatically to keep the aspect ratio
        Dim imageElement1 As New ImageElement(0, 0,
            Path.Combine(imagesPath, "html-to-pdf.png"))
        Dim addResult As AddElementResult = firstPage.AddElement(imageElement1)

        ' add a child bookmark to this image
        document.AddBookmark("HTML to PDF Converter", New ExplicitDestination(
            firstPage, New PointF(0, 0)), firstPageBookmark)

        ' a second image below the first one
        Dim y As Single = addResult.EndPageBounds.Bottom + 20
        Dim imageElement2 As New ImageElement(0, y,
            Path.Combine(imagesPath, "word-to-pdf.png"))
        addResult = firstPage.AddElement(imageElement2)

        ' add a child bookmark to this image
        Dim wordBookmark As Bookmark = document.AddBookmark("Word to PDF Converter",
            New ExplicitDestination(firstPage, New PointF(0, y)), firstPageBookmark)
        wordBookmark.Color = Color.Blue

        ' add a new page to the document
        Dim secondPage As PdfPage = document.AddPage()

        ' add a root bookmark to the second page and set a 150% zoom when visiting
        ' this bookmark
        Dim secondPageDestination As New ExplicitDestination(secondPage)
        secondPageDestination.ZoomPercentage = 150
        Dim secondPageBookmark As Bookmark = document.AddBookmark("Second Page",
            secondPageDestination)

        Dim imageElement3 As New ImageElement(0, 0,
            Path.Combine(imagesPath, "pdf-library.png"))
        secondPage.AddElement(imageElement3)

        ' send the PDF document to the browser
        Dim pdf As Byte() = document.Save()
        Return File(pdf, "application/pdf", "Bookmarks.pdf")
    End Using
End Function
```
