# Merging, splitting and editing existing PDFs

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Merge and split with Document

Online: https://docs.html-to-pdf.net/anywhere-library/html/MergeAndSplit.htm

The document model merges PDF documents and extracts pages from a PDF document with the static methods of the `MergePdf` and `SplitPdf` classes:

- `MergePdf.Merge(string[], string)` merges PDF files into a PDF file, and the overload with streams merges PDF streams into a PDF stream.
- `SplitPdf.ExtractPages(string, int, int, string)` extracts a range of pages - a start page index and a number of pages - from a PDF file or a `Document`, into a new file or into a new `Document` that can be changed further.

A document can also take the pages of other documents: `Document.AppendDocument(Document)` appends all the pages of a document, and `PageCollection.Add(PdfPage)` appends one page. An appended document must stay open until the document it was appended to is saved (`Document.AutoCloseAppendedDocs` closes it with that document). The appended pages keep their own layout, so the document's header, footer, left and right templates are not drawn on them; the other templates (a watermark, for example) are drawn on every page. A PDF/A document takes pages only from documents that conform to its PDF/A level; convert the others first with `Document.ConvertToPdfA(PdfStandardSubset)`.

For merging with more options (page ranges, images, empty pages, bookmarks for the merged documents, security) see "Merging PDF documents" (edit-merge-split.md), and for splitting a document into several files see "Splitting PDF documents" (edit-merge-split.md).

### Code Sample

The code sample below, an ASP.NET Core controller action, merges two PDF files into a file on disk, extracts the first page of the merged document into another file, and merges the two files again into a stream returned to the browser:

```csharp
public IActionResult MergeAndSplit()
{
    string workingFolderPath = Path.Combine(AppContext.BaseDirectory, "Files");

    string firstFilePath = Path.Combine(workingFolderPath, "first.pdf");
    string secondFilePath = Path.Combine(workingFolderPath, "second.pdf");
    string mergedFilePath = Path.Combine(workingFolderPath, "MergeFileResult.pdf");
    string extractedFilePath = Path.Combine(workingFolderPath,
        "SplitFileResult.pdf");

    // merge the two files into mergedFilePath on disk
    string[] filesToMerge = new string[] { firstFilePath, secondFilePath };
    MergePdf.Merge(filesToMerge, mergedFilePath);

    // extract the first page from the merged document into a PDF file
    SplitPdf.ExtractPages(mergedFilePath, 0, 1, extractedFilePath);

    // merge streams into a memory stream
    using (FileStream fs1 = new FileStream(firstFilePath, FileMode.Open,
        FileAccess.Read))
    using (FileStream fs2 = new FileStream(secondFilePath, FileMode.Open,
        FileAccess.Read))
    {
        MemoryStream merged = new MemoryStream();
        MergePdf.Merge(new Stream[] { fs1, fs2 }, merged);

        // send the merge result to the browser
        return File(merged.ToArray(), "application/pdf", "MergeStreamsResult.pdf");
    }
}
```

```vbnet
Public Function MergeAndSplit() As IActionResult
    Dim workingFolderPath As String = Path.Combine(AppContext.BaseDirectory, "Files")

    Dim firstFilePath As String = Path.Combine(workingFolderPath, "first.pdf")
    Dim secondFilePath As String = Path.Combine(workingFolderPath, "second.pdf")
    Dim mergedFilePath As String = Path.Combine(workingFolderPath,
        "MergeFileResult.pdf")
    Dim extractedFilePath As String = Path.Combine(workingFolderPath,
        "SplitFileResult.pdf")

    ' merge the two files into mergedFilePath on disk
    Dim filesToMerge As String() = New String() {firstFilePath, secondFilePath}
    MergePdf.Merge(filesToMerge, mergedFilePath)

    ' extract the first page from the merged document into a PDF file
    SplitPdf.ExtractPages(mergedFilePath, 0, 1, extractedFilePath)

    ' merge streams into a memory stream
    Using fs1 As New FileStream(firstFilePath, FileMode.Open, FileAccess.Read),
          fs2 As New FileStream(secondFilePath, FileMode.Open, FileAccess.Read)
        Dim merged As New MemoryStream()
        MergePdf.Merge(New Stream() {fs1, fs2}, merged)

        ' send the merge result to the browser
        Return File(merged.ToArray(), "application/pdf", "MergeStreamsResult.pdf")
    End Using
End Function
```

## Merging PDF documents

Online: https://docs.html-to-pdf.net/anywhere-library/html/MergePdf.htm

The PDF merge part of ExpertPdf Anywhere combines PDF documents and images into one PDF document. It is in the **ExpertPdf.Anywhere.MergePdf** namespace of the ExpertPdf.Anywhere library and runs on every platform the library supports: Windows, Linux and macOS.

The main class is `PDFMerge`: you append documents, images and empty pages to it in the order they must appear, then save the merged document. Merging is done in managed code and needs neither the Chromium engine nor the native PDF tool.

### Features

