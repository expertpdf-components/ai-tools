# PDF/A, PDF/X, tagged PDF and PDF/UA, ZUGFeRD / Factur-X invoices

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## PDF standards: PDF/A and PDF/X

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfStandard.htm

By default the converter generates standard PDF documents that every PDF reader opens. With the `PdfConverter.PdfStandardSubset` property it generates documents that conform to an ISO subset of PDF - for long term archiving (PDF/A), for printing (PDF/X) or for electronic invoices (PDF/A-3). Accessibility (PDF/UA) is a separate standard, declared with its own property and combined with an archiving level; see "Tagged PDF and PDF/UA" (accessibility-and-compliance.md).

### The PDF standards

| PdfStandardSubset | Standard | Use |
|---|---|---|
| Full | PDF | The default: every PDF feature can be used. |
| Pdf_A_1b | PDF/A-1b (ISO 19005-1) | Long term archiving, for systems that require PDF/A-1. No transparency: translucent content is painted opaque. |
| Pdf_A_1a | PDF/A-1a | PDF/A-1b with a tagged, accessible structure. The PDF/A-1 level that combines with PDF/UA-1. |
| Pdf_A_2b | PDF/A-2b (ISO 19005-2) | Long term archiving; transparency and layers allowed. The usual choice for archiving. |
| Pdf_A_2u | PDF/A-2u | PDF/A-2b with Unicode text that can be reliably extracted and searched. |
| Pdf_A_2a | PDF/A-2a | PDF/A-2u with a tagged, accessible structure. |
| Pdf_A_3b | PDF/A-3b (ISO 19005-3) | PDF/A-2b that can embed files of any type (associated files): electronic invoices, source data. |
| Pdf_A_3u | PDF/A-3u | PDF/A-3b with Unicode text that can be reliably extracted and searched. |
| Pdf_A_3a | PDF/A-3a | PDF/A-3u with a tagged, accessible structure. |
| Pdf_A_4 | PDF/A-4 (ISO 19005-4) | Long term archiving on PDF 2.0. The archiving level that combines with PDF/UA-2. |
| Pdf_A_4e | PDF/A-4e | The engineering variant of PDF/A-4 (embedded 3D and rich media). |
| Pdf_A_4f | PDF/A-4f | The variant of PDF/A-4 that allows embedded files of any kind. |
| Pdf_X_1a | PDF/X-1a:2001 (ISO 15930-1) | Graphics exchange for printing: CMYK colors, no transparency. |

A PDF/A document embeds all its fonts and carries the metadata that identifies it as PDF/A. The Chromium engine embeds every font a page uses, so a converted page needs no preparation. The level A subsets (Pdf_A_1a, Pdf_A_2a, Pdf_A_3a) need the tagged structure of the document: the converter turns tagging on by itself when one of them is set. A PDF/A-4 document carries no document information dictionary, as the standard requires; the title and the other properties are written in its XMP metadata.

A PDF/X-1a document is written with CMYK colors: the converter converts the colors of the rendered page, its images and its gradients, and paints translucent content opaque, since the standard allows neither RGB colors nor transparency.

PDF/A and PDF/X documents cannot be encrypted: the standards forbid it, so do not set a password in `PdfConverter.PdfSecurityOptions` for them.

### Generating a PDF/A document

```csharp
PdfConverter converter = new PdfConverter();
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_2b;
converter.PdfDocumentInfo.Title = "Annual report";
converter.SavePdfFromUrlToFile("https://www.example.com/report", "report-pdfa.pdf");
```

```vbnet
Dim converter As New PdfConverter()
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_2b
converter.PdfDocumentInfo.Title = "Annual report"
converter.SavePdfFromUrlToFile("https://www.example.com/report", "report-pdfa.pdf")
```

### PDF/A and PDF/UA together

Archiving and accessibility are separate ISO standards that combine: set the archiving level with `PdfConverter.PdfStandardSubset` and the accessibility standard with `PdfConverter.AccessibilityStandard`. Each PDF/UA part is defined on a version of PDF, which decides the archiving levels it combines with:

- **PDF/UA-1** (PDF 1.7) combines with PDF/A-1a, the PDF/A-2 and PDF/A-3 levels and PDF/X-1a. With PDF/A-1 use Pdf_A_1a: PDF/A-1b cannot carry the structure PDF/UA needs.
- **PDF/UA-2** (PDF 2.0) combines with PDF/A-4, PDF/A-4e and PDF/A-4f.

