# PDF to Word, Excel and PowerPoint

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## PDF to Word, Excel and PowerPoint

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfToOffice.htm

ExpertPdf Anywhere converts PDF documents to Word, Excel and PowerPoint files on Windows, Linux and macOS: `PdfToWordConverter` writes an editable Word document (docx, or rtf, OpenDocument text, html and markdown), `PdfToExcelConverter` writes the tables of a document to an Excel workbook (xlsx) and `PdfToPowerPointConverter` writes a PowerPoint presentation (pptx) with a slide for each page.

The three converters are in the **ExpertPdf.Anywhere** namespace and run in the application's process: they need no native engine, only the **ExpertPdf.Anywhere** package. In demo mode (no license key) each converted file carries an evaluation line.

### PDF to Word

Every page is read as text, images and graphics. The text is grouped into paragraphs with their role (title, headings, list items, captions, footnotes, headers and footers) and read across its columns in reading order, and the document is written with Word's heading and list styles, tables, pictures, hyperlinks, bookmarks, page setup and the document's fonts. A scanned page without a text layer comes out as its picture.

```csharp
using ExpertPdf.Anywhere;

PdfToWordConverter converter = new PdfToWordConverter();
converter.LicenseKey = "your license key";

// Flowing (the default) for a document to edit,
// Exact for one that looks like the PDF page for page
converter.Layout = PdfToWordLayout.Flowing;

converter.ConvertToWordFile("report.pdf", "report.docx");

// the same document as rich text, held in memory
converter.OutputFormat = PdfToWordFormat.Rtf;
byte[] rtf = converter.ConvertToWord("report.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere

Dim converter As New PdfToWordConverter()
converter.LicenseKey = "your license key"

' Flowing (the default) for a document to edit,
' Exact for one that looks like the PDF page for page
converter.Layout = PdfToWordLayout.Flowing

converter.ConvertToWordFile("report.pdf", "report.docx")

' the same document as rich text, held in memory
converter.OutputFormat = PdfToWordFormat.Rtf
Dim rtf As Byte() = converter.ConvertToWord("report.pdf")
```

`PdfToWordConverter.Images`, `PdfToWordConverter.HeadersAndFooters`, `PdfToWordConverter.Links`, `PdfToWordConverter.Bookmarks` and `PdfToWordConverter.EmbedFonts` (all on by default) choose what the document carries.

### PDF to Excel

The tables of the pages are found from their rules or their aligned columns. Each table becomes cells: numbers, percentages, currency amounts and dates as typed values with a number format, text as text, header rows bold and frozen, merged cells merged, with the shading, borders and column widths of the PDF. `PdfToExcelConverter.Sheets` puts each table on a worksheet of its own (the default) or each page.

```csharp
PdfToExcelConverter converter = new PdfToExcelConverter();
converter.LicenseKey = "your license key";
converter.Sheets = PdfToExcelSheets.PerPage;
converter.Culture = "de-DE";   // read 1.234,50 as a number
converter.ConvertToExcelFile("statement.pdf", "statement.xlsx");
```

```vbnet
Dim converter As New PdfToExcelConverter()
converter.LicenseKey = "your license key"
converter.Sheets = PdfToExcelSheets.PerPage
converter.Culture = "de-DE"   ' read 1.234,50 as a number
converter.ConvertToExcelFile("statement.pdf", "statement.xlsx")
```

`PdfToExcelConverter.Images` keeps the document's pictures and `PdfToExcelConverter.TextOutsideTables` writes the text outside the tables on a worksheet of its own; both are on by default.

### PDF to PowerPoint

Every block of text becomes a text box at its place, with its paragraphs and runs in the document's fonts, sizes and colors; the page color becomes the slide's background, boxes and rules shapes, images and vector art pictures. The slides take the size of the first page.

```csharp
PdfToPowerPointConverter converter = new PdfToPowerPointConverter();
converter.LicenseKey = "your license key";
using (FileStream pdf = File.OpenRead("slides.pdf"))
    converter.ConvertToPowerPointFile(pdf, "slides.pptx");
```

```vbnet
Dim converter As New PdfToPowerPointConverter()
converter.LicenseKey = "your license key"
Using pdf As FileStream = File.OpenRead("slides.pdf")
    converter.ConvertToPowerPointFile(pdf, "slides.pptx")
End Using
```

### Pages, passwords and errors

- **StartPageNumber** and **EndPageNumber** - the pages to convert. The first page is 1; an end page of 0 means the last page of the document.
- **UserPassword** - the user or the owner password of a protected document.
- **LicenseKey** - the license key of this converter; when it is not set, the key set on `LicensingManager` is used. A key that does not license the product throws `LicenseException`.
- A document that cannot be read or converted throws `PdfToWordException`, `PdfToExcelException` or `PdfToPowerPointException`.
