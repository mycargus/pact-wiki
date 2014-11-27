## Gotchas

* Be aware when using the app from the config.ru file is used (the default option) that the Rack::Builder.parse_file seems to require files even if they have already been required, so make sure your boot files are idempotent.

## Show full backtrace for pact:verify

Add the following to your `pact_helper.rb`

```ruby
RSpec.configure do |config|
  config.full_backtrace = true
end

```