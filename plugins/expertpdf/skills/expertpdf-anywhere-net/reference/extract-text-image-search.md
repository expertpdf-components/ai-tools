# PDF to text, text search, PDF to image and image extraction

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## PDF to text

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfToText.htm

The PDF to text part of ExpertPdf Anywhere extracts the text of PDF documents on Windows, Linux and macOS. It extracts the text of a whole document or of a range of pages, keeping the original layout or in reading order, finds words in a document and reads the text of an area of a page.

The main class is `PdfToTextConverter`, in the **ExpertPdf.Anywhere** namespace. The text is read by the native PDF tool of the library, **ExpertPdf.Tools** (**ExpertPdf.Tools.exe** on Windows), which the converter runs as a separate process. The converter is disposable: a *using* statement, or `PdfToTextConverter.Close`, releases the document read by the last call.

### Features

- Extract the text of a whole document or of a range of pages, from a file or a stream (a MemoryStream for a document in memory).
- Keep the original layout of the pages (columns side by side), or put the text in reading order.
- Extract the text page by page in one call: one string for each page.
- Mark the page breaks, to split the text into pages.
- Skip or include the text hidden by clipping paths.
- Produce a HTML document with the title, author, subject and keywords of the PDF document as meta tags.
- Find a text in a document: the page, the position and the size of every match.
- Search for several texts at once, with the results grouped by text and by page.
- Read the text of an area of a page, for example the fields of an invoice.
- Open password protected documents; read the document information, the page count and the size and rotation of each page without extracting the text.
- Run the same code on Windows, Linux and macOS.

### Installation

The converter is part of the **ExpertPdf.Anywhere** package. The PDF tool comes with the native package of your platform (**ExpertPdf.Anywhere.Native.<rid>**) or with a platform package (**ExpertPdf.Anywhere.Windows**, **.Linux**, **.Linux.Arm64**, **.MacOS**), which copies it next to your application when it builds and when it is published. See "Installation" (installation-and-deployment.md).

```text
dotnet add package ExpertPdf.Anywhere.Windows
```

The tool is found next to the application. If it is in another folder, set its full path, or the folder that holds it, with the `PdfToTextConverter.PdfToolsFullPath` property.

### Getting started

Create a `PdfToTextConverter`, set the options and call `PdfToTextConverter.ConvertToText(string)` for a file or `PdfToTextConverter.ConvertToText(Stream)` for a stream. The result is a string:

```csharp
using ExpertPdf.Anywhere;

PdfToTextConverter converter = new PdfToTextConverter();

// the text in reading order: a whole column, then the next one
converter.Layout = TextLayout.ReadingLayout;

string text = converter.ConvertToText("report.pdf");
File.WriteAllText("report.txt", text, System.Text.Encoding.UTF8);

// the document information is available after the conversion
DocumentInformation info = converter.DocumentInformation;
Console.WriteLine("{0} by {1}, {2} pages", info.Title, info.Author, info.PageCount);
```

```vbnet
Imports ExpertPdf.Anywhere

Dim converter As New PdfToTextConverter()

' the text in reading order: a whole column, then the next one
converter.Layout = TextLayout.ReadingLayout

Dim text As String = converter.ConvertToText("report.pdf")
File.WriteAllText("report.txt", text, System.Text.Encoding.UTF8)

' the document information is available after the conversion
Dim info As DocumentInformation = converter.DocumentInformation
Console.WriteLine("{0} by {1}, {2} pages", info.Title, info.Author, info.PageCount)
```

### Text extraction options

- `PdfToTextConverter.Layout` - `TextLayout.OriginalLayout` (the default) keeps the layout of the page: when a page has two columns of text, the lines of the two columns stay side by side. `TextLayout.ReadingLayout` gives the text in reading order, the whole first column, then the second one - the best choice for indexing and search.
- `PdfToTextConverter.StartPageNumber` and `PdfToTextConverter.EndPageNumber` - the pages to convert. The first page is 1; an end page of 0 means the last page of the document.
- `PdfToTextConverter.MarkPageBreaks` - adds the `PdfToTextConverter.PAGE_BREAK_MARK` character (a form feed) after the text of each page, so that you can split the text into pages.
- `PdfToTextConverter.ClipText` - the text hidden by a clipping path is in the document but not visible on the page. It is skipped by default; set ClipText to false to extract it too.
- `PdfToTextConverter.AddHtmlMetaTags` - the result is a HTML document with the text and, as meta tags, the title, subject, keywords and author of the PDF document. `PdfToTextConverter.HtmlCharset` sets the charset meta tag: save the file with the same encoding.
- `PdfToTextConverter.UserPassword` - the password of a protected document.
- `PdfToTextConverter.GetPageCount(string)` - the number of pages of a document; `PdfToTextConverter.DocumentInformation` - the title, author, subject, keywords, dates and page count, after an operation; `PdfToTextConverter.GetDocumentInformation(string)` - the same information without extracting the text, with the size and rotation of each page.
- `PdfToTextConverter.PdfToolsTimeout` - the maximum time of an operation, in seconds (600 by default).

