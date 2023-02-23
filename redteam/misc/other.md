# Other

### Randomized Curl Portscan Scan

```bash
a=( $(shuf -e $(seq 20000 60000)) )
for i in ${a[@]}; do curl -v -k https://<target>:$i -m 1;done | tee out
```

### Port Java Code to NodeJS <a id="port-java-code-to-nodejs"></a>

```text
node> password = "jU5t_a_sna_3lpm1dg347_u_4_mfr54b"> var i;> var buffer = Array(32);> for (i=0; i<8; i++) {...     buffer[i] = password.charAt(i);... }> for (; i<16; i++) {...     buffer[i] = password.charAt(23-i);... }> for (; i<32; i+=2) {...     buffer[i] = password.charAt(46-i);... }> for (i=31; i>=17; i-=2) {...     buffer[i] = password.charAt(i);... }> console.log("picoCTF{" + buffer.join("") + "}");
```

### ​Convert colored terminal output to image

```python
#!/usr/bin/env python2
from pwn import *
from subprocess import check_output
import subprocess
 
p = remote('RHOST',RPORT)
img = p.recvuntil("[+]")
print(img)
subprocess.call("gnome-screenshot -f x.png", shell=True)
p.interactive()
```

### Initrd

```text
zcat <image> | cpio -idmv
```

### CTF Proof of Work

Quick way to solve proof of work challenges that require you to find an input that hashes to a certain hash:

```python
work = b"aaaaaa"
magic = iters.mbruteforce(lambda x: hashlib.sha256(x.encode()).hexdigest()[:len(work)].encode() == work, string.ascii_lowercase, 6)
```

### Basic Reverse Shell Script

[https://gist.github.com/xct/ab71d58a29e9017eb38565e32aeb95b0](https://gist.github.com/xct/ab71d58a29e9017eb38565e32aeb95b0)

### Powershell Fileupload with Kerberos Authentication

```csharp
$FilePath = 'C:\programdata\example.txt';
$URL = 'http://web.test.local/Upload';
$fileBytes = [System.IO.File]::ReadAllBytes($FilePath);
$fileEnc = [System.Text.Encoding]::GetEncoding('UTF-8').GetString($fileBytes);
$boundary = [System.Guid]::NewGuid().ToString(); 
$LF = "`r`n";

$bodyLines = ( 
    "--$boundary",
    "Content-Disposition: form-data; name=`"file`"; filename=`"example.txt`"",
    "Content-Type: application/octet-stream$LF",
    $fileEnc,
    "--$boundary--$LF" 
) -join $LF

Invoke-RestMethod -Uri $URL -Method Post -ContentType "multipart/form-data; boundary=`"$boundary`"" -Body $bodyLines -UseDefaultCredentials;
```

