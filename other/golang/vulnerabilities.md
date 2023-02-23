# Vulnerabilities

### Traversal

#### path.Clean

If the path does not start with a "/" it won't escape it:

```bash
path.Clean("../../../a/b") = "../../../a/b"
```