```csharp
PdfToTextConverter converter = new PdfToTextConverter();
converter.StartPageNumber = 2;
converter.EndPageNumber = 3;
converter.MarkPageBreaks = true;
converter.AddHtmlMetaTags = true;
converter.HtmlCharset = "utf-8";
converter.UserPassword = "open";

string html = converter.ConvertToText("protected.pdf");
File.WriteAllText("protected.html", html, System.Text.Encoding.UTF8);

// the text of each page
string[] pages = html.Split(PdfToTextConverter.PAGE_BREAK_MARK);
```

```vbnet
Dim converter As New PdfToTextConverter()
converter.StartPageNumber = 2
converter.EndPageNumber = 3
converter.MarkPageBreaks = True
converter.AddHtmlMetaTags = True
converter.HtmlCharset = "utf-8"
converter.UserPassword = "open"

Dim html As String = converter.ConvertToText("protected.pdf")
File.WriteAllText("protected.html", html, System.Text.Encoding.UTF8)

' the text of each page
Dim pages As String() = html.Split(PdfToTextConverter.PAGE_BREAK_MARK)
```

### Text per page and document information

`PdfToTextConverter.ConvertToTextPages(string)` extracts the text of the pages in one call and returns one string for each page, from `PdfToTextConverter.StartPageNumber` to `PdfToTextConverter.EndPageNumber` (an empty string for a page without text). It uses the `PdfToTextConverter.Layout` and `PdfToTextConverter.ClipText` options; each entry is the plain text of its page, without page break marks or HTML.

`PdfToTextConverter.GetDocumentInformation(string)` returns the title, author, subject, keywords, creator, producer, dates and page count of a document without extracting its text, and the size of each page in `DocumentInformation.Pages`: a `PdfPageInfo` for each page, in page order, with the width and height of the visible area of the page (the crop box) and of the whole page (the media box) in points (1/72 inch), and the rotation of the page (0, 90, 180 or 270 degrees). The sizes do not include the rotation: `PdfPageInfo.DisplayWidth` and `PdfPageInfo.DisplayHeight` are the size of the page as a viewer shows it. The Pages property is set by GetDocumentInformation; it is null after the other operations.

```csharp
PdfToTextConverter converter = new PdfToTextConverter();
converter.Layout = TextLayout.ReadingLayout;

string[] pages = converter.ConvertToTextPages("report.pdf");
for (int i = 0; i < pages.Length; i++)
{
    int pageNumber = converter.StartPageNumber + i;
    File.WriteAllText("report-page" + pageNumber + ".txt", pages[i]);
}

DocumentInformation info = converter.GetDocumentInformation("report.pdf");
Console.WriteLine("{0} - {1} - {2} pages", info.Title, info.Author, info.PageCount);
foreach (PdfPageInfo page in info.Pages)
{
    Console.WriteLine(
        "page {0}: {1:0.#} x {2:0.#} pt, rotated {3}, shown as {4:0.#} x {5:0.#}",
        page.PageNumber, page.Width, page.Height, page.Rotation,
        page.DisplayWidth, page.DisplayHeight);
}
```

```vbnet
Dim converter As New PdfToTextConverter()
converter.Layout = TextLayout.ReadingLayout

Dim pages As String() = converter.ConvertToTextPages("report.pdf")
For i As Integer = 0 To pages.Length - 1
    Dim pageNumber As Integer = converter.StartPageNumber + i
    File.WriteAllText("report-page" & pageNumber & ".txt", pages(i))
Next

Dim info As DocumentInformation = converter.GetDocumentInformation("report.pdf")
Console.WriteLine("{0} - {1} - {2} pages", info.Title, info.Author, info.PageCount)
For Each page As PdfPageInfo In info.Pages
    Console.WriteLine(
        "page {0}: {1:0.#} x {2:0.#} pt, rotated {3}, shown as {4:0.#} x {5:0.#}",
        page.PageNumber, page.Width, page.Height, page.Rotation,
        page.DisplayWidth, page.DisplayHeight)
Next
```

### Finding text and reading the text of an area

