# Python Snippets

### Bitstring manipulation

```python
def bitstring_to_bytes(s):
    v = int(s, 2)
    b = bytearray()
    while v:
        b.append(v & 0xff)
        v >>= 8
    return bytes(b[::-1])
 
base = "010101..." # the bit string
n = 8 # change something every n bits
result = ""

# Iterate over bitstring, doing something  every n bits
for i in xrange(n,len(base)-n,n):
  # do manipulation here
  result += num
print(bitstring_to_bytes(result))
```

### Named Capture Groups

```python
m = re.search('[e]\s:\s(?P<e>\d+){0,1}',text.decode('utf-8'))
  if m != None and 'e' in m.groupdict():
    e = int(m.group('e'))
```

