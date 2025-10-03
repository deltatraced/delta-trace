---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[001 Event loads can contain complex join structures]]"
context_type: task
status: done
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[001 Event loads can contain complex join structures]]

Spawned in: [[001 Event loads can contain complex join structures#^spawn-task-9c15b1|^spawn-task-9c15b1]]

# 1 Objective

To decide whether events include keys to other events in them or straight to accumulation tables of other events. 

This depends on having made the judgment [[000 To materialize grouped events and accumulated objects into tables via software]]

# 2 Journal

2025-10-03 Wk 40 Fri - 04:29 +03:00

If we include events as keys in other events, how do join them?

Even when they get accumulated, you will see an event in the accumulated table, which should not happen. 

2025-10-03 Wk 40 Fri - 04:55 +03:00

Another problem with using event ids is that if a complex object has $N$ joins, how do we show the final accumulated supertable? If it included $N$ already accumulated objects, this is simple, we apply only one accumulation on the current event table. Otherwise, there are interactions now with partial accumulations to be made, which is very complex.

2025-10-03 Wk 40 Fri - 04:56 +03:00

We also cannot use object ids. As the table represents a history, they are no longer unique. The table will need to use an id unique for an object situated at a point in history.

2025-10-03 Wk 40 Fri - 05:04 +03:00

Spawn [[001 To disallow event keys for joins but require materialized table unique ids for object in history]] ^spawn-jdgmt-fe09db