`PdfToTextConverter.ExtractTextPositions(string, string, bool, bool)` finds a text in a document, case sensitive or not, whole words only or not, and returns a `TextPosition` for every match: the page number, and the position and size of the text in points (1/72 inch) from the top left corner of the page.

`PdfToTextConverter.ExtractTextAtCoords(string, int, double, double, double, double)` returns the text of an area of a page, in the same coordinates. Together they read the fields of documents that have a known structure, like invoices: find the label of a field, then read the text on its right.

```csharp
PdfToTextConverter converter = new PdfToTextConverter();
string pdfFile = "invoice.pdf";

TextPosition[] found = converter.ExtractTextPositions(pdfFile, "Invoice number:",
    true, false);
if (found.Length > 0)
{
    TextPosition label = found[0];
    double x = label.X + label.Width + 5;
    string invoiceNumber = converter.ExtractTextAtCoords(pdfFile, label.PageNumber,
        x, label.Y - 2, 555 - x, label.Height + 4).Trim();
    Console.WriteLine("Invoice number: " + invoiceNumber);
}
```

```vbnet
Dim converter As New PdfToTextConverter()
Dim pdfFile As String = "invoice.pdf"

Dim found As TextPosition() = converter.ExtractTextPositions(pdfFile,
    "Invoice number:", True, False)
If found.Length > 0 Then
    Dim label As TextPosition = found(0)
    Dim x As Double = label.X + label.Width + 5
    Dim invoiceNumber As String = converter.ExtractTextAtCoords(pdfFile,
        label.PageNumber, x, label.Y - 2, 555 - x, label.Height + 4).Trim()
    Console.WriteLine("Invoice number: " & invoiceNumber)
End If
```

`PdfToTextConverter.SearchTexts(string, string[], bool, bool)` searches for several texts at once and returns a `TextSearchResult` for each text, in the order of the texts: all the positions, the number of matches, the pages where the text was found (`TextSearchResult.PageNumbers`) and the positions on one page (`TextSearchResult.GetPositionsOnPage(int)`). Arabic words are found whether the document maps its shaped glyphs to the letters or to their presentation forms.

```csharp
PdfToTextConverter converter = new PdfToTextConverter();
string[] words = { "invoice", "total", "harbor" };

TextSearchResult[] results = converter.SearchTexts("report.pdf",
    words, false, true);
foreach (TextSearchResult result in results)
{
    Console.WriteLine("{0}: {1} matches", result.SearchText, result.Count);
    foreach (int pageNumber in result.PageNumbers)
        Console.WriteLine("  page {0}: {1}", pageNumber, result.GetPositionsOnPage(
            pageNumber).Length);
}
```

```vbnet
Dim converter As New PdfToTextConverter()
Dim words As String() = {"invoice", "total", "harbor"}

Dim results As TextSearchResult() = converter.SearchTexts("report.pdf",
    words, False, True)
For Each result As TextSearchResult In results
    Console.WriteLine("{0}: {1} matches", result.SearchText, result.Count)
    For Each pageNumber As Integer In result.PageNumbers
        Console.WriteLine("  page {0}: {1}", pageNumber, result.GetPositionsOnPage(
            pageNumber).Length)
    Next
Next
```

The positions use the same coordinates as the page elements of `Document`, so you can, for example, highlight the words found in a document:

```csharp
using ExpertPdf.Anywhere;
using ExpertPdf.Anywhere.PdfDocument;

TextPosition[] positions = new PdfToTextConverter().ExtractTextPositions(pdfFile,
    "harbor", false, true);

using (Document document = new Document(pdfFile))
{
    foreach (TextPosition position in positions)
    {
        RectangleElement highlight = new RectangleElement(position.X, position.Y,
            position.Width, position.Height);
        highlight.BackColor = Color.Yellow;
        highlight.Transparency = 50;
        document.Pages[position.PageNumber - 1].AddElement(highlight);
    }
    document.Save("highlighted.pdf");
}
```

```vbnet
Imports ExpertPdf.Anywhere
Imports ExpertPdf.Anywhere.PdfDocument

Dim positions As TextPosition() = New PdfToTextConverter().ExtractTextPositions(
    pdfFile, "harbor", False, True)

Using document As New Document(pdfFile)
    For Each position As TextPosition In positions
        Dim highlight As New RectangleElement(position.X, position.Y, position.Width,
            position.Height)
        highlight.BackColor = Color.Yellow
        highlight.Transparency = 50
        document.Pages(position.PageNumber - 1).AddElement(highlight)
    Next
    document.Save("highlighted.pdf")
End Using
```

### Samples

The samples download has these console applications, in C# and VB.NET, for Windows, Linux and macOS. They work on the sample.pdf document that comes with them; sample-protected.pdf is the same document protected with the password "open".

