# RSA

### RSA Encrypt & Decrypt with Sage

```python
#!/usr/bin/env sage

import sys
from sage.all import *

m = 12

p = 17
q = 23
e = 65537

n = p * q
pn = (p-1)*(q-1)
d = inverse_mod(e, pn)

c = power_mod(m,e,n)
print(c)

m = power_mod(c,d,n)
print(m)
```

### Euler Totient with Sage

```python
#!/usr/bin/env sage

import sys
from sage.all import *

p = 857504083339712752489993810777
q = 1029224947942998075080348647219

print(euler_phi(p*q))
```

### Common Modulus

You have 2 keys \(with common n, but different e\) and 2 ciphertexts. Do:

```python
gcd, aa, bb = extended_gcd(a.e, b.e)

if aa < 0:
    m1 = modinv(m1, a.n)
    aa = -(aa)
    
if bb < 0:
    m2 = modinv(m2, b.n)
    bb = -(bb)
    
m = (pow(m1, aa, a.n) * pow(m2, bb, b.n))%a.n
```

Resource: [https://crypto.stackexchange.com/questions/16283/how-to-use-common-modulus-attack](https://crypto.stackexchange.com/questions/16283/how-to-use-common-modulus-attack)

### Factor N when 2 close Primes are used

```python
from sage.all import *

n = ...

a = ceil(sqrt(n))
b = (a*a) - n
while not b.is_square():
	a = a + 1
	b = (a * a) - n

p = a - sqrt(b)
q = a + sqrt(b)
```



