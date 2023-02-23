# Memcache

### Commands

```bash
stats slabs
stats items
stats cachedump 1 0
get <value>
```

### Memcached-Tools

```bash
sudo apt-get install libmemcached-tools
```

```bash
memcstat --servers=<ip>
memcdump --servers=<ip>
memccat --servers=<ip> keyname
memccp --servers=<ip> <localfile> # (file saved in key)
memcached-tool <ip>:<port> dump  # easiest way to dump all
```

### LRU

There can be hidden items \(LRU\). Memcache has cold, warm & hot storage, depending on what is used most, this can lead to `stats cachedump 1 0` not showing a key anymore \(cachedump only looks only at cold\). The command `lru_crawler metadump all` will show this. We can also force everything into cold storage using `lru mode flat`.

### Monitor Changes

```bash
watch fetchers (freezes terminal, only outputs watch output)
```

### Authentication

Often unauthenticated \(performance reasons\). SASL can protect in binary mode but can be dictionary attacked.