- **ConvertPdfToText** - a command line tool with all the extraction options. Without arguments it converts sample.pdf to sample.txt.
- **TextSearch** - shows the document information and the size of each page, finds a word in a document read from memory and reads the fields of an invoice.

### PDF to Text command line tool

The ConvertPdfToText sample is a complete command line tool:

```text
ConvertPdfToText [/pdf:file.pdf] [/out:file] [/layout:original|reading]
    [/start:n] [/end:n] [/pagebreaks] [/pages] [/html[:charset]] [/noclip]
    [/userpswd:password]
```

/pages saves the text of each page in a file of its own (sample-page1.txt, ...). Examples:

```text
ConvertPdfToText /pdf:sample.pdf /layout:reading /pagebreaks
ConvertPdfToText /pdf:sample.pdf /html /start:2 /end:3
ConvertPdfToText /pdf:sample.pdf /pages /layout:reading
ConvertPdfToText /pdf:sample-protected.pdf /userpswd:open
```

### Licensing

Set `LicensingManager.LicenseKey` once when the application starts, or the `PdfToTextConverter.LicenseKey` property before you call it. Without a license key the converter works fully and adds an evaluation notice to the extracted text. See "License key" (installation-and-deployment.md).

## PDF to image

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfToImage.htm

The PDF to image part of ExpertPdf Anywhere renders the pages of PDF documents to images on Windows, Linux and macOS. The images are returned in memory as the bytes of image files, saved as image files, or put together in one multi-page TIFF document.

The main class is `PdfToImageConverter`, in the **ExpertPdf.Anywhere** namespace. The pages are rendered by the native PDF tool of the library, **ExpertPdf.Tools** (**ExpertPdf.Tools.exe** on Windows), which the converter runs as a separate process. The converter is disposable: a *using* statement, or `PdfToImageConverter.Close`, releases the document read by the last call.

### Features

- Convert all the pages of a document or a range of pages, from a file or a stream (a MemoryStream for a document in memory).
- Any resolution: low for thumbnails, high for printing or OCR.
- Color (RGB) or gray images.
- The images in memory, as the bytes of PNG, JPEG, BMP or TIFF files, or saved as image files with the file name prefix you choose.
- One multi-page TIFF document with all the converted pages, in memory or in a file.
- Thumbnails that fit in a box of the size you choose, keeping the aspect ratio of each page.
- One page to the bytes of an image file in one call.
- The JPEG quality, the TIFF compression (LZW, none, PackBits, CCITT Group 3 and Group 4) and the PNG compression level of the images.
- Open password protected documents; read the document information and the page count.

### Installation

The converter is part of the **ExpertPdf.Anywhere** package. The PDF tool comes with the native package of your platform (**ExpertPdf.Anywhere.Native.<rid>**) or with a platform package (**ExpertPdf.Anywhere.Windows**, **.Linux**, **.Linux.Arm64**, **.MacOS**), which copies it next to your application when it builds and when it is published. See "Installation" (installation-and-deployment.md).

```text
dotnet add package ExpertPdf.Anywhere.Windows
```

The tool is found next to the application. If it is in another folder, set its full path, or the folder that holds it, with the `PdfToImageConverter.PdfToolsFullPath` property.

### Getting started

Create a `PdfToImageConverter`, set the options and call `PdfToImageConverter.ConvertToImagesInFolder(string)` to save one image file for each page in the `PdfToImageConverter.ImagesPath` folder:

```csharp
using ExpertPdf.Anywhere;

PdfToImageConverter converter = new PdfToImageConverter();

converter.Resolution = 150;                     // DPI
converter.ImagesFormat = ImageFormat.Png;
converter.ImagesPath = "images";                // an existing folder
converter.ImagesPrefix = "page_";               // page_1.png, page_2.png, ...

converter.ConvertToImagesInFolder("report.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere

Dim converter As New PdfToImageConverter()

converter.Resolution = 150                      ' DPI
converter.ImagesFormat = ImageFormat.Png
converter.ImagesPath = "images"                 ' an existing folder
converter.ImagesPrefix = "page_"                ' page_1.png, page_2.png, ...

converter.ConvertToImagesInFolder("report.pdf")
```

The files are numbered from 1 for the first converted page. The default prefix is "pdfimage_" and the default format is PNG. `ImageFormat` is the image format type of the library, in the **ExpertPdf.Anywhere.Drawing** namespace: Png, Jpeg, Bmp or Tiff.

### Conversion options

