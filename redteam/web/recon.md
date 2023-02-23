# Recon

### Subenum

```bash
#!/bin/bash
echo "-> Assetfinder"
assetfinder --subs-only $1 | tee -a domains.txt
echo "-> Amass"
amass enum --passive -d $1 -o domains
echo "-> Filter"
sort -u domains -o domains
cat domains | filter-resolved | tee -a domains.txt
rm domains
echo "\n\n[+] Checking for alive domains..\n"
cat domains.txt | ~/go/bin/httprobe | tee -a alive.txt
```

#### Fill Burp Sitemap with Curl

```text
https_proxy=http://127.0.0.1:8081 http_proxy=http://127.0.0.1:8081 curl -k https://www.upwork.com
(while read LINE; do https_proxy=http://127.0.0.1:8081 http_proxy=http://127.0.0.1:8081 curl -k "$LINE"; done < alive.txt)
```

### WPScan

Scan & Enumerate Users:

```text
wpscan -e u,ap --url <url>
```

Bruteforce Login via xmlrpc:

```text
wpscan --url <url> -t 3 -P rockyou.txt -U users.txt
```

