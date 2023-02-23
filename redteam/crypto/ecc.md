# ECC

### General

Elliptic Curve Discrete Logarithm Problem \(ECDLP\) is the problem of finding an integer `n` such that `Q = nP`

### Common Problems

#### Find point Q\(x,y\) such that P +Q = 0

```python
#!/usr/bin/env sage

import sys
from sage.all import *

# Curve:   E: Y2 = X3 + 497 X + 1768, p: 9739
# Problem: Find the point Q(x,y) such that P + Q = O.

F = GF(9739)
E = EllipticCurve(F, [497, 1768])
P = E(8045,6936)

print(P)  # P
print(-P) # Q
print(P+(-P)) # 0 / Point at Infinity
```

#### Calculate order of composed curve by using "subcurves"

Given p,q,a,b,e we want to find g \(requires a prime modulus\).

```python
E1 = EllipticCurve(GF(p),[a,b])
E2 = EllipticCurve(GF(q),[a,b])
o1=K1.order()
o2=K2.order()
d=inverse_mod(e,o1*o2)
```

#### Getting Curve Parameters from 2 Points

```python
a=(A.y**X - A.x**Y - B.y**X + B.x**Y) * inverse_mod(A.x-B.x,n) % n
b=(A.y**X - A.x**Y - a*A.x ) % n
```