- `PdfToImageConverter.StartPageNumber` and `PdfToImageConverter.EndPageNumber` - the pages to convert. The first page is 1; an end page of 0 means the last page of the document.
- `PdfToImageConverter.Resolution` - the resolution in DPI (120 by default). An A4 page is 992 x 1403 pixels at 120 DPI. Use 24 - 72 DPI for thumbnails and 200 - 300 DPI for printing or OCR.
- `PdfToImageConverter.ColorSpace` - `PdfToImageColorSpace.RGB` (the default) or `PdfToImageColorSpace.Gray`.
- `PdfToImageConverter.ImagesFormat`, `PdfToImageConverter.ImagesPath` and `PdfToImageConverter.ImagesPrefix` - the format, the folder and the file name prefix of the image files.
- `PdfToImageConverter.JpegQuality` - the quality of the JPEG images, from 0 to 100 (91 by default), and `PdfToImageConverter.TiffCompression` - the compression of the TIFF images (`PdfToImageTiffCompression.Lzw` by default). `PdfToImageTiffCompression.Ccitt3` and `PdfToImageTiffCompression.Ccitt4` give black and white images, the smallest files for text documents and fax services.
- `PdfToImageConverter.PngCompressionLevel` - the compression level of the PNG images, from 0 (no compression, the fastest) to 9 (the smallest files). PNG compression is lossless: every level gives the same pixels, a higher level a smaller file in more time. The default, -1, saves the images as the renderer writes them; with a level from 0 to 9 the converter writes the PNG files again at that level.
- `PdfToImageConverter.UserPassword` - the password of a protected document.
- `PdfToImageConverter.GetPageCount(string)` - the number of pages of a document; `PdfToImageConverter.DocumentInformation` - the title, author, subject, keywords, dates and page count, after a conversion.
- `PdfToImageConverter.PdfToolsTimeout` - the maximum time of a conversion, in seconds (600 by default).

### Images in memory

`PdfToImageConverter.ConvertToImages(Stream)` and `PdfToImageConverter.ConvertToImages(string)` return the bytes of an image file for each converted page, in the `PdfToImageConverter.ImagesFormat` format, without writing any file - for example to show thumbnails or to store the images in a database:

```csharp
// or from a database, an upload...
byte[] pdfBytes = File.ReadAllBytes("report.pdf");

PdfToImageConverter converter = new PdfToImageConverter();
converter.Resolution = 36;
converter.StartPageNumber = 1;
converter.EndPageNumber = 1;

byte[][] images = converter.ConvertToImages(new MemoryStream(pdfBytes));
File.WriteAllBytes("thumbnail.png", images[0]);
```

```vbnet
' or from a database, an upload...
Dim pdfBytes As Byte() = File.ReadAllBytes("report.pdf")

Dim converter As New PdfToImageConverter()
converter.Resolution = 36
converter.StartPageNumber = 1
converter.EndPageNumber = 1

Dim images As Byte()() = converter.ConvertToImages(New MemoryStream(pdfBytes))
File.WriteAllBytes("thumbnail.png", images(0))
```

### Thumbnails and single pages

`PdfToImageConverter.ConvertToThumbnails(string, int, int)` returns a thumbnail for each converted page, as large as fits in the box you give (a maximum width and height in pixels; 0 leaves that side free), keeping the aspect ratio of each page. You do not have to choose a resolution: each page is rendered at the resolution its thumbnail needs.

`PdfToImageConverter.ConvertPageToImage(string, int)` converts one page to the bytes of an image file in the ImagesFormat format, and `PdfToImageConverter.ConvertPageToImageBytes(string, int, ImageFormat)` in the format you choose, at the Resolution of the converter. They do not use or change StartPageNumber and EndPageNumber. All have overloads for streams.

```csharp
PdfToImageConverter converter = new PdfToImageConverter();

// thumbnails of all the pages, at most 160 x 160 pixels
byte[][] thumbnails = converter.ConvertToThumbnails("report.pdf", 160, 160);
for (int i = 0; i < thumbnails.Length; i++)
{
    File.WriteAllBytes("thumb" + (i + 1) + ".png", thumbnails[i]);
}

// page 3 as a JPEG file in memory, for a web response or a database
converter.Resolution = 96;
converter.JpegQuality = 80;
byte[] jpeg = converter.ConvertPageToImageBytes("report.pdf", 3,
    ImageFormat.Jpeg);

// page 3 as a PNG file, the smallest one: compression level 9
converter.PngCompressionLevel = 9;
byte[] png = converter.ConvertPageToImageBytes("report.pdf", 3,
    ImageFormat.Png);

// a black and white TIFF document for a fax service
converter.Resolution = 200;
converter.TiffCompression = PdfToImageTiffCompression.Ccitt4;
File.WriteAllBytes("report-fax.tiff", converter.ConvertToTiff(
    "report.pdf"));
```

