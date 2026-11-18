# HTML to image

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## The ImgConverter class

Online: https://docs.html-to-pdf.net/anywhere-library/html/ImgConverterClass.htm

`ImgConverter` defines a set of methods to render an image from a URL, an HTML string, an HTML file or an HTML stream. The page is rendered by the same Chromium engine as the PDF converter. The result is an array of bytes holding an encoded image file - PNG, JPEG, BMP or TIFF, chosen with a value of `ImageFormat` - or a file on disk. The bytes can be saved to a file, stored in a database, sent as the HTTP response to a browser or added to a PDF document with an image element.

### ImgConverter Save/Render Methods

The methods below convert a URL to an image and return the bytes of the image file. The second parameter is the format of the image. *GetImageFromUrl* returns the same bytes as *GetImageBytesFromUrl*.

The URL must be accessible from the computer running your application. When the page needs a login, use the `ImgConverter.HttpCookies`, `ImgConverter.AuthenticationUsername` and `ImgConverter.LoginOptions` properties. To find why a page does not load, open the URL in a browser on the same machine as your application.

```csharp
public byte[] GetImageBytesFromUrl(string url, ImageFormat format);
public byte[] GetImageFromUrl(string url, ImageFormat format);
```

```vbnet
Public Function GetImageBytesFromUrl(url As String, format As ImageFormat) As Byte()
Public Function GetImageFromUrl(url As String, format As ImageFormat) As Byte()
```

To convert an HTML string to an image use one of the methods below. The first one renders the HTML string as it is. The second one has the additional parameter *urlBase*, the full URL of the page the HTML string comes from. The converter uses it to resolve the images, CSS and JavaScript files referenced by relative URLs; without it those resources are not loaded.

```csharp
public byte[] GetImageBytesFromHtmlString(string htmlString, ImageFormat format);
public byte[] GetImageBytesFromHtmlString(string htmlString, ImageFormat format,
    string urlBase);
```

```vbnet
Public Function GetImageBytesFromHtmlString(htmlString As String,
    format As ImageFormat) As Byte()
Public Function GetImageBytesFromHtmlString(htmlString As String,
    format As ImageFormat, urlBase As String) As Byte()
```

The corresponding methods that save the image to a file are listed below.

```csharp
public void SaveImageFromUrlToFile(string url, ImageFormat format, string outFile);
public void SaveImageFromHtmlStringToFile(string htmlString, ImageFormat format,
    string outFile);
public void SaveImageFromHtmlStringToFile(string htmlString, ImageFormat format,
    string outFile, string urlBase);
```

```vbnet
Public Sub SaveImageFromUrlToFile(url As String, format As ImageFormat,
    outFile As String)
Public Sub SaveImageFromHtmlStringToFile(htmlString As String,
    format As ImageFormat, outFile As String)
Public Sub SaveImageFromHtmlStringToFile(htmlString As String,
    format As ImageFormat, outFile As String, urlBase As String)
```

Similar methods convert an HTML file (*GetImageBytesFromHtmlFile*, *SaveImageFromHtmlFileToFile*) and an HTML stream (*GetImageBytesFromHtmlStream*, *SaveImageFromHtmlStreamToFile*). The asynchronous methods `ImgConverter.GetImageBytesFromUrlAsync(string, ImageFormat, CancellationToken)`, GetImageBytesFromHtmlStringAsync and SaveImageFromUrlToFileAsync work like the ones of the PDF converter (see PdfConverter class).

### Sample code

```csharp
ImgConverter imgConverter = new ImgConverter();
imgConverter.LicenseKey = "your license key";

// a web page to a PNG file
imgConverter.SaveImageFromUrlToFile("https://www.example.com", ImageFormat.Png,
    "page.png");

// an HTML string to the bytes of a JPEG image
byte[] jpegBytes = imgConverter.GetImageBytesFromHtmlString(
    "<h1>Hello</h1><p>From ExpertPdf</p>", ImageFormat.Jpeg);
File.WriteAllBytes("hello.jpg", jpegBytes);
```

```vbnet
Dim imgConverter As New ImgConverter()
imgConverter.LicenseKey = "your license key"

' a web page to a PNG file
imgConverter.SaveImageFromUrlToFile("https://www.example.com", ImageFormat.Png,
    "page.png")

' an HTML string to the bytes of a JPEG image
Dim jpegBytes As Byte() = imgConverter.GetImageBytesFromHtmlString(
    "<h1>Hello</h1><p>From ExpertPdf</p>", ImageFormat.Jpeg)
File.WriteAllBytes("hello.jpg", jpegBytes)
```

### ImgConverter Configuration Properties

The conversion and the generated image can be configured with the properties below. The converter also has the page loading properties of the PDF converter - ConversionDelay, NavigationTimeout, WindowStatus, CustomCSS, PostLoadingScript, RenderElementSelector and the others (see JavaScript and page loading).

The `ImgConverter.LicenseKey` property takes the license key you received after purchase. When it is not set, the converter runs in evaluation mode and a demo watermark is drawn on the generated image.

```csharp
public string LicenseKey { get; set; }
```

```vbnet
Public Property LicenseKey() As String
```

The `ImgConverter.PageWidth` and `ImgConverter.PageHeight` properties set the width and height, in pixels, of the browser window the page is rendered in - the same effect as resizing a browser window to those dimensions. The image has the width of the window.

The default *PageWidth* is *1024 pixels*; 0 or a negative value also means 1024. The default *PageHeight* is *0*, and the image holds the whole page. Setting a positive height also sets `ImgConverter.ClipHtmlView` to true, and the image is cut at that height; set ClipHtmlView back to false after the height to capture the whole page in a window of that height.

```csharp
public int PageWidth { get; set; }
public int PageHeight { get; set; }
```

```vbnet
Public Property PageWidth() As Integer
Public Property PageHeight() As Integer
```
