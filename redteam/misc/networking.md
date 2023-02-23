# Networking

### Route Windows through Linux VM

Give both VMs a network adapter on the same network. On Windows, set the Linux VM as default gateway. On Linux run the following commands \(assumes eth1 incoming, eth0 outgoing\):

```text
sudo sysctl net.ipv4.ip_forward=1
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
```

### Transparent Proxy

```text
sudo sysctl net.ipv4.ip_forward=1
sudo iptables -t nat -A PREROUTING -i eth1 -p tcp --dport 80 -j REDIRECT --to-port 8080
sudo iptables -t nat -A PREROUTING -i eth1 -p tcp --dport 443 -j REDIRECT --to-port 8080
```

If you use burp make sure to check the "invisible" proxy option.

### Show open ports without netstat

```bash
grep -v "rem_address" /proc/net/tcp  | awk  '{x=strtonum("0x"substr($3,index($3,":")-2,2)); for (i=5; i>0; i-=2) x = x"."strtonum("0x"substr($3,i,2))}{print x":"strtonum("0x"substr($3,index($3,":")+1,4))}'
```

