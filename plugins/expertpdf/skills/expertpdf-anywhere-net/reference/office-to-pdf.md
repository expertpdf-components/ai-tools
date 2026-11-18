# Word, RTF, Excel and PowerPoint to PDF

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Word to PDF

Online: https://docs.html-to-pdf.net/anywhere-library/html/WordToPdf.htm

The Word to PDF converter, `PdfConverter`, converts Word documents to PDF without Microsoft Word: .docx, .docm, .dotx, .dotm, .doc, .dot, RTF, WordML, plain text, and also HTML and Markdown read as Word documents. The document is laid out by a Word layout engine in the library, on Windows, Linux and macOS.

### Converting a document

```csharp
using ExpertPdf.Anywhere.WordToPdf;

PdfConverter converter = new PdfConverter();
// converter.LicenseKey = "your license key";

// page numbers in the footer
converter.PdfDocumentOptions.ShowFooter = true;
converter.PdfFooterOptions.ShowPageNumber = true;

converter.SavePdfFromWordFileToFile("report.docx", "report.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere.WordToPdf

Dim converter As New PdfConverter()
' converter.LicenseKey = "your license key"

' page numbers in the footer
converter.PdfDocumentOptions.ShowFooter = True
converter.PdfFooterOptions.ShowPageNumber = True

converter.SavePdfFromWordFileToFile("report.docx", "report.pdf")
```

The document can come from a file, a stream or bytes (GetPdfBytesFromWordFile, GetPdfBytesFromWordStream, GetPdfBytesFromWordBytes, and the SavePdfFrom... methods). The format is found from the file extension or from the content; pass a `WordFormat` to set it. A password protected document needs `PdfConverter.Password`.

### Page size

By default (`PdfConverter.PageSetup` = WordPageSetup.Document) the PDF pages take the page sizes and margins of the Word document, a different size per section included. With WordPageSetup.Options the document is laid out again on the page size, orientation and margins of `PdfConverter.PdfDocumentOptions`:

```csharp
PdfConverter converter = new PdfConverter();
converter.PageSetup = WordPageSetup.Options;
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4;
converter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Landscape;
converter.PdfDocumentOptions.LeftMargin = 36;
converter.PdfDocumentOptions.RightMargin = 36;
byte[] pdf = converter.GetPdfBytesFromWordFile("report.docx");
```

```vbnet
Dim converter As New PdfConverter()
converter.PageSetup = WordPageSetup.Options
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4
converter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Landscape
converter.PdfDocumentOptions.LeftMargin = 36
converter.PdfDocumentOptions.RightMargin = 36
Dim pdf As Byte() = converter.GetPdfBytesFromWordFile("report.docx")
```

### Other options

- `PdfConverter.Bookmarks`: the PDF outline from the headings (the default), from the bookmarks of the document, or none.
- `PdfConverter.PageRanges`: convert only some pages, for example "1-3,5".
- `PdfConverter.UpdateFields`: recalculate every field before the conversion. When false (the default), the fields Word refreshes when it saves a PDF (dates and times, page numbers and counts, cross-references) are updated and the others keep the result stored in the document, as Word shows them.
- `PdfConverter.PreserveFormFields`: keep the form fields of the document as PDF form fields.
- `PdfConverter.PdfStandardSubset` and `PdfConverter.AccessibilityStandard`: PDF/A, PDF/X and tagged PDF/UA output; set `PdfConverter.Language` and the title for an accessible document.
- The header and footer options (title, footer text, page numbers, a Word document as header or footer with WordToPdfArea), the security options and the document information work as they do for the HTML to PDF converter.

A font the document uses and the host does not have is replaced by a font with the same metrics; on Linux and macOS hosts without fonts, add the ExpertPdf.Anywhere.Fonts package.

## Word to PDF element

Online: https://docs.html-to-pdf.net/anywhere-library/html/WordToPdfElement.htm

`WordToPdfElement` draws a Word document - .docx, .doc, RTF and the other formats of the Word to PDF converter - at a position on a PDF page, like "HTML to PDF element" (pdf-creation-elements.md) does for HTML. A document longer than the page continues on the following pages.

