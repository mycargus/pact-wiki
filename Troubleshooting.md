## Gotchas

* Be aware when using the app from the config.ru file is used (the default option) that the Rack::Builder.parse_file seems to require files even if they have already been required, so make sure your boot files are idempotent.

## Matching problems

* If the match results don't make sense, check that your Content-Type header is set - the matcher used depends on the Content-Type. If the Content-Type doesn't match "application/.*json", then it will be using a text matcher, which will do an exact text match.

* Try using a different [diff formatter][diff-formatter]. Different people find the different formats easier to interpret.

## Provider

### Show full backtrace for pact:verify

    $ bundle exec rake pact:verify BACKTRACE=true

[diff-formatter]: https://github.com/realestate-com-au/pact/blob/master/documentation/configuration.md#diff_formatter
