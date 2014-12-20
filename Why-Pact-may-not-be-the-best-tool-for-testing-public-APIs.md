Each interaction in a pact should be verified in isolation, with no context maintained from the previous interactions. Tests that depend on the outcome of previous tests are brittle and land you back in integration test hell, which is the nasty place you're trying to escape by using pacts.

So how do you test a request that requires data to already exist on the provider? [Provider states][provider-states] allow you to set up data on the provider by injecting it straight into the datasource before the interaction is run, so that it can make a response that matches what the consumer expects. They also allow the consumer to make the same request with different expected responses (eg. different response codes, or the same resource with a different subset of data).

If you use Pact to test a public API, the only way to set up the right provider state is to use the very API that you're actually testing, which will make the tests slower and more brittle compared to the "normal" pact verification tests.

If this is still a better situation for you than integration testing, or using another gem like VCR, then go for it!

[provider-states]: https://github.com/realestate-com-au/pact/wiki/Provider-states
