# Overview

Interactions (ie. a request/response pair) are matched in two places:

1. The incoming request is matched in the mock server, to ensure the request received fits what is expected.
  * The request part of the interaction is verified here.

2. The request is read from the pact file, replayed against the provider and the response is matched against the expected response.
  * The response part of the interaction is verified here.

# Request:
Requests are checked on 5 parameters:
* Method e.g. POST, GET
* Path e.g. /user
* Queries (URL parameters) e.g. ?id=123
* Header  e.g.  {'Content-Type' => 'application/x-www-form-urlencoded' }
* Body

When a field is not specified, any value is accepted. When a fixed value is provided, only an exact match is accepted. For flexible matching, read on.

## Query
When a query is specified as a string, then it will be matched exactly and order matters. However, a query can also be specified as a hash, in which case, the order of parameters does not matter. In the special case of multiple parameters having the same key, the order of these will be checked.

```ruby
animal_service.given("an alligator named Mary exists").
  upon_receiving("a request for an alligator").
  with(
    method: "get",
    path: "/visitors", 
    headers: {"Accept" => "application/json"}
    query: { 
      animal: "zebra", 
      height: ["tall"], #single parameters, can be specified as a string or as a 1 element array
      colour: ["black", "white"] #duplicate parameters need to be specified as an array, order will matter
  ).
  will_respond_with(
    status: 200,
    headers: {"Content-Type" => "application/json"},
    body: {
      name: "Mary",
    })
```
The below requests will all succeed:
```
GET /visitors?animal=zebra&height=tall&colour=black&colour=white 
GET /visitors?colour=black&height=tall&colour=white&animal=zebra
```
however
```
GET /visitors?animal=zebra&height=tall&colour=white&colour=black 
```
will be rejected, as zebras are black and white, not white and black.

More complex cases can be handled by using a Pact::Term [**](#footnote), which allows you to provide a regular expression. In the example below, any word will be accepted for height.
```ruby
  query: { 
    animal: "zebra", 
    height: Pact::Term.New(matcher: /\w/, generate: "tall")
  })
```


## Body
Body matching depends on content type. If the content type is specified as JSON, the body is automatically JSON parsed, and then a JSON comparison is made, rather than a String comparison.

If the Content-Type is 'application/x-www-form-urlencoded', then the body will be matched as a form, where the order of the parameters does not matter. The body can be specified either as a string or as a hash.

```ruby
      zebra_service.
        given("the zebras like using forms").
        upon_receiving("a create Mallory request").with({
          method: :post,
          path: '/mallory',
          headers: { 'Content-Type' => 'application/x-www-form-urlencoded' },
          body: {
            param1: Pact::Term.new(generate: 'woger', matcher: /w/),
            param2: 'penguin'
          }
        }).
        will_respond_with(
          status: 200
        )
```

Note:

* Most tools set the content type to 'application/x-www-form-urlencoded' when it is not otherwise specified. Pact will only accept an order agnostic form body when the Content-Type 'application/x-www-form-urlencoded' is specified. 
* Some servers accept form-data when it is provided as query instead of body. Pact makes the distinction between body and parameters. 

<a name="footnote">**</a> Pact::Term are described in detail [here][regular_expression_matching]. Note there are no restrictions in using Pact::Term in the queries or in the body request as mentioned here. However, when using Pact::Term in the body of the response, then pact verification is limited to the ruby implementation or using [pact-provider-proxy][pact-provider-proxy].

[pact-provider-proxy]: https://github.com/bethesque/pact-provider-proxy
[regular_expression_matching]: Regular-expressions-and-type-matching-with-Pact