### Adding a Word document to a page

```csharp
using ExpertPdf.Anywhere.PdfDocument;

Document document = new Document();
PdfPage page = document.AddPage();
PdfFont font = document.AddFont(StdFontBaseFamily.HelveticaBold);
page.AddElement(new TextElement(0, 0, "Quarterly report", font));

// the Word document below the title, the full width of the page, as long as it
// needs to be (it continues on new pages)
WordToPdfElement word = new WordToPdfElement(0, 30, "report.docx");
page.AddElement(word);

document.Save("report.pdf");
document.Close();
```

```vbnet
Imports ExpertPdf.Anywhere.PdfDocument

Dim document As New Document()
Dim page As PdfPage = document.AddPage()
Dim font As PdfFont = document.AddFont(StdFontBaseFamily.HelveticaBold)
page.AddElement(New TextElement(0, 0, "Quarterly report", font))

' the Word document below the title, the full width of the page, as long as it
' needs to be (it continues on new pages)
Dim word As New WordToPdfElement(0, 30, "report.docx")
page.AddElement(word)

document.Save("report.pdf")
document.Close()
```

### Options

- The constructors take the position, optionally a width and a height, and a file path or a stream. With a height, only that much of the document is drawn.
- `WordToPdfElement.Format`, `WordToPdfElement.Password`, `WordToPdfElement.PageRanges`, `WordToPdfElement.Bookmarks`: as on the Word to PDF converter.
- `WordToPdfElement.LiveUrlsEnabled` and `WordToPdfElement.InternalLinksEnabled`: keep the links of the document.
- `WordToPdfElement.PdfPagesRectangles`: after the element is added, where it was drawn on each page.

A Word document can also be the header or footer of a Word to PDF conversion: set *WordToPdfArea* on the converter's header or footer options.

## RTF to PDF

Online: https://docs.html-to-pdf.net/anywhere-library/html/RtfToPdf.htm

The RTF to PDF converter of ExpertPdf Anywhere converts RTF (Rich Text Format) documents to PDF, on Windows, Linux and macOS. The RTF document comes from a file or from a string - for example the text of a rich text editor, a database field or a template filled in code - and the PDF document is saved to a file, written to a stream or returned in memory.

The main class is `PdfConverter`, in the **ExpertPdf.Anywhere.RtfToPdf** namespace. The RTF document is laid out by the Word engine of the library, in managed code, directly on the PDF pages: no browser engine and no native tool is involved. For Word documents (.docx, .doc) use the converter in the **ExpertPdf.Anywhere.WordToPdf** namespace (`PdfConverter`), which has the same shape.

### Features

- Convert RTF files and RTF strings to PDF files, streams or byte arrays.
- Page size (A0 to A10, B0 to B5, Letter, Legal and more), portrait or landscape orientation, margins.
- A header with a title, a subtitle and a line; a footer with a text, page numbers and a line; the colors of their texts.
- Embedded fonts (always subset-embedded), compression levels and a maximum image resolution for smaller files.
- Append existing PDF documents (files or streams) at the end of the result.
- Document information: title, author, subject, keywords, creation date.
- Passwords and permissions, with AES encryption (128 or 256 bit keys) or RC4 (40 bit key).
- PDF/A-1a, 1b, 2a, 2b, 2u, 3a, 3b, 3u, 4, 4e and 4f documents for long term archiving, and PDF/X-1a.
- Accessible, tagged PDF/UA-1 and PDF/UA-2 documents, built from the structure of the RTF document.

### Installation

The converter is part of the **ExpertPdf.Anywhere** package; there is no separate package to install. See "Installation" (installation-and-deployment.md) for the packages and "Requirements" (installation-and-deployment.md) for the supported platforms.

```text
dotnet add package ExpertPdf.Anywhere
```

The text is drawn with the fonts installed on the computer. When a font the document names is missing, a bundled Liberation font with the same metrics is used for Latin text. On a Linux or macOS host without fonts (a Docker image, for example), add the **ExpertPdf.Anywhere.Fonts** package for the other scripts.

### Getting started

