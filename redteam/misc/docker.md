# Docker

### Pull from custom registry

```bash
sudo nano /etc/docker/daemon.json -> 
{
  "insecure-registries" : ["docker.vulndev.io:80"]
}

sudo systemctl restart docker
docker login <registry>
docker pull <registry>/image
```

### Execute commands in docker network namespace

```bash
 /usr/bin/nsenter --mount --target $PID -- <command>
```

