# pact_From_Scratch_StepByStep

Pact is a contract testing framework where we can define consumer expectations for a given provider 
(that can be an HTTP REST service). Contract testing on a broader level can be thought of testing
the communication layers between the services. 
There are two main type of actors in contract testing:-
1. Providers
2. Consumers

Provider is an application responsible for publishing an API;
Consumer is another application which uses the API;

There are 2 types of contract testing:
1. Provider Driven (Provider defines the contract and consumer verifies)
2. Consumer Driven (Consumer defines expectations and provider checks)

In this demo, I have used Consumer driven contract testing.
It is a way to formalize these expectations into a contract between consumer-provider pair. 
Once contract is established, contract testing ensured automatically that contract doesn't break unexpectedly.
If API changes break anything, it also allows provider to know which of their consumers are affected and when 
it is safe to push the changes, if any.

So, now lets start with step-by-step demo.