- Merge PDF documents from files, streams and byte arrays: the whole document, the pages from a start page to the end, or a range of pages.
- Merge password protected documents, opened with their user or owner password.
- Add images - PNG, JPEG, GIF, BMP, WebP or TIFF, from a file or as the bytes of an image file - and empty pages, each on a new page.
- Insert a document, a range of pages, an image or an empty page at any position, and change the order of the merged pages.
- Add a bookmark for each merged document (titled from its file name or document title), with the bookmarks of the document nested under it; or leave the bookmarks out.
- Save the merged pages as a booklet: two pages on each landscape sheet, in saddle stitch order, for single or double-sided printing.
- Choose the page size and orientation of the pages the library creates, and the compression level of the merged document.
- Set the document information: title, subject, author, keywords, creation date.
- Protect the merged document: user and owner passwords, print, copy, edit, form fill and assemble permissions, RC4 (40 or 128 bit), AES (128 or 256 bit) or AES-256 GCM encryption.
- Save the merged document as PDF/A-1b, PDF/A-2b, PDF/A-2u, PDF/A-3b, PDF/A-3u, PDF/A-4, PDF/A-4e, PDF/A-4f or PDF/X-1a.
- Save to a file, a stream or a byte array.

### Installation

The merge classes are part of the **ExpertPdf.Anywhere** package; there is no separate package to install. See "Installation" (installation-and-deployment.md) for the packages and "Requirements" (installation-and-deployment.md) for the supported platforms. An application that only merges documents does not need a native engine package.

```text
dotnet add package ExpertPdf.Anywhere
```

### Getting started

Create a `PDFMerge`, append the documents and images, and save the result with `PDFMerge.SaveMergedPDFToFile(string)`. `PDFMerge` is disposable: a *using* statement releases the documents it holds.

```csharp
using ExpertPdf.Anywhere.MergePdf;

using (PDFMerge pdfMerge = new PDFMerge())
{
    pdfMerge.AppendPDFFile("report.pdf");
    pdfMerge.AppendImageFile("chart.png");    // on a new page
    pdfMerge.AppendEmptyPage();
    pdfMerge.AppendPDFFile("invoice.pdf");

    pdfMerge.SaveMergedPDFToFile("merged.pdf");
}
```

```vbnet
Imports ExpertPdf.Anywhere.MergePdf

Using pdfMerge As New PDFMerge()
    pdfMerge.AppendPDFFile("report.pdf")
    pdfMerge.AppendImageFile("chart.png")    ' on a new page
    pdfMerge.AppendEmptyPage()
    pdfMerge.AppendPDFFile("invoice.pdf")

    pdfMerge.SaveMergedPDFToFile("merged.pdf")
End Using
```

The options of the merged document are given to the constructor in a `PdfDocumentOptions` object. The page size and orientation (A4 and portrait by default) are used for the pages the library creates - images and empty pages; the pages of the appended PDF documents keep their own size.

```csharp
PdfDocumentOptions options = new PdfDocumentOptions();
options.PdfPageSize = PdfPageSize.Letter;
options.PdfPageOrientation = PDFPageOrientation.Landscape;
options.PdfCompressionLevel = PDFCompressionLevel.Best;

PDFMerge pdfMerge = new PDFMerge(options);
```

```vbnet
Dim options As New PdfDocumentOptions()
options.PdfPageSize = PdfPageSize.Letter
options.PdfPageOrientation = PDFPageOrientation.Landscape
options.PdfCompressionLevel = PDFCompressionLevel.Best

Dim pdfMerge As New PDFMerge(options)
```

### Page ranges, streams and memory

Each Append method has versions that take only some pages of the source document: from a zero based start index to the end, or a number of pages from the start index (a negative number of pages also means "to the end"). Documents can come from a file (`PDFMerge.AppendPDFFile(string, int, int)`), a stream (`PDFMerge.AppendPDFStream(Stream, int, int)`) or a byte array (`PDFMerge.AppendPDFBytes(byte[], int, int)`), for example a document just created in memory by the HTML to PDF converter. A stream is read when it is appended, so it can be closed right after the call. An image can also be given as the bytes of an image file, with `PDFMerge.AppendImageObject(byte[])`.

```csharp
PDFMerge pdfMerge = new PDFMerge();

// the first two pages of the report
pdfMerge.AppendPDFFile("report.pdf", 0, 2);
// a whole document from memory
pdfMerge.AppendPDFBytes(File.ReadAllBytes("invoice.pdf"));
// the report from its fifth page to the end
pdfMerge.AppendPDFFile("report.pdf", 4);
// an image given as the bytes of a PNG file
pdfMerge.AppendImageObject(File.ReadAllBytes("chart.png"));

Console.WriteLine("Pages: " + pdfMerge.GetCurrentPageCount());

// the merged document in memory, for example to send it in a web response
byte[] mergedBytes = pdfMerge.RenderMergedPDFDocument();
```

```vbnet
Dim pdfMerge As New PDFMerge()

' the first two pages of the report
pdfMerge.AppendPDFFile("report.pdf", 0, 2)
' a whole document from memory
pdfMerge.AppendPDFBytes(File.ReadAllBytes("invoice.pdf"))
' the report from its fifth page to the end
pdfMerge.AppendPDFFile("report.pdf", 4)
' an image given as the bytes of a PNG file
pdfMerge.AppendImageObject(File.ReadAllBytes("chart.png"))

Console.WriteLine("Pages: " & pdfMerge.GetCurrentPageCount())

' the merged document in memory, for example to send it in a web response
Dim mergedBytes As Byte() = pdfMerge.RenderMergedPDFDocument()
```

`PDFMerge.GetPageCount(string)` is a fast way to get the page count of a document before you merge it.

### Document information, security and protected sources

