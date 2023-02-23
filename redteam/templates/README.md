# Templates

### Find Basic Block with Angr

```python
import angr
import sys

def main(argv):
  path_to_binary = "<binary>"
  project = angr.Project(path_to_binary)
  initial_state = project.factory.entry_state()
  sm = project.factory.simgr(initial_state)
  # list of basic blocks to find or to avoid
  sm.explore(find=[], avoid=[])  
  for state in sm.deadended:
    print(state.posix.dumps(sys.stdin.fileno()))
  else:
    raise Exception('Could not find the solution')

if __name__ == '__main__':
  main(sys.argv)
```

### Execute program over ssh via pwntools

```python
#!/usr/bin/python
from pwn import *

s =  ssh(host='', user='', password='')
p = s.run('cd <path> && ./<vuln>')
p.recv()
p.sendline(<payload>)
p.interactive()
s.close()
```

### Extract value after string

```python
get = lambda x: [sh.recvuntil('{} : '.format(x)), int(sh.recvline())][1]
p = get('p')
```

### LD\_Preload in pwntools

```python
libc = ELF(<name>)
main = ELF(<name>)
r = main.process(env={'LD_PRELOAD' : libc.path})
```

### Generate shellcode on commandline

```bash
pwn shellcraft -f d amd64.linux.setreuid 1002
```

### Pwntools Crossarch Template

```python
#!/usr/bin/env python3
from pwn import *

context.terminal = ['alacritty', '-e', 'zsh', '-c']
target = './target'

context.binary = target
binary = ELF(target)
 
if args['REMOTE']:
 libc = ELF("./libc.so.6") 
else: 
 libc = ELF("/usr/arm-linux-gnueabihf/lib/libc.so.6")

service_host = ""
service_port = 0

ssh_remote = 0
ssh_host = ''
ssh_user = ''
ssh_pass = ''
ssh_port = 22

if args['GDB']:
 p = gdb.debug(target,
 '''
 source /home/xct/tools/pwndbg/gdbinit.py
 continue
 ''')
else:
 if args['SSH']:
  sh = ssh(host=ssh_host, user=ssh_user, password=ssh_pass, port=ssh_port)
  p = sh.run('/bin/bash')
  junk = p.recv(4096,timeout=2)
  p.sendline(target)
 else:
  if args['REMOTE']:
   p = remote(service_host,service_port)
  else:
   p = process(target,setuid=True)
```