```vbnet
Dim converter As New PdfToImageConverter()

' thumbnails of all the pages, at most 160 x 160 pixels
Dim thumbnails As Byte()() = converter.ConvertToThumbnails("report.pdf", 160, 160)
For i As Integer = 0 To thumbnails.Length - 1
    File.WriteAllBytes("thumb" & (i + 1) & ".png", thumbnails(i))
Next

' page 3 as a JPEG file in memory, for a web response or a database
converter.Resolution = 96
converter.JpegQuality = 80
Dim jpeg As Byte() = converter.ConvertPageToImageBytes("report.pdf", 3,
    ImageFormat.Jpeg)

' page 3 as a PNG file, the smallest one: compression level 9
converter.PngCompressionLevel = 9
Dim png As Byte() = converter.ConvertPageToImageBytes("report.pdf", 3,
    ImageFormat.Png)

' a black and white TIFF document for a fax service
converter.Resolution = 200
converter.TiffCompression = PdfToImageTiffCompression.Ccitt4
File.WriteAllBytes("report-fax.tiff", converter.ConvertToTiff(
    "report.pdf"))
```

### Multi-page TIFF documents

`PdfToImageConverter.ConvertToTiff(string)` returns one TIFF image with a frame for each converted page, compressed as set by the `PdfToImageConverter.TiffCompression` property (LZW by default) - the format of document archives and fax services. `PdfToImageConverter.ConvertToTiffInFolder(string)` saves it as <ImagesPrefix>.tiff in the ImagesPath folder. Both have overloads for streams.

```csharp
PdfToImageConverter converter = new PdfToImageConverter();
converter.Resolution = 200;
converter.ColorSpace = PdfToImageColorSpace.Gray;

byte[] tiff = converter.ConvertToTiff("report.pdf");
File.WriteAllBytes("report.tiff", tiff);

// or directly to archive/report.tiff
converter.ImagesPath = "archive";
converter.ImagesPrefix = "report";
converter.ConvertToTiffInFolder("report.pdf");
```

```vbnet
Dim converter As New PdfToImageConverter()
converter.Resolution = 200
converter.ColorSpace = PdfToImageColorSpace.Gray

Dim tiff As Byte() = converter.ConvertToTiff("report.pdf")
File.WriteAllBytes("report.tiff", tiff)

' or directly to archive/report.tiff
converter.ImagesPath = "archive"
converter.ImagesPrefix = "report"
converter.ConvertToTiffInFolder("report.pdf")
```

### Samples

The samples download has these console applications, in C# and VB.NET, for Windows, Linux and macOS. They work on the sample.pdf document that comes with them; sample-protected.pdf is the same document protected with the password "open".

- **ConvertPdfToImages** - a command line tool with all the options. Without arguments it converts the pages of sample.pdf to PNG files.
- **PdfToImageInMemory** - thumbnails of all the pages put together in a PDF contact sheet, a page as JPEG bytes and a gray multi-page TIFF, from a document in memory.

The command line tool:

```text
ConvertPdfToImages [/pdf:file.pdf] [/out:folder] [/format:png|jpg|bmp|tiff] [/tiff]
    [/dpi:n] [/gray] [/start:n] [/end:n] [/prefix:name] [/userpswd:password]
    [/quality:0-100] [/compression:lzw|none|rle|ccitt3|ccitt4] [/pngcompression:0-9]
    [/thumbs:size]

ConvertPdfToImages /pdf:sample.pdf /format:jpg /dpi:72 /start:1 /end:2
ConvertPdfToImages /pdf:sample.pdf /tiff /gray /dpi:200
ConvertPdfToImages /pdf:sample.pdf /pngcompression:9
```

### Licensing

Set `LicensingManager.LicenseKey` once when the application starts, or the `PdfToImageConverter.LicenseKey` property before you call it. Without a license key the converter works the same and draws an evaluation notice on the images. See "License key" (installation-and-deployment.md).

## Extracting images

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfImageExtraction.htm

The image extraction part of ExpertPdf Anywhere extracts the images embedded in PDF documents on Windows, Linux and macOS. The images are saved as image files, returned in a list, or given to your code through an event, with the number of their page. Each image is the bytes of an image file in the format you choose.

The main class is `PdfImgExtract`, in the **ExpertPdf.Anywhere** namespace. The images are read by the native PDF tool of the library, **ExpertPdf.Tools** (**ExpertPdf.Tools.exe** on Windows), which the extractor runs as a separate process. The extractor is disposable: a *using* statement, or `PdfImgExtract.Close`, releases the document read by the last call.