`PDFMerge.DocumentInfo` sets the title, subject, author and keywords of the merged document, and `PDFMerge.SecurityOptions` protects it. The user password opens the document with the permissions you set; the owner password opens it with all the rights. When only a user password is set, it is also the owner password. The encryption is AES with a 128 bit key by default; a 256 bit key gives AES-256 (ISO 32000-2), a 40 bit key RC4, and `EncryptionAlgorithm.AESGCM` AES-256 in Galois/Counter mode, which writes the document as PDF 2.0.

```csharp
PDFMerge pdfMerge = new PDFMerge();

pdfMerge.DocumentInfo.Title = "Report with invoice";
pdfMerge.DocumentInfo.AuthorName = "Sample Company";
pdfMerge.DocumentInfo.Keywords = "report, invoice";

pdfMerge.SecurityOptions.UserPassword = "user";
pdfMerge.SecurityOptions.OwnerPassword = "owner";
pdfMerge.SecurityOptions.KeySize = EncryptionKeySize.EncryptKey256Bit;
pdfMerge.SecurityOptions.CanCopyContent = false;
pdfMerge.SecurityOptions.CanEditContent = false;

// a protected source document is opened with its user or owner password (-1 = all
// the pages)
pdfMerge.AppendPDFFile("protected.pdf", "password", 0, -1);
pdfMerge.AppendPDFFile("invoice.pdf");

using (FileStream output = File.Create("secured.pdf"))
{
    pdfMerge.SaveMergedPDFToStream(output);
}
```

```vbnet
Dim pdfMerge As New PDFMerge()

pdfMerge.DocumentInfo.Title = "Report with invoice"
pdfMerge.DocumentInfo.AuthorName = "Sample Company"
pdfMerge.DocumentInfo.Keywords = "report, invoice"

pdfMerge.SecurityOptions.UserPassword = "user"
pdfMerge.SecurityOptions.OwnerPassword = "owner"
pdfMerge.SecurityOptions.KeySize = EncryptionKeySize.EncryptKey256Bit
pdfMerge.SecurityOptions.CanCopyContent = False
pdfMerge.SecurityOptions.CanEditContent = False

' a protected source document is opened with its user or owner password (-1 = all the
' pages)
pdfMerge.AppendPDFFile("protected.pdf", "password", 0, -1)
pdfMerge.AppendPDFFile("invoice.pdf")

Using output As FileStream = File.Create("secured.pdf")
    pdfMerge.SaveMergedPDFToStream(output)
End Using
```

### Insert and reorder pages

The Insert methods add a document, a range of pages, an image or an empty page at a zero based position in the merged document: 0 inserts before the first page, `PDFMerge.GetCurrentPageCount` at the end. They take the same arguments as the Append methods, after the position: `PDFMerge.InsertPDFFile(int, string)`, `PDFMerge.InsertPDFStream(int, Stream, string, int, int)`, `PDFMerge.InsertPDFBytes(int, byte[], int, int)`, `PDFMerge.InsertImageFile(int, string)` and `PDFMerge.InsertEmptyPage(int)`. `PDFMerge.ReorderPages(int[])` changes the order of the pages merged so far: the new order lists, for each position, the current index of the page that goes there, and contains every page once. The bookmarks keep opening the same pages and are listed in the order of the pages.

```csharp
PDFMerge pdfMerge = new PDFMerge();

pdfMerge.AppendPDFFile("report.pdf");          // 6 pages
pdfMerge.InsertPDFFile(1, "invoice.pdf");      // after the first page
pdfMerge.InsertEmptyPage(0);                   // in front

// reverse the order of the 8 pages
pdfMerge.ReorderPages(new int[] { 7, 6, 5, 4, 3, 2, 1, 0 });

pdfMerge.SaveMergedPDFToFile("merged.pdf");
```

```vbnet
Dim pdfMerge As New PDFMerge()

pdfMerge.AppendPDFFile("report.pdf")          ' 6 pages
pdfMerge.InsertPDFFile(1, "invoice.pdf")      ' after the first page
pdfMerge.InsertEmptyPage(0)                   ' in front

' reverse the order of the 8 pages
pdfMerge.ReorderPages(New Integer() {7, 6, 5, 4, 3, 2, 1, 0})

pdfMerge.SaveMergedPDFToFile("merged.pdf")
```

### Bookmarks

By default the merged document keeps the bookmarks of the merged documents. With `PdfBookmarkOptions.AddDocumentBookmarks` set to true, each merged document - and each image appended from a file - gets a bookmark that opens its first page, and its own bookmarks are nested under it. `PdfBookmarkOptions.TitleSource` takes the title from the file name or from the document title (a document from a stream or memory without a title is "Document N"), and `PDFMerge.SetBookmarkTitle(string)` sets the title of the document added last. `PdfBookmarkOptions.KeepSourceBookmarks` set to false leaves the bookmarks of the merged documents out.

```csharp
PDFMerge pdfMerge = new PDFMerge();
pdfMerge.BookmarkOptions.AddDocumentBookmarks = true;
pdfMerge.BookmarkOptions.TitleSource = BookmarkTitleSource.DocumentTitle;

pdfMerge.AppendPDFFile("report.pdf");
pdfMerge.AppendPDFFile("invoice.pdf");
pdfMerge.SetBookmarkTitle("Invoice");

pdfMerge.SaveMergedPDFToFile("merged.pdf");
```

