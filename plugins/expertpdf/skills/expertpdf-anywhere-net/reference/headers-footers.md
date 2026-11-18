# Headers, footers and page numbering

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Headers and footers

Online: https://docs.html-to-pdf.net/anywhere-library/html/HeadersAndFooters.htm

The header and the footer are shown in the generated document when the `PdfDocumentOptions.ShowHeader` and `PdfDocumentOptions.ShowFooter` properties of the `PdfConverter.PdfDocumentOptions` object are true. For example, to add both a header and a footer to the generated document:

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.PdfDocumentOptions.ShowHeader = true;
pdfConverter.PdfDocumentOptions.ShowFooter = true;
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.PdfDocumentOptions.ShowHeader = True
pdfConverter.PdfDocumentOptions.ShowFooter = True
```

The content and the look of the header and the footer are set with the `PdfConverter.PdfHeaderOptions` and `PdfConverter.PdfFooterOptions` properties of the `PdfConverter` object. The HTML content is placed between the header and the footer: their heights are taken from the page.

### Predefined Header and Footer Elements

The header can have a title and a subtitle, an image at a given position and a line under the header; the font, size and color of the texts and the background color of the header can be changed. The image is given as the bytes of an image file (PNG, JPEG, BMP...):

```csharp
pdfConverter.PdfHeaderOptions.HeaderBackColor = Color.LightGray;
pdfConverter.PdfHeaderOptions.HeaderHeight = 50;
pdfConverter.PdfHeaderOptions.HeaderText = "Title";
pdfConverter.PdfHeaderOptions.HeaderTextColor = Color.Black;
pdfConverter.PdfHeaderOptions.HeaderTextFontType = PdfFontType.Helvetica;
pdfConverter.PdfHeaderOptions.HeaderTextFontSize = 18;
pdfConverter.PdfHeaderOptions.HeaderTextYLocation = 5;
pdfConverter.PdfHeaderOptions.HeaderSubtitleText = "Subtitle";
pdfConverter.PdfHeaderOptions.HeaderSubtitleTextColor = Color.Black;
pdfConverter.PdfHeaderOptions.HeaderSubtitleTextFontType = PdfFontType.Helvetica;
pdfConverter.PdfHeaderOptions.HeaderSubtitleTextFontSize = 12;
pdfConverter.PdfHeaderOptions.HeaderTitleSubtitleYSpacing = 7;
pdfConverter.PdfHeaderOptions.HeaderImageLocation = new PointF(0, 0);
pdfConverter.PdfHeaderOptions.HeaderImage = File.ReadAllBytes(logoImageFullPath);
pdfConverter.PdfHeaderOptions.DrawHeaderLine = true;
```

```vbnet
pdfConverter.PdfHeaderOptions.HeaderBackColor = Color.LightGray
pdfConverter.PdfHeaderOptions.HeaderHeight = 50
pdfConverter.PdfHeaderOptions.HeaderText = "Title"
pdfConverter.PdfHeaderOptions.HeaderTextColor = Color.Black
pdfConverter.PdfHeaderOptions.HeaderTextFontType = PdfFontType.Helvetica
pdfConverter.PdfHeaderOptions.HeaderTextFontSize = 18
pdfConverter.PdfHeaderOptions.HeaderTextYLocation = 5
pdfConverter.PdfHeaderOptions.HeaderSubtitleText = "Subtitle"
pdfConverter.PdfHeaderOptions.HeaderSubtitleTextColor = Color.Black
pdfConverter.PdfHeaderOptions.HeaderSubtitleTextFontType = PdfFontType.Helvetica
pdfConverter.PdfHeaderOptions.HeaderSubtitleTextFontSize = 12
pdfConverter.PdfHeaderOptions.HeaderTitleSubtitleYSpacing = 7
pdfConverter.PdfHeaderOptions.HeaderImageLocation = New PointF(0, 0)
pdfConverter.PdfHeaderOptions.HeaderImage = File.ReadAllBytes(logoImageFullPath)
pdfConverter.PdfHeaderOptions.DrawHeaderLine = True
```

The dimensions are specified in points; a point is 1/72 inch. The A4 page size in points is 595 x 842. At 96 pixels per inch, an A4 page is 794 pixels wide and 1123 pixels high.

The footer can have a text, the page numbers with a text before them, a line above the footer and a background color; the font and color of the texts can be changed:

```csharp
pdfConverter.PdfFooterOptions.FooterText = "Footer text";
pdfConverter.PdfFooterOptions.FooterBackColor = Color.LightGray;
pdfConverter.PdfFooterOptions.FooterHeight = 40;
pdfConverter.PdfFooterOptions.FooterTextColor = Color.Black;
pdfConverter.PdfFooterOptions.FooterTextFontType = PdfFontType.HelveticaOblique;
pdfConverter.PdfFooterOptions.FooterTextFontSize = 8;
pdfConverter.PdfFooterOptions.DrawFooterLine = true;
pdfConverter.PdfFooterOptions.PageNumberText = "Page";
pdfConverter.PdfFooterOptions.PageNumberTextColor = Color.Black;
pdfConverter.PdfFooterOptions.PageNumberTextFontType = PdfFontType.HelveticaBold;
pdfConverter.PdfFooterOptions.PageNumberTextFontSize = 10;
pdfConverter.PdfFooterOptions.ShowPageNumber = true;
```

```vbnet
pdfConverter.PdfFooterOptions.FooterText = "Footer text"
pdfConverter.PdfFooterOptions.FooterBackColor = Color.LightGray
pdfConverter.PdfFooterOptions.FooterHeight = 40
pdfConverter.PdfFooterOptions.FooterTextColor = Color.Black
pdfConverter.PdfFooterOptions.FooterTextFontType = PdfFontType.HelveticaOblique
pdfConverter.PdfFooterOptions.FooterTextFontSize = 8
pdfConverter.PdfFooterOptions.DrawFooterLine = True
pdfConverter.PdfFooterOptions.PageNumberText = "Page"
pdfConverter.PdfFooterOptions.PageNumberTextColor = Color.Black
pdfConverter.PdfFooterOptions.PageNumberTextFontType = PdfFontType.HelveticaBold
pdfConverter.PdfFooterOptions.PageNumberTextFontSize = 10
pdfConverter.PdfFooterOptions.ShowPageNumber = True
```

`PdfFooterOptions.PageNumberingFormatString` replaces the text of the page numbers with a format of your own, for example "Page &p; of &P;" (see the placeholders below). `PdfFooterOptions.PageNumberingStartIndex` and `PdfFooterOptions.PageNumberingPageCountIncrement` change the first page number and the total page count. ShowOnFirstPage, ShowOnOddPages and ShowOnEvenPages choose the pages a header or a footer is shown on.

### Custom Header and Footer Elements

Besides the predefined title, subtitle and page numbers, any number of custom elements can be placed at any position in the header and the footer:

- `HtmlToPdfArea` - HTML from a URL or a string, rendered by Chromium with options of its own (page loading, timeouts, CSS, scripts) and drawn on every page.
- `TextArea` - a text in a font given by an `Font`.
- `ImageArea` - an image, given as the bytes of an image file or a file path.
- `WordToPdfArea` - the first page of a Word document (docx, doc, rtf...), for example a letterhead.
- `ExcelToPdfArea` - the first page of an Excel workbook (xlsx, xls, csv...), the sheet fitted on it by default, for example a table of totals.
- `PowerPointToPdfArea` - the first slide of a PowerPoint presentation, scaled to the width of the area, for example a banner.

`PdfHeaderOptions` and `PdfFooterOptions` have a property for one element of the first three types: `PdfHeaderOptions.HtmlToPdfArea`, `PdfHeaderOptions.TextArea` and `PdfHeaderOptions.ImageArea`. Any number of elements can be added with the methods below:

```csharp
public void AddHtmlToPdfArea(HtmlToPdfArea htmlToPdfArea);
public void AddImageArea(ImageArea imageArea);
public void AddTextArea(TextArea textArea);
public void AddWordToPdfArea(WordToPdfArea wordToPdfArea);
public void AddExcelToPdfArea(ExcelToPdfArea excelToPdfArea);
public void AddPowerPointToPdfArea(PowerPointToPdfArea powerPointToPdfArea);
```

```vbnet
Public Sub AddHtmlToPdfArea(htmlToPdfArea As HtmlToPdfArea)
Public Sub AddImageArea(imageArea As ImageArea)
Public Sub AddTextArea(textArea As TextArea)
Public Sub AddWordToPdfArea(wordToPdfArea As WordToPdfArea)
Public Sub AddExcelToPdfArea(excelToPdfArea As ExcelToPdfArea)
Public Sub AddPowerPointToPdfArea(powerPointToPdfArea As PowerPointToPdfArea)
```

Page numbers can also be placed anywhere in the header and the footer with a `TextArea` element whose text contains the **&p;** and **&P;** placeholders, replaced with the current page number and the total number of pages.

The example below adds HTML to the header and to the footer, and the page numbers to the footer:

```csharp
private void AddHeader(PdfConverter pdfConverter, string headerAndFooterHtmlUrl)
{
    // enable the header
    pdfConverter.PdfDocumentOptions.ShowHeader = true;
    // set the header height in points
    pdfConverter.PdfHeaderOptions.HeaderHeight = 60;
    // set the header HTML area
    pdfConverter.PdfHeaderOptions.HtmlToPdfArea = new HtmlToPdfArea(
        headerAndFooterHtmlUrl);
}