A combination that cannot be produced, such as PDF/UA-2 with PDF/A-3 or PDF/UA-1 with PDF/A-4, is refused with an exception rather than written as a document that conforms to neither.

```csharp
PdfConverter converter = new PdfConverter();
// archived and accessible: PDF/A-4 with PDF/UA-2
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_4;
converter.AccessibilityStandard = PdfUAStandard.Pdf_UA_2;
// an accessible document needs a title and a language
converter.PdfDocumentInfo.Title = "Annual report";
converter.Language = "en-US";
converter.SavePdfFromUrlToFile("https://www.example.com/report", "report.pdf");
```

```vbnet
Dim converter As New PdfConverter()
' archived and accessible: PDF/A-4 with PDF/UA-2
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_4
converter.AccessibilityStandard = PdfUAStandard.Pdf_UA_2
' an accessible document needs a title and a language
converter.PdfDocumentInfo.Title = "Annual report"
converter.Language = "en-US"
converter.SavePdfFromUrlToFile("https://www.example.com/report", "report.pdf")
```

### Associated files (PDF/A-3)

A PDF/A-3 document can carry other files - the data behind a report, the source document, an XML invoice. Convert to a `Document` object, add the files with `AddAssociatedFile`, then save. The `AttachmentRelationship` says how the file relates to the document (Source, Data, Alternative, Supplement).

```csharp
PdfConverter converter = new PdfConverter();
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_3b;
using (Document document = converter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/sales-report"))
{
    // the data the report was made from, embedded as an associated file
    document.AddAssociatedFile("sales.csv", AttachmentRelationship.Data, "text/csv",
        "Sales figures");
    document.Save("sales-report.pdf");
}
```

```vbnet
Dim converter As New PdfConverter()
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_3b
Using document As Document = converter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/sales-report")
    ' the data the report was made from, embedded as an associated file
    document.AddAssociatedFile("sales.csv", AttachmentRelationship.Data, "text/csv",
        "Sales figures")
    document.Save("sales-report.pdf")
End Using
```

For electronic invoices, use AddZugferdInvoice, which also writes the invoice metadata the standards require.

### Documents built in code and existing documents

A document built in code takes its level from the `Document.ctor(PdfStandardSubset)` constructor. An existing PDF document loaded into a `Document` is converted with `Document.ConvertToPdfA(PdfStandardSubset)` to one of the PDF/A-1, PDF/A-2 or PDF/A-3 levels (a level A needs a document that is already tagged). A PDF/A document only takes appended documents that conform to its level; see "Prepending and appending documents" (html-to-pdf.md).

## Tagged PDF and PDF/UA

Online: https://docs.html-to-pdf.net/anywhere-library/html/TaggedPdf.htm

A tagged PDF carries the logical structure of the document - headings, paragraphs, lists, tables, figures with their alternate text, links, and the reading order - next to its visual content. Screen readers and other assistive technology use it, and it is required for accessible documents (PDF/UA, ISO 14289) and for the level A PDF/A subsets (PDF/A-1a, PDF/A-2a, PDF/A-3a).

ExpertPdf Anywhere produces both parts of the accessibility standard: **PDF/UA-1** (ISO 14289-1, on PDF 1.7) and **PDF/UA-2** (ISO 14289-2, written as a PDF 2.0 document). The standard is chosen with `PdfUAStandard`: *Pdf_UA_1* or *Pdf_UA_2*.

### Tagged PDF from HTML

Set `PdfConverter.AccessibilityStandard` to the PDF/UA part the document must declare; this turns tagging on. The structure comes from the HTML itself: write semantic HTML (h1-h6, p, ul/ol, table with th, img with alt, a with text) and the PDF gets the matching structure. Both standards require a document title and a language: `PdfConverter.Language` sets the language of the document (en-US when not set), and the title is `PdfDocumentInfo.Title`, or the title of the HTML page when that is not set.

```csharp
PdfConverter converter = new PdfConverter();
converter.AccessibilityStandard = PdfUAStandard.Pdf_UA_1;
converter.Language = "en-US";
converter.PdfDocumentInfo.Title = "Accessible report";
converter.SavePdfFromUrlToFile("https://www.example.com/report",
    "accessible-report.pdf");
```