### Features

- Extract the images of all the pages of a document or of a range of pages, from a file or a stream (a MemoryStream for a document in memory).
- Save them as PNG, JPEG, BMP or TIFF files, with the file name prefix you choose.
- Receive every image in memory, as the bytes of an image file, with the number of its page - to filter, index or store the images yourself.
- Get all the images in one list, with the page number and position of each image.
- Skip the small images (icons, bullets) with a minimum width and height, and the repeated images (a logo on every page).
- Open password protected documents; read the document information and the page count.

### Installation

The extractor is part of the **ExpertPdf.Anywhere** package. The PDF tool comes with the native package of your platform (**ExpertPdf.Anywhere.Native.<rid>**) or with a platform package (**ExpertPdf.Anywhere.Windows**, **.Linux**, **.Linux.Arm64**, **.MacOS**), which copies it next to your application when it builds and when it is published. See "Installation" (installation-and-deployment.md).

```text
dotnet add package ExpertPdf.Anywhere.Windows
```

The tool is found next to the application. If it is in another folder, set its full path, or the folder that holds it, with the `PdfImgExtract.PdfToolsFullPath` property.

### Getting started

Create a `PdfImgExtract`, set the options and call `PdfImgExtract.ExtractImages(string, bool)`. With true as the second argument the images are saved in the `PdfImgExtract.ExtractPath` folder, as <ExtractPrefix><page number>_<image index>.<format> (the index counts from 0), in the `PdfImgExtract.ExtractImageFormat` format (`ImageFormat`: Png, Jpeg, Bmp or Tiff). The `PdfImgExtract.ImageExtractedEvent` event is raised for every image:

```csharp
using ExpertPdf.Anywhere;

PdfImgExtract extractor = new PdfImgExtract();

extractor.ExtractPath = "images";             // an existing folder
extractor.ExtractPrefix = "image_";
extractor.ExtractImageFormat = ImageFormat.Png;

extractor.ImageExtractedEvent += delegate (ImageExtractedEventArgs e)
{
    Console.WriteLine("page {0}: {1} bytes", e.PageNumber, e.ExtractedBitmap.Length);
};

extractor.ExtractImages("report.pdf", true);
```

```vbnet
Imports ExpertPdf.Anywhere

Sub ExtractImages()
    Dim extractor As New PdfImgExtract()

    extractor.ExtractPath = "images"             ' an existing folder
    extractor.ExtractPrefix = "image_"
    extractor.ExtractImageFormat = ImageFormat.Png

    AddHandler extractor.ImageExtractedEvent, AddressOf OnImageExtracted

    extractor.ExtractImages("report.pdf", True)
End Sub

Sub OnImageExtracted(e As ImageExtractedEventArgs)
    Console.WriteLine("page {0}: {1} bytes", e.PageNumber, e.ExtractedBitmap.Length)
End Sub
```

### Extraction options

- `PdfImgExtract.StartPageNumber` and `PdfImgExtract.EndPageNumber` - the pages to extract the images from. The first page is 1; an end page of 0 means the last page of the document.
- `PdfImgExtract.ExtractImageFormat`, `PdfImgExtract.ExtractPath` and `PdfImgExtract.ExtractPrefix` - the format, the folder and the file name prefix of the image files.
- `PdfImgExtract.MinimumWidth` and `PdfImgExtract.MinimumHeight` - a smaller image is skipped: no event, no file, not in the list (0 by default: every image).
- `PdfImgExtract.SkipDuplicateImages` - an image with the same size and the same pixels as an image extracted before is skipped (false by default).
- `PdfImgExtract.UserPassword` - the password of a protected document.
- `PdfImgExtract.GetPageCount(string)` - the number of pages of a document; `PdfImgExtract.DocumentInformation` - the title, author, subject, keywords, dates and page count, after an extraction.
- `PdfImgExtract.PdfToolsTimeout` - the maximum time of an extraction, in seconds (600 by default).

### Images in memory

With false as the second argument of `PdfImgExtract.ExtractImages(Stream, bool)` the extractor saves nothing: your event handler receives every image in the `ImageExtractedEventArgs.ExtractedBitmap` field - the bytes of an image file in the ExtractImageFormat format - and the page number in `ImageExtractedEventArgs.PageNumber`, and decides what to keep.

