# Linux in Windows Domains

#### DNS

Linux likes to do reverse dns lookups, e.g. on ping or ssh. By default, active directory domain controllers do not create a reverse lookup zone. This causes timeout issues (about 7s after which the DCs answer with SERVFAIL).

