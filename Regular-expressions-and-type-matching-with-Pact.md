# Note 

Version 2 matching has been released, see docs [here](https://github.com/realestate-com-au/pact/wiki/v2-flexible-matching)
 
# Regular expressions
Sometimes you will have keys in a request or response with values that are hard to know beforehand - timestamps and generated IDs are two examples.

What you need is a way to say "I expect something matching this regular expression, but I don't care what the actual value is". If you are using the Ruby implementation of Pact for both the Consumer and the Provider, or if you are using regular expressions only in the request part and not the answer, then you are in luck! [**](#footnote)

```ruby
animal_service.given("an alligator named Mary exists").
  upon_receiving("a request for an alligator").
  with(
    method: "get",
    path: "/alligators/Mary", 
    headers: {"Accept" => "application/json"}).
  will_respond_with(
    status: 200,
    headers: {"Content-Type" => "application/json"},
    body: {
      name: "Mary",
      dateOfBirth: Pact::Term.new(
        generate: "02/11/2013", 
        matcher: /\d{2}\/\d{2}\/\d{4}/)
    })
```

Note the use of the `Pact::Term`. When you run the Consumer tests, the mock server will return the value that you specified to "generate", and when you verify the pact in the Provider codebase, it will ensure that the value at that key matches the specified regular expression.

You can also use `Pact::Term` for request matching.

```ruby
animal_service.given("an alligator named Mary exists").
  upon_receiving("a request for an alligator").
  with(
    method: "get",
    path: "/alligators/Mary", 
    query: Pact::Term.new(
      generate: "transactionId=1234", 
      matcher: /transactionId=\d{4}/),
  will_respond_with(
    status: 200, ...)
```

The `matcher` will be used to ensure that the actual request query was in the right format, and the value specified in the `generate` field will be the one that is replayed against the provider as an example of what a real value would look like. This means that your provider states can still use known values to set up their data, but your Consumer tests can generate data on the fly.

You can use `Pact::Term` for request and response header values, the request query, and inside request and response bodies. Note that regular expressions can only be used on Strings. Furthermore, request queries, when specified as strings are just matched as normal String - no flexible ordering of parameters is catered for. For flexible ordering, specify it as a Hash, which in turn may include `Pact::Terms`


<a name="footnote">**</a> (Unfortunately, this technique involves serialising Ruby specific JSON, so when using in the response content, it will not work with any of the other Pact implementations. You will need to use [pact-provider-proxy][pact-provider-proxy] to verify against non Ruby servers. Hang around for v2 of the [Pact Specification](https://github.com/bethesque/pact-specification) for cross language regular expressions.)

# Type matching

Often, you will not care what the exact value is at a particular path is, you just care that a value is present and that it is of the expected type. For this scenario, you can use `Pact::SomethingLike`.

```ruby
animal_service.given("an alligator named Mary exists").
  upon_receiving("a request for an alligator").
  with(
    method: "get",
    path: "/alligators/Mary", 
    headers: {"Accept" => "application/json"}).
  will_respond_with(
    status: 200,
    headers: {"Content-Type" => "application/json"},
    body: {
      Pact::SomethingLike.new(
        name: "Mary",
        age: 73
      )
    })
```

The mock server will return `{"name": "Mary", "age": 73}` in the consumer tests, but when `pact:verify` is run in the provider, it will just check that the type of the `name` value is a String, and that the type of the `age` value is a Fixnum. If you wanted an exact match on "Mary", but to allow any age, you would only wrap the `73` in the `Pact::SomethingLike`.

For request matching, the mock server will allow any values of the same type to be used in the consumer test, but will replay the given values in `pact:verify`.

```ruby
animal_service.given("an alligator named Mary exists").
  upon_receiving("a request to update an alligator").
  with(
    method: "put",
    path: "/alligators/Mary", 
    headers: {"Accept" => "application/json"},
    body: {
      age: SomethingLike.new(10)
    }).
  will_respond_with(
    status: 200,
    headers: {"Content-Type" => "application/json"},
    body: {
      age: 10
    })
```

Like regular expressions, `SomethingLike` is a Ruby specific feature, and will only work if your provider is also a Ruby project, or you are using [pact-provider-proxy] to verify. Type based matching will be incorporated as part of the v2 Pact Specification, and will then be cross language compatible.

# Query params

Query params can be specified as a string or a hash.
When specified as a string, an exact match will be performed. You may use a Pact::Term, but only over the query string as a whole. Note that the query params must already be URL encoded in the expectation. (This will change for v2 matching.)

```ruby

animal_service.given("some alligators exist").
  upon_receiving("a request to search for alligators").
  with(
    method: "get",
    path: "/alligators",
    query: "name=Mary+Jane&age=8")
  ...
    
```

Alternatively, if the order of the query parameters does not matter, you can specify the query as a hash. You can embed Pact::Terms or Pact::SomethingLike inside the hash. Remember that all query params will be parsed to strings, so don't use a SomethingLike with a number.

```ruby

animal_service.given("some alligators exist").
  upon_receiving("a request to search for alligators").
  with(
    method: "get",
    path: "/alligators",
    query: {
      # No need to encode params in the hash
      name: 'Mary Jane',
      age: '8',
      # Specify a param with multiple values using an 
      # array - order will be enforced
      children: ['Betty', 'Sue'] 
    })
  ...
    
```


[pact-provider-proxy]: https://github.com/bethesque/pact-provider-proxy