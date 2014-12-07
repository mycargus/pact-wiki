## Pact uses Postel's law

_Be conservative in what you send_ - when the mock server in the consumer project compares the actual request with the expected request, the actual request body is not allowed to contain fields that are not defined in the expected request body. We don't want the situation where our real consumer is "leaking" data that we don't know about. 

The exception to this is the request headers - we have found that frameworks tend to add their own headers, and that maintaining these can be extremely tedious, so be aware that if you are using a header that will change the behaviour of the provider, you _must_ specify it in your expectations.

_Be liberal in what you accept_ - when verifying a pact in the provider project, the response body and headers may contain fields that were not defined in the expectations, on the assumption that any extra field will be ignored by your consumer. This allows a provider to evolve without breaking existing consumers (unlike the bad old WSDL days).