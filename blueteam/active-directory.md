# Active Directory

### Best Practices

* limit login of DAs to DCs only
* never run a service with DA privileges
* check out temporary group memberships \(Can have TTL\)
* disable account delegation for sensitive accounts \(in ad usersettings\)

### Windows events

#### Golden Ticket

* 4624: Account Logon
* 4634: Account Logoff 
* 4672: Admin Logon \(should be monitored on the dc\)

```text
Get-WinEvent -FilterHashtable @{Logname='Security';ID=4672} -MaxEvents 1 |Format-List -Property *
```

#### Skeleton Key

* 7045: A Service was installed in the system.
* 4673: Sensitive Privilege user \(requires audit privileges\)
* 4611: Trusted logon process has been registered with the Local Security Authority \(requires audit privileges\)

```text
Get-WinEvent -FilterHashtable @{Logname='System';ID=7045} | ?{$_.message -like "*Kernel Mode Driver*"}
```

#### DSRM

* 4657: Audit creating/Change of `HKLM:\System\CurrentControlSet\Control\Lsa\DsrmAdminLogonBehaviour`

#### Malcious SSP

* 4657: Audit/creation of `HKLM:\System\CurrentControlSet\Control\Lsa\SecurityPackages`

#### Kerberoast

There are no mitigations for kerberoasting so make sure the service accounts that have an spn associated have strong passwords and are changed regularly.

* 4769: A Kerberos ticket as requested, Filter: Name != krbtgt, does not end with $, not machine@domain, Failure code is 0x0 \(success\), ticket encryption is 0x17 \(rc4-hmac\)

#### ACL Scan

* 4662: Operation was performed on an object
* 5136: directory service object was modified
* 4670:  permissions on an object were changed

### Forest Trust Attacks

* Enable SID Filtering
* Enable Selective Authentication \(access between forests not automated\)

### Advanced Threat Analytics

* Traffic for DCs is mirrored to ATA Sensors \(or installed on dc as service\), activity profile is build
* Collects 4776 \(credential validation of a user\) to detect replay attacks, detects behavioral anomalies
* Detects: account enumeration, netsession enumeration, Brute Force, exposed cleartext credentials, honey tokens, unusual protocols, credential attacks \(pth,ptt,ticket replay\)
* Will NOT detect non existent users for golden ticket
* Detects DCSync, but not DCShadow

### LAPS

* Centralized password storage with periodic randomization, stored in computer objects in fields mc-mcsAdmPwd \(cleartext\), ms-mcs-AdmPwdExperiationTime

### Device Guard

* Hardens against malware
* Run trusted code only, enforced in Kernel and Userspace \(CCI, UMCI, KMCI\)
* UEFI SEcure Boot protects bios and firmware

### Procted Users Group

* Cannot use CredSSP & Wdigest \(no more cleartext creds\)
* NTLM Hash not cached
* Kerberos does not use DES or RC4
* Requires at least server 2008, need to  test impact, no offline sign-on \(no caching\), useless for computers and service accounts

### Privileged Administrative Workstations

* Use hardened workstation for performing sensitive tasks

### Layered architecture

* Tier0: Domain Admins/Enterprise Admins
* Tier1: Significant Resource Access 
* Tier2: Administrator for Workstations / Support etc.

### Red Forest

* ESAE Enhanced Security Admin Environment
* Dedicated administrative forest for managing critical assets \(forests are security boundaries\)

