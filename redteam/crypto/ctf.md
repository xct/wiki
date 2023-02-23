---
description: The more obscure crypto stuff goes here
---

# CTF

### Decode Flag from Long

```python
from Crypto.Util.number import long_to_bytes

m = ...
print(long_to_bytes(m))
```

### Subtract 2 Images \(similar to xor\)

```python
from PIL import Image, ImageChops

i1 = Image.open('1.png')
i2 = Image.open('2.png')

iFlag = ImageChops.add(ImageChops.subtract(i2, i1), ImageChops.subtract(i1, i2))
iFlag.show()
```



