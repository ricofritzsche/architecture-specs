# Providers

A Provider gives access to an external capability outside the domain.

It hides technical access behind a small callable surface.

A Provider is used when an interaction needs something the domain does not own.

## External Capability

A Provider represents an external capability.

Examples:

- authentication
- email delivery
- payment processing
- notifications
- file storage
- third-party APIs

The Provider owns the technical access to that capability.

The domain does not own it.

## Provider Boundary

A Provider may know about protocols, credentials, APIs, network calls, retries, rate limits, or external data formats.

The code that uses a Provider should not need to know those details.

The Provider exposes the external capability through a clear boundary.

## Shape

A Provider exposes operations.

The exact operation names depend on the external capability.

Examples:

```text
authenticate(request) -> result
send_email(request) -> result
create_payment(request) -> result
send_notification(request) -> result
upload_file(request) -> result
```

The important part is not the method name.

The important part is that external access is separated from domain decisions.

## Relationship to Reactors

A Reactor may coordinate RPUs and Providers.

The Reactor controls the interaction flow.

The Provider performs the external operation.

The RPU owns the domain capability.

The Provider does not contain domain logic.

## Relationship to RPUs

An RPU owns a domain capability.

A Provider owns access to an external capability.

If a domain decision is needed, it belongs in an RPU.

If external access is needed, it belongs behind a Provider.

## Testability

Code that uses a Provider can be tested with a replacement Provider.

The test does not need the real external system.

The test can define the Provider response directly and verify how the caller reacts.

Provider implementation tests can separately verify the real external integration.

## Summary

A Provider is:

* a boundary to an external capability
* outside the domain
* technical rather than domain-owning
* replaceable for tests
* callable through a small public surface
* free of domain decisions
