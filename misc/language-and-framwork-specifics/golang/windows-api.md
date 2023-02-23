# Windows API

### LogonUser

Based on "github.com/itchio/ox/winox". This requires special privileges \(which CreateProcessWithLogon\) does not.

```go
func LogonUser(command string, domain string, user string, pass string, c net.Conn) {
    token, err := winox.Logon(user, domain, pass)
    defer token.Close()
    if err != nil {
        fmt.Println(err)
    }
    path := "C:\\Windows\\System32\\cmd.exe"
    cmd := exec.Command(path, "/c", command+"\n")
    cmd.SysProcAttr = &syscall.SysProcAttr{Token: syscall.Token(token)}
    cmd.Stdout = c
    cmd.Stderr = c
    cmd.Run()
}
```

### Resources

[https://docs.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-logonuserw\#return-value](https://docs.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-logonuserw#return-value) [https://golang.org/src/internal/syscall/windows/exec\_windows\_test.go](https://golang.org/src/internal/syscall/windows/exec_windows_test.go) [https://medium.com/jettech/breaking-all-the-rules-using-go-to-call-windows-api-2cbfd8c79724](https://medium.com/jettech/breaking-all-the-rules-using-go-to-call-windows-api-2cbfd8c79724)

