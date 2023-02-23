# Tools & Usage

### List Shares

```text
smbclient -L \\<ip> -U anonymous
```

### Connect to Share

#### Without credentials:

```text
smbclient \\server\\sharename -U "" -N
```

#### With credentials:

```text
smbclient \\server\\sharename -U domain\user
```

