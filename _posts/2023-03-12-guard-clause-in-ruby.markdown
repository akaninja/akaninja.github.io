---
layout: post
title: "Guard Clauses in Ruby"
date: 2023-03-12
abstract: "Let's see how this early return strategy works in Ruby."
categories: ruby tips
---
> Você pode ler uma versão desse artigo em português [aqui](https://campuscode.com.br/conteudos/guard-clause-em-ruby)

*Guard clause* is the term used in Ruby for a code structure in programming,
also known as *early return*. This pattern is widelly used to reduce `if else`
blocks of code. Let's take a look at an example:

 ```ruby
def full_name(name, last_name, title)
  if title.present?
    "#{title} #{name} #{last_name}"
  else
    "#{name} #{last_name}"
  end
end
```

In this Ruby code there is a `full_name` method, which receives `name`,
`last_name` and `title`. If `title` is present, then we want this
method to return the full name with the title, otherwise, it should
return just name and last name.

Now let's see how this could be written if a *guard clause* was used.

 ```ruby
def full_name(name, last_name, title)
  return "#{title} #{name} #{last_name}" if title.present?

  "#{name} #{last_name}"
end
```

You can see in the code above that the *guard clause* was used to stop
execution and return the full name with title immediately. The lines bellow are
skipped.

You can also use guard clauses just to skip blocks of code:

```ruby
def send_welcome_email(user)
  return if user.nil? 
  # we avoid calling the lines bellow if user is absent

  user.send_email('Welcome!')
  logger.info("E-mail sent successfully to #{user.email}")
end
```


You can also have multiple *guard clauses* within a block of code. In the
example bellow, the `event_fee` method must return the fee rate, taking in
consideration a couple of rules:

```ruby
def calculate_fee(event, user)
  return 0 if event.free?
  return 5 if user.vip?

  10
end
```

And that is it!
