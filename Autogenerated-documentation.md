To generate markdown documentation from the pact files, in the `pact_helper.rb` file in the consumer project configure:

```ruby
Pact.configure do | config |
  config.doc_generator = :markdown
  config.doc_dir = "..." # optional, defaults to "./doc/pacts" 
end
```