```csharp
using ExpertPdf.Anywhere;

// or from a database, an upload...
byte[] pdfBytes = File.ReadAllBytes("report.pdf");

PdfImgExtract extractor = new PdfImgExtract();
extractor.ExtractImageFormat = ImageFormat.Jpeg;
// keep the photos and charts, skip the small images (logos, icons)
extractor.MinimumWidth = 100;
extractor.MinimumHeight = 100;

int count = 0;
extractor.ImageExtractedEvent += delegate (ImageExtractedEventArgs e)
{
    count++;
    string file = string.Format("page{0}-{1}.jpg", e.PageNumber, count);
    File.WriteAllBytes(Path.Combine("photos", file), e.ExtractedBitmap);
};

extractor.ExtractImages(new MemoryStream(pdfBytes), false);
```

```vbnet
Imports ExpertPdf.Anywhere

Private count As Integer

Sub ExtractPhotos()
    ' or from a database, an upload...
    Dim pdfBytes As Byte() = System.IO.File.ReadAllBytes("report.pdf")

    Dim extractor As New PdfImgExtract()
    extractor.ExtractImageFormat = ImageFormat.Jpeg
    ' keep the photos and charts, skip the small images (logos, icons)
    extractor.MinimumWidth = 100
    extractor.MinimumHeight = 100

    AddHandler extractor.ImageExtractedEvent, AddressOf OnImageExtracted
    extractor.ExtractImages(New MemoryStream(pdfBytes), False)
End Sub

Sub OnImageExtracted(e As ImageExtractedEventArgs)
    count += 1
    Dim file As String = String.Format("page{0}-{1}.jpg", e.PageNumber, count)
    System.IO.File.WriteAllBytes(Path.Combine("photos", file), e.ExtractedBitmap)
End Sub
```

`PdfImgExtract.ExtractImagesToMemory(string)` returns all the images in a list of `ExtractedImage`: the `ExtractedImage.Image` bytes, its `ExtractedImage.PageNumber`, its `ExtractedImage.Index` among all the images of the document (the number in the names of the files the extractor saves) and its `ExtractedImage.IndexOnPage`. The event is raised for these images too.

```csharp
PdfImgExtract extractor = new PdfImgExtract();
extractor.MinimumWidth = 100;          // no icons and bullets
extractor.MinimumHeight = 100;
extractor.SkipDuplicateImages = true;  // the logo of every page only once
extractor.ExtractImageFormat = ImageFormat.Png;

List<ExtractedImage> images = extractor.ExtractImagesToMemory("report.pdf");
foreach (ExtractedImage image in images)
{
    string file = string.Format("page{0}-{1}.png", image.PageNumber,
        image.IndexOnPage);
    File.WriteAllBytes(Path.Combine("photos", file), image.Image);
}
```

```vbnet
Dim extractor As New PdfImgExtract()
extractor.MinimumWidth = 100          ' no icons and bullets
extractor.MinimumHeight = 100
extractor.SkipDuplicateImages = True  ' the logo of every page only once
extractor.ExtractImageFormat = ImageFormat.Png

Dim images As List(Of ExtractedImage) = extractor.ExtractImagesToMemory("report.pdf")
For Each image As ExtractedImage In images
    Dim file As String = String.Format("page{0}-{1}.png", image.PageNumber,
        image.IndexOnPage)
    System.IO.File.WriteAllBytes(Path.Combine("photos", file), image.Image)
Next
```

### Samples

The samples download has these console applications, in C# and VB.NET, for Windows, Linux and macOS. They work on the sample.pdf document that comes with them; sample-protected.pdf is the same document protected with the password "open".

- **ExtractPdfImages** - a command line tool with all the options. Without arguments it saves the images of sample.pdf as PNG files.
- **ExtractImagesInMemory** - extracts the images of a document read from memory into a list, keeps only the large ones and each repeated image once.

### PDF Images Extractor command line tool

The ExtractPdfImages sample is a complete command line tool:

```text
ExtractPdfImages [/pdf:file.pdf] [/out:folder] [/imgformat:png|jpg|bmp|tiff]
                 [/start:n] [/end:n] [/prefix:name] [/userpswd:password]
                 [/minwidth:n] [/minheight:n] [/nodup]
```

Examples:

```text
ExtractPdfImages /pdf:sample.pdf /imgformat:jpg /start:4 /end:4
ExtractPdfImages /pdf:sample.pdf /minwidth:100 /minheight:100 /nodup
ExtractPdfImages /pdf:sample-protected.pdf /userpswd:open
```

### Licensing

Set `LicensingManager.LicenseKey` once when the application starts, or the `PdfImgExtract.LicenseKey` property before you call it. Without a license key the extractor works the same and draws an evaluation notice on the images that are more than 100 pixels wide. See "License key" (installation-and-deployment.md).
