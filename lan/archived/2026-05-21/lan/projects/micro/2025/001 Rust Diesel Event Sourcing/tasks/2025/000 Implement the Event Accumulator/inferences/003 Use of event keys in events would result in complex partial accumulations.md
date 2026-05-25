---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[001 To disallow event keys for joins but require materialized table unique ids for object in history]]"
context_type: inference
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[001 To disallow event keys for joins but require materialized table unique ids for object in history]]

Spawned in: [[001 To disallow event keys for joins but require materialized table unique ids for object in history#^spawn-infer-c52a0d|^spawn-infer-c52a0d]]

# 1 Related

This depends on having made the judgment [[000 To materialize grouped events and accumulated objects into tables via software]]

# 2 Journal

2025-10-03 Wk 40 Fri - 05:08 +03:00

From [[004 Choosing accumulations or events as keys in events]],

If events could contain keys to other events, support we have an event table with $N$ keys to join on. An accumulation will now show $N$ event ids in it, if we were to join on them, it would be 1 accumulated and $N$ event data, which would need to be accumulated on their own. This is complex to handle and does not scale well with our current approach of creating an accumulation view per event table.

For more info on the current approach with event views, see [[002 Investigate group by logic for frame and span to include up to span]].