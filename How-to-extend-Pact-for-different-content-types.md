## 1. Make a differ

This must return a structure representing the differences between the expected and the actual content.

eg. The existing Pact [JSON Differ](https://github.com/bethesque/pact-support/blob/master/lib/pact/shared/json_differ.rb) and the bare bones of the [XML Differ here](https://github.com/bethesque/pact-xml/blob/master/lib/pact/xml/differ.rb)

## 2. Make a diff formatter

This should accept the diff created by the differ, and turn it into a string in a human readable format. Some examples are the [UnixDiffFormatter](https://github.com/bethesque/pact-support/blob/master/lib/pact/matchers/unix_diff_formatter.rb) and the [EmbeddedDiffFormatter](https://github.com/bethesque/pact-support/blob/master/lib/pact/matchers/embedded_diff_formatter.rb). For an example of how the diffs are represented, have a look at the [documentation](https://github.com/realestate-com-au/pact/blob/master/documentation/configuration.md#diff_formatter).

## 3. Configure the matching with the differ and formatter

```ruby
require 'pact'
require 'pact/xml'

Pact.configure do | config |

    config.register_body_differ /xml/, Pact::XML::Differ
    config.register_diff_formatter /xml/, Pact::XML::DiffFormatter

end
```