private void AddFooter(PdfConverter pdfConverter, string headerAndFooterHtmlUrl)
{
    // enable the footer
    pdfConverter.PdfDocumentOptions.ShowFooter = true;
    // set the footer height in points
    pdfConverter.PdfFooterOptions.FooterHeight = 60;
    // write the page numbers
    pdfConverter.PdfFooterOptions.TextArea = new TextArea(0, 30,
        "This is page &p; of &P; ", new Font("Arial", 10));
    pdfConverter.PdfFooterOptions.TextArea.EmbedTextFont = true;
    pdfConverter.PdfFooterOptions.TextArea.TextAlign = HorizontalTextAlign.Right;
    // set the footer HTML area
    pdfConverter.PdfFooterOptions.HtmlToPdfArea = new HtmlToPdfArea(
        headerAndFooterHtmlUrl);
}
```

```vbnet
Private Sub AddHeader(pdfConverter As PdfConverter, headerAndFooterHtmlUrl As String)
    ' enable the header
    pdfConverter.PdfDocumentOptions.ShowHeader = True
    ' set the header height in points
    pdfConverter.PdfHeaderOptions.HeaderHeight = 60
    ' set the header HTML area
    pdfConverter.PdfHeaderOptions.HtmlToPdfArea = New HtmlToPdfArea(
        headerAndFooterHtmlUrl)
