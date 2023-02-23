# Misc

### Windows Packet Capture

#### Localhost

[https://www.netresec.com/index.ashx?page=RawCap](https://www.netresec.com/index.ashx?page=RawCap) :

To File:

```text
RawCap.exe 127.0.0.1 localhost_capture.pcap
```

To Wireshark:

```text
RawCap.exe -q 127.0.0.1 - | "C:\Program Files\Wireshark\Wireshark.exe" -i - -k
```

### Extract useful stuff from PCAP

[https://github.com/lgandx/PCredz](https://github.com/lgandx/PCredz)

### Remove Obfuscation from .NET Binaries

* [de4dot](https://github.com/0xd4d/de4dot)
* confuserexstringdecryptor
* confuserexswitchkiller

### Esoteric Language / Other Decodings

* [CyberChef](https://gchq.github.io/CyberChef/)
* [dcode.fr](https://www.dcode.fr/)

### Update all pip installed python packages

```text
pip freeze --user | cut -d'=' -f1 | xargs -n1 pip install -U
```

### Find what program listens on a port on linux

```text
sudo lsof -i -P -n | grep LISTEN
```

#### Delete all docker container & images

```text
docker rm -vf $(docker ps -a -q)
docker rmi -f $(docker images -a -q)
```

### Json to NDJson

```text
cat file.json | jq -c '.[]' > converted.json
```

### Domain Fronting

Connect to a benign domain where you know it will be served by a big CDN \(SNI is set to this domain\). Change the host header to your malicious site which is hosted on the same CDN.

### Screenshot from PowerShell

```aspnet
[Reflection.Assembly]::LoadWithPartialName("System.Drawing")
function screenshot([Drawing.Rectangle]$bounds, $path) {
   $bmp = New-Object Drawing.Bitmap $bounds.width, $bounds.height
   $graphics = [Drawing.Graphics]::FromImage($bmp)

   $graphics.CopyFromScreen($bounds.Location, [Drawing.Point]::Empty, $bounds.size)

   $bmp.Save($path)

   $graphics.Dispose()
   $bmp.Dispose()
}

$bounds = [Drawing.Rectangle]::FromLTRB(0, 0, 1920, 1080)
screenshot $bounds "C:\programdata\screenshot.png"
```

### Simple HTTP Server

This short snippet will print the request headers & allow graceful shutdown.

```python
#!/usr/bin/env python3

import http.server as SimpleHTTPServer
import socketserver as SocketServer

class StoppableHTTPServer(SimpleHTTPServer.HTTPServer):
    def run(self):
        try:
            self.serve_forever()
        except KeyboardInterrupt:
            pass
        finally:
            self.server_close()

class GetHandler(SimpleHTTPServer.SimpleHTTPRequestHandler):

    def do_GET(self):
        print(self.headers)
        SimpleHTTPServer.SimpleHTTPRequestHandler.do_GET(self)

server = StoppableHTTPServer(("", 80), GetHandler)
server.run()
```

### NetBSD useful commands

#### List ports

```text
netstat -na -f inet
```

### Setup p4wnp1\_aloa

[https://github.com/RoganDawes/P4wnP1\_aloa](https://github.com/RoganDawes/P4wnP1_aloa)

[https://jamesachambers.com/kali-linux-p4wnp1-aloa-guide-setup-usage-examples/](https://jamesachambers.com/kali-linux-p4wnp1-aloa-guide-setup-usage-examples/)

