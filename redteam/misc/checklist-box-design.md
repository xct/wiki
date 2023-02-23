# Checklist Boot2Root

### Windows

#### Change IIS User

Install IIS Servermanager \(and PHP if required via Web Platform Installer\), create a new user and then, inside IIS Manager Right Click "DefaultAppPool" and change Identity to the user. In addition, on the "Default Web Site",  change Authentication-&gt;Anonymous Authentication to the new user.

### Linux

Run:

```text
cd /var/log/
rm dpkg.log faillog dmesg* alternatives.log auth.log bootstrap.log cloud-init* syslog vmware-*
apt clean
apt autoclean
rm unattended-upgrades/* apt/* ubuntu-advantage.log
rm -rf installer/*
cd /root
rm .viminfo
rm .bash_history
rm .wget_hsts
ln -s /dev/null .bash_history
journalctl --rotate
journalctl --vacuum-time=1s
```

Then symlink bash\_history files of all users to /dev/null.

