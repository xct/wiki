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

### Pwntools Template

```python
from pwn import *
import struct

context.terminal = ['alacritty', '-e', 'zsh', '-c']
target = '<target>'
context.binary = target
binary = ELF(target)
libc = ELF("./libc.so.6")

ssh_host = '<ip>'
ssh_user = '<user>'
ssh_pass = '<pass>'
ssh_port = 22

if args['SSH']:
	sh = ssh(host=ssh_host, user=ssh_user, password=ssh_pass, port=ssh_port)
	p = sh.run('/bin/bash')
	junk = p.recv(4096,timeout=2)
	p.sendline(target)
else:
	p = process(target,setuid=True)

#gdb.attach(p, gdbscript='continue')
p.interactive()
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

