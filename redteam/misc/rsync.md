# Rsync

#### Connect and list shares

```text
nc -vn <ip> 873
@RSYNCD: 31.0
#list 
```

#### List files on the target

```text
rsync -av --list-only rsync://<ip>/<share>
# or with creds
rsync -av --list-only rsync://user@<ip>/<share>
```

#### Copy all files from the target

```text
rsync -av rsync://<ip>/<share> /tmp/out
```

#### Upload file to the target

```text
rsync -av /tmp/out rsync://username@<ip>/<share>/<file>
```

