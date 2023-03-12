---
layout: post
title: "Loop Control With Next and Break in Ruby"
date: 2023-03-12
abstract: "In this article we see how next and break methods work in Ruby."
categories: ruby tips
---

> Você pode ler uma versão desse artigo em português [aqui](https://campuscode.com.br/conteudos/controle-de-loops-com-next-e-break-em-ruby)

## Introduction

In programming we can use `next` and `break` to control a running *loop*. While
`break` stops the loop immediatly, `next` will skip the current iteration,
jumping to the next. In this article we will use the code bellow as our base
example to further analyze how these methods work:

```ruby
list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
list.map do |item|
  puts item
end

# 1
# 2
# 3
# 4
# 5
# 6
# 7
# 8
# 9
# 10
# => [nil, nil, nil, nil, nil, nil, nil, nil, nil, nil]
```

An *array* is defined and we iterate through it, printing in the screen
`item` values. In the end, the full result of the iteration is returned.

Let's start by showing how `break` works.

## Break

In the code bellow, `break` was included to interrupt the *looop* when the item
with the value `5` is reached.

```ruby
list.map do |item|
  break if item == 5
  puts item
end

# 1
# 2
# 3
# 4
# => nil
```

Notice how only the numbers `1` through `4` are printed. After all, the loop was
interrupted at `5`. And, in the end, `nil` is returned as the result of the
iteration. If you want to define the return value, you can pass it as an
argument to the `break` method.

### Passing arguments to break method

Consider the example bellow:

```ruby
list.map do |item|
  break item if item == 5
  puts item
end

# 1
# 2
# 3
# 4
# => 5
```

In this way, `break` returns the value of `item` when the loop is interrupted.

You might be wondering why not use `return` instead. If we implement a new
method with a loop and use `return` to stop it, the whole method execution is
interrupted, not just the loop. `break` will stop the loop and continue with the
rest of the method execution.

## Next

Unlike `break`, the `next` method won't stop the loop entirely, it will just
skip the current iteration and move to the next:

```ruby
list.map do |item|
  next if item.odd?
  puts item
end

# 2
# 4
# 6
# 8
# 10
# => [nil, nil, nil, nil, nil, nil, nil, nil, nil, nil]
```

Notice that with each iteration, `next` prevents odd numbers from being printed.
In the end, an array with `nil` values is returned. Now let's see how the method
behaves when arguments are passed.

### Passing arguments to next method

Consider the example below:

```ruby
list.map do |item|
  next item if item.odd?
  puts item
end

# 2
# 4
# 6
# 8
# 10
# => [1, nil, 3, nil, 5, nil, 7, nil, 9, nil] 
```

In this way, the same values are printed in screen, however the returned *array*
contains every odd value from the original `list` and `nil` where each even
value would be.

Since `next` is implemented in classes like `String` and `Integer`, it is
possible to do things like this:

```ruby
41.next
# => 42

"a".next
# => "b"
```

## Conclusion

`next` and `break` are great tools to control exactly how you want your loops to
behave. :)

## References

- [next](https://ruby-doc.org/docs/keywords/1.9/Object.html#method-i-next)
- [break](https://ruby-doc.org/docs/keywords/1.9/Object.html#method-i-break)
- [return](https://ruby-doc.org/docs/keywords/1.9/Object.html#method-i-return)
