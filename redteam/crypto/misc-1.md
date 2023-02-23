# Misc

### Parse PEM

```python
#!/usr/bin/env sage

from Crypto.PublicKey import RSA
from Crypto.Util.number import long_to_bytes

key = RSA.importKey(open("key.pem").read())
print(f"d= {key.d}") # example for d
```

### Parse DER

```python
#!/usr/bin/env sage

from asn1crypto.x509 import Certificate

with open("cert.der", "rb") as f:
    cert = Certificate.load(f.read())

print(cert.public_key.native["public_key"]["modulus"])
print(cert.public_key.native["public_key"]["public_exponent"])
```

