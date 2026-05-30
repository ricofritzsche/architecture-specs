# Reactors

A Reactor coordinates an interaction that needs more than one capability or an external operation.

It receives a request, orchestrates the required calls, and returns a response.

```text
react(request) -> response
```

A Reactor is not part of the domain. It coordinates domain capabilities and external operations from the outside.

## Coordination

A Reactor is used when one interaction cannot be handled by a single RPU.

Typical cases:

* one interaction calls more than one RPU
* one interaction combines an RPU with an external operation
* one interaction needs a defined order of steps
* one interaction needs to collect results and return one response

If an interaction needs only one domain capability, it can call the RPU directly. A Reactor is not required.

## Boundary

A Reactor knows the public contracts it coordinates.

It does not know the internal implementation of an RPU.

It does not access domain state directly.

It does not contain domain decisions.

The domain decision stays inside the RPU that owns the capability.

## Flow

A Reactor controls the order of calls.

```text
Interaction request
-> call capability
-> call external operation
-> call another capability
-> build interaction response
```

The exact steps depend on the interaction.

The important rule is that the Reactor coordinates the flow. It does not own the domain behavior of the individual steps.

## Domain Logic

A Reactor must not contain domain logic.

It can decide which step comes next in an interaction.

It can pass data from one call to another.

It can map technical or coordination results into an interaction response.

It should not decide domain rules that belong to an RPU.

## Independence

A Reactor is technology-independent.

It should not depend on a UI framework, HTTP framework, CLI framework, or message transport.

Those technologies belong outside the Reactor.

This keeps the Reactor testable without a real user interface or real external infrastructure.

## Testability

A Reactor can be tested by replacing the capabilities and external operations it coordinates.

The test does not need the real domain implementation.

The test does not need the real external system.

The test verifies the coordination behavior:

* which calls are made
* in which order they are made
* how results are passed forward
* which response is returned

## Summary

A Reactor is:

* an interaction coordinator
* technology-independent
* outside the domain
* free of domain logic
* dependent only on public contracts
* useful when one interaction needs more than one capability or external operation
