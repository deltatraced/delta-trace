---
status: todo
---
# 1 Journal

2025-09-20 Wk 38 Sat - 20:46 +03:00

Spawn [[000 Allow users to append new credit store events in various ways]] ^spawn-task-b96c13

2025-09-21 Wk 38 Sun - 20:29 +03:00

We need to revise how event sourcing is implemented. Using complex instructions for the event accumulator like `undo N` and `redo N` would make it extremely difficult to query events. Similar for global/local events. Instead of popping frames, we can simply add local events which are where actual objects are touched, at a specific scale no and frame no. Frame no is so that even though we may add events at the same scale, we start from scratch (relative to that scale) on a new frame. It's up to software how events at different scales interact. Whether higher scales aggregate into lower scales, or whether lower scales are treated as more permanent effects that are applied to higher scales.

We might not need a Head managed by an event accumulator and a Version to check out with, if we can simply get the state of affairs by filter by scale, and frame no.  We should look into use of query projections to get the state of current objects. We might also need to leave it to software what updating means. For numerical values it may be differential, so that the column needs to aggregate. For others, we may need to replace the column value by the latest. Another thing is how deletions affect

2025-09-21 Wk 38 Sun - 21:01 +03:00

Might be best to separate local and global events. Local being changes to objects, and global being changes to the entire state of affairs.

Objects should have a status that can be aggregated. Inserted, Updated, Deleted. An Insert event followed by updates, gives us Updated. An Insert event alone gives us Inserted, and if it ends with a Delete, we get Deleted. This could be satisfied with a sum aggregate with the following rules:

1 is inserted. 0 is deleted. $\gt$ 1 is updated. On insert, the value is set to 1. On each update, it's incremented once, and finally on a delete, the number of events added for that object are counted, and then they are subtracted, so $-N$ to get us to 0. Now a sum aggregate will work to give us what objects currently exist.

^recall-244d1b

2025-09-21 Wk 38 Sun - 21:34 +03:00

Spawn [[000 Investigate summing and latest aggregation with diesel]] ^spawn-invst-bb50da

Spawn [[001 Use of views and CTEs with sqlite3 and diesel-rs]] ^spawn-invst-3617a0

2025-09-21 Wk 38 Sun - 23:07 +03:00

Spawn [[000 Resources encountered during event accumulator impl]] ^spawn-entry-4b539d

2025-09-21 Wk 38 Sun - 23:21 +03:00

Spawn [[001 Create coin table events to experiment with aggregation being in views]] ^spawn-task-9b8a2b


# 2 Index

**entry**

[[000 Resources encountered during event accumulator impl.md]]

**howto**

[[000 Create sqlite3 dbs from sql script.md]]

[[001 Creating a basic counter with a recursive CTE in sqlite3.md]]

[[002 Creating a basic table duplicator with recursive CTE in sqlite3.md]]

**inference**

[[000 diesel-rs does not yet support views.md]]

[[001 Event loads can contain complex join structures.md]]

[[002 Use of sql_query in diesel-rs disrupts use of diesel query builder which requires manual SQL.md]]

[[003 Use of event keys in events would result in complex partial accumulations.md]]

[[004 IDs in events must be to a unique object, which cannot be object id for a historic table.md]]

**investigation**

[[000 Investigate summing and latest aggregation with diesel.md]]

[[001 Use of views and CTEs with sqlite3 and diesel-rs.md]]

[[002 Investigate group by logic for frame and span to include up to span.md]]

[[003 Create a natural numbers table and group by divisibility up to N.md]]

[[004 Investigate options for materializing views into tables using SQL.md]]

**judgment**

[[000 To materialize grouped events and accumulated objects into tables via software.md]]

[[001 To disallow event keys for joins but require materialized table unique ids for object in history.md]]

**task**

[[000 Allow users to append new credit store events in various ways.md]]

[[001 Create coin table events to experiment with aggregation being in views.md]]

[[002 Add event accumulation events through diesel.md]]

[[003 Attempt to use partial view support branch of diesel.md]]

[[004 Choosing accumulations or events as keys in events.md]]

