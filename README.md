# Dynamic Web Apps with Rack

Making web apps that always give the same response are boring. Programming is fun because of it's ability to create dynamic responses that change depending any sort of input. A dynamic web app in Rack is pretty straightforward. Let's say we wanted to create a simple slots game. We first would set up our basic Rack app:

```ruby
class Application

  def call(env)
    resp = Rack::Response.new
    resp.write "Hello, World"
    resp.finish
  end

end
```

Then run it with `rackup config.ru`. If we go to `localhost:9292` in our browser, we should see "Hello, World". This is just so boring. Let's liven things up a bit. The amazing part of Rack and everything (like Rails) that is built on top of Rack is that it's *just Ruby*. If you were writing a command line slots game generator, you would first need to generate three numbers between 1 and 20. You could do that like this:

```ruby
num_1 = Kernel.rand(1..20)
num_2 = Kernel.rand(1..20)
num_3 = Kernel.rand(1..20)
```

Then, to check to see if you won or not, we'd have an if statement like this:

```ruby
num_1 = Kernel.rand(1..20)
num_2 = Kernel.rand(1..20)
num_3 = Kernel.rand(1..20)

if num_1==num_2 && num_2==num_3
  puts "You Win"
else
  puts "You Lose"
end
```

So how do we now make this application work on the web? Almost none of the code actually is specific to a command line interface. The only parts that require a command line are the two `puts` lines. All that needs to change to adopt this for the web is instead of `puts` we need a different way to express output to our user. Because this is the web, that means adding it to our response. Instead of `puts` now we are just going to use the `#write` method of our `Rack::Response` object.

Remember, to modify our web server, we have to first exit out of the running server by typing CTRL-C. Then open up your Application and modify it to look like this:

```ruby
class Application

  def call(env)
    resp = Rack::Response.new

    num_1 = Kernel.rand(1..20)
    num_2 = Kernel.rand(1..20)
    num_3 = Kernel.rand(1..20)

    if num_1==num_2 && num_2==num_3
      resp.write "You Win"
    else
      resp.write "You Lose"
    end

    resp.finish
  end

end
```

I only changed the `puts` statements into `resp.write` statements. That's it! Web servers are just big ruby apps, that respond to the user in an HTTP response rather then `puts`. Let's give the user a bit more information about what numbers they received, by writing the numbers to the response as well. The `#write` method can be called many times to build up the full response. The response isn't sent back to the user until `#finish` is called.

```ruby
class Application

  def call(env)
    resp = Rack::Response.new

    num_1 = Kernel.rand(1..20)
    num_2 = Kernel.rand(1..20)
    num_3 = Kernel.rand(1..20)

    resp.write "#{num_1}\n"
    resp.write "#{num_2}\n"
    resp.write "#{num_3}\n"

    if num_1==num_2 && num_2==num_3
      resp.write "You Win"
    else
      resp.write "You Lose"
    end

    resp.finish
  end

end
```

The `\n`s are just a special character which gets rendered by the browser as a new line. As if somebody had pressed the Return key. Kill your running server with CTRL-C and re-run it and refresh your browser. Feel free to cheat a bit and change the random numbers to just be between one and two. That way you can test that both work
