# Tools

### Ffuf

#### Usage

```bash
# files/direcotires
~/tools/ffuf/ffuf -w /home/xct/tools/SecLists/Discovery/Web-Content/raft-large-directories.txt -u http://<ip>/FUZZ
# params via get
~/tools/ffuf/ffuf -w ./input -u http://<file>?<param>=FUZZ  -t <threads>
# params via post
~/tools/ffuf/ffuf -w ./input -X POST -d "params" -u http://<url> -t <threads>
```

#### Pure Brute

```python
import itertools
import string
for x in itertools.product(string.ascii_lowercase+string.digits, repeat=4):
    print(''.join(x))
```

```bash
python3 fuzz.py | ffuf -w -:BRUTE -u http://10.10.117.205/BRUTE
```

#### Filters

```bash
# Exclude
-fc(chars), -fr(regex), -fs(string), -fw(words)
# Include
-mc(chars), -mr(regex), -ms(string), -mw(words)
```

### JWT

[https://github.com/ticarpi/jwt\_tool](https://github.com/ticarpi/jwt\_tool)

Before using the tool, make sure to clone a fresh copy of github so it generates new keys & jwks.json files in your current working directory.

```
python3 jwt_tool.py <ey..>
```

If we have access to the private key, we can change the contents of a JWT token interactively like so:

```
python3 jwt_tool.py ey... -T -S rs256
```

The private key should be named `jwttool_custom_private_RSA.pem` .

[https://github.com/lmammino/jwt-cracker](https://github.com/lmammino/jwt-cracker)

```
jwt-cracker <token> [<alphabet>] [<maxLength>]
```

###
