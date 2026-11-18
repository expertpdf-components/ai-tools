# Redaction

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Redacting PDF documents

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfRedaction.htm

`PdfRedactionManager` removes content from an existing PDF document: the text, images, vector graphics, links, annotations and form fields inside the marked areas are deleted from the file - not hidden - and each area is painted over. Areas are marked by text, by regular expression or by rectangle.

### Redacting a document

```csharp
using ExpertPdf.Anywhere.Drawing;
using ExpertPdf.Anywhere.PDFRedaction;
using System.Text.RegularExpressions;

PdfRedactionManager manager = new PdfRedactionManager();
manager.Load("contract.pdf");

// every occurrence of a name, in any case, as a whole word
manager.AddRedaction("John Smith", false, true);
// every account number
manager.AddRedaction(new Regex(@"\b\d{4}-\d{4}-\d{4}\b"));
// an area of the first page, in points from its top-left corner
manager.AddRedaction(0, new RectangleF(40, 700, 200, 50));

manager.OverlayText = "REDACTED";
RedactionResult[] removed = manager.Redact();
manager.Save("contract-redacted.pdf");
manager.Close();
```

```vbnet
Imports ExpertPdf.Anywhere.Drawing
Imports ExpertPdf.Anywhere.PDFRedaction
Imports System.Text.RegularExpressions

Dim manager As New PdfRedactionManager()
manager.Load("contract.pdf")

' every occurrence of a name, in any case, as a whole word
manager.AddRedaction("John Smith", False, True)
' every account number
manager.AddRedaction(New Regex("\b\d{4}-\d{4}-\d{4}\b"))
' an area of the first page, in points from its top-left corner
manager.AddRedaction(0, New RectangleF(40, 700, 200, 50))

manager.OverlayText = "REDACTED"
Dim removed As RedactionResult() = manager.Redact()
manager.Save("contract-redacted.pdf")
manager.Close()
```

### What is removed

- `PdfRedactionManager.Redact` returns the areas it redacted, with their page and the text found there.
- A text redaction also removes the text from what the document keeps off its pages: the document information and metadata, bookmarks, annotations, form field values and attached files.
- Images are erased pixel by pixel inside the areas; text drawn at an angle and text in any script is found and removed.
- `PdfRedactionManager.FillColor` (black by default) paints the areas, and `PdfRedactionManager.OverlayText` writes a text in each of them.
- The document is written whole, so nothing removed survives in an earlier revision of the file. A signature it carried no longer verifies. An encrypted document stays encrypted with its passwords, and a tagged or PDF/A document keeps its structure and conformance.
