# PKI & Certificates

## Table of Contents
1. [Terms and Definitions](#terms-definitions)
2. [Certificate Use Cases](#certificate-use-cases)
   1. [SSH Authentication](#ssh-authentication)
   2. [SSH Key Signing](#ssh-key-signing)
   3. [TLS Web Certificates](#tls-web-certificates)
   4. [Code Signing](#code-signing)
3. [Certificate Formats and File Types](#certificate-formats-and-file-types)
4. [References and Attribution](#references-and-attribution)

# Terms & Definitions
## Asymmetric Key Pair
Asymmetric cryptography is the foundation of PKI and certificates.  A key pair is made up of a public key and a private key, which are linked to each other cryptographically (mathematically) in a way that is prohibitively hard to fake, but fairly easy to verify

## Certificate
A certificate is essentially a signed container for the public key.  It contains the public key and additional cryptographic information (for example, if signed by a certificate authority, it will may have the certificate authority's public key as well as the nature of the CA's signature, as well as metadata such as expiration--how long it is good for--and purpose--the types of actions the CA approves the certificate to do)
	
## Private Key

A private key is a separate entity.  Often it is encrypted by a passphrase to better secure it

# Certificate Use Cases

## SSH Authentication
> WIP

### How To Implement SSH Authentication
WIP

## SSH Key Signing
> SSH Key signing is a process used primarily as a form of authentication.  The process creates a container for a SSH public key that is signed by a CA.  A host that has the CA's public key associated with an SSH user will then trust the signed key

### How To Implement SSH Key Signing

WIP

## TLS Web Certificates
> WIPscription

An example 'Chain of Trust' in an example PKI hierarchy
```BASH
# If a client trusts the Root certificate, it will trust the End Entity's certificate -- assuming trust isn't broken by something like an expired cert

Root CA Certificate
|  # signs a...
└─ Subordinate CA Certificate
    |  # signs a...
    └─ End Entity Certificate
```

### How To Implement TLS Web Certs

|Supporting Resource|Path|Notes|
|--|--|--|
|TLS Cert Request Config File|[template.cnf](template.cnf)||

### Troubleshooting TLS Web Certs
```bash
# use openssl to examine a remote host's certificate
echo | openssl s_client -showcerts -servername server.domain.com -connect server.domain.com:443 2>/dev/null | openssl x509 -inform pem -noout -text

# check is a cert file is valid
openssl rsa -check -noout -in myserver.key          # expected: 'RSA key ok'

# compare key, CSR, and certificate hashes to see if they are related
openssl rsa -noout -modulus -in mykey.key | openssl md5
openssl req -noout -modulus -in mycsr.csr | openssl md5
openssl x509 -noout -modulus -in mycert.crt | openssl md5

```

## Code Signing
> An executable (or installer such as an MSI) can have a certificate signed by a trusted CA.  This 

### How To Implement Code Signing
> The below is a WIP, not fully developed/documented

**Signing PowerShell Scripts**
```PowerShell
# Not vetted/validated, use at own risk for the time being
Get-ChildItem -Path cert:\CurrentUser\my -CodeSigningCert
$CodeSigningCert = Get-ChildItem -Path cert:\CurrentUser\my -CodeSigningCert
Set-AuthenticodeSignature -FilePath "path\to\file.ps1" -Certificate $CodeSigningCert
```

**Signing Executables With Microsoft codesign**
> Internally developed executables (including Python EXEs created with PyInstaller or similar) should have a certificate signed by a CA prior to distribution with a package manager

Microsoft provides `codesign.exe`, a code signing tool, in their Windows 10 SDK.  To use

1. Download and install the [Windows 10 SDK](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk)
2. Generate a code signing key is needed in PFX format


Signing PyInstaller OneFile Executables
https://docs.microsoft.com/en-us/windows/msix/package/sign-app-package-using-signtool

Tool located in C:\Program Files (x86)\Windows Kits\10\bin\10.0.19041.0\x64
PyInstaller
* https://github.com/pyinstaller/pyinstaller/wiki/Recipe-Win-Code-Signing

```PowerShell
C:\'Program Files (x86)\Windows Kits\10\bin\10.0.19041.0\x64\signtool.exe' sign /fd sha256 /f 'C:\Path\to\code_signing_cert.pfx' 'C:\Path\to\app_to_sign.exe'
```

# Certificate Formats and File Types
|Format|Extension*|Header|Description|
|--|--|--|--|
|x509|||aka X.509.  Standard defining public key certificates, crls, and other cryptographic entities.  See below for the different formats x509 certificates come in.|
||||
|PKCS#7|.p7b, .p7c||Often used for SSO, S/MIME.  Certificate and chain only, not the private key|
|PCKS#12|.pfx, .p12||container for an x509 certificate (with or without the supporting chain of trust) and the corresponding private key.  Should ALWAYS be password protected for production use, but the format doesn't require it|
|DER|.cer, .crt, .der, .key|N/A|serialized, binary format (not legible in a text editor)|
|PEM|.cer, .crt, .pem, .key|-----BEGIN PRIVATE KEY-----<br>-----BEGIN PUBLIC KEY-----|base64 format with header/footers (reasonably legible in a text editor).  Can be a certificate, public key, private key.  Don't trust the extension to tell you what it is|
|CSR|.csr|----BEGIN CERTIFICATE REQUEST-----|Certificate signing request - this is a format used to request a certificate authority to sign a public key, generating a certificate|

*As always, Windows-style file extensions don't mean anything outside of a Windows system: usually a certificate's format is determined by the header built into the file, not the extension.  This can cause additional confusion when the extension convention isn't used, but the cert still works because the underlying format is what was expected.  Or doesn't work, because the underlying format was NOT what was expected.

## Converting Between Formats
> Use the openssl utility to convert between common certificate formats

Note: the `-nodes` flag stands for 'No DES', as in 'don't encrypt the private key'.  If this flag isn't used then a password will be required.  Alternately use the `-aes256` flag to encrypt using AES instead of 3DES

### Convert to PEM
```
openssl x509 -in certificatename.cer -outform PEM -out certificatename.pem
```

### Convert PEM to DER
```
openssl x509 -outform der -in certificatename.pem -out certificatename.der
```

### Convert DER to PEM
```
openssl x509 -inform der -in certificatename.der -out certificatename.pem
```

### Convert PEM to P7B
> The PKCS#7 or P7B format is stored in Base64 ASCII format and has a file extension of .p7b or .p7c.  A P7B file only contains certificates and chain certificates (Subordinate CAs), not the private key. The most common platforms that support P7B files are Microsoft Windows and Java Tomcat
```
openssl crl2pkcs7 -nocrl -certfile certificatename.pem -out certificatename.p7b -certfile CACert.cer
```

### Convert PKCS7 to PEM
```
openssl pkcs7 -print_certs -in certificatename.p7b -out certificatename.pem
```

### Convert PFX to PEM
> The PKCS#12 or PFX format is a binary format for storing the server certificate, intermediate certificates, and the private key in one encryptable file. PFX files usually have extensions such as .pfx and .p12. PFX files are typically used on Windows machines to import and export certificates and private keys
```
openssl pkcs12 -in certificatename.pfx -out certificatename.pem
```

### Convert PFX to PKCS#8
Requires 2 commands to convert PFX to PEM, then to PKCS8
```
openssl pkcs12 -in certificatename.pfx -nocerts -nodes -out certificatename.pem
openSSL pkcs8 -in certificatename.pem -topk8 -nocrypt -out certificatename.pk8
```

### Convert PEM to PFX (where cacertfile is the CA cert/chain)
```
openssl pkcs12 -export -in certificatename.cer -inkey privateKey.key -out certificatename.pfx -certfile cacert.cer
```

### Convert P7B to PFX
Requires 2 commands to convert P7B to CER, then CER and Private Key to PFX
```
openssl pkcs7 -print_certs -in certificatename.p7b -out certificatename.cer
openssl pkcs12 -export -in certificatename.cer -inkey privateKey.key -out certificatename.pfx -certfile cacert.cer
```

# References and Attribution
* [Digicert: converting between formats](https://knowledge.digicert.com/solution/SO26449.html)
* [Digicert: signing code with the Windows SDK](https://www.digicert.com/kb/code-signing/signcode-signtool-command-line.htm)