Create a `PdfConverter`, set the options and call one of the conversion methods. This code converts an RTF file to an A4 PDF document with a header and a footer:

```csharp
using ExpertPdf.Anywhere.Drawing;
using ExpertPdf.Anywhere.RtfToPdf;

PdfConverter converter = new PdfConverter();

// the PDF page; the margins are in points (1/72 inch)
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4;
converter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Portrait;
converter.PdfDocumentOptions.LeftMargin = 36;
converter.PdfDocumentOptions.RightMargin = 36;
converter.PdfDocumentOptions.TopMargin = 20;
converter.PdfDocumentOptions.BottomMargin = 20;

// a header with a title and a subtitle
converter.PdfDocumentOptions.ShowHeader = true;
converter.PdfHeaderOptions.HeaderText = "Quarterly Sales Report";
converter.PdfHeaderOptions.HeaderSubtitleText = "Sales Analysis team";
converter.PdfHeaderOptions.HeaderTextColor = Color.Navy;

// the footer is shown by default: a text and the page numbers ("Page 1 / 3")
converter.PdfFooterOptions.FooterText = "Internal use";
converter.PdfFooterOptions.PageNumberText = "Page";

converter.SavePdfFromRtfFileToFile("report.rtf", "report.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere.Drawing
Imports ExpertPdf.Anywhere.RtfToPdf

Dim converter As New PdfConverter()

' the PDF page; the margins are in points (1/72 inch)
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4
converter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Portrait
converter.PdfDocumentOptions.LeftMargin = 36
converter.PdfDocumentOptions.RightMargin = 36
converter.PdfDocumentOptions.TopMargin = 20
converter.PdfDocumentOptions.BottomMargin = 20

' a header with a title and a subtitle
converter.PdfDocumentOptions.ShowHeader = True
converter.PdfHeaderOptions.HeaderText = "Quarterly Sales Report"
converter.PdfHeaderOptions.HeaderSubtitleText = "Sales Analysis team"
converter.PdfHeaderOptions.HeaderTextColor = Color.Navy

' the footer is shown by default: a text and the page numbers ("Page 1 / 3")
converter.PdfFooterOptions.FooterText = "Internal use"
converter.PdfFooterOptions.PageNumberText = "Page"

converter.SavePdfFromRtfFileToFile("report.rtf", "report.pdf")
```

The conversion methods:

- `PdfConverter.SavePdfFromRtfFileToFile(string, string)`, `PdfConverter.SavePdfFromRtfFileToStream(string, Stream)`, `PdfConverter.GetPdfBytesFromRtfFile(string)` - from an RTF file
- `PdfConverter.SavePdfFromRtfStringToFile(string, string)`, `PdfConverter.SavePdfFromRtfStringToStream(string, Stream)`, `PdfConverter.GetPdfBytesFromRtfString(string)` - from an RTF string

A conversion problem - for example an RTF document that cannot be converted, or an output file that cannot be written - is reported with a `RtfConvertException`. The methods can also throw the usual file exceptions, for example when the RTF file does not exist.

### Converting in memory and in a web application

An RTF document that is already in memory does not need a temporary file. This ASP.NET Core action converts an uploaded RTF file and returns the PDF document to the browser:

```csharp
using Microsoft.AspNetCore.Http;
using ExpertPdf.Anywhere.RtfToPdf;

public IActionResult ConvertRtf(IFormFile rtfUpload)
{
    string rtf;
    using (StreamReader reader = new StreamReader(rtfUpload.OpenReadStream()))
        rtf = reader.ReadToEnd();

    PdfConverter converter = new PdfConverter();
    byte[] pdfBytes = converter.GetPdfBytesFromRtfString(rtf);

    return File(pdfBytes, "application/pdf", "document.pdf");
}
```

```vbnet
Imports Microsoft.AspNetCore.Http
Imports ExpertPdf.Anywhere.RtfToPdf

Public Function ConvertRtf(rtfUpload As IFormFile) As IActionResult
    Dim rtf As String
    Using reader As New StreamReader(rtfUpload.OpenReadStream())
        rtf = reader.ReadToEnd()
    End Using

    Dim converter As New PdfConverter()
    Dim pdfBytes As Byte() = converter.GetPdfBytesFromRtfString(rtf)

    Return File(pdfBytes, "application/pdf", "document.pdf")
End Function
```

