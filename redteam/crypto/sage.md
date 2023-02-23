# Sage

### GCD

```python
#!/usr/bin/env sage

import sys
from sage.all import *

print(gcd(a,b))
```

### Extended GCD

```python
#!/usr/bin/env sage

import sys
from sage.all import *

print(xgcd(a,b)) # returns d,u,v
```

### Solve Congruence

```python
#!/usr/bin/env sage

import sys
from sage.all import *

var('x') # < symbolic variable, solve for that one
print(solve_mod(a  == x, b)) 
```

### Solve Power Modulo

```python
#!/usr/bin/env sage

import sys
from sage.all import *

print(power_mod(a,b,c))
```

### Multiplicative Inverse

```python
#!/usr/bin/env sage

import sys
from sage.all import *

print(inverse_mod(a,b))
```

### Factor Prime

```python
#!/usr/bin/env sage

import sys
import hashlib
from sage.all import *

print(factor(a))
```



