# HTML elements mapping, excluding elements and server authentication

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## HTML elements mapping

Online: https://docs.html-to-pdf.net/anywhere-library/html/HTMLElementsMapping.htm

The converter can report the position in the generated PDF document of any HTML element: the page and the rectangle where the element was rendered. With these positions you can create bookmarks and internal links to HTML elements, place text or images over them, or put a digital signature on the place of an element.

This feature can be accessed using the `PdfConverter.HtmlElementsMappingOptions` property of the `PdfConverter` object. This property allows you to define a list of page elements for which you want to retrieve position. That is done using the `HtmlElementsMappingOptions.HtmlElementSelectors` property. This property specifies the CSS selectors of the HTML elements. For example, the selector for all the *H1* elements is *"H1"*, the selector for all the elements with the CSS class name *'myclass'* is *"*.myclass"* and the selector for the elements with the id *'myid'* is *"*#myid"*.

Read more about CSS selectors [here](https://developer.mozilla.org/docs/Web/CSS/CSS_selectors).

The `PdfConverter.HtmlElementsMappingOptions` property must be set before calling the convert method.

The HTML elements mapping is returned in the `HtmlElementsMappingOptions.HtmlElementsMappingResult` property. The `HtmlElementsMappingOptions.HtmlElementsMappingResult` result is a collection of `HtmlElementMapping` objects which offers the PDF page index where the element was mapped by the converter and the rectangle where the element was rendered inside that page, the element HTML ID, the element tag name, the CSS class name, the element text and the element outer HTML code. An element that spans several pages has one rectangle for each page. `HtmlElementMappingsCollection.GetElementByHtmlId(string)` and `HtmlElementMappingsCollection.GetElementsByTagName(string)` find the elements in the result.

### Code Sample

The code below draws a green frame around all the images and the h1 and h2 headings of the page in the generated PDF document.

```csharp
PdfConverter pdfConverter = new PdfConverter();

// the HTML elements whose position in the PDF we want: the images and the h1 / h2
// headings
pdfConverter.HtmlElementsMappingOptions.HtmlElementSelectors = new string[] { "img",
    "h1", "h2" };

Document pdfDocument = pdfConverter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/article");
try
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
finally
{
    pdfDocument.Close();
}
```

```vbnet
Dim pdfConverter As New PdfConverter()

' the HTML elements whose position in the PDF we want: the images and the h1 / h2
' headings
pdfConverter.HtmlElementsMappingOptions.HtmlElementSelectors = New String() {"img",
    "h1", "h2"}

Dim pdfDocument As Document = pdfConverter.GetPdfDocumentObjectFromUrl(
    "https://www.example.com/article")
Try
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
Finally
    pdfDocument.Close()
End Try
```

## Excluding elements, converting part of a page

Online: https://docs.html-to-pdf.net/anywhere-library/html/ExcludeHTMLRegion.htm

Parts of a page that make no sense on paper - menus, buttons, banners, forms - can be left out of the PDF, and a single part of a page can be converted on its own.

### Excluding elements

List the elements to exclude in `HtmlExcludedRegionsOptions.HtmlElementSelectors` (property `PdfConverter.HtmlExcludedRegionsOptions`): "img" selects all images, "*.myclass" the elements with the CSS class myclass, "*#myid" the element with the id myid. The elements are hidden before the page is converted, and the content after them moves up to take their place, as with display: none.

```csharp
PdfConverter converter = new PdfConverter();
converter.HtmlExcludedRegionsOptions.HtmlElementSelectors = new string[] { "*#menu",
    "*.no-pdf", "form" };
converter.SavePdfFromUrlToFile("https://www.example.com/article", "article.pdf");
```

```vbnet
Dim converter As New PdfConverter()
converter.HtmlExcludedRegionsOptions.HtmlElementSelectors = New String() {"*#menu",
    "*.no-pdf", "form"}
converter.SavePdfFromUrlToFile("https://www.example.com/article", "article.pdf")
```

The same can be done with CSS: the `PdfConverter.CustomCSS` property adds styles to the page before the conversion. A print style sheet of the page works too, when `PdfConverter.UsePrintCSSMediaType` is true.

```csharp
PdfConverter converter = new PdfConverter();
converter.CustomCSS = "nav, .no-pdf, #comments { display: none !important; }";
converter.SavePdfFromUrlToFile("https://www.example.com/article", "article.pdf");
```

```vbnet
Dim converter As New PdfConverter()
converter.CustomCSS = "nav, .no-pdf, #comments { display: none !important; }"
converter.SavePdfFromUrlToFile("https://www.example.com/article", "article.pdf")
```

### Converting a part of the page

`PdfConverter.RenderElementSelector` converts only the element matched by a CSS selector - an article, an invoice, a table. The element is lifted out of the page and rendered on its own, at the width of the page. `PdfConverter.RenderElementId` does the same for an element id; when both are set, RenderElementSelector is used. When no element matches, or the element has no visible size, the conversion fails with an error instead of converting the whole page.

```csharp
PdfConverter converter = new PdfConverter();
converter.RenderElementSelector = "main article";
converter.SavePdfFromUrlToFile("https://www.example.com/article",
    "article-only.pdf");
```

```vbnet
Dim converter As New PdfConverter()
converter.RenderElementSelector = "main article"
converter.SavePdfFromUrlToFile("https://www.example.com/article", "article-only.pdf")
```

`ImgConverter`, HtmlToPdfArea, HtmlToPdfElement and HtmlToImageElement have the same RenderElementSelector and RenderElementId properties.

## Server authentication, cookies and HTTP headers

Online: https://docs.html-to-pdf.net/anywhere-library/html/ServerAuthentication.htm

The converter can convert pages that require the visitor to authenticate. Which settings to use depends on how the web site asks for the credentials.

### HTTP authentication

When the web server asks for credentials with an HTTP authentication challenge (Basic authentication, for example - the browser would show a login dialog), set the user name and the password in the `PdfConverter.AuthenticationOptions` property of the `PdfConverter` object. The converter answers the challenge with them. No credentials are sent by default.

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.AuthenticationOptions.Username = username;
pdfConverter.AuthenticationOptions.Password = password;
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/protected", "page.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.AuthenticationOptions.Username = username
pdfConverter.AuthenticationOptions.Password = password
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/protected", "page.pdf")
```

The `ImgConverter` class has the same settings for converting HTML to images:

```csharp
public string AuthenticationPassword { get; set; }
public string AuthenticationUsername { get; set; }
```

```vbnet
Public Property AuthenticationPassword() As String
Public Property AuthenticationUsername() As String
```

### Login forms, cookies and headers

Many web sites use a login form instead. The converter can fill it in before it converts the page: set the address of the login page, the CSS selectors of the user name and password fields and of the submit button, and the values, in `PdfConverter.LoginOptions`.

```csharp
PdfConverter pdfConverter = new PdfConverter();
pdfConverter.LoginOptions.LoginPage = "https://www.example.com/login";
pdfConverter.LoginOptions.UsernameFieldSelector = "#username";
pdfConverter.LoginOptions.PasswordFieldSelector = "#password";
pdfConverter.LoginOptions.SubmitButtonSelector = "#login-button";
pdfConverter.LoginOptions.Username = username;
pdfConverter.LoginOptions.Password = password;
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/account", "account.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()
pdfConverter.LoginOptions.LoginPage = "https://www.example.com/login"
pdfConverter.LoginOptions.UsernameFieldSelector = "#username"
pdfConverter.LoginOptions.PasswordFieldSelector = "#password"
pdfConverter.LoginOptions.SubmitButtonSelector = "#login-button"
pdfConverter.LoginOptions.Username = username
pdfConverter.LoginOptions.Password = password
pdfConverter.SavePdfFromUrlToFile("https://www.example.com/account", "account.pdf")
```

When the application converts a page of its own for a user who is already signed in, pass the session on: add the authentication cookie to `PdfConverter.HttpCookies`, or a token to `PdfConverter.HttpHeaders` (for example an Authorization header).

```csharp
public IActionResult DownloadOrders()
{
    PdfConverter pdfConverter = new PdfConverter();
    // the page sees the request as coming from the signed-in user
    pdfConverter.HttpCookies.Add(".AspNetCore.Cookies",
        Request.Cookies[".AspNetCore.Cookies"]);
    byte[] pdf =
        pdfConverter.GetPdfBytesFromUrl("https://www.example.com/orders");
    return File(pdf, "application/pdf", "orders.pdf");
}
```

```vbnet
Public Function DownloadOrders() As IActionResult
    Dim pdfConverter As New PdfConverter()
    ' the page sees the request as coming from the signed-in user
    pdfConverter.HttpCookies.Add(".AspNetCore.Cookies",
        Request.Cookies(".AspNetCore.Cookies"))
    Dim pdf As Byte() =
        pdfConverter.GetPdfBytesFromUrl("https://www.example.com/orders")
    Return File(pdf, "application/pdf", "orders.pdf")
End Function
```
