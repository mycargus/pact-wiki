In the `pact_helper.rb` file in the consumer project:

```ruby
Pact.configure do | config |
  config.doc_generator = :markdown
end
```