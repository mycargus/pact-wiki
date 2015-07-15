You can now use regular expressions and type based matching between JVM and Ruby consumers and providers! Also new in v2 matching is the ability to specify flexible length arrays. For example:

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
      children: each_like(name: "Fred", age: 2)
    })
```

When the provider verification is run, it will ensure that each of the elements in the `children` array has a String name, an Integer age, and that there is at least one element in the array.

To turn the v2 Pact serialisation on, you will need version 1.9.0 of the pact gem, and to configure the `pact_specification_version` in the mock service configuration in the consumer. The provider will pick it up automatically.

```ruby

require 'pact/consumer/rspec'

Pact.service_consumer "Zoo App" do
  has_pact_with "Animal Service" do
    mock_service :animal_service do
      port 1234
      pact_specification_version "2.0.0"
    end
  end
end
```