The content of a rich text editor control is converted the same way, from the RTF text the control gives.

### Page layout

The RTF document is laid out on the PDF pages themselves: the page size and orientation of `PdfConverter.PdfDocumentOptions`, inside the margins, with the space of the header and the footer taken off each page. The text wraps and breaks between pages as in a word processor, and the fonts the document uses are subset-embedded.

- `PdfDocumentOptions.PdfCompressionLevel` sets the compression of the content.
- `PdfDocumentOptions.ImagesMaxResolution` resamples images drawn at a higher resolution than this, in pixels per inch (0, the default, keeps every image as it is; 150 suits screen reading, 300 print).

### Appending PDF documents

The converter can add existing PDF documents at the end of the converted document, for example terms and conditions after an offer: set `PdfDocumentOptions.AppendPDFFile` or `PdfDocumentOptions.AppendPDFStream` for one document, `PdfDocumentOptions.AppendPDFFileArray` or `PdfDocumentOptions.AppendPDFStreamArray` for several.

```csharp
PdfConverter converter = new PdfConverter();
converter.PdfDocumentOptions.AppendPDFFile = "terms.pdf";

using (FileStream output = new FileStream("offer.pdf", FileMode.Create))
    converter.SavePdfFromRtfFileToStream("offer.rtf", output);
```

```vbnet
Dim converter As New PdfConverter()
converter.PdfDocumentOptions.AppendPDFFile = "terms.pdf"

Using output As New FileStream("offer.pdf", FileMode.Create)
    converter.SavePdfFromRtfFileToStream("offer.rtf", output)
End Using
```

### Document information, security and PDF/A

`PdfConverter.PdfDocumentInfo` sets the title, author, subject, keywords and creation date that PDF viewers show in the document properties.

`PdfConverter.PdfSecurityOptions` protects the document: a user password to open it, an owner password that gives all the rights, and the permissions (print, copy the content, copy for accessibility, edit the content, edit annotations, fill form fields, assemble the document). A 128 bit key (the default) gives AES-128, a 256 bit key AES-256 and a 40 bit key RC4; `PdfSecurityOptions.EncryptMetadata` set to false leaves the XMP metadata readable. The document is encrypted only when one of these options is changed from its default.

`PdfConverter.PdfStandardSubset` produces a PDF/A document for long term archiving (PDF/A-1a to PDF/A-4f) or a PDF/X-1a document for print exchange. The archiving levels embed the fonts; PDF/A-1 and PDF/X-1a also remove transparency, and the level A subsets tag the document. A PDF/A or PDF/X document cannot be encrypted (setting PdfSecurityOptions too throws an RtfConvertException), and the documents appended to it must conform to the same level (see "PDF standards: PDF/A and PDF/X" (accessibility-and-compliance.md)).

`PdfConverter.AccessibilityStandard` declares PDF/UA-1 or PDF/UA-2: the document is tagged from the structure of the RTF document (headings, paragraphs, lists, tables, pictures). Set `PdfConverter.Language` and the title too; both standards require them. PDF/UA-2 writes the document as PDF 2.0, so it combines only with PDF/A-4 or no archiving level (see "Tagged PDF and PDF/UA" (accessibility-and-compliance.md)).

```csharp
PdfConverter converter = new PdfConverter();
converter.PdfDocumentInfo.Title = "Quarterly report";
converter.PdfDocumentInfo.AuthorName = "Sales team";
converter.PdfDocumentInfo.Subject = "Sales by region";
converter.PdfDocumentInfo.Keywords = "sales, quarterly";

// a protected document: "open" opens it without printing or copying, "owner" gives
// all the rights
converter.PdfSecurityOptions.UserPassword = "open";
converter.PdfSecurityOptions.OwnerPassword = "owner";
converter.PdfSecurityOptions.CanPrint = false;
converter.PdfSecurityOptions.CanCopyContent = false;
converter.SavePdfFromRtfFileToFile("report.rtf", "protected.pdf");

// an accessible PDF/A-2a document for archiving
PdfConverter archive = new PdfConverter();
archive.PdfDocumentInfo.Title = "Quarterly report";
archive.Language = "en-US";
archive.PdfStandardSubset = PdfStandardSubset.Pdf_A_2a;
archive.AccessibilityStandard = ExpertPdf.Anywhere.HtmlToPdf.PdfUAStandard.Pdf_UA_1;
archive.SavePdfFromRtfFileToFile("report.rtf", "archive.pdf");
```

