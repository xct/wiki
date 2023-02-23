# Pivoting

### reGeorg

* download [reGeorg](https://github.com/sensepost/reGeorg)
* upload one of the tunnel files to the target \(might need to change some strings to trick AV\)
* adjust local `/etc/proxychains.conf` to a port of choice
* run reGeorg:

  ```bash
  python reGeorgSocksProxy.py -u http://<ip>/<tunnel file> -p <socks port>
  ```



