# Networking

### Multiplex tcp/stdin/stdout connection

```go
// multiplex output of connection
prOut, pwOut := io.Pipe()
csOut := bufio.NewScanner(prOut)
trOut := io.TeeReader(c, pwOut)

// multiplex input of connection
prIn, pwIn := io.Pipe()
csIn := bufio.NewScanner(prIn)
trIn := io.TeeReader(os.Stdin, pwIn)

// these will block and copy forever
go io.Copy(c, trIn)
go io.Copy(os.Stdout, trOut)

go func() {
  for {
    csOut.Scan()
    output := csOut.Text()
    _ = output
    //fmt.Printf("%s\n", output)
  }
}()
// this is not a goroutine so we stay in this function
for {
  for {
    csIn.Scan()
    input := csIn.Text()
    _ = input
    //fmt.Printf("%s\n", input)
  }
}
```

### Simple Webserver

```go
package main

import (
    "fmt"
    "net/http"
)

func handle(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Response\r\n")
}

func main() {
    http.HandleFunc("/", handle)
    http.ListenAndServe(":80", nil)
}
```