End Sub

Private Sub AddFooter(pdfConverter As PdfConverter, headerAndFooterHtmlUrl As String)
    ' enable the footer
    pdfConverter.PdfDocumentOptions.ShowFooter = True
    ' set the footer height in points
    pdfConverter.PdfFooterOptions.FooterHeight = 60
    ' write the page numbers
    pdfConverter.PdfFooterOptions.TextArea = New TextArea(0, 30,
        "This is page &p; of &P; ", New Font("Arial", 10))
    pdfConverter.PdfFooterOptions.TextArea.EmbedTextFont = True
    pdfConverter.PdfFooterOptions.TextArea.TextAlign = HorizontalTextAlign.Right
    ' set the footer HTML area
    pdfConverter.PdfFooterOptions.HtmlToPdfArea = New HtmlToPdfArea(
        headerAndFooterHtmlUrl)
End Sub
```

A Word document as the header, for example a letterhead:

```csharp
pdfConverter.PdfDocumentOptions.ShowHeader = true;
pdfConverter.PdfHeaderOptions.HeaderHeight = 60;
pdfConverter.PdfHeaderOptions.AddWordToPdfArea(
    new WordToPdfArea(0, 0, "letterhead.docx"));
```

```vbnet
pdfConverter.PdfDocumentOptions.ShowHeader = True
pdfConverter.PdfHeaderOptions.HeaderHeight = 60
pdfConverter.PdfHeaderOptions.AddWordToPdfArea(
    New WordToPdfArea(0, 0, "letterhead.docx"))
```

A slide of a PowerPoint presentation as the header, and the totals sheet of a workbook as the footer:

```csharp
pdfConverter.PdfDocumentOptions.ShowHeader = true;
pdfConverter.PdfHeaderOptions.HeaderHeight = 90;
pdfConverter.PdfHeaderOptions.AddPowerPointToPdfArea(
    new PowerPointToPdfArea(0, 0, 160, 90, "banner.pptx"));

pdfConverter.PdfDocumentOptions.ShowFooter = true;
pdfConverter.PdfFooterOptions.FooterHeight = 60;
ExcelToPdfArea totals = new ExcelToPdfArea(0, 0, "totals.xlsx");
totals.Worksheets = "Totals";
pdfConverter.PdfFooterOptions.AddExcelToPdfArea(totals);
```

```vbnet
pdfConverter.PdfDocumentOptions.ShowHeader = True
pdfConverter.PdfHeaderOptions.HeaderHeight = 90
pdfConverter.PdfHeaderOptions.AddPowerPointToPdfArea(
    New PowerPointToPdfArea(0, 0, 160, 90, "banner.pptx"))

pdfConverter.PdfDocumentOptions.ShowFooter = True
pdfConverter.PdfFooterOptions.FooterHeight = 60
Dim totals As New ExcelToPdfArea(0, 0, "totals.xlsx")
totals.Worksheets = "Totals"
pdfConverter.PdfFooterOptions.AddExcelToPdfArea(totals)
```
