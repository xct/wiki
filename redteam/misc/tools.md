# Tools

### Superuseful Docker by qtc

[https://github.com/qtc-de/container-arsenal](https://github.com/qtc-de/container-arsenal)

Setup:

```text
python3 setup.py sdist
pip3 install -r requirements.txt --user
pip3 install dist/car-1.0.0.tar.gz --user
```

Usage:

```text
car run ssh|smb|ftp|h2b|mysql|neo4j|nginx|samba|tftp
```

samba shared folder is in ~/arsenal/public

### Other Useful Docker

#### Neo4J

```text
docker run --env NEO4J_AUTH=neo4j/xct -p7687:7687 neo4j
```

### Note Taking

* [https://notable.md/](https://notable.md/)
* [https://joplinapp.org/](https://joplinapp.org/)

