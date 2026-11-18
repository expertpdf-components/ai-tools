# PDF forms

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Filling PDF forms

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfFormFilling.htm

`PdfFormManager` reads and fills the form fields (AcroForm) of an existing PDF document: text boxes, check boxes, radio button lists, combo boxes and list boxes. The filled form can stay fillable, become read-only, or be flattened into page content.

### Filling a form

```csharp
using ExpertPdf.Anywhere.PDFForms;

PdfFormManager form = new PdfFormManager();
form.Load("application.pdf");

FormFieldTextBox name = form.Fields["name"] as FormFieldTextBox;
if (name != null)
    name.Text = "John Smith";

FormFieldCheckBox agree = form.Fields["agree"] as FormFieldCheckBox;
if (agree != null)
    agree.Checked = true;

FormFieldComboBox country = form.Fields["country"] as FormFieldComboBox;
if (country != null)
    country.SelectedValue = "Canada";

form.Save("application-filled.pdf");
form.Close();
```

```vbnet
Imports ExpertPdf.Anywhere.PDFForms

Dim form As New PdfFormManager()
form.Load("application.pdf")

Dim name As FormFieldTextBox = TryCast(form.Fields("name"), FormFieldTextBox)
If name IsNot Nothing Then name.Text = "John Smith"

Dim agree As FormFieldCheckBox = TryCast(form.Fields("agree"), FormFieldCheckBox)
If agree IsNot Nothing Then agree.Checked = True

Dim country As FormFieldComboBox = TryCast(form.Fields("country"), FormFieldComboBox)
If country IsNot Nothing Then country.SelectedValue = "Canada"

form.Save("application-filled.pdf")
form.Close()
```

### Reading the fields

`PdfFormManager.Fields` lists every field, by index or by name (the partial or the full name). Each field has its *Name* and *FullName*, the page and rectangle it is shown at, and its widgets when it is shown in several places.

```csharp
PdfFormManager form = new PdfFormManager();
form.Load("application.pdf");
foreach (FormField field in form.Fields)
    Console.WriteLine("{0} ({1}) on page {2}", field.FullName, field.GetType().Name,
        field.DisplayPage);
form.Close();
```

```vbnet
Dim form As New PdfFormManager()
form.Load("application.pdf")
For Each field As FormField In form.Fields
    Console.WriteLine("{0} ({1}) on page {2}", field.FullName, field.GetType().Name,
        field.DisplayPage)
Next
form.Close()
```

### Saving

- `PdfFormManager.Flatten` draws the values into the pages and removes the fields; a field's own Flatten flattens only that field.
- `PdfFormManager.ReadOnly` keeps the fields but makes them read-only.
- Saving appends the changes to the original file (an incremental update), so a signature the document already carries stays valid. Set `PdfFormManager.CompactSave` to write a new, smaller file instead.
- Text in any script is drawn with a font that has it, from the system or from the ExpertPdf.Anywhere.Fonts package.

Forms made with Adobe LiveCycle Designer that keep their fields only as XFA (no AcroForm fields) cannot be filled; hybrid forms, which have both, are filled through their AcroForm fields.
