# Ruby

### Funny Operators

This one will only excute the right hand side if the left hand side is not null or undefined:

```text
a ||= <some statement>
```

### Common Exploit Techniques

#### Reflection

```ruby
obj.instance_eval('ruby code')
```

