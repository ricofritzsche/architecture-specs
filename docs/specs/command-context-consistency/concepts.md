# Command Context Consistency Concepts

This page defines the core concepts used by the Command Context Consistency specification.

## Command Context Consistency

Command Context Consistency means that a command appends new facts only when the facts relevant to its decision have not changed.

The consistency boundary is defined by the command’s context query.

## Fact

A fact is an immutable record that something happened.

In this specification, facts are represented as events.

A fact has:

```text
event_type
payload
```

## New Event

A new event is a fact submitted for append.

It has not been committed yet.

It has no sequence number.

## Event Record

An event record is a committed fact.

It has:

```text
sequence_number
occurred_at
event_type
payload
```

The sequence number defines committed order.

The occurrence time is metadata.

## Event Type

The event type describes the kind of fact.

Examples:

```text
tool_registered
tool_checked_out
tool_returned
```

The event type helps queries select relevant facts.

## Payload

The payload contains the recorded data of a fact.

Example:

```json
{
  "tool_id": "tool-123",
  "serial_number": "SN-001"
}
```

The payload is part of the fact.

## Sequence Number

A sequence number is the global committed position of an event record.

Sequence numbers define the order in which facts were committed.

## Event Filter

An event filter describes one way to match event records.

It can constrain:

```text
event_types
payload_predicates
```

A record matches a filter when all constraints of that filter match.

## Event Query

An event query describes a set of relevant event records.

It contains zero or more event filters.

It may also contain a read cursor named `min_sequence_number`.

If an event query has no filters, it matches all event records.

## Payload Predicate

A payload predicate is a condition against the payload.

Example:

```json
{
  "tool_id": "tool-123"
}
```

A payload predicate helps select facts by their recorded data.

## Read Cursor

`min_sequence_number` is an exclusive read cursor.

It is used to return only records after a known sequence number.

The read cursor controls returned records.

It does not define the command context.

## Query Result

A query result is the result of reading event records with an event query.

It contains:

```text
event_records
last_returned_sequence_number
current_context_version
```

## Last Returned Sequence Number

`last_returned_sequence_number` is the highest sequence number among the returned event records.

It describes the result of the read.

If the query returns no records, it is absent.

## Current Context Version

`current_context_version` is the highest sequence number among all event records matching the query context.

It describes the current version of the command context.

It is calculated independently from the read cursor.

If the context is empty, it is absent.

## Append

Append means committing one or more new events as one batch.

A successful append creates event records and assigns sequence numbers.

## Append Result

An append result describes a committed append batch.

It contains:

```text
first_sequence_number
last_sequence_number
committed_count
```

## Conditional Append

Conditional append means appending new events only if a context version still matches the expected version.

It is the operation that enforces Command Context Consistency.

## Context Query

A context query is the event query used to define the facts relevant to a command decision.

The same context query is used to check whether the command context changed before appending new events.

## Expected Context Version

The expected context version is the context version observed before the command produced new events.

It is passed to conditional append.

## Actual Context Version

The actual context version is the context version calculated by the event store during conditional append.

If it matches the expected context version, the append can succeed.

If it differs, the append is rejected.

## Conditional Append Conflict

A conditional append conflict means the command context changed before the new events could be appended.

The conflict contains:

```text
expected_context_version
actual_context_version
```