```vbnet
Dim pdfMerge As New PDFMerge()
pdfMerge.BookmarkOptions.AddDocumentBookmarks = True
pdfMerge.BookmarkOptions.TitleSource = BookmarkTitleSource.DocumentTitle

pdfMerge.AppendPDFFile("report.pdf")
pdfMerge.AppendPDFFile("invoice.pdf")
pdfMerge.SetBookmarkTitle("Invoice")

pdfMerge.SaveMergedPDFToFile("merged.pdf")
```

### Booklets

With `PdfBookletOptions.Enabled` set to true the merged pages are saved two by two on landscape sheets of `PdfBookletOptions.PageSize` (A4 sheets, so A5 pages, by default), in the order that gives a booklet when the printed sheets are folded in the middle. Each page is scaled to fit its half of the sheet. The page count is completed to a multiple of 4 with empty halves at the end. With `PdfBookletOptions.TwoSided` the sheets are ordered for double-sided printing (for 8 pages: 8|1, 2|7, 6|3, 4|5; flip on the short edge); otherwise each sheet holds a page and its counterpart from the end, in reading order (1|8, 2|7, 3|6, 4|5). A booklet has no bookmarks; the document information, security and PDF standard apply to it as usual.

```csharp
PDFMerge pdfMerge = new PDFMerge();
pdfMerge.BookletOptions.Enabled = true;
pdfMerge.BookletOptions.PageSize = PdfPageSize.A4;
pdfMerge.BookletOptions.TwoSided = true;

pdfMerge.AppendPDFFile("report.pdf");
pdfMerge.SaveMergedPDFToFile("booklet.pdf");
```

```vbnet
Dim pdfMerge As New PDFMerge()
pdfMerge.BookletOptions.Enabled = True
pdfMerge.BookletOptions.PageSize = PdfPageSize.A4
pdfMerge.BookletOptions.TwoSided = True

pdfMerge.AppendPDFFile("report.pdf")
pdfMerge.SaveMergedPDFToFile("booklet.pdf")
```

### PDF/A and PDF/X

Set `PdfDocumentOptions.PdfStandardSubset` to save the merged document as PDF/A-1b, PDF/A-2b, PDF/A-2u, PDF/A-3b, PDF/A-3u, PDF/A-4, PDF/A-4e or PDF/A-4f for long term archiving, or as PDF/X-1a for graphics exchange. The pages are imported as they are, so a PDF/A merge only takes source documents that conform to the level. Convert a document that does not with `Document.ConvertToPdfA(PdfStandardSubset)` first, as below. The PDF/A standards do not allow encryption: do not set passwords for a PDF/A document.

```csharp
using ExpertPdf.Anywhere.MergePdf;
using ExpertPdf.Anywhere.PdfDocument;

PdfDocumentOptions options = new PdfDocumentOptions();
options.PdfStandardSubset = ExpertPdf.Anywhere.MergePdf.PdfStandardSubset.Pdf_A_2b;

PDFMerge pdfMerge = new PDFMerge(options);
pdfMerge.DocumentInfo.Title = "Archived report";

foreach (string source in new string[] { "report.pdf", "invoice.pdf" })
{
    // each source converted to PDF/A-2b before it is merged
    using (Document document = new Document(source))
    {
        document.ConvertToPdfA(
            ExpertPdf.Anywhere.HtmlToPdf.PdfStandardSubset.Pdf_A_2b);
        pdfMerge.AppendPDFBytes(document.Save());
    }
}

pdfMerge.SaveMergedPDFToFile("archive.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere.MergePdf
Imports ExpertPdf.Anywhere.PdfDocument

Dim options As New PdfDocumentOptions()
options.PdfStandardSubset = ExpertPdf.Anywhere.MergePdf.PdfStandardSubset.Pdf_A_2b

Dim pdfMerge As New PDFMerge(options)
pdfMerge.DocumentInfo.Title = "Archived report"

For Each source As String In New String() {"report.pdf", "invoice.pdf"}
    ' each source converted to PDF/A-2b before it is merged
    Using document As New Document(source)
        document.ConvertToPdfA(
            ExpertPdf.Anywhere.HtmlToPdf.PdfStandardSubset.Pdf_A_2b)
        pdfMerge.AppendPDFBytes(document.Save())
    End Using
Next

pdfMerge.SaveMergedPDFToFile("archive.pdf")
```

### Samples

The samples download has these console applications, in C# and VB.NET, for Windows, Linux and macOS:

- **MergePdfCommandLine** - a command line tool that merges PDF files, images and empty pages, with options for the page size, orientation, compression, PDF standard, title, passwords, a bookmark per document and booklets.
- **MergePdfFeatures** - page ranges, documents from memory and streams, images, document information and security, password protected sources, PDF/A-2b, inserting and reordering pages, a bookmark per document, a booklet.

They work on the documents and the image that come with them and need no arguments.

### PDF Merge command line tool

The MergePdfCommandLine sample is also a ready to use tool. The syntax is:

```text
MergePdfCommandLine {/pdf:file | /image:file | /emptypage}+ [/out:file]
    [/pagesize:A4|Letter|...] [/orientation:Portrait|Landscape]
    [/compression:NoCompression|BestSpeed|BelowNormal|Normal|AboveNormal|Best]
    [/standard:Full|Pdf_A_1b|Pdf_A_2b|Pdf_A_3b|Pdf_A_3u|Pdf_X_1a]
    [/title:text] [/userpswd:password] [/ownerpswd:password]
    [/bookmarks] [/booklet | /booklet:twosided]
```

