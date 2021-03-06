# Frequently asked questions

### Why doesn't Pact use JSON Schema?

Whether you define a schema or not, you will still need a concrete example of the response to return from the mock server, and a concrete example of the request to replay against the provider. If you just used a schema, then the code would have to generate an example, and generated values are not very helpful when used in tests, nor do they give any readable, meaningful documentation. If you use a schema *and* an example, then you are duplicating effort. The schema can almost be implied from an example. The ability to specify more flexible matching like "an array of any length" that is currently missing from v1 matching will be available in v2 matching (beta is out now, see [v2 flexible matching](https://github.com/realestate-com-au/pact/wiki/v2-flexible-matching)).

### Why does Pact use concrete JSON documents rather than using more flexible JSONPaths?

Pact was written by a team that was using microservices that had read/write RESTful interfaces. Flexible JSONPaths are useful when reading JSON documents, but no good for creating concrete examples of JSON documents to POST or PUT back to a service.

### Why is there no support for specifying optional attributes?

Firstly, it is assumed that you have control over the provider's data (and consumer's data) when doing the verification tests. If you don't, then maybe Pact is [not the best tool for your situation](https://github.com/realestate-com-au/pact#what-is-it-good-for).

Secondly, if you think about it, if Pact supports making an assertion that element `$.body.name` may be present in a response, then you write consumer code that can handle an optional `$.body.name`, but in fact, the provider gives `$.body.firstname`, no test will ever fail to tell you that you've made an incorrect assumption. Remember that a provider may return extra data without failing the contract, but it must provide at minimum the data you expect.

### Why are the pacts generated and not static?

* Maintainability: Pact is "contract by example", and the examples may involve large quantities of JSON. Maintaining the JSON files by hand would be both time consuming and error prone. By dynamically creating the pacts, you have the option to keep your expectations in fixture files, or to generate them from your domain (the recommended approach, as it ensures your domain objects and their JSON representations in the pacts can never get out of sync).

* Provider states: Dynamically setting expectations on the mock server allows the use of provider states, meaning you can make the same request in different tests, with different expected responses. This allows you to properly test all the code paths in your consumer (eg. with different response codes, or different states of the resource). If all the interactions were loaded at start up from a static file, the mock server wouldn't know which response to return. See this [gist](https://gist.github.com/bethesque/7fa8947c107f92ace9a4) as an example.

### What does PACT stand for?

It doesn't stand for anything. It is the word "pact", as in, another word for a contract. Google defines a "pact" as "a formal agreement between individuals or parties." That sums it up pretty well.

### How does Pact differ from VCR?

Pact is like VCR in reverse. VCR records actual provider behaviour, and verifies that the consumer behaves as expected. Pact records consumer behaviour, and verifies that the provider behaves as expected. The advantages Pact provides are:

* The ability to develop the consumer (eg. a Javascript rich client UI) before the provider (eg. the JSON backend API).
* The ability to drive out the requirements for your provider first, meaning you implement exactly and only what you need in the provider.
* Well documented use cases ("Given ... a request for ... will return ...") that show exactly how a provider is being used.
* The ability to see exactly which fields each consumer is interested in, allowing unused fields to be removed, and new fields to be added in the provider API without impacting a consumer. 
* The ability to immediately see which consumers will be broken if a change is made to the provider API.
* When using the [Pact Broker](https://github.com/bethesque/pact_broker), the ability to map the relationships between your services.

### How does Pact differ from Webmock?

Unlike Webmock:

* Pact provides verification that the responses that have been stubbed are actually the responses that will be returned in the given conditions.
* Pact runs a mock server in an actual process, rather than intercepting requests within the Ruby code, allowing Javascript rich UI clients to be tested in a browser.

### How does Pact differ from Pacto?

[Pacto][pacto] is another Ruby implementation of a library that provides a mock service and provider verification using consumer driven contracts. It differs from Pact in the following ways.

* Pacto has the ability to create contracts by recording interactions with an existing service. This makes the contracts easy to set up.
* Once the Pacto contracts are created, they are static, and are used to verify both the consumer and the provider. This would make it easy to determine whether a broken contract is due to a change in the consumer or a change in the provider.
* Pact's contracts are dynamically generated artefacts. This makes them easier to maintain.
* Pact allows you to make the same request with a different "provider state", allowing you to test different HTTP response codes for the same endpoint, or test the same resource in different states.
* Pact allows you to do regular expression matching.
* Pact has native support for Ruby, JVM, and .Net consumers, with a Javascript wrapper using the Ruby mock server.
* Pact has the [Pact Broker][pact_broker] which provides autogenerated documentation, network diagrams, and enables cross testing of the production and head versions of your consumer and provider, allowing you to decouple your consumer and provider release cycles.

In summary:

* The ability to record contracts would probably make Pacto a better choice than Pact for stubbing an existing 3rd party service (see their example for [Github][pacto_example]). The lack of provider states and regular expression matching would probably not matter in this scenario, as you are unlikely to be able to set up data on the provider without using the the very API you are testing.
* Pact is probably a better choice for a new project where the provider service does not yet exist, where the consumer's functionality is driving out the requirements for the provider.

### How does Pact differ from Accurest?
 [Accurest](https://github.com/Codearte/accurest) is another tool for creating and enforcing Consumer Driven Contracts.
 It has a Contract Definition Language, written in Groovy, which is used to produce:

 * JSON stubs for Wiremock, for testing consumer code
 * Acceptance tests, in Spock or JUnit, for testing against the provider
 * Messaging routes if you're using one

Both Pact and Accurest are essentially solving the same problem. The main difference between them is that Pact generates language-neutral acceptance contracts, in the form of JSON Pactfiles. These Pactfiles can be created, or tested, by anything that implements the [Pact specification](https://github.com/pact-foundation/pact-specification), whether the code is [Ruby](https://github.com/realestate-com-au/pact), [Javascript](https://github.com/DiUS/pact-consumer-js-dsl), the [JVM](https://github.com/DiUS/pact-jvm), or any other language.
Accurest is very tied to Groovy on the consumer-side, and JUnit or Spock on the provider-side; there is no intermediate format. 
As stated above, Pact also has the [Pact Broker][pact_broker] which provides autogenerated documentation, network diagrams, and enables cross testing of the production and head versions of your consumer and provider, allowing you to decouple your consumer and provider release cycles.

In summary:
 * if you're tied to the JVM, and using Spock or JUnit, Accurest might be easier for you to integrate into your tests
 * if you want increased flexibility with your choice of language, and to not be tied to a particular implementation, Pact might suit you better.

### Do I still need end-to-end tests?

The answer to this question depends on your organisation's risk profile. There is generally a trade off between the amount of confidence you have that your system is bug free, and the speed with which you can respond to any bugs you find. A 10 hour test suite may make you feel secure that all the functionality of your system is working, but it will decrease your ability to put out a new release quickly when a bug is inevitably found. If you work in an environment where you prioritise "agility" over "stability", then maybe you would be better off investing the time that you would have spent maintaining end-to-end tests in improving your production monitoring. If you work in a more traditional "Big Bang Release" environment, a carefully selected small set of end-to-end tests that focus on the core business value provided by your system should provide the confidence you need to release. Consider "Semantic monitoring" (a type of "testing in production") as an alternative.

### How can I handle versioning?

Consumer driven contracts to some extent allows you to do away with versioning. As long as all your contract tests pass, you should be able to deploy changes without versioning the API. If you need to make a breaking change to a provider, you can do it in a multiple step process - add the new fields/endpoints to the provider and deploy. Update the consumers to use the new fields/endpoints, then deploy. Remove the old fields/endpoints from the provider and deploy. At each step of the process, all the contract tests remain green.

Using a [Pact Broker](https://github.com/bethesque/pact_broker), you can tag the production version of a pact when you make a release of a consumer. Then, any changes that you make to the provider can be checked agains the production version of the pact, as well as the latest version, to ensure backward compatiblity.

If you need to support multiple versions of the provider API concurrently, then you will probably be specifying which version your consumer uses by setting a header, or using a different URL component. As these are actually different requests, the interactions can be verified in the same pact without any problems.

### Should the database or any other part of the provider be stubbed?

The pact authors' experience with using pacts to test microservices has been that using the set_up hooks to populate the database, and running pact:verify with all the real provider code has worked very well, and gives us full confidence that the end to end scenario will work in the deployed code.

However, if you have a large and complex provider, you might decide to stub some of your application code. You will definitely need to stub calls to downstream systems or to set up error scenarios. Make sure, if you stub, that you don't stub the code that actually parses the request and pulls the expected data out, because otherwise the consumer could be sending absolute rubbish, and the pact:verify won't fail because that code won't get executed. If the validation happens when you insert a record into the datasource, either don't stub anything, or rethink your validation code.

### How can I extend Pact for different content types?

Read this: [[https://github.com/realestate-com-au/pact/wiki/How-to-extend-Pact-for-different-content-types]]

### How can I verify a pact against a non-ruby provider?

You can verify a pact against any running server, regardless of language, using [pact-provider-proxy](https://github.com/bethesque/pact-provider-proxy). There is also native support for the [JVM and .net](https://github.com/realestate-com-au/pact/wiki#implementations-in-other-languages).

### How can I create a pact for a consumer that is not Ruby/JVM/.net/Javascript?

Become famous, and write a pact-consumer library yourself! Join the [pact-dev][pact-dev] Google group and have a chat to the other implementors before you start.

[pacto]: https://github.com/thoughtworks/pacto
[pact_broker]: https://github.com/bethesque/pact_broker
[pacto_example]: http://thoughtworks.github.io/pacto/usage/
[pact-dev]: https://groups.google.com/forum/#!forum/pact-dev