```vbnet
Dim converter As New PdfConverter()
converter.PdfDocumentInfo.Title = "Quarterly report"
converter.PdfDocumentInfo.AuthorName = "Sales team"
converter.PdfDocumentInfo.Subject = "Sales by region"
converter.PdfDocumentInfo.Keywords = "sales, quarterly"

' a protected document: "open" opens it without printing or copying, "owner" gives
' all the rights
converter.PdfSecurityOptions.UserPassword = "open"
converter.PdfSecurityOptions.OwnerPassword = "owner"
converter.PdfSecurityOptions.CanPrint = False
converter.PdfSecurityOptions.CanCopyContent = False
converter.SavePdfFromRtfFileToFile("report.rtf", "protected.pdf")

' an accessible PDF/A-2a document for archiving
Dim archive As New PdfConverter()
archive.PdfDocumentInfo.Title = "Quarterly report"
archive.Language = "en-US"
archive.PdfStandardSubset = PdfStandardSubset.Pdf_A_2a
archive.AccessibilityStandard = ExpertPdf.Anywhere.HtmlToPdf.PdfUAStandard.Pdf_UA_1
archive.SavePdfFromRtfFileToFile("report.rtf", "archive.pdf")
```

### Samples

The ConvertRtfToPdf page of the web sample (for Windows, Linux and macOS) converts the sample.rtf document that comes with it, a report with formatted text, lists, tables and pictures, with the page size, orientation, margins, a footer text and page numbers you choose.

### Licensing

Set `LicensingManager.LicenseKey` once when the application starts, or the `PdfConverter.LicenseKey` property before you call a conversion method. Without a license key the PDF documents have an evaluation notice. See "License key" (installation-and-deployment.md).

## Excel to PDF

Online: https://docs.html-to-pdf.net/anywhere-library/html/ExcelToPdf.htm

The Excel to PDF converter, `PdfConverter`, converts Excel workbooks to PDF without Microsoft Excel: .xlsx, .xlsm, .xltx, .xltm, .xls, .xlt, comma and tab separated values, the Excel 2003 XML spreadsheet and Markdown, whose tables become worksheets. The workbook is laid out by a spreadsheet engine in the library, on Windows, Linux and macOS.

### Converting a workbook

```csharp
using ExpertPdf.Anywhere.ExcelToPdf;

PdfConverter converter = new PdfConverter();
// converter.LicenseKey = "your license key";

// page numbers in the footer
converter.PdfDocumentOptions.ShowFooter = true;
converter.PdfFooterOptions.ShowPageNumber = true;

converter.SavePdfFromExcelFileToFile("sales.xlsx", "sales.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere.ExcelToPdf

Dim converter As New PdfConverter()
' converter.LicenseKey = "your license key"

' page numbers in the footer
converter.PdfDocumentOptions.ShowFooter = True
converter.PdfFooterOptions.ShowPageNumber = True

converter.SavePdfFromExcelFileToFile("sales.xlsx", "sales.pdf")
```

The workbook can come from a file, a stream or bytes (GetPdfBytesFromExcelFile, GetPdfBytesFromExcelStream, GetPdfBytesFromExcelBytes, and the SavePdfFrom... methods). The format is found from the file extension or from the content; pass an `ExcelFormat` to set it - tab separated values and Markdown given as a stream must name it. A password protected workbook needs `PdfConverter.Password`. A workbook that cannot be read fails with `ExcelConvertException`.

