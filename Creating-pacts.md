## With RSpec

Follow the instructions in the [README](https://github.com/realestate-com-au/pact), this is the default behaviour.

## With Capybara

Configure your Rack application to be started by Pact, and tell it what port to run on.

```ruby
Pact.service_consumer "My Consumer" do
  app your_rack_app
  port 8001
  has_pact_with "My Provider" do
    ...
  end
end

```

## With Minitest

Use [pact-consumer-minitest](https://github.com/bethesque/pact-consumer-minitest).


