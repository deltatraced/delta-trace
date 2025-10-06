---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[000 To materialize grouped events and accumulated objects into tables via software]]"
context_type: inference
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[000 To materialize grouped events and accumulated objects into tables via software]]

Spawned in: [[000 To materialize grouped events and accumulated objects into tables via software#^spawn-infer-bcaf57|^spawn-infer-bcaf57]]

# 1 Inference

In [[001 Create coin table events to experiment with aggregation being in views]] and [[002 Investigate group by logic for frame and span to include up to span]],

We created an event accumulation view that takes into account sourcing of events from lower spans at frame creation time.

This is complex, however, the test was made with a simple table with no join structure or any other features. It is possible for the load diff to compose with other tables, which will also need to be events if they can change or can remain as objects if they're append-only.

~~Events may compose with other events, but accumulations should only compose with accumulations.~~

As per judgment [[001 To disallow event keys for joins but require materialized table unique ids for object in history]],

Both events and accumulations will only hold keys to historic or ahistoric objects, to simplify joins and decouple them from the dimension of accumulation.

# 2 Journal

2025-10-03 Wk 40 Fri - 03:41 +03:00

Spawn [[004 Choosing accumulations or events as keys in events]] ^spawn-task-9c15b1