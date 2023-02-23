# Commands

### Docker

After suspending a VM with a running docker container and resuming it, the connection to the  container might be broken. To fix this run:

```text
systemctl restart NetworkManager docker
```

