> # WIP - incomplete!

# Trusting Certificates During Git Development
> Solutions for trusting proxy certs during development

Having your `git push` or similar fail because your traffic is being decrypted by some legitimate middleman on the network (such as Zscaler ZPA or a Palo Alto firewalls) is a pain.  These services' certificates would need to be trusted, or certificate verification would have to be bypassed.  However we don't want to just ignore certificate verification--what if an illegitimate middleman was on the network?

**To state it explicitly, using SSLVerify=False or similar in a production context is not acceptable at Foot Locker**

Below the issue and several approaches to resolution are described.  Take a look, or skip right to [the preferred fix](#trusting-certificates-per-scriptmodule).

# Validate the Issue

Test whether the issue is present for your base install of Git for Windows with the following command, executed from a terminal
```
{command used against example.com here}
```

If the command fails with an `SSLCertVerificationError`, then unless something has gone very wrong with [https://example.com](https://example.com) your connection to the site is being proxied and this guide is for you!

## Troubleshooting

If the intent is to solve this issue for an entire environment (eg your entire local workstation) then find what certificate store is being used with the following command

From terminal:
```
something something git config
```


# Resolution
> Below here was copied from the Python version of the same guide, and is WIP!
## Trusting Certificates Per Request
Not ideal (even when using a variable, it doesn't scale tremendously well for large applications) but this may be the only option if the cert store otherwise used requires local admin privileges to modify.  Another drawback is this only works for local code, and doesn't update the cert store for imported modules

```bash

```

or even better
```bash

```
---
## Trusting Certificates Per Script/Module

Use python-certifi-win32, a handy Python module that patches certifi (default Python cert store) at runtime to include your Windows cert store.  This is the preferred fix, but only applies if the cert in question is in your Windows cert store (most agent-based proxy solutions, including Zscaler and Palo Alto GlobalProtect, will place the cert there during agent install)

```
pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org python-certifi-win32
```
The `--trusted-host` args are present in order permit pip to work despite not trusting the current certificate store and prevent a chicken-and-egg scenario

---
## Trusting Certificates Per Git for Windows Install (Local User Context)
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
* TBD

## See also
[Trusting Certificates During Python Development](Trusting_Certificates_During_Python_Development.md)