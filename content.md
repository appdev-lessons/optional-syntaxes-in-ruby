# Different Ruby styles

Ruby, unlike many other languages, often supports multiple different syntax styles to achieve the same thing. This is both good and bad. It's good because it lets us choose a style that we are most comfortable with, but it's bad because we need to learn to at least recognize all of the styles so that we can read other people's code.

Here are a few optional syntax styles to become familiar with:

## No parentheses around arguments

When we call a method that has arguments, we put the arguments within parentheses (`()`) immediately next to the method name (with no space between the method name and the opening parenthesis):

```ruby
pp "hello".gsub("l", "z")
```
{: .repl #with_parentheses title="with parentheses" points="1"}

Ruby will allow you to, optionally, drop the parentheses around arguments:

```ruby
pp "hello".gsub "l", "z"
```
{: .repl #without_parentheses title="without parentheses" points="1"}

It's important to remember **not to mix these two by putting a space before parentheses!**

```ruby
# If you're going to use parens, don't also have a space
pp "hello".gsub ("l", "z") # bad
```
{: .repl #no_whitespace title="no whitespace before parentheses" points="1"}

I like to include the parentheses around arguments because it makes it clear what's what, _especially_ when you are chaining multiple methods together. The only time that I drop the parentheses is if I am using trivial methods at the beginning of a line, like `pp`:

```ruby
# Rather than

pp("Hi there")

# I will usually do this instead:

pp "Hi there"
```
{: .repl #dropping_parentheses title="dropping parentheses" points="1"}


## Curly braces around hash arguments

Consider a hypothetical method (I defined the `print_this` method in some hidden setup code) that accepts a Hash as its last argument:

```ruby
# define a method
def print_this(first_arg, second_arg, third_arg)
  puts "First argument: #{first_arg}"
  puts "Second argument: #{second_arg}"
  puts "Third argument: #{third_arg}"
end

# now use the method
print_this("a string", 2, { :this => "argument", :is => "a Hash" })
```
{: .repl #hash_argument_with_braces title="Hash argument with curly braces" points="1" setup_code="1-7"}

How many arguments does the above method have? You might be tempted to say four or even six if you just count, but really it's three: a string is first, an integer is second, and the entire hash (`{ :this => "argument", :is => "a Hash" }`) is the _third_ and final argument.

In the case that a Hash literal is being used as the last argument to a method, you can optionally drop the curly braces around it:

```ruby
# define a method
def print_this(first_arg, second_arg, third_arg)
  puts "First argument: #{first_arg}"
  puts "Second argument: #{second_arg}"
  puts "Third argument: #{third_arg}"
end

# now use the method
print_this("a string", 2, :this => "argument", :is => "a Hash")
```
{: .repl #hash_argument_without_braces title="Hash argument without curly braces" points="1" setup_code="1-7"}

Now it _really_ looks like the method is taking more than three arguments, but it's not; Ruby can figure out from the hash rockets that the stuff at the end is really just one Hash.

**Note that you can _only_ drop the curly braces around a Hash in this one, very specific case** — if the Hash is the _last_ argument to a method. So this:

```ruby
# Creating a Hash literal and storing it in a variable:

my_hash = { :fruit => "banana", :sport => "hockey" }

pp my_hash
```
{: .repl #hash_literal_with_braces title="Hash literal with curly braces" points="1"}

is **not** the same as this:

```ruby
# The below is nonsensical, as far as Ruby is concerned:

my_hash = :fruit => "banana", :sport => "hockey"

pp my_hash

# You can't drop the curly braces around the Hash unless it is the last argument to a method.
```
{: .repl #hash_literal_without_braces title="Hash literal without curly braces" points="1"}

## New Hash Syntax

Consider a `Hash`:

```ruby
my_hash = { :fruit => "banana", :sport => "hockey" }

pp my_hash 
```
{: .repl #hash_syntax_1 title="New Hash syntax, 1" points="1"}

We can also (as of Ruby version 1.9) write the same thing as:

```ruby
my_hash = { fruit: "banana", sport: "hockey" }

pp my_hash 
```
{: .repl #hash_syntax_2 title="New Hash syntax, 2" points="1"}

In other words, if the key in a key-value pair in a hash is a `Symbol`, you can move the colon (`:`) from the beginning of the symbol to the end of the symbol, and get rid of the hash rocket (`=>`). This is known as the "new hash syntax" (even though Ruby 1.9 is pretty old by now).

Consider the following Hash:

```ruby
my_hash = { 1 => "pink", 2 => "cookies" }

pp my_hash
```
{: .repl #hash_syntax_3 title="New Hash syntax, 3" points="1"}

Can we do the same trick and write this as:

```ruby
my_hash = { 1: "pink", 2: "cookies" }

pp my_hash
```
{: .repl #hash_syntax_4 title="New Hash syntax, 4" points="1"}

No! The "new" hash syntax is for when the keys are `Symbol`s — then you can swing the `Symbol`'s colon around to the other side of it.

## Putting it all together

Consider a method that is designed like the following (very common in Rails); it has one or two arguments followed by a `Hash` in the last position:

```ruby
get("/rock", { :controller => "moves", :action => "play_rock" })
```

Since the keys are symbols, we can use the new Hash syntax:

```ruby
get("/rock", { controller: "moves", action: "play_rock" })
```

Since the Hash is the final argument to the `get()` method, we can drop the curly braces:

```ruby
get("/rock", controller: "moves", action: "play_rock" )
```

And since there are no order-of-operations concerns, we can drop the parentheses around the arguments:

```ruby
get "/rock", controller: "moves", action: "play_rock"
```

Much more concise! Again, I personally prioritize readability far above brevity, so I like making things explicit rather than concise. However, when you are reading Ruby code out in the wild (on Stack Overflow or gem READMEs on GitHub), you will most often encounter code using all of these shortcuts, so you have to know how to read it.

Most importantly, the Rails team has adopted the above optional syntaxes as their default, so when you're reading the [Rails Guides](https://guides.rubyonrails.org/), you will often see things like this (these are made up methods and names, just to illustrate):

```ruby
some_method :zebra, uniqueness: { scope: :year, message: "howdy" }, presence: true
```

Holy moly! What's going on here? You just have to unwind each optional syntax one by one to get to something more familiar.

First of all, we dropped the parentheses around the arguments to `some_method()`. We can replace them:

```ruby
some_method(:zebra, uniqueness: { scope: :year, message: "howdy" }, presence: true)
```

Next: whenever you see a colon at the end of a token, you know it's the new Hash syntax. So we can unwind it by moving the colons to the beginning and putting back the Hash rockets:

```ruby
some_method(:zebra, :uniqueness => { :scope => :year, :message => "howdy" }, :presence => true)
```

Notice that I didn't (and can't) move the colon in `:zebra` — it's already at the beginning, and this Symbol is not being used as the key in a Hash. It is simply the first argument to `some_method()`. Similarly, the  Symbol`:year` is the _value_ associated to the key `:scope`, so we leave it alone.

Next: the second and last argument to `some_method()` is a Hash with two keys (`:uniqueness` and `:presence`), so the we dropped the curly braces around it. We can put them back:

```ruby
some_method(:zebra, { :uniqueness => { :scope => :year, :message => "howdy" }, :presence => true })
```

We might also take advantage of whitespace to indent more helpfully:

```ruby
some_method(
  :zebra, 
  { 
    :uniqueness => { 
      :scope => :year, 
      :message => "howdy"
    }, 
    :presence => true 
  }
)
```

Now that we've fully unwound the optional syntaxes, it's easier to see that:

 - `some_method()` is taking two arguments; the first is a Symbol and the second is a Hash.
 - The value associated with the `:uniqueness` key is itself another, nested, Hash: `{ :scope => :year, :message => "howdy" }`.
 - That Hash has two keys in it: `:scope` and `:message`.
 - We cannot drop the curly braces around `{ :scope => :year, :message => "howdy" }` because **it is not the last argument to a method** — it is the value associated to the key `:uniqueness` in a parent Hash. If we tried to drop them, we'll run into problems:
 
```ruby
some_method(:zebra, { :uniqueness => :scope => :year, :message => "howdy", :presence => true })
```
    
Ruby can't make sense of `:uniqueness => :scope => :year`, and can't tell which things are in the inner Hash vs. the outer Hash.

So: don't fret when you see seemingly unfamiliar syntax like `scope: :year` — it's nothing new, it's just a different style. You've got the tools you need to understand any Ruby you encounter.

## Single line blocks

Often, we only have one line of code within our `do`/`end` blocks:

```ruby
numbers = [8, 3, 1, 4, 3, 9]

numbers.each do |num|
  pp num
end
```
{: .repl #blocks_1 title="Single line blocks, 1" points="1"}

If you wanted to, you could fit it all on one line:

```ruby
numbers = [8, 3, 1, 4, 3, 9]

numbers.each do |num| pp num end
```
{: .repl #blocks_2 title="Single line blocks, 2" points="1"}

You can also replace the `do` with an opening curly bracket (`{`) and the `end` with a closing curly bracket (`}`):

```ruby
numbers = [8, 3, 1, 4, 3, 9]

numbers.each { |num| pp num }
```
{: .repl #blocks_3 title="Single line blocks, 3" points="1"}

This can get confusing, because now we're overloading the curly braces (`{}`) for more than one purpose: Hash literals _and_ blocks. However, it's a very common style so you should get used to figuring out which one it is from the context. If the curly braces are next to a method and there are no key/value pairs, then it's a block, not a Hash. 

This style is most often used when chaining more methods:

```ruby
numbers = [8, 3, 1, 4, 3, 9]

numbers.select { |num| num > 3 }.sort

# => [4, 8, 9]
```

---

Ruby wants developers to be happy, so it gives them lots of options — this is a double-edged sword! It means we can be expressive, but it also means we need to be able to parse many different styles when reading other people's writing. As beginners, it can be frustrating; but in the long-run, you'll appreciate it.
