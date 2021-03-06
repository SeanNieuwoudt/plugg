## Plugg: A dependency-free plugin framework for Ruby applications

[![Gem Version](https://badge.fury.io/rb/plugg.svg)](https://rubygems.org/gems/plugg)

Simple & efficient asynchronous plugin framework for Ruby applications.

Plugg automatically loads all plugins from the paths you specify and creates instances of each inside an internal registry. When you send messages to Plugg, it relays those messages to each plugin that can respond to the message. Once all plugins have responded, it hands back an array containing the output from each plugin. 

Each plugin is called in it's own Thread so that all plugins are executed asynchronously.

Requirements
-----------------

It's recommend that you use Ruby 2.0.0 or higher.

Installation
-----------------

```
gem install plugg
```

Getting Started
-----------------

It's really simple to get Plugg running, after the installation, you set the source directory where the plugin classes should be loaded from. You can also specify more than one source directory by passing an array to the *Plugg.source(path)* method instead of a single string path. One caveat is that plugin class names should match the plugin file names exactly.

```ruby
require 'plugg'

Plugg.source('./plugins') # or Plugg.source(['./plugins1', './plugins2'])

result = Plugg.send(:test_method, 'a parameter')

result = Plugg.send('/about', request)
```

In the above example, you are sending the *:test_method* message to each loaded plugin and returning the output from each of these calls in an array to the `result` variable.

The *:test_method* message should correspond to a method name in each plugin class.

```ruby
class DemoPlugin
  def test_method(param)
    puts "Inside test_method with #{param}"
  end

  def to_s
    'Demo Plugin'
  end
end
```

You can pass any number of arguments to the plugins:

```ruby
result = Plugg.send(:test_method, arg1, arg2 arg3, etc)
```

Default Parameters
-----------------

If you wish to share default parameters or arguments with your plugins, you can do so by passing a hash as the second parameter to _Plugg.source()_.

```ruby
Plugg.source('./plugins', { param1: 'A value', param2: 'Another value' })
```

To be able to inject the parameter hash from the registry into your plugin class, you need to implement a `setup(p)` method that will be called after instantiating the plugin class:

```ruby
class DemoPlugin
  def test_method(param)
    puts "Inside test_method with #{param}"
  end

  def setup(p)
    @params = p
  end

  def to_s
    'Demo Plugin'
  end
end
```

Controlling Timeouts
-----------------

By default, plugins receive 30 seconds to perform their tasks. If you have long running jobs, you can set the timeout value as needed.

```ruby
Plugg.timeout(120) # Now 120 seconds instead of the default 30
```

One thing to be aware of is that Plugg will only send back the return data once the last plugin has finished executing.

Return value
-----------------

You can return anything you need from your plugin methods and can easily access the return data inspecting the result from *Plugg.send()* method for each plugin that was invoked:

```ruby
[
  {
    plugin: 'Demo Plugin',
    return: nil,
    timing: 0.013
  }
]
```

Plug is powerful in the sense that it allows you to separate any application logic from actor logic. Each plugin can act as a self contained application that responds to a set protocol. Your plugins can also return any data types.

If no plugin class is able to respond, the result of *Plugg.send()* will be empty.

Running the tests
-----------------

To test the current stable version of Plugg, simply run:

    rake test

License
-----------------

Please see [LICENSE](https://github.com/sn/plugg/blob/master/LICENSE) for licensing details.

Author
-----------------

[github.com/sn](https://github.com/sn) 
