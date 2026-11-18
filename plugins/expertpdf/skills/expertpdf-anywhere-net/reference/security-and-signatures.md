# Security, encryption and digital signatures

Converted from the ExpertPdf Anywhere v22.0 help topics (online: https://docs.html-to-pdf.net/anywhere-library/html/Index.htm). C# and VB.NET samples are the official ones.

## Security options of the converter

Online: https://docs.html-to-pdf.net/anywhere-library/html/SecurityOptions.htm

With the security options you can allow or deny printing, editing, copying and filling form fields, and set a user password and an owner password. When you set a user password the PDF document is encrypted and the PDF viewer asks for that password to open it. The owner password is required to change the permissions of the document. When only the user password is set, it is also used as the owner password, and a reader who opens the document with it has every permission.

The `PdfConverter.PdfSecurityOptions` property of the `PdfConverter` object sets the security options of the generated PDF document:

```csharp
PdfConverter pdfConverter = new PdfConverter();

pdfConverter.PdfSecurityOptions.CanCopyContent = false;
pdfConverter.PdfSecurityOptions.CanEditContent = false;
pdfConverter.PdfSecurityOptions.CanFillFormFields = true;
pdfConverter.PdfSecurityOptions.CanPrint = true;
pdfConverter.PdfSecurityOptions.CanEditAnnotations = false;
pdfConverter.PdfSecurityOptions.CanAssembleDocument = false;

pdfConverter.PdfSecurityOptions.KeySize = EncryptionKeySize.EncryptKey256Bit;
pdfConverter.PdfSecurityOptions.UserPassword = "user-password";
pdfConverter.PdfSecurityOptions.OwnerPassword = "owner-password";

pdfConverter.SavePdfFromUrlToFile("https://www.example.com", "protected.pdf");
```

```vbnet
Dim pdfConverter As New PdfConverter()

pdfConverter.PdfSecurityOptions.CanCopyContent = False
pdfConverter.PdfSecurityOptions.CanEditContent = False
pdfConverter.PdfSecurityOptions.CanFillFormFields = True
pdfConverter.PdfSecurityOptions.CanPrint = True
pdfConverter.PdfSecurityOptions.CanEditAnnotations = False
pdfConverter.PdfSecurityOptions.CanAssembleDocument = False

pdfConverter.PdfSecurityOptions.KeySize = EncryptionKeySize.EncryptKey256Bit
pdfConverter.PdfSecurityOptions.UserPassword = "user-password"
pdfConverter.PdfSecurityOptions.OwnerPassword = "owner-password"

pdfConverter.SavePdfFromUrlToFile("https://www.example.com", "protected.pdf")
```

### Encryption

`PdfSecurityOptions.KeySize` chooses the encryption: *EncryptKey128Bit* (the default) encrypts with AES-128, *EncryptKey256Bit* with AES-256, readable by Adobe Reader X and later and by every current PDF reader, and *EncryptKey40Bit* with 40 bit RC4, the weakest, for very old readers only. `PdfSecurityOptions.EncryptMetadata` set to false leaves the XMP metadata of the document readable - by a search index, for example - while everything else stays encrypted.

A PDF/A or PDF/X document cannot be encrypted: those standards forbid it (see "PDF standards: PDF/A and PDF/X" (accessibility-and-compliance.md)). To protect, sign or remove the protection of existing PDF files, use the PDF security tool described in "Passwords, permissions and encryption" (security-and-signatures.md).

## Passwords, permissions and encryption

Online: https://docs.html-to-pdf.net/anywhere-library/html/PdfSecurity.htm

The PDF security part of ExpertPdf Anywhere protects PDF documents with passwords and permissions, removes the protection of documents you have the password of, and signs documents and validates their signatures. It is in the **ExpertPdf.Anywhere.PDFSecurity** namespace of the ExpertPdf.Anywhere library and runs on every platform the library supports: Windows, Linux and macOS.

The main class is `PdfSecurityManager`; the protection to apply is described by a `PdfSecurityOptions` object given to its constructor. All of it is done in managed code and needs no native engine.

### Features

- A user password to open the document, and an owner password that gives all the rights.
- Permissions: print, print in high quality, copy the content, copy for accessibility, edit the content, edit annotations, fill form fields, assemble the document.
- Encryption with AES (128 or 256 bit keys), AES-256 in Galois/Counter mode (PDF 2.0) or RC4 (40 or 128 bit keys), with or without the XMP metadata.
- Remove the security of a protected document, with its user or owner password.
- Sign documents with a PKCS#12 (.pfx) certificate or with a key held outside the application (a certificate store, a PKCS#11 token, a signing service), with an optional time stamp and long-term validation data, or certify them (see Digital Signatures).
- Validate the signatures of a document: integrity, signer, certificate trust, time stamp, long-term validation and, optionally, revocation.
- Read the security settings of a document without changing it: encryption, permissions, passwords and signatures.
- Work with files, streams and byte arrays.

### Installation

The security classes are part of the **ExpertPdf.Anywhere** package; there is no separate package to install. See "Installation" (installation-and-deployment.md) for the packages and "Requirements" (installation-and-deployment.md) for the supported platforms.

```text
dotnet add package ExpertPdf.Anywhere
```

### Getting started

Set the passwords and the permissions in a `PdfSecurityOptions` object, create the security manager with it and save the protected document:

```csharp
using ExpertPdf.Anywhere.PDFSecurity;

PdfSecurityOptions options = new PdfSecurityOptions();
options.UserPassword = "user";      // needed to open the document
options.OwnerPassword = "owner";    // opens the document with all the rights
options.CanPrint = false;
options.CanCopyContent = false;
options.KeySize = EncryptionKeySize.EncryptKey256Bit;
options.Algorithm = EncryptionAlgorithm.AES;

PdfSecurityManager securityManager = new PdfSecurityManager(options);

securityManager.SaveSecuredPdfToFile("report.pdf", "secured.pdf");
```

```vbnet
Imports ExpertPdf.Anywhere.PDFSecurity

Dim options As New PdfSecurityOptions()
options.UserPassword = "user"      ' needed to open the document
options.OwnerPassword = "owner"    ' opens the document with all the rights
options.CanPrint = False
options.CanCopyContent = False
options.KeySize = EncryptionKeySize.EncryptKey256Bit
options.Algorithm = EncryptionAlgorithm.AES

Dim securityManager As New PdfSecurityManager(options)

securityManager.SaveSecuredPdfToFile("report.pdf", "secured.pdf")
```

### Passwords, permissions and encryption

- With a user password only, the document needs that password to open and the same password is the owner password: whoever opens the document has all the rights.
- With an owner password only, the document opens without a password and the permissions apply to every reader; the owner password is needed to change them.
- With both passwords, the user password opens the document with the permissions you set, the owner password opens it with all the rights.

The permissions are `PdfSecurityOptions.CanPrint`, `PdfSecurityOptions.CanFullQualityPrint`, `PdfSecurityOptions.CanCopyContent`, `PdfSecurityOptions.CanAccessibilityCopyContent`, `PdfSecurityOptions.CanEditContent`, `PdfSecurityOptions.CanEditAnnotations`, `PdfSecurityOptions.CanFillFormFields` and `PdfSecurityOptions.CanAssembleDocument`; all are allowed by default. The default encryption is AES with a 128 bit key (`PdfSecurityOptions.Algorithm`, `PdfSecurityOptions.KeySize`). A 256 bit key gives AES-256 (security handler revision 6, ISO 32000-2), the choice for new documents; a 40 bit key always uses RC4, and RC4 is only for very old PDF viewers. `EncryptionAlgorithm.AESGCM` is AES-256 in Galois/Counter mode, whatever the key size; the document is written as PDF 2.0, which older readers cannot open. `PdfSecurityOptions.EncryptMetadata` (true by default) set to false leaves the XMP metadata readable, for example by a search index.

```csharp
// anyone can read the document; printing in low quality, form filling and
// accessibility only
PdfSecurityOptions options = new PdfSecurityOptions();
options.OwnerPassword = "owner";
options.CanFullQualityPrint = false;
options.CanCopyContent = false;
options.CanAccessibilityCopyContent = true;
options.CanEditContent = false;
options.CanEditAnnotations = false;
options.CanAssembleDocument = false;

// from memory to memory
byte[] securedBytes = new PdfSecurityManager(options).GetSecuredPdfBytes(
    File.ReadAllBytes("report.pdf"));
```

```vbnet
' anyone can read the document; printing in low quality, form filling and
' accessibility only
Dim options As New PdfSecurityOptions()
options.OwnerPassword = "owner"
options.CanFullQualityPrint = False
options.CanCopyContent = False
options.CanAccessibilityCopyContent = True
options.CanEditContent = False
options.CanEditAnnotations = False
options.CanAssembleDocument = False

' from memory to memory
Dim securedBytes As Byte() = New PdfSecurityManager(options).GetSecuredPdfBytes(
    File.ReadAllBytes("report.pdf"))
```

### Removing the security

`PdfSecurityManager.SaveUnSecuredPdfToFile(string, string, string)` and `PdfSecurityManager.GetUnSecuredPdfBytes(byte[], string)` remove the passwords, the permissions and the encryption of a document. They need its user or owner password; a wrong password throws a `SecurePdfException`.

```csharp
PdfSecurityManager securityManager = new PdfSecurityManager();
try
{
    securityManager.SaveUnSecuredPdfToFile("secured.pdf", "plain.pdf", "owner");
}
catch (SecurePdfException ex)
{
    Console.WriteLine("The document could not be opened: " + ex.Message);
}
```

```vbnet
Dim securityManager As New PdfSecurityManager()
Try
    securityManager.SaveUnSecuredPdfToFile("secured.pdf", "plain.pdf", "owner")
Catch ex As SecurePdfException
    Console.WriteLine("The document could not be opened: " & ex.Message)
End Try
```

### Reading the security settings of a document

`PdfSecurityManager.GetSecurityInfo(string)` returns a `PdfSecurityInfo` with the security settings of a document, without changing it: whether it is encrypted, the algorithm, the key length and the revision of the security handler, the permissions, whether a password is needed to open it (`PdfSecurityInfo.IsUserPasswordRequired`) and whether the owner password is needed for full rights (`PdfSecurityInfo.IsOwnerPasswordRequired`), and its signatures.

The encryption settings and the permissions are read even from a document that has an open password. The signature information needs the document opened: pass its user or owner password to `PdfSecurityManager.GetSecurityInfo(string, string)` (`PdfSecurityInfo.IsOpened` tells if the document could be opened). A wrong password throws a SecurePdfException.

```csharp
PdfSecurityManager securityManager = new PdfSecurityManager();
PdfSecurityInfo info = securityManager.GetSecurityInfo("secured.pdf");

if (info.IsEncrypted)
    Console.WriteLine("{0} {1} bit, revision {2}", info.Algorithm, info.KeyLength,
        info.Revision);
Console.WriteLine("Open password: {0}, owner password needed: {1}",
    info.IsUserPasswordRequired, info.IsOwnerPasswordRequired);
Console.WriteLine("Print: {0}, copy: {1}, edit: {2}", info.CanPrint,
    info.CanCopyContent, info.CanEditContent);

// the signatures of a document with an open password
PdfSecurityInfo opened = securityManager.GetSecurityInfo("secured.pdf", "user");
Console.WriteLine("Signatures: {0}, certified: {1}", opened.SignatureCount,
    opened.IsCertified);
```

```vbnet
Dim securityManager As New PdfSecurityManager()
Dim info As PdfSecurityInfo = securityManager.GetSecurityInfo("secured.pdf")

If info.IsEncrypted Then
    Console.WriteLine("{0} {1} bit, revision {2}", info.Algorithm, info.KeyLength,
        info.Revision)
End If
Console.WriteLine("Open password: {0}, owner password needed: {1}",
    info.IsUserPasswordRequired, info.IsOwnerPasswordRequired)
Console.WriteLine("Print: {0}, copy: {1}, edit: {2}", info.CanPrint,
    info.CanCopyContent, info.CanEditContent)

' the signatures of a document with an open password
Dim opened As PdfSecurityInfo = securityManager.GetSecurityInfo("secured.pdf",
    "user")
Console.WriteLine("Signatures: {0}, certified: {1}", opened.SignatureCount,
    opened.IsCertified)
```

### Samples

The samples download has these console applications, in C# and VB.NET, for Windows, Linux and macOS:

- **PdfSecurityCommandLine** - a command line tool: passwords, every permission, key size and algorithm, removing the security, showing the security settings of a document.
- **PdfSecurityFeatures** - an open password only, permissions without an open password, RC4 and AES with 40, 128 and 256 bit keys, files and memory, reading the security settings, removing the security with the owner or the user password.
- **PdfDigitalSignature** - digital signatures from a file, from memory and from a stream with a password protected document, with optional time stamps; a certified document signed by an approver; the validation of all the signatures.

They work on the documents that come with them and need no arguments.

### PDF Security command line tool

The PdfSecurityCommandLine sample is also a ready to use tool. The syntax is:

```text
PdfSecurityCommandLine /pdf:source_file [/out:file]
    [/userpswd:password] [/ownerpswd:password] [/keysize:40|128|256] [/algo:RC4|AES]
    [/noprint] [/nofullqualityprint] [/nocopy] [/noaccessibilitycopy]
    [/noeditcontent] [/noeditannot] [/nofillform] [/noassembly]
    [/removesecurity] [/info]
```

With /removesecurity the owner or the user password of the source document is required. /info shows the security settings of the source document and changes nothing. Examples:

```text
PdfSecurityCommandLine /pdf:report.pdf /noprint /keysize:256 /out:Secured.pdf
    /ownerpswd:owner /userpswd:user
PdfSecurityCommandLine /pdf:Secured.pdf /ownerpswd:owner /removesecurity
    /out:Unsecured.pdf
PdfSecurityCommandLine /pdf:Secured.pdf /info
```

### Licensing

Set `LicensingManager.LicenseKey` once when the application starts, or the `PdfSecurityManager.LicenseKey` property before you use the security manager. Without a license key the library adds an evaluation notice to the pages of the documents it secures, unsecures or signs, and of the documents it adds a signature field to. A document that is signed already gets no notice, because it would invalidate the signatures. Reading the security settings and validating the signatures is not limited. A license key that is not valid makes the next operation throw a `LicenseException`. See "License key" (installation-and-deployment.md).

## Digital signatures and signature validation

Online: https://docs.html-to-pdf.net/anywhere-library/html/DigitalSignatures.htm

`PdfSecurityManager.Sign(string, string, string, string, string, string, string, string, int, RectangleF, TimeStampAuthority)` adds a digital signature to an existing PDF document. The signature proves who signed the document and that it was not changed after it was signed. PDF viewers show it in their signatures panel, with the signer, the reason, the location and the contact information you give. `PdfSecurityManager.ValidateSignatures(string)` checks the signatures of a document.

Signing and validation are done in managed code, on Windows, Linux and macOS. To sign a document while you create it with the document model, see "Digital signature element" (security-and-signatures.md).

### The certificate

The document is signed with a certificate and its private key from a PKCS#12 file (.pfx or .p12), opened with the password of the file, or with a key held outside the application (see Keys held outside the application). For documents that other people must trust, use a certificate issued for you by a certification authority. For tests you can make a self-signed certificate, for example with OpenSSL on any platform:

```text
openssl req -x509 -newkey rsa:2048 -nodes -days 365 -subj "/CN=Test Signer"
    -keyout key.pem -out cert.pem
openssl pkcs12 -export -inkey key.pem -in cert.pem -out test-signer.pfx
    -passout pass:password
```

Each command is one line; it is wrapped here to fit the page.

PDF viewers show the signatures made with a self-signed certificate as valid (the document was not changed) but the signer as not trusted.

### Signing a document

The signature is placed in a box on one page: the page index starts at 0, and the box is given in points (1/72 inch) from the top left corner of the page; an empty rectangle makes an invisible signature. The methods sign from a file to a file, from a stream to a stream, and from a byte array to a byte array. The last argument is a time stamp authority, or null.

```csharp
using ExpertPdf.Anywhere.Drawing;
using ExpertPdf.Anywhere.PDFSecurity;

PdfSecurityManager securityManager = new PdfSecurityManager();

// the bottom right of the first page of an A4 document
RectangleF bounds = new RectangleF(345, 700, 200, 60);

// file to file
securityManager.Sign("report.pdf", "signed-report.pdf",
    "signer.pfx", "certificate password",
    "Approval", "I approve this report", "Head office", "signer@example.com",
    0, bounds, null);

// memory to memory
byte[] signedBytes = securityManager.Sign(File.ReadAllBytes("invoice.pdf"),
    "signer.pfx", "certificate password",
    "Issuer", "Invoice issued", "Head office", "billing@example.com",
    0, bounds, null);
```

```vbnet
Imports ExpertPdf.Anywhere.Drawing
Imports ExpertPdf.Anywhere.PDFSecurity

Dim securityManager As New PdfSecurityManager()

' the bottom right of the first page of an A4 document
Dim bounds As New RectangleF(345, 700, 200, 60)

' file to file
securityManager.Sign("report.pdf", "signed-report.pdf",
    "signer.pfx", "certificate password",
    "Approval", "I approve this report", "Head office", "signer@example.com",
    0, bounds, Nothing)

' memory to memory
Dim signedBytes As Byte() = securityManager.Sign(File.ReadAllBytes("invoice.pdf"),
    "signer.pfx", "certificate password",
    "Issuer", "Invoice issued", "Head office", "billing@example.com",
    0, bounds, Nothing)
```

A document can carry several signatures: sign the signed document again, each time in a save of its own. The earlier signatures stay valid.

### Digest, appearance and long-term validation

- `PdfSecurityManager.DigestAlgorithm` - the hash algorithm of the signature: SHA-256 (the default), SHA-384 or SHA-512.
- `PdfSecurityManager.SignatureBackgroundImage` - the bytes of a PNG, JPEG, GIF, BMP or WebP image (a company logo, a seal, a scanned handwritten signature) drawn behind the text of a visible signature, fitted in the box with its aspect ratio kept. `PdfSecurityManager.SignatureBackgroundOpacity` sets how strongly it is drawn, from 0 to 1 (0.25 by default). The image is lightened, not made transparent, so the appearance is allowed in PDF/A and PDF/X documents.
- `PdfSecurityManager.EnableLtv` - makes the signature LTV-enabled (long-term validation): the revocation data of the signing certificate and of its issuers (an OCSP response or a certificate revocation list for each) is obtained from the addresses the certificates name and embedded in the signed document with the issuer certificates. The signature can then be validated after the certificates expire, or once the issuer's services can no longer be reached, without going online. Signing fails with a `SecurePdfException` when a certificate of the chain names no revocation service or none can be reached; a self-signed root needs no revocation data.

```csharp
PdfSecurityManager securityManager = new PdfSecurityManager();
securityManager.DigestAlgorithm = SignatureDigest.SHA384;
securityManager.SignatureBackgroundImage = File.ReadAllBytes("logo.png");
securityManager.SignatureBackgroundOpacity = 0.25f;
securityManager.EnableLtv = true;

securityManager.Sign("report.pdf", "signed-report.pdf",
    "signer.pfx", "certificate password",
    "Approval", "I approve this report", "Head office", "signer@example.com",
    0, new RectangleF(320, 36, 240, 70), null);
```

```vbnet
Dim securityManager As New PdfSecurityManager()
securityManager.DigestAlgorithm = SignatureDigest.SHA384
securityManager.SignatureBackgroundImage = File.ReadAllBytes("logo.png")
securityManager.SignatureBackgroundOpacity = 0.25F
securityManager.EnableLtv = True

securityManager.Sign("report.pdf", "signed-report.pdf",
    "signer.pfx", "certificate password",
    "Approval", "I approve this report", "Head office", "signer@example.com",
    0, New RectangleF(320, 36, 240, 70), Nothing)
```

### Signing a password protected document

Set `PdfSecurityManager.SourcePdfPassword` to the user or the owner password of a protected document. The signed document keeps its protection. When a document must be both protected and signed, protect it first and sign it after: a change made after signing invalidates the signature.

```csharp
PdfSecurityManager securityManager = new PdfSecurityManager();
securityManager.SourcePdfPassword = "owner";

using (FileStream source = File.OpenRead("secured.pdf"))
using (FileStream destination = File.Create("secured-signed.pdf"))
{
    securityManager.Sign(source, destination, "signer.pfx", "certificate password",
        "Approval", "Approved for distribution", "Head office", "signer@example.com",
        0, new RectangleF(345, 700, 200, 60), null);
}
```

```vbnet
Dim securityManager As New PdfSecurityManager()
securityManager.SourcePdfPassword = "owner"

Using source As FileStream = File.OpenRead("secured.pdf"),
      destination As FileStream = File.Create("secured-signed.pdf")
    securityManager.Sign(source, destination, "signer.pfx", "certificate password",
        "Approval", "Approved for distribution", "Head office", "signer@example.com",
        0, New RectangleF(345, 700, 200, 60), Nothing)
End Using
```

### Time stamps

Without a time stamp, the signing time comes from the clock of the computer that signed. A `TimeStampAuthority` adds a time stamp from a time stamp server (RFC 3161), which proves when the document was signed. Give the address of the server, and a user name and a password when the server requires them. Signing then needs a connection to the server.

```csharp
TimeStampAuthority tsa = new TimeStampAuthority(new Uri("https://your-tsa-server/"));
// tsa.UserName = "user";
// tsa.Password = "password";

new PdfSecurityManager().Sign("report.pdf", "signed-report.pdf",
    "signer.pfx", "certificate password",
    "Approval", "I approve this report", "Head office", "signer@example.com",
    0, new RectangleF(345, 700, 200, 60), tsa);
```

```vbnet
Dim tsa As New TimeStampAuthority(New Uri("https://your-tsa-server/"))
' tsa.UserName = "user"
' tsa.Password = "password"

Dim securityManager As New PdfSecurityManager()
securityManager.Sign("report.pdf", "signed-report.pdf",
    "signer.pfx", "certificate password",
    "Approval", "I approve this report", "Head office", "signer@example.com",
    0, New RectangleF(345, 700, 200, 60), tsa)
```

### Certifying a document

A certification (author) signature also states the changes allowed after it. Set `PdfSecurityManager.CertificationLevel` before calling Sign: `PdfCertificationLevel.NoChangesAllowed`, `PdfCertificationLevel.FormFillingAllowed` (form filling and signing) or `PdfCertificationLevel.FormFillingAndAnnotationsAllowed`. The default, `PdfCertificationLevel.None`, makes an ordinary approval signature.

A document has at most one certification signature and it must be its first signature: certifying a signed document throws an exception, and so does signing a document certified with no changes allowed. When other people sign the document after it was certified, add their empty signature fields first with `PdfSecurityManager.AddSignatureField(string, string, string, int, RectangleF)`: signing an existing field is form filling, which the certification allows, while some validators report a signature field added after the certification as a change it does not allow. A Sign call with the name of an empty field signs that field, at its position.

```csharp
PdfSecurityManager securityManager = new PdfSecurityManager();

// 1. the empty field of the approver, on the first page
securityManager.AddSignatureField("report.pdf", "report-field.pdf",
    "Approval", 0, new RectangleF(40, 700, 200, 60));

// 2. the author certifies the document: form filling and signing remain allowed
PdfSecurityManager author = new PdfSecurityManager();
author.CertificationLevel = PdfCertificationLevel.FormFillingAllowed;
author.Sign("report-field.pdf", "certified.pdf",
    "author.pfx", "certificate password",
    "Author", "I am the author of this report", "Head office", "author@example.com",
    0, new RectangleF(345, 700, 200, 60), null);

// 3. the approver signs the empty field (its position comes from the field)
new PdfSecurityManager().Sign("certified.pdf", "approved.pdf",
    "approver.pfx", "certificate password",
    "Approval", "Approved", "Branch office", "approver@example.com",
    0, RectangleF.Empty, null);
```

```vbnet
Dim securityManager As New PdfSecurityManager()

' 1. the empty field of the approver, on the first page
securityManager.AddSignatureField("report.pdf", "report-field.pdf",
    "Approval", 0, New RectangleF(40, 700, 200, 60))

' 2. the author certifies the document: form filling and signing remain allowed
Dim author As New PdfSecurityManager()
author.CertificationLevel = PdfCertificationLevel.FormFillingAllowed
author.Sign("report-field.pdf", "certified.pdf",
    "author.pfx", "certificate password",
    "Author", "I am the author of this report", "Head office", "author@example.com",
    0, New RectangleF(345, 700, 200, 60), Nothing)

' 3. the approver signs the empty field (its position comes from the field)
Dim approver As New PdfSecurityManager()
approver.Sign("certified.pdf", "approved.pdf",
    "approver.pfx", "certificate password",
    "Approval", "Approved", "Branch office", "approver@example.com",
    0, RectangleF.Empty, Nothing)
```

### Keys held outside the application

The Sign overloads that take an `IExternalSigner` instead of a certificate file sign with a key that never leaves its device or service - a hardware security module, a smart card or a USB token, a cloud key service. The library builds the whole signature and asks the signer for one value: the signature of an `ExternalSigningRequest` with the private key of the first of its `IExternalSigner.Certificates`. The value is checked against the certificate before it is written. The digest, the certification level, the background image, long-term validation and time stamps work as with a certificate file. Two signers are ready to use:

- `CertificateStoreSigner` - a certificate of the system certificate store, found by its thumbprint (in the current user's store, then the local machine's), with its key in the store or on a token whose middleware the store reaches.
- `Pkcs11Signer` - a key on a PKCS#11 device, through the vendor's PKCS#11 library, on Windows, Linux and macOS.

Both are disposable and keep the certificate or the token session open until they are disposed.

```csharp
// a certificate of the system store, by its thumbprint
using (CertificateStoreSigner signer = new CertificateStoreSigner("3F8A...C2"))
{
    new PdfSecurityManager().Sign("report.pdf", "signed-report.pdf", signer,
        "Signature1", "Approved", "Head office", "signer@example.com",
        0, new RectangleF(345, 700, 200, 60), null);
}

// a key on a PKCS#11 token: the vendor's library and the PIN
using (Pkcs11Signer signer = new Pkcs11Signer("/usr/lib/softhsm/libsofthsm2.so",
    "1234", null, null))
{
    new PdfSecurityManager().Sign("report.pdf", "token-signed.pdf", signer,
        "Signature1", "Approved", "Head office", "signer@example.com",
        0, new RectangleF(345, 700, 200, 60), null);
}
```

```vbnet
' a certificate of the system store, by its thumbprint
Using signer As New CertificateStoreSigner("3F8A...C2")
    Dim securityManager As New PdfSecurityManager()
    securityManager.Sign("report.pdf", "signed-report.pdf", signer,
        "Signature1", "Approved", "Head office", "signer@example.com",
        0, New RectangleF(345, 700, 200, 60), Nothing)
End Using

' a key on a PKCS#11 token: the vendor's library and the PIN
Using signer As New Pkcs11Signer("/usr/lib/softhsm/libsofthsm2.so",
    "1234", Nothing, Nothing)
    Dim securityManager As New PdfSecurityManager()
    securityManager.Sign("report.pdf", "token-signed.pdf", signer,
        "Signature1", "Approved", "Head office", "signer@example.com",
        0, New RectangleF(345, 700, 200, 60), Nothing)
End Using
```

For another key service implement the interface. For an RSA key the value is a PKCS#1 v1.5 signature of `ExternalSigningRequest.Digest`, the hash of `ExternalSigningRequest.Data` computed with `ExternalSigningRequest.DigestAlgorithm`; for an ECDSA key the DER-encoded signature, or r and s concatenated. The certificates are DER-encoded: the signing certificate first, then its issuers. This signer uses an RSA key the application can reach:

```csharp
using System.Collections.Generic;
using System.Security.Cryptography;
using System.Security.Cryptography.X509Certificates;
using ExpertPdf.Anywhere.PDFSecurity;

class RsaKeySigner : IExternalSigner
{
    private readonly X509Certificate2 certificate;

    public RsaKeySigner(X509Certificate2 certificate)
    {
        this.certificate = certificate;
    }

    public IList<byte[]> Certificates
    {
        get { return new List<byte[]> { certificate.RawData }; }
    }

    public byte[] Sign(ExternalSigningRequest request)
    {
        HashAlgorithmName hash = HashAlgorithmName.SHA256;
        if (request.DigestAlgorithm == SignatureDigest.SHA512)
            hash = HashAlgorithmName.SHA512;
        else if (request.DigestAlgorithm == SignatureDigest.SHA384)
            hash = HashAlgorithmName.SHA384;
        using (RSA rsa = certificate.GetRSAPrivateKey())
        {
            return rsa.SignHash(request.Digest, hash, RSASignaturePadding.Pkcs1);
        }
    }
}
```

```vbnet
Imports System.Collections.Generic
Imports System.Security.Cryptography
Imports System.Security.Cryptography.X509Certificates
Imports ExpertPdf.Anywhere.PDFSecurity

Class RsaKeySigner
    Implements IExternalSigner

    Private ReadOnly certificate As X509Certificate2

    Public Sub New(certificate As X509Certificate2)
        Me.certificate = certificate
    End Sub

    Public ReadOnly Property Certificates As IList(Of Byte()) _
        Implements IExternalSigner.Certificates
        Get
            Return New List(Of Byte()) From {certificate.RawData}
        End Get
    End Property

    Public Function Sign(request As ExternalSigningRequest) As Byte() _
        Implements IExternalSigner.Sign
        Dim hash As HashAlgorithmName = HashAlgorithmName.SHA256
        If request.DigestAlgorithm = SignatureDigest.SHA512 Then
            hash = HashAlgorithmName.SHA512
        ElseIf request.DigestAlgorithm = SignatureDigest.SHA384 Then
            hash = HashAlgorithmName.SHA384
        End If
        Using rsa As RSA = certificate.GetRSAPrivateKey()
            Return rsa.SignHash(request.Digest, hash, RSASignaturePadding.Pkcs1)
        End Using
    End Function
End Class
```

When the signature value cannot be produced during the call - a remote signing service where the signer approves on another device, or a smart card in a user's browser - sign in two steps. `PdfSecurityManager.PrepareExternalSignature(string, string, >, string, string, string, string, int, RectangleF)` writes the document with a reserved signature and returns the request to be signed; `PdfSecurityManager.CompleteExternalSignature(string, string, string, >, SignatureDigest, byte[])` writes the signature value into it, with the same certificates and digest. The two steps can run in different processes; long-term validation and time stamps are not available in this mode.

### Validating signatures

`PdfSecurityManager.ValidateSignatures(string)` checks every signature of a document and returns a `SignatureValidationResult` for each: the field name, the signer, the signing time, the reason, location and contact information, the digest and signature algorithms, whether the document was changed after signing in a way the signature does not allow (`SignatureValidationResult.IsDocumentModified`), whether the certificate was valid at the signing time and its chain ends in a trusted root certificate, the time stamp, whether the signature is LTV-enabled (`SignatureValidationResult.IsLtvEnabled`), the revocation status, the certification level, and the overall `SignatureValidationResult.Status`: Valid, Unknown (the document is intact but the signer could not be verified, for example a self-signed certificate) or Invalid. `SignatureValidationResult.Messages` explains a status that is not Valid. A password protected document is opened with SourcePdfPassword.

`SignatureValidationOptions` adds root certificates to trust (your own certification authority, or a test certificate), can limit the trust to them (`SignatureValidationOptions.UseSystemTrustStore`), and turns on the online revocation check (`SignatureValidationOptions.CheckRevocation`, off by default), which asks the revocation lists and OCSP servers named in the certificates. The operating system checks the revocation of a certificate chain that ends in a root certificate the computer trusts; for a chain trusted only through TrustedRootCertificates the revocation status of a certificate that is not revoked is reported as Unknown.

```csharp
SignatureValidationOptions options = new SignatureValidationOptions();
options.CheckRevocation = true;
// options.TrustedRootCertificates.Add(new X509Certificate2("our-root-ca.cer"));

PdfSecurityManager securityManager = new PdfSecurityManager();
foreach (SignatureValidationResult result in securityManager.ValidateSignatures(
    "approved.pdf", options))
{
    Console.WriteLine("{0}: {1}, signed by {2} on {3}", result.FieldName,
        result.Status, result.SignerName, result.SigningTime);
    Console.WriteLine("  changed: {0}, trusted: {1}, time stamp: {2}, LTV: {3}",
        result.IsDocumentModified, result.IsCertificateChainTrusted,
        result.HasTimeStamp, result.IsLtvEnabled);
    foreach (string message in result.Messages)
        Console.WriteLine("  " + message);
}
```

```vbnet
Dim options As New SignatureValidationOptions()
options.CheckRevocation = True
' options.TrustedRootCertificates.Add(New X509Certificate2("our-root-ca.cer"))

Dim securityManager As New PdfSecurityManager()
For Each result As SignatureValidationResult In securityManager.ValidateSignatures(
    "approved.pdf", options)
    Console.WriteLine("{0}: {1}, signed by {2} on {3}", result.FieldName,
        result.Status, result.SignerName, result.SigningTime)
    Console.WriteLine("  changed: {0}, trusted: {1}, time stamp: {2}, LTV: {3}",
        result.IsDocumentModified, result.IsCertificateChainTrusted,
        result.HasTimeStamp, result.IsLtvEnabled)
    For Each message As String In result.Messages
        Console.WriteLine("  " & message)
    Next
Next
```

A signature followed by later changes (another signature, for example) does not cover the whole file (`SignatureValidationResult.CoversWholeDocument` is false); IsDocumentModified tells whether those changes are allowed.

### Samples

The PdfDigitalSignature console sample (C# and VB.NET, for Windows, Linux and macOS) signs the documents that come with it from a file, from memory and, protected with a password, from a stream. It uses certificate.pfx, a self-signed test certificate (password: expertpdf). Run it with /tsa:server_url to add time stamps. It also certifies a document with an empty field that an approver signs, and validates all the signatures. The SignAndValidate page of the web sample signs a form with a chosen digest and a background image and shows the validation report.

## Digital signature element

Online: https://docs.html-to-pdf.net/anywhere-library/html/DigitalSignatureElement.htm

Digital signatures are added to a PDF document with the `DigitalSignatureElement`, placed on a page of a new document or of an existing one. The element is drawn in its rectangle with the name of the signer, the reason, the location and the date, and the document is signed when it is saved. The signature is computed with SHA-256 by default (`DigitalSignatureElement.DigestAlgorithm`).

The signing certificate, with its private key, is a `DigitalCertificate` obtained from `DigitalCertificatesStore`: `DigitalCertificatesStore.GetCertificates(string, string)` reads a password protected *PKCS#12* file (*.pfx* or *.p12*), on every platform, and `DigitalCertificatesStore.GetCertificates(CertSystemStore)` reads a certificate store of the system. The returned `DigitalCertificatesCollection` lists the certificates that hold a private key first, and finds a certificate with `DigitalCertificatesCollection.GetCertBySubject(string)`, `DigitalCertificatesCollection.GetCertByIssuer(string)` or `DigitalCertificatesCollection.GetCertBySerialNumber(byte[])`.

A key held outside the application - in a hardware security module, a smart card, a USB token or a key service - signs through an `IExternalSigner` passed to the other constructor of the element: `CertificateStoreSigner` for a certificate of the system store, `Pkcs11Signer` for a PKCS#11 device, or an implementation of your own. With `DigitalSignatureElement.EnableLtv` set, the revocation data of the certificates is embedded when the document is saved, so the signature can be validated later without going online. `DigitalSignatureElement.SignatureBackgroundImage` draws a logo or a seal behind the text of the signature.

A document gets one new signature per save. To sign a document a second time, save it, load the saved file and add the next signature: each signature then covers its own revision of the document and all of them stay valid. Signing an existing PDF file with a timestamp and validating the signatures of a document are done with the PDF security manager.

### Sample Code

Below there is a sample showing how to add a digital signature to a PDF document:

```csharp
// add a page to the PDF document
PdfPage firstPage = document.AddPage();

string logoImagePath = Path.Combine("img", "logo.jpg");
string certificateFilePath = "certificate.pfx";

// create the area where the digital signature is displayed in the PDF document;
// in this sample the area is a logo image but it could be anything else
ImageElement logoElement = new ImageElement(0, 0, logoImagePath);
AddElementResult addResult = firstPage.AddElement(logoElement);

// get the PKCS#12 certificate from file
DigitalCertificatesCollection certificates =
    DigitalCertificatesStore.GetCertificates(certificateFilePath, "password");
DigitalCertificate certificate = certificates[0];

// create the digital signature over the logo image element
DigitalSignatureElement signature = new DigitalSignatureElement(
    addResult.EndPageBounds, certificate);
signature.Reason = "Protect the document from unwanted changes";
signature.ContactInfo = "The contact email is office@example.com";
signature.Location = "Development server";
firstPage.AddElement(signature);

// the document is signed when it is saved
document.Save("signed.pdf");
```

```vbnet
' add a page to the PDF document
Dim firstPage As PdfPage = document.AddPage()

Dim logoImagePath As String = Path.Combine("img", "logo.jpg")
Dim certificateFilePath As String = "certificate.pfx"

' create the area where the digital signature is displayed in the PDF document;
' in this sample the area is a logo image but it could be anything else
Dim logoElement As New ImageElement(0, 0, logoImagePath)
Dim addResult As AddElementResult = firstPage.AddElement(logoElement)

' get the PKCS#12 certificate from file
Dim certificates As DigitalCertificatesCollection =
    DigitalCertificatesStore.GetCertificates(certificateFilePath, "password")
Dim certificate As DigitalCertificate = certificates(0)

' create the digital signature over the logo image element
Dim signature As New DigitalSignatureElement(addResult.EndPageBounds, certificate)
signature.Reason = "Protect the document from unwanted changes"
signature.ContactInfo = "The contact email is office@example.com"
signature.Location = "Development server"
firstPage.AddElement(signature)

' the document is signed when it is saved
document.Save("signed.pdf")
```