The documents, images and empty pages are merged in the order you give them. /bookmarks adds a bookmark for each document, named after the file; /booklet saves a booklet on landscape sheets of the page size. Quote the paths that contain spaces. Example:

```text
MergePdfCommandLine /pdf:"My Documents/Report.pdf" /emptypage /image:chart.png
    /standard:Pdf_A_2b /out:Merged.pdf
```

### Licensing

Set `LicensingManager.LicenseKey` once when the application starts, or the `PDFMerge.LicenseKey` property before you save the merged document. Without a license key the library adds an evaluation notice to the pages of the merged document. See "License key" (installation-and-deployment.md).

## Splitting PDF documents

Online: https://docs.html-to-pdf.net/anywhere-library/html/SplitPdf.htm

The PDF split part of ExpertPdf Anywhere extracts pages from PDF documents into new PDF documents. It is in the **ExpertPdf.Anywhere.PDFSplit** namespace of the ExpertPdf.Anywhere library and runs on every platform the library supports: Windows, Linux and macOS.

The main class is `PDFSplitManager`. It is disposable: a *using* statement, or `PDFSplitManager.Close`, releases what it holds.

### Features

- Extract a range of pages into a new document.
- Split a document into a number of documents of the same size, or into documents of a given number of pages up to the end of the document.
- Read the source document from a file or from memory, and save the new documents to files or keep them in memory.
- Split password protected documents, opened with their user or owner password.
- Set the document information (title, subject, author, keywords) and the security (passwords, permissions, RC4 with a 40 or 128 bit key, AES with a 128 or 256 bit key, or AES-256 GCM) of the new documents.
- Split a document at its top level bookmarks: one document per chapter, named after the bookmark.
- Extract a list of pages ("1-3,7,10-"), or the odd or even pages.
- Split a document into documents under a maximum file size.
- Get the page count of a document.

### Installation

The split classes are part of the **ExpertPdf.Anywhere** package; there is no separate package to install. See "Installation" (installation-and-deployment.md) for the packages and "Requirements" (installation-and-deployment.md) for the supported platforms. Splitting is done in managed code: an application that only splits documents does not need a native engine package.

```text
dotnet add package ExpertPdf.Anywhere
```

### Getting started

The page numbers start at 1. A range of pages is given by the number of the first page and the number of pages; when the range goes past the end of the document, the new document ends with the last page.

```csharp
using ExpertPdf.Anywhere.PDFSplit;

PDFSplitManager splitManager = new PDFSplitManager();
// splitManager.LicenseKey = "your license key";

// pages 2 to 4 into a new document
splitManager.ExtractPagesFromFileToFile("report.pdf", "pages-2-4.pdf", 2, 3);

Console.WriteLine("Pages: " + splitManager.GetPdfPageCount("report.pdf"));
```

```vbnet
Imports ExpertPdf.Anywhere.PDFSplit

Dim splitManager As New PDFSplitManager()
' splitManager.LicenseKey = "your license key"

' pages 2 to 4 into a new document
splitManager.ExtractPagesFromFileToFile("report.pdf", "pages-2-4.pdf", 2, 3)

Console.WriteLine("Pages: " & splitManager.GetPdfPageCount("report.pdf"))
```

### Splitting into several documents

The methods with a repeat count extract that number of documents of the same size; the methods with a "repeat to end" flag go on to the end of the document. A file name given for several documents gets the index of each document: *part.pdf* becomes part_1.pdf, part_2.pdf and so on. The memory versions return an array of `PDFSplitDoc` objects: `PDFSplitDoc.Buffer` has the bytes of the document and `PDFSplitDoc.SaveToFile(string)` saves it.

```csharp
PDFSplitManager splitManager = new PDFSplitManager();

// documents of 1 page up to the end: part_1.pdf, part_2.pdf, ...
splitManager.ExtractPagesFromFileToFile("report.pdf",
    "part.pdf", 1, 1, true);

// two documents of 3 pages, starting with page 1: half_1.pdf and half_2.pdf
splitManager.ExtractPagesFromFileToFile("report.pdf",
    "half.pdf", 1, 3, 2);

// documents of 2 pages up to the end, in memory
byte[] pdfBytes = File.ReadAllBytes("report.pdf");
PDFSplitDoc[] parts = splitManager.ExtractPagesFromMemoryToMemory(pdfBytes, 1, 2,
    true);
foreach (PDFSplitDoc part in parts)
{
    Console.WriteLine("{0} bytes, {1} pages", part.Buffer.Length,
        splitManager.GetPdfPageCount(part.Buffer));
}
parts[0].SaveToFile("first-part.pdf");
```

```vbnet
Dim splitManager As New PDFSplitManager()

' documents of 1 page up to the end: part_1.pdf, part_2.pdf, ...
splitManager.ExtractPagesFromFileToFile("report.pdf",
    "part.pdf", 1, 1, True)

' two documents of 3 pages, starting with page 1: half_1.pdf and half_2.pdf
splitManager.ExtractPagesFromFileToFile("report.pdf",
    "half.pdf", 1, 3, 2)

' documents of 2 pages up to the end, in memory
Dim pdfBytes As Byte() = File.ReadAllBytes("report.pdf")
Dim parts As PDFSplitDoc() = splitManager.ExtractPagesFromMemoryToMemory(pdfBytes,
    1, 2, True)
For Each part As PDFSplitDoc In parts
    Console.WriteLine("{0} bytes, {1} pages", part.Buffer.Length,
        splitManager.GetPdfPageCount(part.Buffer))
Next
parts(0).SaveToFile("first-part.pdf")
```

