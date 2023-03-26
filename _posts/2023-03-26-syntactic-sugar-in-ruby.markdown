---
layout: post
title: "Syntactic Sugar in Ruby"
date: 2023-03-26
abstract: "Ruby is a programming language known for being easy to learn and to love. And Ruby's syntactic sugar is one of the reasons for that."
categories: ruby
---
> Você poder ler uma versão deste artigo em português [aqui](https://www.campuscode.com.br/conteudos/como-funciona-o-syntactic-sugar-em-ruby).

Ruby is a programming language that allows us to write readable and concise
code. One of the reasons for that is this set of characteristics commonly
called **"Syntactic Sugar"**, since they make the lives of people who write code a
little _"sweeter"_.

Ruby has small language rules that permit code syntaxes that seem not to
correspond to the usual norms, but makes them a lot easier to remember and
understand.

For instance, Ruby doesn't require you to include certain symbols or parenthesis
in some scenarios. In this article we will describe a few examples of Ruby's
**syntactic sugar**. After all, it would take a lot of time to explain the whole
inner workings of the programming language (I wouldn't know how to, honestly).

## Names and method calls
### Using special characters in method names

In Ruby it is allowed to use special characters to name methods. We can use
methods like `save!` or `odd?`, for example. Characters like these are permitted:
`=`, `[`, `]`, `?`, `%`, `&`, `|`, `<`, `>`, `*`, `-`, `+` e `/`. 

This property allows us to use method names that are very representative of
their function. It might sound silly at first, but using the right characters
can make code comprehension a lot easier. Ruby itself uses this property in
methods like `odd?`, and the code looks like it is asking a number if it is odd
and returns `true` or `false`: `2.odd?`. Code can't get clearer than that.

This way it is possible to create methods like `user.employee?` or
`profile.destroy!`, that can make a lot of sense in the context of the
application.

Another interesting (but not really useful in practice) thing about this
property is that we could implement methods in classes like:

```ruby
class User
  def initialize(name)
    @name = name
  end

  def name=(value)
    @name = value
  end

  def name
    return @name
  end
end
```

In the code above, we have a `User` class and two methods, the first one assigns
(setter) a value to `name` and the second, obtains the value (getter). Notice
that the setter method `name=(value)` receives a parameter between the
parenthesis. When a class is set up like that, we can do the following:


```ruby
user = User.new("João")

user.name
#=> "João"

user.name=("Henrique")
user.name
#=> "Henrique"
```

### Skiping parenthesis and including white spaces

More interestingly, we can ignore the parenthesis and add whitespaces to method
calls. Ruby still understands what is supposed to be done and executes the
operation:

```ruby
user.name = "André"
user.name
#=> "André"
```

This improves code readability tremendously (at least in my opinion), and
therefore becomes easier to recall. Another instance where omitting parenthesis
helps readability is when we use `if`:

```ruby
puts "Hello, João" if(user.name == "João")
puts "Hello, João" if user.name == "João"
```

Both lines above work. :)

### Using attr methods

To make things easier, Ruby provides the methods `attr_accessor`, `attr_writer`
and `attr_reader` to classes implementation. "Under the hood", Ruby creates the
methods getter and setter automatically:

```ruby
class User
  attr_accessor :name

  def initialize(name)
    @name = name
  end
end

user = User.new("André")
user.name
#=> "André"
```

Here is the [official documentation](https://ruby-doc.org/core/Module.html#method-i-attr)
on these methods.

### Mathematical operators

An interesting exception to the rules on method calls applies to mathematical
operations: the `.` is not required. Each number is an object and each operator
is a method. Look at the example bellow, a basic addition operation:

```ruby
4 + 2
#=> 6
```

could be written in this way:

```ruby
4.+(2)
#=> 6
```

This can be applied to many methods:

```
+   -   *   /   =   ==    !=    >   <   >=    <=    []
```

The `[]` in particular, in addition to not requiring `.`, can encapsulate the
values of the arguments:

```
list = ["banana", "eggs", "bread", "milk", "rice"]

list[1]
#=> "eggs"

list.[](1)
#=> "eggs"
```

## The for loop

In Ruby, when the language construct `for` is used to create a loop,
internally the programming language uses the `each` implementation to iterate
the elements in the array. So `for` only actually exists for our convenience.

You can read more about the differences between `each` and `for` in this
article [How Ruby Reads Your Code](https://www.andrekanamura.com.br/ruby/2023/03/26/how-ruby-reads-code.html).

## Final considerations

This concludes our quick view on Ruby's **syntactic sugar**. We talked about a
few examples, but this encompasses only a superficial view on the inner workings
of the programming language.

With this knowledge you can start exploring other possibilities that make use of
Ruby's properties and the creative freedom it offers.

To know more about how Ruby works you can read Pat Shaughnessy's book
[Ruby Under The Microscope](https://patshaughnessy.net/ruby-under-a-microscope).

**References:**

- [Ruby Magic Syntactic Sugar Methods](https://blog.appsignal.com/2018/02/20/ruby-magic-syntactic-sugar-methods.html)
- [Ruby Syntactic Sugar](https://rubylearning.com/satishtalim/ruby_syntactic_sugar.html)
- [Official Ruby Doc](https://ruby-doc.org/)