```vbnet
Dim converter As New PdfConverter()
converter.AccessibilityStandard = PdfUAStandard.Pdf_UA_1
converter.Language = "en-US"
converter.PdfDocumentInfo.Title = "Accessible report"
converter.SavePdfFromUrlToFile("https://www.example.com/report",
    "accessible-report.pdf")
```

`PdfConverter.Tagged` set to true produces the same structure without choosing the part: the document then declares PDF/UA-1, or PDF/UA-2 at a PDF/A-4 level. The level A PDF/A subsets turn tagging on by themselves. Headers and footers are tagged as artifacts (decorative content that is not read).

An accessible document can also be archived: combine the PDF/UA part with a PDF/A level set in `PdfConverter.PdfStandardSubset`. PDF/UA-1 combines with PDF/A-1a, PDF/A-2 and PDF/A-3; PDF/UA-2 combines with PDF/A-4. See "PDF standards: PDF/A and PDF/X" (accessibility-and-compliance.md).

```csharp
PdfConverter converter = new PdfConverter();
// accessible and archived: PDF/A-3a with PDF/UA-1
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_3a;
converter.AccessibilityStandard = PdfUAStandard.Pdf_UA_1;
converter.Language = "en-US";
converter.PdfDocumentInfo.Title = "Accessible report";
converter.SavePdfFromUrlToFile("https://www.example.com/report",
    "accessible-report-pdfa.pdf");
```

```vbnet
Dim converter As New PdfConverter()
' accessible and archived: PDF/A-3a with PDF/UA-1
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_3a
converter.AccessibilityStandard = PdfUAStandard.Pdf_UA_1
converter.Language = "en-US"
converter.PdfDocumentInfo.Title = "Accessible report"
converter.SavePdfFromUrlToFile("https://www.example.com/report",
    "accessible-report-pdfa.pdf")
```

### Tagged documents built in code

When you build or extend a `Document` yourself, set `Document.Tagged` - or declare the standard with `Document.AccessibilityStandard`, which turns tagging on - before you add any content, set `Document.Language` and a title, and say how every element you add is tagged:

- `PageElement.TagType` - the structure type of real content (`PdfTagType`: Paragraph, Heading1, Figure, Table...).
- `PageElement.AlternateText` - the description of a figure, read instead of it.
- `PageElement.ActualText` - the text of content drawn as graphics.
- `PageElement.Artifact` - decorative content (rules, backgrounds, page numbers), left out of the structure.
- `Document.BeginTag(PdfTagType)` and `Document.EndTag` - group elements in containers (Section, List, Table...).

An element added to a tagged document without a TagType and not marked as an Artifact is refused, so nothing ends up outside the structure by mistake. Link elements and HTML elements tag themselves. A document that declares a standard is checked when it is saved: content that is neither tagged nor marked as an artifact makes the save fail rather than produce a document that claims a standard it does not meet. Embed the fonts (the second argument of AddFont), as PDF/UA requires.

```csharp
Document document = new Document();
document.AccessibilityStandard = PdfUAStandard.Pdf_UA_1;
document.Language = "en-US";
document.DocumentInformation.Title = "Tagged document";
document.ViewerPreferences.DisplayDocTitle = true;

PdfPage page = document.AddPage();
PdfFont titleFont = document.AddFont(
    new Font("Arial", 20, FontStyle.Bold), true);
PdfFont textFont = document.AddFont(new Font("Arial", 11), true);

document.BeginTag(PdfTagType.Section);

TextElement title = new TextElement(0, 0, "Quarterly results", titleFont);
title.TagType = PdfTagType.Heading1;
page.AddElement(title);

TextElement paragraph = new TextElement(0, 40, "Sales grew in every region.",
    textFont);
paragraph.TagType = PdfTagType.Paragraph;
page.AddElement(paragraph);

// a decorative rule: not part of the structure
RectangleElement rule = new RectangleElement(0, 30, 300, 1);
rule.Artifact = true;
page.AddElement(rule);

document.EndTag();
document.Save("tagged.pdf");
document.Close();
```