Every combination exists: ExtractPagesFromFileToFile, ExtractPagesFromFileToMemory, ExtractPagesFromMemoryToFile and ExtractPagesFromMemoryToMemory, each for one range, a repeat count or "repeat to end".

### One document per chapter (bookmarks)

`PDFSplitManager.SplitByBookmarksFromFileToFolder(string, string)` splits a document at its top level bookmarks: each chapter goes from the page of its bookmark to the page before the next chapter, and is saved in the folder as a file named after the bookmark (the characters that are not allowed in file names become '_', a repeated title gets a number: "Title (2).pdf"). The pages before the first chapter are saved as "Pages 1-N.pdf"; a document without bookmarks gives one file with all its pages. The methods that keep the documents in memory (`PDFSplitManager.SplitByBookmarksFromFileToMemory(string)`, `PDFSplitManager.SplitByBookmarksFromMemoryToMemory(byte[])`) give the bookmark title in `PDFSplitDoc.Title` and the source page numbers in `PDFSplitDoc.PageNumbers`.

```csharp
PDFSplitManager splitManager = new PDFSplitManager();

string[] files = splitManager.SplitByBookmarksFromFileToFolder(
    "manual.pdf", "Chapters");

foreach (PDFSplitDoc chapter in splitManager.SplitByBookmarksFromFileToMemory(
    "manual.pdf"))
    Console.WriteLine("{0}: {1} page(s)", chapter.Title, chapter.PageNumbers.Length);
```

```vbnet
Dim splitManager As New PDFSplitManager()

Dim files As String() = splitManager.SplitByBookmarksFromFileToFolder(
    "manual.pdf", "Chapters")

For Each chapter As PDFSplitDoc In splitManager.SplitByBookmarksFromFileToMemory(
    "manual.pdf")
    Console.WriteLine("{0}: {1} page(s)", chapter.Title, chapter.PageNumbers.Length)
Next
```

### Page lists, odd and even pages

The ExtractPages methods that take a page list string extract the listed pages into one new document, in the order of the list: page numbers start at 1 and are separated by commas, "a-b" is a range (taken backwards when a is greater than b), "a-" goes to the last page and "-b" starts with the first page. The overloads that take a `PageSelection` extract the odd or the even pages.

```csharp
PDFSplitManager splitManager = new PDFSplitManager();

splitManager.ExtractPagesFromFileToFile("report.pdf",
    "selected.pdf", "1-3,7,10-");
splitManager.ExtractPagesFromFileToFile("report.pdf",
    "odd.pdf", PageSelection.OddPages);
PDFSplitDoc even = splitManager.ExtractPagesFromFileToMemory(
    "report.pdf", PageSelection.EvenPages);
```

```vbnet
Dim splitManager As New PDFSplitManager()

splitManager.ExtractPagesFromFileToFile("report.pdf",
    "selected.pdf", "1-3,7,10-")
splitManager.ExtractPagesFromFileToFile("report.pdf",
    "odd.pdf", PageSelection.OddPages)
Dim even As PDFSplitDoc = splitManager.ExtractPagesFromFileToMemory(
    "report.pdf", PageSelection.EvenPages)
```

### Split by maximum file size

`PDFSplitManager.SplitBySizeFromFileToFile(string, string, long)` splits a document into documents of consecutive pages that are each at most the given number of bytes - for example to stay under an e-mail attachment limit. Each document takes as many pages as fit, with the security and document information of the new documents included in the size. A page that is larger than the limit on its own makes a document of its own. The files are saved as <name>_1.pdf, <name>_2.pdf..., and the method returns their paths; `PDFSplitManager.SplitBySizeFromFileToMemory(string, long)` keeps them in memory.

```csharp
PDFSplitManager splitManager = new PDFSplitManager();

string[] parts = splitManager.SplitBySizeFromFileToFile("report.pdf",
    "Part.pdf", 5 * 1024 * 1024);
foreach (string part in parts)
    Console.WriteLine(part);
```

```vbnet
Dim splitManager As New PDFSplitManager()

Dim parts As String() = splitManager.SplitBySizeFromFileToFile(
    "report.pdf", "Part.pdf", 5 * 1024 * 1024)
For Each part As String In parts
    Console.WriteLine(part)
Next
```

### Document information, security and protected sources

`PDFSplitManager.PdfDocumentInfo` and `PDFSplitManager.PdfSecurityOptions` apply to every new document. `PDFSplitManager.SourcePdfPassword` opens a password protected source document.

```csharp
PdfDocumentOptions options = new PdfDocumentOptions();
options.PdfCompressionLevel = PDFCompressionLevel.Best;

PDFSplitManager splitManager = new PDFSplitManager(options);

// the password of the source document (its user or owner password)
splitManager.SourcePdfPassword = "source password";

// the new documents: document information, open password, no printing
splitManager.PdfDocumentInfo.Title = "Report - first part";
splitManager.PdfDocumentInfo.AuthorName = "Sample Company";
splitManager.PdfSecurityOptions.UserPassword = "secret";
splitManager.PdfSecurityOptions.OwnerPassword = "owner";
splitManager.PdfSecurityOptions.CanPrint = false;
splitManager.PdfSecurityOptions.KeySize = EncryptionKeySize.EncryptKey256Bit;

splitManager.ExtractPagesFromFileToFile("protected.pdf",
    "first-part.pdf", 1, 3);
```

