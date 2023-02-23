# Path Redirection

### Symlinks

#### Create Softlink \(Junction\):

```bash
mklink /j <name> <target>
```

#### Create Hardlink to file:

```bash
mklink /h <name> <target>
```

#### List all links of a given file:

```bash
fsutil.exe hardlink list <filename>
```

#### CreateSymlink.exe

This creates symlinks via RPC Control and requires write permission on the \*folder\*, not just the file.

```text
Createsymlink.exe "srcfile" "dstfile"
```

### Tools

[https://github.com/googleprojectzero/symboliclink-testing-tools](https://github.com/googleprojectzero/symboliclink-testing-tools)

