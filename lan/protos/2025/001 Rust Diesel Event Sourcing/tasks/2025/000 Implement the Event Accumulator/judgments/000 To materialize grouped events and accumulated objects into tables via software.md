---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[002 Add event accumulation events through diesel]]"
context_type: judgment
status: done
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[002 Add event accumulation events through diesel]]

Spawned in: [[002 Add event accumulation events through diesel#^spawn-jdgmt-733656|^spawn-jdgmt-733656]]

# 1 Judgment

We have created event accumulation views in SQL, but then learned that [[000 diesel-rs does not yet support views|(1) diesel does not support them]].

We could use [sql_query](https://github.com/sgrif/diesel.rs-website/blob/25a2a888112ccf9f9467d9294f726b0d82fd9c48/src/index.md?plain=1#L422) to interface with the views [[002 Use of sql_query in diesel-rs disrupts use of diesel query builder which requires manual SQL|(3) but that will require manual SQL queries]] for most uses and this would be akin to a downgrade for the developer experience of our events.

Even if we use raw SQL to interface with them, we need to resolve the fact that [[001 Event loads can contain complex join structures|(2) historic objects join with others]]. To resolve this, events must keep IDs only to [[001 To disallow event keys for joins but require materialized table unique ids for object in history|(4) historic objects]]

By historic objects, we mean the records in our materialized table, which have a unique ID for them. They are not objects, but objects at a history.

The grouped views need to also be materialized, as they can be of use for event-filtered histories, or for individual searches made on events rather than histories.

Tables like `v_coin_store_objects` are renamed to `v_coin_store_history` to signify that we are dealing with historic records.

$\therefore$ We will materialize both the grouped events views and the history views so that the user is capable to interact with events in diesel.

# 2 Reasons

(1)

2025-10-03 Wk 40 Fri - 03:24 +03:00

Spawn [[000 diesel-rs does not yet support views]] ^spawn-infer-640f48

(2)

2025-10-03 Wk 40 Fri - 03:38 +03:00

Spawn [[001 Event loads can contain complex join structures]] ^spawn-infer-bcaf57

(3)

2025-10-03 Wk 40 Fri - 03:46 +03:00

Spawn [[002 Use of sql_query in diesel-rs disrupts use of diesel query builder which requires manual SQL]] ^spawn-infer-b130c8

(4)

2025-10-03 Wk 40 Fri - 08:28 +03:00

[[001 To disallow event keys for joins but require materialized table unique ids for object in history]]

# 3 Journal

2025-10-03 Wk 40 Fri - 08:35 +03:00

Spawn [[004 Investigate options for materializing views into tables using SQL]] ^spawn-invst-f54b9e