```vbnet
Dim options As New PdfDocumentOptions()
options.PdfCompressionLevel = PDFCompressionLevel.Best

Dim splitManager As New PDFSplitManager(options)

' the password of the source document (its user or owner password)
splitManager.SourcePdfPassword = "source password"

' the new documents: document information, open password, no printing
splitManager.PdfDocumentInfo.Title = "Report - first part"
splitManager.PdfDocumentInfo.AuthorName = "Sample Company"
splitManager.PdfSecurityOptions.UserPassword = "secret"
splitManager.PdfSecurityOptions.OwnerPassword = "owner"
splitManager.PdfSecurityOptions.CanPrint = False
splitManager.PdfSecurityOptions.KeySize = EncryptionKeySize.EncryptKey256Bit

splitManager.ExtractPagesFromFileToFile("protected.pdf",
    "first-part.pdf", 1, 3)
```

### Samples

The samples download has these console applications, in C# and VB.NET, for Windows, Linux and macOS:

- **SplitPdfCommandLine** - a command line tool: start page, split size, repeat count or repeat to the end, password of the source document; or split at the bookmarks, extract a page list or the odd/even pages, split by size.
- **SplitPdfFeatures** - page ranges and chunks from and to files or memory, the page count, document information and security of the new documents, splitting a password protected document, one document per chapter, page lists, odd and even pages, split by size.

They split the 6 page document that comes with them and need no arguments.

### PDF Split command line tool

The SplitPdfCommandLine sample is also a ready to use tool. The syntax is:

```text
SplitPdfCommandLine /pdf:source_file [/startpage:number] [/splitsize:pages]
    [/repeatcount:count] [/password:source_password] [/out:file]
SplitPdfCommandLine /pdf:source_file
    {/bookmarks | /pages:list | /odd | /even | /maxsize:KB}
    [/password:source_password] [/out:file]
```

By default the split starts with the first page, makes documents of 1 page and repeats to the end of the document. /bookmarks saves one document per chapter in the folder named after the out file; /pages, /odd and /even save one document with those pages; /maxsize saves documents of at most that many kilobytes. Example - two documents of 2 pages, from page 3:

```text
SplitPdfCommandLine /pdf:"My Documents/Report.pdf" /startpage:3 /splitsize:2
    /repeatcount:2 /out:Part.pdf
```

### Licensing

Set `LicensingManager.LicenseKey` once when the application starts, or the `PDFSplitManager.LicenseKey` property before you split documents. Without a license key the library adds an evaluation notice to the pages of the new documents. See "License key" (installation-and-deployment.md).

## Resizing pages

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfResize.htm

`PdfResizeManager` places the pages of an existing PDF document on pages of another size, orientation and margins - for example an A4 document on US Letter - scaling and aligning the content. Links, bookmarks, form fields, attachments and the document information go with the pages.

### Resizing a document

```csharp
using ExpertPdf.Anywhere.PdfDocument;
using ExpertPdf.Anywhere.PDFResize;

PdfResizeManager resizer = new PdfResizeManager();
resizer.Load("report-a4.pdf");

resizer.PageSize = PageSize.Letter;
resizer.PageOrientation = ExpertPdf.Anywhere.HtmlToPdf.PDFPageOrientation.Portrait;
resizer.PageMargins = new Margins(36);
resizer.AllowScale = true;
resizer.KeepAspectRatio = true;
resizer.HorizontalAlign = HorizontalAlign.Center;
resizer.VerticalAlign = VerticalAlign.Top;

resizer.Save("report-letter.pdf");
resizer.Close();
```

```vbnet
Imports ExpertPdf.Anywhere.PdfDocument
Imports ExpertPdf.Anywhere.PDFResize

Dim resizer As New PdfResizeManager()
resizer.Load("report-a4.pdf")

resizer.PageSize = PageSize.Letter
resizer.PageOrientation = ExpertPdf.Anywhere.HtmlToPdf.PDFPageOrientation.Portrait
resizer.PageMargins = New Margins(36)
resizer.AllowScale = True
resizer.KeepAspectRatio = True
resizer.HorizontalAlign = HorizontalAlign.Center
resizer.VerticalAlign = VerticalAlign.Top

resizer.Save("report-letter.pdf")
resizer.Close()
```

### Options

- `PdfResizeManager.KeepOriginalPageSettings` keeps the size, orientation and rotation of each page and only applies the margins.
- `PdfResizeManager.StartPosition` and `PdfResizeManager.ContentSize` set the position and size of the content on the new page.
- `PdfResizeManager.ViewerPreferences` and `PdfResizeManager.Security` start as the document's own and can be changed.
- A PDF/A, PDF/UA or PDF/X document is resized into a document of the same standard.

## PDF portfolios

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfPortfolio.htm

A PDF portfolio is a PDF file that holds other files - PDF documents, images, office documents, anything - and that PDF viewers show as a list of files. Create and read portfolios with `PdfPortfolioManager`.

### Creating a portfolio

