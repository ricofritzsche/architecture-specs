# Command Context Consistency Specification

Status: Draft
Version: 0.1

This specification defines the minimal event-store capabilities required to support Command Context Consistency.

Command Context Consistency means that new facts are appended only when the fact context used for a command decision has not changed.

## Required Capabilities

A compliant event store provides three operations:

```text
query(event_query) -> query_result

append(new_events) -> append_result

append_if(new_events, context_query, expected_context_version) -> append_result
````

## Facts

A new event contains:

```text
event_type
payload
```

A committed event record contains:

```text
sequence_number
occurred_at
event_type
payload
```

`sequence_number` defines the committed order.

`occurred_at` is metadata and does not define committed order.

## Query

`query` reads committed event records matching an event query.

An event query contains zero or more event filters and may contain `min_sequence_number`.

An event filter may constrain:

```text
event_types
payload_predicates
```

A record matches a filter when all constraints of that filter match.

Multiple filters are alternatives. A record matches the query when it matches at least one filter.

If a query has no filters, it matches all committed event records.

`min_sequence_number` is an exclusive read cursor.

`min_sequence_number` affects returned records only.

`min_sequence_number` does not affect the current context version.

## Query Result

A query result contains:

```text
event_records
last_returned_sequence_number
current_context_version
```

`event_records` are returned in ascending `sequence_number` order.

`last_returned_sequence_number` is the highest sequence number among returned records.

If no records are returned, `last_returned_sequence_number` is absent.

`current_context_version` is the highest sequence number among all records matching the event query without applying `min_sequence_number`.

If no records match the query context, `current_context_version` is absent.

`last_returned_sequence_number` and `current_context_version` are different values.

## Append

`append` commits one or more new events as one batch.

The batch must contain at least one event.

A successful append commits the complete batch.

A failed append commits nothing.

A successful append assigns one consecutive global sequence range to the committed batch.

An append result contains:

```text
first_sequence_number
last_sequence_number
committed_count
```

## Conditional Append

`append_if` commits one or more new events only when the command context has not changed.

`append_if` receives:

```text
new_events
context_query
expected_context_version
```

The event store calculates the actual context version for `context_query`.

The actual context version is calculated without applying `min_sequence_number`.

If the actual context version equals `expected_context_version`, the store commits the new events as one batch.

If the actual context version differs from `expected_context_version`, the store rejects the append with a conditional append conflict.

On conflict, no event is committed.

A conditional append conflict contains:

```text
expected_context_version
actual_context_version
```

## Sequence Guarantees

Sequence numbers are global.

Sequence numbers are monotonically increasing.

One committed batch receives one consecutive sequence range.

Failed appends and failed conditional appends do not commit partial batches.

A strict implementation does not consume sequence numbers for failed appends or failed conditional appends.

## Compliance

An event store is compliant with this specification when its `query`, `append`, and `append_if` operations preserve the semantics defined above.
