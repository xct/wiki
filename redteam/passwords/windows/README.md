# Windows

#### Decrypt stored secure credential

```bash
$pw = Get-Content .\<file> | ConvertTo-SecureString
$bstr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($pw)
$UnsecurePassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
```

or:

```text
powershell (Import-Clixml cred.xml).GetNetworkCredential().Password
```

#### Decrypt GPP CPassword

```text
gpp-decrypt <crypted pass>
```

* [https://github.com/BustedSec/gpp-decrypt](https://github.com/BustedSec/gpp-decrypt)
* [https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1)