```csharp
using ExpertPdf.Anywhere.PDFPortfolio;

PdfPortfolioManager portfolio = new PdfPortfolioManager();

PortfolioFile contract = new PortfolioFile("contract.pdf");
contract.Description = "The signed contract";
portfolio.Files.Add(contract);
portfolio.Files.Add(new PortfolioFile("prices.xlsx"));

// how the viewer lists the files, and the file it opens first
portfolio.ViewMode = PortfolioViewMode.Details;
portfolio.StartupFileName = "contract.pdf";

portfolio.Save("package.pdf");
portfolio.Close();
```

```vbnet
Imports ExpertPdf.Anywhere.PDFPortfolio

Dim portfolio As New PdfPortfolioManager()

Dim contract As New PortfolioFile("contract.pdf")
contract.Description = "The signed contract"
portfolio.Files.Add(contract)
portfolio.Files.Add(New PortfolioFile("prices.xlsx"))

' how the viewer lists the files, and the file it opens first
portfolio.ViewMode = PortfolioViewMode.Details
portfolio.StartupFileName = "contract.pdf"

portfolio.Save("package.pdf")
portfolio.Close()
```

A file can also come from bytes or a stream, with the name it has in the portfolio: *new PortfolioFile("data.csv", bytes)*.

### Reading a portfolio

```csharp
PdfPortfolioManager portfolio = new PdfPortfolioManager();
portfolio.Load("package.pdf");
for (int i = 0; i < portfolio.Files.Count; i++)
{
    PortfolioFile file = portfolio.Files[i];
    File.WriteAllBytes(Path.Combine("extracted", file.Name), file.Content);
}
portfolio.Close();
```

```vbnet
Dim portfolio As New PdfPortfolioManager()
portfolio.Load("package.pdf")
For i As Integer = 0 To portfolio.Files.Count - 1
    Dim file As PortfolioFile = portfolio.Files(i)
    System.IO.File.WriteAllBytes(Path.Combine("extracted", file.Name), file.Content)
Next
portfolio.Close()
```

## Reading and changing page objects

Online: https://docs.html-to-pdf.net/anywhere-library/html/PageObjects.htm

ExpertPdf reads and changes what is drawn on the pages of an existing PDF document through `PdfPage.ObjectModel`: every text, image, path and group of a page, in the order it is painted, with its position, colors and opacity. Objects can be moved, rotated, resized, removed, duplicated, brought to the front or sent to the back, recolored, and text can be replaced. Changes are written when the document is saved.

Coordinates are in points with the origin at the top-left corner of the page as it is displayed (after its rotation). Each change is applied to the page at once; objects obtained earlier keep pointing at the same content after changes made through the model.

A text object holds the text drawn by one text instruction of the page. Documents created with the library keep each line or paragraph together, while documents converted from HTML, and many others, usually draw each character separately, so a word spans several text objects there.

### Page Objects

- `TextObject` - the text, the font name and size, whether the font is embedded and how the glyphs are painted; `ReplaceText` writes new text from the same starting point.
- `ImageObject` - the pixel size; `GetImageBytes` returns the picture as PNG.
- `PathObject` - lines, rectangles and curves: filled or outlined, the line width and the points.
- `FormObject` - a group of objects drawn together.

The object model is available for the pages of a loaded document.

### Sample Code

```csharp
Document document = new Document("in.pdf");
PageObjectModel model = document.Pages[0].ObjectModel;

foreach (TextObject text in model.GetTextObjects())
    Console.WriteLine(text.Text + " at " + text.Bounds);

TextObject title = model.GetTextObjects()[0];
title.ReplaceText("Annual Report 2026");
title.FillColor = new PdfColor(0, 70, 140);
model.GetImageObjects()[0].Translate(0, 20);

document.Save("out.pdf");
document.Close();
```

```vbnet
Dim document As New Document("in.pdf")
Dim model As PageObjectModel = document.Pages(0).ObjectModel

For Each text As TextObject In model.GetTextObjects()
    Console.WriteLine(text.Text & " at " & text.Bounds.ToString())
Next

Dim title As TextObject = model.GetTextObjects()(0)
title.ReplaceText("Annual Report 2026")
title.FillColor = New PdfColor(0, 70, 140)
model.GetImageObjects()(0).Translate(0, 20)

document.Save("out.pdf")
document.Close()
```

## PDF layers

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfLayers.htm

Layers (optional content) are parts of a PDF document a viewer can show or hide: watermarks, translations, draft marks, the levels of a technical drawing. ExpertPdf reads and changes the layers of a loaded document through `Document.Layers`.

### Layer Operations

- List the layers in a viewer's order; the layers nested under one are in its `Layers`.
- Show or hide a layer when the document opens (`Visible`), lock it (`Locked`), rename it (`Name`).
- Add a layer, and move page objects into it with their `Layer` property.
- Remove a layer with what it draws, or keep its content as plain, always visible page content; `Flatten` does that for every layer.

### Sample Code

```csharp
Document document = new Document("drawing.pdf");
foreach (Layer layer in document.Layers)
    Console.WriteLine(layer.Name + (layer.Visible ? "" : " (hidden)"));

document.Layers["Dimensions"].Visible = false;
document.Layers.Remove(document.Layers["Draft"], true);

document.Save("out.pdf");
document.Close();
```

```vbnet
Dim document As New Document("drawing.pdf")
For Each layer As Layer In document.Layers
    Console.WriteLine(layer.Name & If(layer.Visible, "", " (hidden)"))
Next

document.Layers("Dimensions").Visible = False
document.Layers.Remove(document.Layers("Draft"), True)

document.Save("out.pdf")
document.Close()
```
