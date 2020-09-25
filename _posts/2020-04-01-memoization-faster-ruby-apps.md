---
layout    : post
title     : "Memoization: Faster Ruby apps"
date      : 2020-04-01 12:00:00 -0500
categories: ruby
---

Once your application has lived enough to grow older, it tends to become slower with each new feature that includes, the performance impact that your code has in a certain programming language and/or platform is often overlooked, which might make you prone to blame those tools, instead of analyzing what and how your implementation made your application slower. Memoization may help you to improve your application's speed with a non-significant impact on readability or flexibility on your code.

## What is Memoization?
Memoization is a technique where you can cache a value (like a process result) to use it later without the need to compute that value again. This has the potential to save a massive amount of computing/networking power, thus improving the general performance of your application.

## How does it work in Ruby?
In Ruby, the easiest way to memoize a value is to assign it to an instance variable (since instance variables live long after an object method has finished).

{% highlight ruby %}
class Foo
  def bar
    return @bar if @bar

    @bar = 'expensive value'
  end
end

foo = Foo.new
foo.bar # => 'expensive value'
# You'll keep getting the same value as long the foo instance lives.
foo.bar # => 'expensive value'
{% endhighlight %}

You can make use of ruby's conciseness with the `||=` (or equals) operator. Since instance variables won’t raise an exception when accessed before being declared, you can use the “or equals” operator:

{% highlight ruby %}
class Foo
  def bar
    @bar ||= 'expensive value'
  end
end

foo = Foo.new
foo.bar # => 'expensive value'
foo.bar # => 'expensive value'
{% endhighlight %}

Here we are saying to Ruby:

“If you don’t have a truthy value for `@bar` variable, then assign it the value at the right, otherwise just return that value”

This is a simple example, but what would happen if instead of a string value, that assignment made a database query? Or an API call? A heavy object creation?

Here’s an example of a dummy user model with a dummy database connection object:

{% highlight ruby %}
class User
  def save
    connection.query(:user, :insert, props)
  end
 
  def destroy
    connection.query(:user, :destroy, id)
  end
 
  private
 
  def connection
    @connection ||= Database.new
  end
end
user = User.new(name: 'John')
# This will assign the connection object since @connection is nil.
user.save
# This will use the cached Database object in @connection.
user.destroy
{% endhighlight %}

## How to update cached values
There’s not a “clean” way of updating a cached value, the only way is to directly override the instance variable like so:

{% highlight ruby %}
def update_bar
  @bar = 'new bar'
end
{% endhighlight %}

Keep in mind that memoized values’ ideal use case is for **never-changing values.**

## Formatting and styling
When you need to memoize a not-so-straightforward value, you tend to write code like this:

{% highlight ruby %}
def bar
  return @bar if @bar

  step_value = some_method
  @bar = another_method(step_value).property
end
{% endhighlight %}

Here you need to pay special attention to what each line of code does, so you start asking questions like: 

“Did you finish the routine early because a certain value is not present?”

“Is this just an assignment method?”

You can certainly rewrite the code so it removes that unwanted cognitive load from you:

{% highlight ruby %}
def bar
  @bar ||= calculate_bar
end

def calculate_bar
  step_value = some_method
  another_method(step_value).property
end
{% endhighlight %}

This will work; it does what it says it does, but now you have created a rather verbose method to encapsulate an obvious task.

Finally, you can rewrite it, so it takes advantage of a Ruby block:

{% highlight ruby %}
def bar
  @bar ||= begin
    step_value = some_method
    another_method(step_value).property
  end
end
{% endhighlight %}

This way, the code clearly expresses its intention, and you didn’t need a special method for it, you just used a block that does what the previous `calculate_bar` method did. Now, within the same method, you read: 

“Oh, this is a memoized value”

“…And here is the source of that value”

## Final thoughts
As you may see, Memoization can improve your application’s performance, and the greatest thing about it is that it comes with a little to no-downside on your code readability.

Remember, to use it properly, you need to find the kind of calculated values and/or reused objects which are being processed expecting the same result over and over again in your application, then memoize them and enjoy your milliseconds of saved time!

## Notes
This is an updated version of this post:
[Memoization: Faster Ruby app](http://tangosource.com/blog/memoization-faster-ruby-app/)
