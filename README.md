[![Build status](https://secure.travis-ci.org/barsoom/attr_extras.png)](https://travis-ci.org/#!/barsoom/attr_extras/builds)

# attr\_extras

Takes some boilerplate out of Ruby and complements `attr_accessor`, `attr_reader` and `attr_writer` nicely by providing:

`attr_initialize :foo, :bar`<br>
Defines an initializer that takes two arguments and assigns `@foo` and `@bar`.

`attr_initialize :foo, [:bar, :baz]`<br>
Defines an initializer that takes one regular argument, assigning `@foo`, and one hash argument, assigning `@bar` and `@baz`.

`attr_private :foo, :bar`<br>
Defines private readers for `@foo` and `@bar`.

`pattr_initialize :foo, :bar`<br>
Defines both initializer and private readers.

`method_object :fooable?, :foo`<br>
Defines a `.fooable?` class method that delegates to an instance method.

`attr_id_query :foo?, :bar?`<br>
Defines query methods like `foo?`, which is true iff `foo_id` is truthy. Goes well with Active Record.

`attr_query :foo?, :bar?`<br>
Defines query methods like `foo?`, which is true iff `foo` is truthy.

Findability has been a central consideration.
Hence the long name `attr_initialize`, so you see it when scanning for the initializer;
and the enforced questionmarks with `attr_id_query :foo?`, so you can search for that method.


## Example

``` ruby
class MyClass
  attr_initialize :foo, :bar
  attr_private :foo
  attr_id_query :item?
  attr_query :oof?

  def oof
    foo.reverse
  end

  def item_id
    123
  end
end

x = MyClass.new("Foo!", "Bar!")
x.oof    # => "!ooF"
x.foo    # NoMethodError: private method `foo' called.
x.item?  # => true
x.oof?   # => true

class MyMethodObject
  method_object :fooable?,
    :foo

  def fooable?
    foo == :some_value
  end
end

MyMethodObject.fooable?(:some_value)     # => true
MyMethodObject.fooable?(:another_value)  # => false

class MyHashyObject
  attr_initialize :foo, [:bar, :baz]
  attr_reader :bar
end

x = MyHashyObject.new("Foo!", bar: "Bar!", baz: "Baz!")
x.bar  # => "Bar!"
```

## Why not use `Struct`?

`Struct` has some behavior you may not expect. Say you have this:

``` ruby
class Greeter < Struct.new(:user)
  def greet
    puts "Hello #{user.name}!"
  end
end
```

The `Struct` won't actually require you to provide any arguments. You could do this and it won't complain until `nil.name` explodes on you:

``` ruby
Greeter.new.greet
```

Also, the Greeter will have a public `user` accessor even if you only need it internally, so your public interface is unnecessarily large.

Further, inheriting from `Struct` arguably suggests that you have a mere data structure, not a full-blown class with rich behavior.

With `attr_extras`, you have none of these issues:

``` ruby
class Greeter
  pattr_initialize :user

  def greet
    puts "Hello #{user.name}!"
  end
end
```

## Why not use `private; attr_reader :foo`?

Instead of `attr_private :foo`, you could do `private; attr_reader :foo`.

Other than being more to type, declaring `attr_reader` after `private` will actually give you a warning (deserved or not) if you run Ruby with warnings turned on.

## Installation

Add this line to your application's `Gemfile`:

    gem "attr_extras"

And then execute:

    bundle

Or install it yourself as:

    gem install attr_extras

## License

Copyright (c) 2012 [Barsoom AB](http://barsoom.se)

MIT License

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
