# Trusting Certificates During Python Development
> Solutions for trusting proxy certs during development

Having your `requests.get` or similar fail because your traffic is being decrypted by some legitimate middleman on the network (such as Zscaler ZPA or a Palo Alto firewalls) is a pain.  These services' certificates would need to be trusted, or certificate verification would have to be bypassed.  However we don't want to just ignore certificate verification--what if an illegitimate middleman was on the network?

**To state it explicitly, using SSLVerify=False or similar in a production script, module, or application is not acceptable at Foot Locker**

Below the issue and several approaches to resolution are described.  Take a look, or skip right to [the preferred fix](#trusting-certificates-per-scriptmodule).

# Python
## Validate the Issue

Test whether the issue is present for your base install of Python with the following command, executed from a terminal
```
python -c "import requests; print(requests.get('https://example.com'))"
```
Alternately, or if you use virtual environments (protip: you should be using virtual environments) the same can be done within a Python script/module using
```python
import requests
print(requests.get('https://example.com'))
```

If the command fails with an `SSLCertVerificationError`, then unless something has gone very wrong with [https://example.com](https://example.com) your connection to the site is being proxied and this guide is for you!

## Troubleshooting

If the intent is to solve this issue for an entire environment (eg your entire local workstation, or a virtual environment) then find what certificate store is being used with the following command

From terminal:
```
python -c "import requests; print(requests.certs.where())"
```
Within a script/module
```python
import requests
print(requests.certs.where())
```

Note the path returned:
* `C:\Program Files\...` - Python is installed for all users, possibly via `Software Center` or with local admin rights, and the cert store will require local admin to add new certs which may limit the resolution options available.
* `C:\Users\...` - Python is installed in the local user context, and the cert store can be modified without local admin rights 

# Resolution

## Trusting Certificates Per Request
Not ideal (even when using a variable, it doesn't scale tremendously well for large applications) but this may be the only option if the cert store otherwise used requires local admin privileges to modify.  Another drawback is this only works for local code, and doesn't update the cert store for imported modules

```python
requests.get(
    "https://example.com",
    verify="C:/Development/documentation/certificates/<certname>.crt"
    )
```

or even better
```python
example_tls_cert = "C:/Development/documentation/certificates/<certname>.crt"
requests.get(
    "https://example.com",
    verify=example_tls_cert
    )
```
---
## Trusting Certificates Per Script/Module

Use python-certifi-win32, a handy Python module that patches certifi (default Python cert store) at runtime to include your Windows cert store.  This is the preferred fix, but only applies if the cert in question is in your Windows cert store (most agent-based proxy solutions, including Zscaler and Palo Alto GlobalProtect, will place the cert there during agent install)

```
pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org python-certifi-win32
```
The `--trusted-host` args are present in order permit pip to work despite not trusting the current certificate store and prevent a chicken-and-egg scenario

---
## Trusting Certificates Per Python Install (Local User Context)
By default installing under the local user context does not require Local Admin, and would install in the location `C:\Users\<username>\AppData\Local\Programs\Python\Python##` 

A cert can be appended to the certifi module's trusted certificates by appending it like so:

#### Using PowerShell
```PowerShell
Get-Content 'C:\Development\documentation\certificates\<certname>.crt' | Add-Content 'C:\Users\<username>\AppData\Local\Programs\Python\Python<##>\Lib\site-packages\pip\_vendor\certifi\cacert.pem'
```
#### Using OpenSSL
```
openssl x509 -in $specific_ca.crt -text >> $virtualenv/lib/python2.7/site-packages/certifi/cacert.pem
```
---
## Trusting Certificates Per Python Install (System Context or Software Center)

If you have installed Python using the Software Center, it has been installed at the system level, not the user level.  Its files will be located in `C:\Program Files\Python37\Lib\site-packages\pip\_vendor\certifi\cacert.pem` or similar, which can't be modified without local admin.
If you had local admin, you could modify the certifi module's trusted certificates by appending the ZScaler certificate like so:
```PowerShell
Get-Content 'C:\Development\documentation\Certificates\ZscalerRootCertificate-2048-SHA256.crt' | Add-Content 'C:\Program Files\Python<##>\Lib\site-packages\pip\_vendor\certifi\cacert.pem'
```
# Other
> Placeholder - I may populate this section with the same processes for other languages (PowerShell, Go, etc) in the future.

# References
* [Zscaler: Installing TLS/SSL Root Certificates to Non-Standard Environments](https://community.zscaler.com/t/installing-tls-ssl-root-certificates-to-non-standard-environments/7261)
* [StackOverflow: Certificate verification when using virtual environments](https://stackoverflow.com/questions/34931378/certificate-verification-when-using-virtual-environments)
* [PyPI: python-certif-win32 ](https://pypi.org/project/python-certifi-win32/)

## See also
[Trusting Certificates During Git Development](Trusting_Certificates_During_Git_Development.md)