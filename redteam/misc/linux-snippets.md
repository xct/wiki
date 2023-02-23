# Linux Snippets

### Get capabilities

```bash
/sbin/getcap -r / 2>/dev/null
```

### Get suid binaries

```bash
find / -perm -u=s -type f 2>/dev/null
```

### Check sudo configuration

```bash
sudo -l
cat /etc/sudoers
```

### Check open files on linux

```bash
fuser <filename>
lsof <filename>
```

### Check for unmounted disks on linux

```bash
ls /dev
```

### Bash port scan

```bash
for p in {1..65535}; do echo hi > /dev/tcp/<ip>/$p && echo port $p is open > scan 2>/dev/null; done
```

### Using gateway finder to detect rogue gateways

```bash
arp-scan -l | tee <name>.txt
python gateway-finder.py -f arp.txt -i <public ip>
```

### Mount vmdk file

```bash
modprobe nbd
qemu-nbd -r -c /dev/nbd2 <name>.vmdk
mount /dev/nbd1p1 /mnt
```

### Find files by date

```bash
find / -newermt "<start-date>" ! -newermt '<end-date>' 2>/dev/null
```

### Get proper tty on shell

```bash
# stty method
python -c "import pty; pty.spawn('/bin/bash')"
ctrl+z
stty raw -echo
fg
<enter>
<enter>
# rlwrap method
rlwrap <command>
```

### Get Apt History

```text
gunzip -dc history.log.1.gz | less #from /var/log/apt
```