```vbnet
Dim document As New Document()
document.AccessibilityStandard = PdfUAStandard.Pdf_UA_1
document.Language = "en-US"
document.DocumentInformation.Title = "Tagged document"
document.ViewerPreferences.DisplayDocTitle = True

Dim page As PdfPage = document.AddPage()
Dim titleFont As PdfFont = document.AddFont(
    New Font("Arial", 20, FontStyle.Bold), True)
Dim textFont As PdfFont = document.AddFont(New Font("Arial", 11), True)

document.BeginTag(PdfTagType.Section)

Dim title As New TextElement(0, 0, "Quarterly results", titleFont)
title.TagType = PdfTagType.Heading1
page.AddElement(title)

Dim paragraph As New TextElement(0, 40, "Sales grew in every region.", textFont)
paragraph.TagType = PdfTagType.Paragraph
page.AddElement(paragraph)

' a decorative rule: not part of the structure
Dim rule As New RectangleElement(0, 30, 300, 1)
rule.Artifact = True
page.AddElement(rule)

document.EndTag()
document.Save("tagged.pdf")
document.Close()
```

## ZUGFeRD / Factur-X electronic invoices

Online: https://docs.html-to-pdf.net/anywhere-library/html/ZugferdInvoices.htm

ZUGFeRD (Germany) and Factur-X (France) are the same European standard for hybrid electronic invoices: a PDF/A-3 document that people read, with the invoice data embedded as XML (EN 16931, Cross Industry Invoice) that accounting software reads. Render the invoice from HTML, add the XML with `AddZugferdInvoice`, and save.

### Creating a ZUGFeRD / Factur-X invoice

```csharp
PdfConverter converter = new PdfConverter();
// a ZUGFeRD invoice is a PDF/A-3 document
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_3b;
converter.PdfDocumentInfo.Title = "Invoice INV-2026-0042";

// the readable invoice, rendered from your invoice page or template
using (Document document =
    converter.GetPdfDocumentObjectFromHtmlString(invoiceHtml))
{
    // the machine-readable invoice: the Factur-X / ZUGFeRD XML of the same invoice
    // (EN 16931 profile)
    document.AddZugferdInvoice("factur-x.xml", ZugferdProfile.En16931,
        AttachmentRelationship.Alternative);

    document.Save("INV-2026-0042.pdf");
}
```

```vbnet
Dim converter As New PdfConverter()
' a ZUGFeRD invoice is a PDF/A-3 document
converter.PdfStandardSubset = PdfStandardSubset.Pdf_A_3b
converter.PdfDocumentInfo.Title = "Invoice INV-2026-0042"

' the readable invoice, rendered from your invoice page or template
Using document As Document =
    converter.GetPdfDocumentObjectFromHtmlString(invoiceHtml)
    ' the machine-readable invoice: the Factur-X / ZUGFeRD XML of the same invoice
    ' (EN 16931 profile)
    document.AddZugferdInvoice("factur-x.xml", ZugferdProfile.En16931,
        AttachmentRelationship.Alternative)

    document.Save("INV-2026-0042.pdf")
End Using
```

The XML must describe the same invoice the PDF shows; ExpertPdf embeds it and writes the metadata, it does not create or validate the XML. Generate it with your accounting or invoicing software, or with a library for the Cross Industry Invoice format.

### Profiles, relationship and schema

| Parameter | Values |
|---|---|
| `ZugferdProfile` | The profile of the XML: Minimum, BasicWL, Basic, En16931 (the full European standard, the usual choice), Extended, XRechnung (the German public sector; the embedded file is named xrechnung.xml). |
| `AttachmentRelationship` | Alternative (the XML is another form of the invoice - the usual value), Data or Source. |
| `ZugferdSchema` (optional) | FacturX10 (the default: Factur-X 1.0 / ZUGFeRD 2.1+, file factur-x.xml) or Zugferd20 (ZUGFeRD 2.0, file zugferd-invoice.xml). |

The document must be PDF/A-3: Pdf_A_3b, Pdf_A_3u, or Pdf_A_3a for an invoice that is also accessible (tagging is turned on by that level; set `PdfConverter.AccessibilityStandard` to Pdf_UA_1 to declare PDF/UA as well - see "Tagged PDF and PDF/UA" (accessibility-and-compliance.md)). A document of another level is refused. An existing PDF invoice can be made one: load it into a Document, convert it with `Document.ConvertToPdfA(PdfStandardSubset)` to Pdf_A_3b, then add the XML. The XML can also be given as a stream; it is read when the method is called.
