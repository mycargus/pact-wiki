To generate markdown documentation from the pact files, in the `pact_helper.rb` file in the consumer project configure:

```ruby
Pact.configure do | config |
  config.doc_generator = :markdown
  config.doc_dir = "..." # optional, defaults to "./doc/pacts" 
end
```

If you would like another type of documentation (eg. HTML), you can roll your own, and configure the `doc_generator` with anything that responds to `call` and accepts two String arguments (the `pact_dir` and the `doc_dir`). See the [doc](https://github.com/realestate-com-au/pact/tree/master/lib/pact/doc) module in the Pact gem, and the [HTML renderer](https://github.com/bethesque/pact_broker/blob/master/lib/pact_broker/api/renderers/html_pact_renderer.rb) in the Pact Broker for examples.


An example of the generated documentation follows:
* * *

### A pact between Zoo App and Animal Service

#### Requests from Zoo App to Animal Service

* [A request for an alligator](#a_request_for_an_alligator_given_there_is_an_alligator_named_Mary) given there is an alligator named Mary

* [A request for an alligator](#a_request_for_an_alligator_given_there_is_not_an_alligator_named_Mary) given there is not an alligator named Mary

* [A request for an alligator](#a_request_for_an_alligator_given_an_error_occurs_retrieving_an_alligator) given an error occurs retrieving an alligator

#### Interactions

<a name="a_request_for_an_alligator_given_there_is_an_alligator_named_Mary"></a>
Given **there is an alligator named Mary**, upon receiving **a request for an alligator** from Zoo App, with
```json
{
  "method": "get",
  "path": "/alligators/Mary",
  "headers": {
    "Accept": "application/json"
  }
}
```
Animal Service will respond with:
```json
{
  "status": 200,
  "headers": {
    "Content-Type": "application/json;charset=utf-8"
  },
  "body": {
    "name": "Mary"
  }
}
```
<a name="a_request_for_an_alligator_given_there_is_not_an_alligator_named_Mary"></a>
Given **there is not an alligator named Mary**, upon receiving **a request for an alligator** from Zoo App, with
```json
{
  "method": "get",
  "path": "/alligators/Mary",
  "headers": {
    "Accept": "application/json"
  }
}
```
Animal Service will respond with:
```json
{
  "status": 404
}
```
<a name="a_request_for_an_alligator_given_an_error_occurs_retrieving_an_alligator"></a>
Given **an error occurs retrieving an alligator**, upon receiving **a request for an alligator** from Zoo App, with
```json
{
  "method": "get",
  "path": "/alligators/Mary",
  "headers": {
    "Accept": "application/json"
  }
}
```
Animal Service will respond with:
```json
{
  "status": 500,
  "headers": {
    "Content-Type": "application/json;charset=utf-8"
  },
  "body": {
    "error": "Argh!!!"
  }
}
```