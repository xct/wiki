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

### Send Mails from Bash

```text
cat emails.txt | while read u ; do swaks --to $u --from <> --header "Subject: <>" --body "<>" --server <> ; done
```

