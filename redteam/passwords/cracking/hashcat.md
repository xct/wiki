# Hashcat

### Mask Attacks

Mask attack mode is `-a 3`. We can specify custom charsets with `-1` . An attack targeting all combinations of 8 character passwords consisting of upper- and lowercase characters, followed by 2 numbers and a ! would look like that:

```text
hashcat -m 1000 -a0 -1 ?l?d <hashes> ?1?1?1?1?1?d?d!
```

### Kerberoast Hashes

```text
hashcat -m 13100 --force <hashes> <wordlist>
```

### ASREPRoast Hashes

```text
hashcat -m 18200 -a 0 <hashes> <wordlist>
```

### NTLM

Format: `username:hash`

```text
hashcat -m 1000 --username -p : -a0 <input> <wordlist>
hashcat --username --show <input>
```

