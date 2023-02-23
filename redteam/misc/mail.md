# Mail

### Imap 

#### Login

```bash
a login <username>@<domain> <password>
```

#### List mailboxes

```bash
a LIST "" "*"
```

#### Select mailbox

```bash
a SELECT <mailboxname>
```

#### Search messages:

```bash
a UID SEARCH ALL`
```

#### Read Messages

```bash
a UID FETCH <ID> (BODY[1.1])
BODY.PEEK[]
UID FETCH <uid> (BODY[1])
```

#### Connect to pop3:

```bash
openssl s_client -connect <ip>:<port> -crlf
```

#### Send Mail from CLI:

```bash
sendEmail -t <rcptAddr> -f <senderAddr> -s <mailSrvAddr> -u "Some Subject" -m "Some Content"
```

Append Files with "-a &lt;filename&gt;", example of html mail with appended file:

```bash
sendemail -s <target>:25 -f customer@example.com -t will@example.com -o message-content-type=html -m  "<html><head><title>Issue</title></head><body></body></html>" -a important.docx
```

### Send Mails from Bash

```text
cat emails.txt | while read u ; do swaks --to $u --from <> --header "Subject: <>" --body "<>" --server <> ; done
```

We can also attach files with swaks using "--attach".

