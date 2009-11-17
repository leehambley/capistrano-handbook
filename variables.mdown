Variables
=========

Capistrano variables work somewhat like regular Ruby variables, with a simple framework around them to make them easier to work with.

Setting Variables
-----------------

The simplest way to set a variable:

    set :application, "set your application name here"

This becomes available as the standard Ruby variable `application` available in all contexts, for string interpolation and other calculations. It also transcends scope, which is important for some of the internals of Capistrano

You can also set a variable that should be lazy-evaluated with the following:

    set :example, lambda { // code here only runs when you use `example` }

In this example, it is possible to define a variable early in the deploy process; which relies upon something not set until later, this is often useful when building variables which might rely on other environmental factors, or when you want to ask the user for the information using the `Capistrano::CLI.ui`, for example:

    set :branch, lambda { Capistrano::CLI.ui.ask "Branch Name: " }

For clarity, you could also use this syntax, which is often cleaner if you have to perform a lot of logic to derive your value:

    set :branch do
      Capistrano::CLI.ui.ask "Branch Name: "
    end

A great example of lazy-loading is how `:application` is used internally, it is considered to be part of the `:deploy_to` variable which is defined inside the gem; evaluated at usage-time to include your application name:

    set :deploy_to, lambda { "/u/apps/#{application}/" }

**It's worth noting whilst we think about it, that spaces in your application name cause crazy problems, and it has been known to break horribly - you should be aware of this when deciding what paths to use!**

Fetching Variables
------------------

Consider that for the following block, we have done the following:

    set :alpha, "Hello World"
    set :bravo, lambda { Capistrano::CLI.ui.ask "Cliché Please: " }

As these are regular ruby variables, you can query them as so:

    puts alpha
    => "Hello World"
    
    puts bravo
    # First queries the user for input, then ...
    => "the result is returned"

We can use the `fetch()` method to query these using the Capistrano DSL `fetch` takes two arguments, the first a symbol representation of the variable name, the second a default to return if the variable is not set; this defaults to nil. Here's an example

    puts fetch(:clarlie)
    => nil
    
    puts fetch(:charlie, alpha)
    => "Hello World"
    
Fetch uses another method, which strictly we consider to be part of the internal API, more-so than for public-use, this is called `exists?()` and takes one argument, a symbol representation of the variable name.

Setting variables, conditionally
--------------------------------

Using what we learned so far here, you can combine the techniques a handful of ways, here are some examples

    # Using Ruby Statement Modifiers
    set :delta, "Navy Seals!" unless delta

    # Using Capistrano's DSL
    set :delta, "Navy Seals" unless fetch(:delta)
    
    # Using our internal exists?()
    set :delta, "Navy Seals" unless exists?(:delta)
    
    # Using our internal _cset() 
    # – Not Recommended, this is internal, and may be removed in the future
    _cset(:delta, "Navy Seals")