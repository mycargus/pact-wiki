* Just because it's called "consumer driven contracts" doesn't mean that the team writing the consumer gets to tell the team writing the provider what to do! The pact is the starting point of a collaborative effort.
* The way Pact works, it's the pact verification task (on in the provider codebase) that fails when a consumer expects things that are different from what a provider responds with, even if the consumer itself is "wrong". This is a little unfortunate, but it's the nature of the beast.
* Running the pact verification task in a separate CI build from the rest of the tests for the provider is a good idea - if you have it in the same build, someone is going to get cranky about another team being able to break their build.
* It's very important for the consumer team to know when pact verification fails, because it means they cannot deploy the consumer. If the consumer team is using a different CI instance from the provider team, consider how you might communicate to the consumer team when pact verification has failed. You should do one of the following:
 * Configure the pact verification build to send an email to the consumer team when the build fails.
 * Even better, if you can, have a copy of the provider build run on the consumer CI that just runs the unit tests and pact verification. That way the consumer team has the same red build that the provider team will get, and it gives them a vested interest in keeping it green.

# How to develop new functionality while keeping all the builds green
See [Development workflow](Development-workflow)