Every visible sheet is converted, in workbook order, as Excel prints it: its print area, its page breaks and its own printed header and footer. Each sheet becomes a bookmark of the PDF (`PdfConverter.SheetBookmarks`), and `PdfConverter.Worksheets` converts only the sheets it names, for example "Summary, Q1".

### Page size and scaling

By default (`PdfConverter.PageSetup` = ExcelPageSetup.Document) every sheet is printed on the paper size, orientation and margins of its own page setup. With ExcelPageSetup.Options the sheets are laid out on the page size, orientation and margins of `PdfConverter.PdfDocumentOptions`. `PdfConverter.Layout` scales the content of each sheet - the whole sheet, all its columns or all its rows on one page - and `PdfConverter.GridLines` draws or hides the cell gridlines:

```csharp
PdfConverter converter = new PdfConverter();
converter.PageSetup = ExcelPageSetup.Options;
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4;
converter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Landscape;
converter.PdfDocumentOptions.LeftMargin = 36;
converter.PdfDocumentOptions.RightMargin = 36;
converter.Layout = ExcelLayout.FitAllColumnsOnOnePage;
converter.GridLines = ExcelGridLines.Visible;
byte[] pdf = converter.GetPdfBytesFromExcelFile("sales.xlsx");
```

```vbnet
Dim converter As New PdfConverter()
converter.PageSetup = ExcelPageSetup.Options
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4
converter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Landscape
converter.PdfDocumentOptions.LeftMargin = 36
converter.PdfDocumentOptions.RightMargin = 36
converter.Layout = ExcelLayout.FitAllColumnsOnOnePage
converter.GridLines = ExcelGridLines.Visible
Dim pdf As Byte() = converter.GetPdfBytesFromExcelFile("sales.xlsx")
```

### Other options

- `PdfConverter.SheetHeadersAndFooters`: draw the headers and footers set in the sheets' page setup (the default).
- `PdfConverter.ConvertBlankSheets` and `PdfConverter.ConvertBlankPages`: keep empty sheets and empty pages.
- `PdfConverter.PageRanges`: convert only some pages, for example "1-3,5".
- `PdfConverter.PreserveFormFields`: keep the form controls of the sheets as PDF form fields.
- `PdfConverter.AutoDetectComplexScript`: shape Arabic, Hebrew, Thai and other complex scripts the workbook does not mark.
- `PdfConverter.PdfStandardSubset` and `PdfConverter.AccessibilityStandard`: PDF/A, PDF/X and tagged PDF/UA output - the sheets' tables with their header cells, charts and pictures with their alternative text, and links; set `PdfConverter.Language` and the title for an accessible document.
- The header and footer options (title, footer text, page numbers, a Word document as header or footer with WordToPdfArea), the security options and the document information work as they do for the HTML to PDF converter.

A font the workbook uses and the host does not have is replaced by a font with the same metrics; on Linux and macOS hosts without fonts, add the ExpertPdf.Anywhere.Fonts package.

## Excel to PDF element

Online: https://docs.html-to-pdf.net/anywhere-library/html/ExcelToPdfElement.htm

`ExcelToPdfElement` draws an Excel workbook - .xlsx, .xls, .csv and the other formats of the Excel to PDF converter - at a position on a PDF page, like "Word to PDF element" (office-to-pdf.md) does for a Word document. Every sheet is laid out on pages the size of the element, and a workbook longer than the page continues on the following pages.

### Adding an Excel workbook to a page

```csharp
using ExpertPdf.Anywhere.PdfDocument;

Document document = new Document();
PdfPage page = document.AddPage();
PdfFont font = document.AddFont(StdFontBaseFamily.HelveticaBold);
page.AddElement(new TextElement(0, 0, "Quarterly figures", font));

// the workbook below the title, the full width of the page, all the columns of
// each sheet on that width; it continues on new pages
ExcelToPdfElement sheet = new ExcelToPdfElement(0, 30, "sales.xlsx");
sheet.Layout = ExpertPdf.Anywhere.ExcelToPdf.ExcelLayout.FitAllColumnsOnOnePage;
page.AddElement(sheet);

document.Save("report.pdf");
document.Close();
```

