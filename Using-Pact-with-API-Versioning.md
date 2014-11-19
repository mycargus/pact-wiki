_Question:_ Why do we version our APIs?

_Answer:_ Because we don't want to break our existing consumers when we need to modify the API.

Some sort of protection for existing consumers is required when you have a public API, because you can't personally go around to everyone who is using it, and tell them that you need to change something, and ask them to kindly modify their client code.

But for an internal API, where we know all the consumers, what if we could find out exactly which consumers relied on each field? Then we could add the new functionality in parallel, release that functionality, move all the existing consumers over to the new functionality, and remove the old functionality. No versioning required! Pact gives you that ability because each consumer only specifies the fields in the pact that it cares about. If you remove a field from your API, and run the pact verification, you will know immediately whether or not you will break your consumers. Remember, you can always add new fields to the response without breaking a consumer, because extra fields (in any sane client!) will be ignored.