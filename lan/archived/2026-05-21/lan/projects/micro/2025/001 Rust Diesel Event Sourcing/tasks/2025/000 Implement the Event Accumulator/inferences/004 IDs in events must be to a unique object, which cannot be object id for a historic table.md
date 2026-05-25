---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[001 To disallow event keys for joins but require materialized table unique ids for object in history]]"
context_type: inference
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[001 To disallow event keys for joins but require materialized table unique ids for object in history]]

Spawned in: [[001 To disallow event keys for joins but require materialized table unique ids for object in history#^spawn-infer-0bd9ad|^spawn-infer-0bd9ad]]

# 1 Related

This inference depends on having made the judgment [[000 To materialize grouped events and accumulated objects into tables via software]]

# 2 Journal

2025-10-03 Wk 40 Fri - 05:13 +03:00

From [[004 Choosing accumulations or events as keys in events]],

Events that compose with other events need to include unique object IDs situated at a given history. Which means the unique ID must consider Object Id & Span & Frame

2025-10-03 Wk 40 Fri - 05:28 +03:00

The views are in [[002 Investigate group by logic for frame and span to include up to span]],