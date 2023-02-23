# General

### Hash Extension

When your application relies on `sha256(SECRET+message)` to create/verify cookies it is probably vulnerable to this attack. Vulnerable functions are MD5, SHA1 and SHA-2. If the message and length of the secret are known, we can append additional data and still produce a valid hash. This only works if a secret is prepended. Good tools to exploit this kind of vulnerability are [hash\_extender](https://github.com/iagox86/hash_extender) and [HashPump](https://github.com/bwall/HashPump).

### SQL Truncation

Sometime data gets truncated in the database \(because a field has a fixed length that it is exceeded\), so you could register `admin@victim.com       1` even when `admin@victim.com` already exists, overwriting the entry. If a login functions that only checks for the existence of at least one entry for the email & password, we can login as admin.

### Cookie Signature Cut off

On JWT Cookies some Algorithms don't use the full payload while signing. E.g. this gets truncated after 70 characters:

```php
$json = $this->toJson();
$jsonb64 = base64_encode($json);
$signature = base64_encode(password_hash(SECRET.$json, PASSWORD_BCRYPT));
setcookie('PHPSESSID', "${jsonb64}.${signature}", time()+60*60*24, '/');
```

### CSP

* [https://csp-evaluator.withgoogle.com](https://csp-evaluator.withgoogle.com)