```vbnet
Imports ExpertPdf.Anywhere.PdfDocument

Dim document As New Document()
Dim page As PdfPage = document.AddPage()
Dim font As PdfFont = document.AddFont(StdFontBaseFamily.HelveticaBold)
page.AddElement(New TextElement(0, 0, "Quarterly figures", font))

' the workbook below the title, the full width of the page, all the columns of
' each sheet on that width; it continues on new pages
Dim sheet As New ExcelToPdfElement(0, 30, "sales.xlsx")
sheet.Layout = ExpertPdf.Anywhere.ExcelToPdf.ExcelLayout.FitAllColumnsOnOnePage
page.AddElement(sheet)

document.Save("report.pdf")
document.Close()
```

### Options

- The constructors take the position, optionally a width and a height, and a file path or a stream. With a height, only that much of the workbook is drawn.
- `ExcelToPdfElement.Format`, `ExcelToPdfElement.Password`, `ExcelToPdfElement.Worksheets`, `ExcelToPdfElement.Layout`, `ExcelToPdfElement.GridLines`, `ExcelToPdfElement.PageRanges` and `ExcelToPdfElement.SheetBookmarks`: as on the Excel to PDF converter.
- `ExcelToPdfElement.LiveUrlsEnabled` and `ExcelToPdfElement.InternalLinksEnabled`: keep the links of the workbook.
- `ExcelToPdfElement.Tagged`: in a tagged document, the workbook's tables, charts, pictures and links join the structure of the document.
- `ExcelToPdfElement.PdfPagesRectangles`: after the element is added, where it was drawn on each page.

A workbook can also be drawn in the header or footer of an HTML to PDF conversion with `ExcelToPdfArea`; see "Headers and footers" (headers-footers.md).

## PowerPoint to PDF

Online: https://docs.html-to-pdf.net/anywhere-library/html/PowerPointToPdf.htm

The PowerPoint to PDF converter, `PdfConverter`, converts PowerPoint presentations (.pptx, .pptm, .potx, .potm) to PDF without Microsoft PowerPoint: one page per slide, the notes pages, or handouts with several slides on a page. The presentation is laid out by a presentation engine in the library, on Windows, Linux and macOS.

### Converting a presentation

```csharp
using ExpertPdf.Anywhere.PowerPointToPdf;

PdfConverter converter = new PdfConverter();
// converter.LicenseKey = "your license key";

converter.SavePdfFromPowerPointFileToFile("deck.pptx", "deck.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere.PowerPointToPdf

Dim converter As New PdfConverter()
' converter.LicenseKey = "your license key"

converter.SavePdfFromPowerPointFileToFile("deck.pptx", "deck.pdf")
```

The presentation can come from a file, a stream or bytes (GetPdfBytesFromPowerPointFile, GetPdfBytesFromPowerPointStream, GetPdfBytesFromPowerPointBytes, and the SavePdfFrom... methods). A password protected presentation needs `PdfConverter.Password`. A presentation that cannot be read fails with `PowerPointConvertException`.

### Slides, notes pages or handouts

`PdfConverter.Publish` chooses what is converted: PowerPointPublish.Slides (the default, one page per slide), PowerPointPublish.NotesPages (each slide with its speaker notes below it) or PowerPointPublish.Handouts (pages with `PdfConverter.SlidesPerHandout` slides each: 1, 2, 3, 4, 6 or 9). Hidden slides are left out unless `PdfConverter.ShowHiddenSlides` is set.

By default (`PdfConverter.PageSetup` = PowerPointPageSetup.Document) every page has the size of the slides. With PowerPointPageSetup.Options the slides are scaled onto the page size, orientation and margins of `PdfConverter.PdfDocumentOptions`:

```csharp
PdfConverter converter = new PdfConverter();
converter.Publish = PowerPointPublish.Handouts;
converter.SlidesPerHandout = 4;
converter.PageSetup = PowerPointPageSetup.Options;
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4;
converter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Portrait;
byte[] pdf = converter.GetPdfBytesFromPowerPointFile("deck.pptx");
```

