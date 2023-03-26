---
layout: post
title: "How Ruby Reads Your Code"
date: 2023-03-26
abstract: "Before actually executing your code, Ruby needs to interpret and transform it. In this article you'll see (roughly) how this process takes place."
categories: ruby
---
> Você pode ler uma versão deste artigo em português [aqui](https://campuscode.com.br/conteudos/como-o-ruby-le-e-executa-seu-codigo).

## Introduction

Ruby is a programming language loved by many programmers. In addition to having many methods
that make our work a lot easier, Ruby is known for this set of characteristics
that is commonly called "Syntactic Sugar", which makes writing code more
pleasant. You can read more about Syntactic Sugar in [this
article](https://www.andrekanamura.com.br/ruby/2023/03/26/syntactic-sugar-in-ruby.html).

The [language construct](https://en.wikipedia.org/wiki/Language_construct) `for`,
for instance, is present in many programming languages, including Ruby.
Even though it's use is not recommended by the community, having
this construct available can ease the transition from a different programming language to
Ruby.

Usually you should use methods like `each` or `map` instead of `for` when
iterating collections. In addition to usually making more readable code,
internally the `for` construct is implemented in terms of `each`. So that we can
better understand what that means, we need to analyze how Ruby reads and
interprets the code you write.

Just a "heads up", this will not be a deep dive into how programming languages
work internally. So this article should be better suited for people who have a basic to
intermediate understanding of programming, but might not be familiar with some Computer
Science topics.

## The three phases of Ruby code transformation

Before executing the code you wrote, Ruby needs to read it and transform these
lines of code 3 times. Which means that every time a Ruby script is ran, it goes
through a 3 step process, that will transform your code into the instructions
that will be executed: tokenization, parse and, finally, compilation.

```
 ___________      ______________      _______      _____________      ______________
| Your code | -> | Tokenization | -> | Parse | -> | Compilation | -> | Instructions |
 -----------      --------------      -------      -------------      --------------
```

In this article we will briefly discuss this phases, but if you are
interested in knowing more you should read the book
[**Ruby Under a Microscope**](https://patshaughnessy.net/ruby-under-a-microscope),
which was used as the main reference for this article. You can follow along the instructions in
your own computer if you like, the only requirement being a Ruby version 1.9 or
higher installed. For the examples in this article, version 3.0.0 was used.

Let's begin with a simple Ruby script:

```ruby
[1, 3].each do |e|
  p e
end
```

When the script above is ran, we get:

```shell
1
3
# => [1, 3]
```

The numbers 1 and 3 are printed in screen and the array is returned. The same
result can be obtained with this implementation using `for`:

```ruby
for e in [1, 3] do
  p e
end
```

Before executing the code, the Ruby interpreter needs to translate it and, to do
that, it reads each character and "tokenizes" it. In other words, the
interpreter converts this sequences into tokens or words. Consider the line
bellow:

|0|1|2|3|4|5|6|7|8|9|10|11|12|13|14|15|16|17|
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|[|1|,| |3|]|.|e|a|c|h| |d|o| |\||e|\||

First, the interpreter finds the opening square bracket. Then, an integer (1),
followed by a comma, followed by white space, which means that another element is
expected, and that the first element of the array is an integer number of one
digit. And so on... step by step, the interpreter transforms these characters
into tokens, and identifies the method `each`, the keyword `do` etc.

If you would like to see for yourself how Ruby interprets different code, you
can use the class `Ripper` with the method `lex`, which allows you to visualize
the "tokenized code":

```ruby
require 'ripper'
require 'pp'

code = <<STR
[1, 3].each do |e|
  p e
end
STR

puts code
pp Ripper.lex(code)
```

Running the script above we get:

```shell
[1, 3].each do |e|
  p e
end
[[[1, 0], :on_lbracket, "[", BEG|LABEL],
 [[1, 1], :on_int, "1", END],
 [[1, 2], :on_comma, ",", BEG|LABEL],
 [[1, 3], :on_sp, " ", BEG|LABEL],
 [[1, 4], :on_int, "3", END],
 [[1, 5], :on_comma, ",", BEG|LABEL],
 [[1, 6], :on_sp, " ", BEG|LABEL],
 [[1, 7], :on_int, "5", END],
 [[1, 8], :on_rbracket, "]", END],
 [[1, 9], :on_period, ".", DOT],
 [[1, 10], :on_ident, "each", ARG],
 [[1, 14], :on_sp, " ", ARG],
 [[1, 15], :on_kw, "do", BEG],
 [[1, 17], :on_sp, " ", BEG],
 [[1, 18], :on_op, "|", BEG|LABEL],
 [[1, 19], :on_ident, "e", ARG],
 [[1, 20], :on_op, "|", BEG|LABEL],
 [[1, 21], :on_ignored_nl, "\n", BEG|LABEL],
 [[2, 0], :on_sp, "  ", BEG|LABEL],
 [[2, 2], :on_ident, "p", CMDARG],
 [[2, 3], :on_sp, " ", CMDARG],
 [[2, 4], :on_ident, "e", END|LABEL],
 [[2, 5], :on_nl, "\n", BEG],
 [[3, 0], :on_kw, "end", END],
 [[3, 3], :on_nl, "\n", BEG]]
```

Each line is read and translated. The characters are identified with it's
position and symbol. For instance, `[1, 10]` indicates line 1, position 10, and
`:on_ident` indicates the `each` block. In this step, even if the code contains
syntax mistakes, it can still be tokenized.

When we use `Ripper` to read the `for` implementation, we get:

```shell
for e in [1, 3] do
  p e
end
[[[1, 0], :on_kw, "for", BEG],
 [[1, 3], :on_sp, " ", BEG],
 [[1, 4], :on_ident, "e", CMDARG],
 [[1, 5], :on_sp, " ", CMDARG],
 [[1, 6], :on_kw, "in", BEG],
 [[1, 8], :on_sp, " ", BEG],
 [[1, 9], :on_lbracket, "[", BEG|LABEL],
 [[1, 10], :on_int, "1", END],
 [[1, 11], :on_comma, ",", BEG|LABEL],
 [[1, 12], :on_sp, " ", BEG|LABEL],
 [[1, 13], :on_int, "3", END],
 [[1, 14], :on_rbracket, "]", END],
 [[1, 15], :on_sp, " ", END],
 [[1, 16], :on_kw, "do", BEG],
 [[1, 18], :on_ignored_nl, "\n", BEG],
 [[2, 0], :on_sp, "  ", BEG],
 [[2, 2], :on_ident, "p", CMDARG],
 [[2, 3], :on_sp, " ", CMDARG],
 [[2, 4], :on_ident, "e", END|LABEL],
 [[2, 5], :on_nl, "\n", BEG],
 [[3, 0], :on_kw, "end", END],
 [[3, 3], :on_nl, "\n", BEG]]
```

As expected, the results are different from the `each` implementation, since in
this stage, the only thing Ruby does is read characters and transforms them into
tokens.

After "tokenizing" the full script, Ruby still needs to understand exactly what
it has to be executed and, to do that, reading the tokens is not enough. Like
many programming languages, the Ruby "parser" contains a series of grammatical
rules that will be used to analyze and interpret your code. Just like the
tokenization process, we can use the `Ripper` class to see how the "parser" does
that. We can use the same script as before, only this time we use the method
`sexp` instead of `lex`:

```ruby
require 'ripper'
require 'pp'

code = <<STR
[1, 3].each do |e|
  p e
end
STR

puts code
pp Ripper.sexp(code)
```

Running the code above we get the following:

```shell
[1, 3].each do |e|
  p e
end
[:program,
 [[:method_add_block,
   [:call,
    [:array, [[:@int, "1", [1, 1]], [:@int, "3", [1, 4]]]],
    [:@period, ".", [1, 6]],
    [:@ident, "each", [1, 7]]],
   [:do_block,
    [:block_var,
     [:params, [[:@ident, "e", [1, 16]]], nil, nil, nil, nil, nil, nil],
     false],
    [:bodystmt,
     [[:command,
       [:@ident, "p", [2, 2]],
       [:args_add_block, [[:var_ref, [:@ident, "e", [2, 4]]]], false]]],
     nil,
     nil,
     nil]]]]]
```

We can start to notice a few elements that represent components of a script.
The line bellow, for instance, defines an array with 2 integer
numbers (1 and 3), being 1 on line 1, in position 1, and 3 on line 1, position 4:

```
[:array, [[:@int, "1", [1, 1]], [:@int, "3", [1, 4]]]]
```

Note that the symbol `:method_add_block` indicates that a block of code is
called right at the beginning. Just for comparison, let's see how to the code bellow is read:

```ruby
code = <<STR
arr = []
arr << "Pedro"
STR

puts code
pp Ripper.sexp(code)
```

```shell
[:program,
 [[:assign, [:var_field, [:@ident, "arr", [1, 0]]], [:array, nil]],
  [:binary,
   [:var_ref, [:@ident, "arr", [2, 0]]],
   :<<,
   [:string_literal,
    [:string_content, [:@tstring_content, "Pedro", [2, 8]]]]]]]
```

There we can find the value attribution (`:assign`) to the variable (`:var_field`)
identified by `[:@ident, "arr", [1, 0]]]`, and then further down `:<<` is called, adding an
element to the array.

Now going back to the code transformation phases, the main difference between
this stage and the previous, is that the interpreter needs to understand the
**prioritization** of operations that will be executed. And
at this stage of the process, syntax mistakes will raise errors.

After the first 2 phases, the code is converted into an [abstract syntax
tree](https://en.wikipedia.org/wiki/Abstract_syntax_tree) data structure.

Finally, the tokenized and parsed code will go through the
[compiler](https://en.wikipedia.org/wiki/Compiler), which means it will be
translated into another language that will be easier for computers to
understand. Unlike programming languages like Java or C, Ruby compiles code
automatically without us even noticing. The compiler will generate bytecode
instructions, called YARV, which will be interpreted in the Ruby Virtual Machine
(YARV - Yet Another Ruby Virtual machine).

Using this data structure, the compiler is able to understand the node
prioritization and will add objects and method calls to the execution stack.

In the same manner as the previous stages, we can visualize the compiled code
using the class `Ripper`. The lines shown bellow are different from the actual
compiled results in the C compiler, but this already allows us to have a way of
seeing how Ruby works.

Let's start with a very simple code:

```
code = <<STR
2 + 3
STR

puts code
puts RubyVM::InstructionSequence.compile(code).disasm
```

The result of running this script is shown bellow:

```shell
2 + 3
== disasm: #<ISeq:<compiled>@<compiled>:1 (1,0)-(1,5)> (catch: FALSE)
0000 putobject                   2                         (   1)[Li]
0002 putobject                   3
0004 opt_plus                    <calldata!mid:+, argc:1, ARGS_SIMPLE>
0006 leave
```

The first instruction sends an integer (2) to the execution stack, the second, passes
the argument (integer 3). Finally, the third instruction sends the method `+`
(`mid:+`, which means something like `method_id = +`). Just for comparison, if we
switch the operation to multiplication, the line would look like this:

```shell
opt_mult                       <calldata!mid:*, argc:1, ARGS_SIMPLE>
```

Now let's compile the initial script, the `each` block:

```shell
[1, 3].each do |e|
  p e
end
== disasm: #<ISeq:<compiled>@<compiled>:1 (1,0)-(3,3)> (catch: FALSE)
== catch table
| catch type: break  st: 0000 ed: 0005 sp: 0000 cont: 0005
| == disasm: #<ISeq:block in <compiled>@<compiled>:1 (1,12)-(3,3)> (catch: FALSE)
| == catch table
| | catch type: redo   st: 0001 ed: 0006 sp: 0000 cont: 0001
| | catch type: next   st: 0001 ed: 0006 sp: 0000 cont: 0006
| |------------------------------------------------------------------------
| local table (size: 1, argc: 1 [opts: 0, rest: -1, post: 0, block: -1, kw: -1@-1, kwrest: -1])
| [ 1] e@0<Arg>
| 0000 nop                                                       (   1)[Bc]
| 0001 putself                                                   (   2)[Li]
| 0002 getlocal_WC_0                          e@0
| 0004 opt_send_without_block   <calldata!mid:p, argc:1, FCALL|ARGS_SIMPLE>
| 0006 nop
| 0007 leave                                                     (   3)[Br]
|------------------------------------------------------------------------
0000 duparray                               [1, 3]               (   1)[Li]
0002 send                  <calldata!mid:each, argc:0>, block in <compiled>
0005 nop
0006 leave                                                       (   1)
```

Each line that starts with `== disasm:` indicates a scope, and each scope can
have it's own local table (`local table (...)`). To facilitate visualization,
the lines bellow will focus only on the scope of the `each` block.

```shell
 == disasm: #<ISeq:block in <compiled>@<compiled>:1 (1,12)-(3,3)> (catch: FALSE)
 == catch table
 | catch type: redo   st: 0001 ed: 0006 sp: 0000 cont: 0001
 | catch type: next   st: 0001 ed: 0006 sp: 0000 cont: 0006
 |------------------------------------------------------------------------
 local table (size: 1, argc: 1 [opts: 0, rest: -1, post: 0, block: -1, kw: -1@-1, kwrest: -1])
 [ 1] e@0<Arg>
 0000 nop                                                       (   1)[Bc]
 0001 putself                                                   (   2)[Li]
 0002 getlocal_WC_0                          e@0
 0004 opt_send_without_block   <calldata!mid:p, argc:1, FCALL|ARGS_SIMPLE>
 0006 nop
 0007 leave                                                     (   3)[Br]
```

You can see the `e` variable with `getlocal_WC_0`, and next, the
method call `p` (`calldata!mid:p`). In the end, `leave` indicates the end of the
block. The vertical line of pipes (`|`) will give you a visual indication of the scopes.

Going back the rest of the compiled code we can see the method call `each` (with
`send`) to the array `[1, 3]` (`duparray`), which opens the previous code block.

Now that we can better understand how Ruby reads our code, we'll see how our
initial example scripts, with `for` and `each` are compiled. We will analyze and compare
them.

```
for e in [1, 3] do
  p e
end
== disasm: #<ISeq:<compiled>@<compiled>:1 (1,0)-(3,3)> (catch: FALSE)
== catch table
| catch type: break  st: 0000 ed: 0005 sp: 0000 cont: 0005
| == disasm: #<ISeq:block in <compiled>@<compiled>:1 (1,0)-(3,3)> (catch: FALSE)
| == catch table
| | catch type: redo   st: 0005 ed: 0010 sp: 0000 cont: 0005
| | catch type: next   st: 0005 ed: 0010 sp: 0000 cont: 0010
| |------------------------------------------------------------------------
| local table (size: 1, argc: 1 [opts: 0, rest: -1, post: 0, block: -1, kw: -1@-1, kwrest: -1])
| [ 1] ?@0<Arg>
| 0000 getlocal_WC_0                          ?@0                (   1)
| 0002 setlocal_WC_1                          e@0
| 0004 nop                                    [Bc]
| 0005 putself                                                   (   2)[Li]
| 0006 getlocal_WC_1                          e@0
| 0008 opt_send_without_block   <calldata!mid:p, argc:1, FCALL|ARGS_SIMPLE>
| 0010 nop
| 0011 leave                                                     (   3)[Br]
|------------------------------------------------------------------------
local table (size: 1, argc: 0 [opts: 0, rest: -1, post: 0, block: -1, kw: -1@-1, kwrest: -1])
[ 1] e@0
0000 duparray                               [1, 3]               (   1)[Li]
0002 send                  <calldata!mid:each, argc:0>, block in <compiled>
0005 nop
0006 leave                                                       (   1)
```

Let's focus on the lines bellow to make things easier:

```shell
|------------------------------------------------------------------------
local table (size: 1, argc: 0 [opts: 0, rest: -1, post: 0, block: -1, kw: -1@-1, kwrest: -1])
[ 1] e@0
0000 duparray                               [1, 3]               (   1)[Li]
0002 send                  <calldata!mid:each, argc:0>, block in <compiled>
0005 nop
0006 leave                                                       (   1)
```

Note that the array definition and the method call are exactly the same as the result
from the `each` script compilation. This means that, after being "tokenized" and
interpreted, the `for` script is translated just like the `each` script.
However, not everything is exactly the same. Bellow we'll present the compiled
`each` script for comparison:

```shell
|------------------------------------------------------------------------
0000 duparray                               [1, 3]               (   1)[Li]
0002 send                  <calldata!mid:each, argc:0>, block in <compiled>
0005 nop
0006 leave
```

Notice that there is no local table or `e` variable definition. This means that,
when we use `for`, a variable is created **outside** of the scope, and it
will be used inside the block. That structure has implications when the code
ran. Let's take a look at another example:

```ruby
arr = [1, 2, 3]
for elem in arr do
  puts elem
end

# elem is accessible outside of the for scope
elem # => 3
```

```ruby
arr = [1, 2, 3]
arr.each { |elem| puts elem }

# elem is not accessible outside the each scope
elem # => NameError: undefined local variable or method `elem'
```

This means that `for` behaves differently from `each` and that can affect
variables that were already defined outside of the scope with the same name. For
exemple:

```ruby
i = "a"

for i in [2,3] do
  p i
end
2
3
# => [2, 3]

i # => 3
```

Variable `i` changes after the `for` block.

```ruby
i = "a"

[2, 3].each { |i| p i }
2
3
# => [2, 3]

i # => "a"
```

Variable `i` stays the same after the `each` block.

If you would like to see a more detailed example of the side effects of the `for`
construct in Ruby, you can checkout the article
[“The Evils of the For Loop”](https://graysoftinc.com/early-steps/the-evils-of-the-for-loop).

## Conclusion

In this article we summarize the three steps of the process Ruby uses to read
your code up to the instructions to be executed. Knowing this allows us to
better understand how the programming language works and, potentially, helps us
evolve as programmers.

In addition to that, we analyzed how Ruby processes `for` and `each` calls and
the differences between them, specially how the use of the first can lead to
unwanted side effects.

**References:**

- [The evils of the for Loop](http://graysoftinc.com/early-steps/the-evils-of-the-for-loop)
- [Livro: Ruby Under a Microscope](https://patshaughnessy.net/ruby-under-a-microscope)