```vbnet
Dim converter As New PdfConverter()
converter.Publish = PowerPointPublish.Handouts
converter.SlidesPerHandout = 4
converter.PageSetup = PowerPointPageSetup.Options
converter.PdfDocumentOptions.PdfPageSize = PdfPageSize.A4
converter.PdfDocumentOptions.PdfPageOrientation = PDFPageOrientation.Portrait
Dim pdf As Byte() = converter.GetPdfBytesFromPowerPointFile("deck.pptx")
```

### Other options

- `PdfConverter.PageRanges`: convert only some pages, for example "1-3,5".
- `PdfConverter.EmbedCompleteFonts`: embed whole fonts rather than the characters used; `PdfConverter.OptimizeIdenticalImages`: store an image used several times once.
- `PdfConverter.PreserveFormFields`: keep the form fields of the presentation as PDF form fields.
- `PdfConverter.PdfStandardSubset` and `PdfConverter.AccessibilityStandard`: PDF/A, PDF/X and tagged PDF/UA output - the slides' titles, text, tables, pictures with their descriptions and links; set `PdfConverter.Language` and the title for an accessible document. Notes pages and handouts are not produced as tagged documents: publish the slides for one.
- The header and footer options (title, footer text, page numbers, a Word document as header or footer with WordToPdfArea), the security options and the document information work as they do for the HTML to PDF converter.

A font the presentation uses and the host does not have is replaced by a font with the same metrics; on Linux and macOS hosts without fonts, add the ExpertPdf.Anywhere.Fonts package.

## PowerPoint to PDF element

Online: https://docs.html-to-pdf.net/anywhere-library/html/PowerPointToPdfElement.htm

`PowerPointToPdfElement` draws the slides of a PowerPoint presentation (.pptx, .pptm, .potx, .potm) at a position on a PDF page, like "Word to PDF element" (office-to-pdf.md) does for a Word document. Each slide - or notes page, or handout page - keeps its shape, scaled to the width of the element; the slides are stacked one under the other and continue on the following pages.

### Adding the slides of a presentation to a page

```csharp
using ExpertPdf.Anywhere.PdfDocument;

Document document = new Document();
PdfPage page = document.AddPage();
PdfFont font = document.AddFont(StdFontBaseFamily.HelveticaBold);
page.AddElement(new TextElement(0, 0, "Quarterly review", font));

// the slides below the title, 300 points wide, stacked; they continue on new pages
PowerPointToPdfElement slides = new PowerPointToPdfElement(0, 30, 300, "deck.pptx");
page.AddElement(slides);

document.Save("review.pdf");
document.Close();
```

```vbnet
Imports ExpertPdf.Anywhere.PdfDocument

Dim document As New Document()
Dim page As PdfPage = document.AddPage()
Dim font As PdfFont = document.AddFont(StdFontBaseFamily.HelveticaBold)
page.AddElement(New TextElement(0, 0, "Quarterly review", font))

' the slides below the title, 300 points wide, stacked; they continue on new pages
Dim slides As New PowerPointToPdfElement(0, 30, 300, "deck.pptx")
page.AddElement(slides)

document.Save("review.pdf")
document.Close()
```

### Options

- The constructors take the position, optionally a width and a height, and a file path or a stream. With a height, only that much of the presentation is drawn.
- `PowerPointToPdfElement.Publish`, `PowerPointToPdfElement.SlidesPerHandout`, `PowerPointToPdfElement.ShowHiddenSlides`, `PowerPointToPdfElement.Password` and `PowerPointToPdfElement.PageRanges`: as on the PowerPoint to PDF converter - "3" draws only the third slide.
- `PowerPointToPdfElement.LiveUrlsEnabled` and `PowerPointToPdfElement.InternalLinksEnabled`: keep the links of the presentation.
- `PowerPointToPdfElement.Tagged`: in a tagged document, the slides' titles, text, tables, pictures and links join the structure of the document.
- `PowerPointToPdfElement.PdfPagesRectangles`: after the element is added, where each slide was drawn.

A slide can also be drawn in the header or footer of an HTML to PDF conversion with `PowerPointToPdfArea`; see "Headers and footers" (headers-footers.md).
