---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[000 To materialize grouped events and accumulated objects into tables via software]]"
context_type: inference
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[000 To materialize grouped events and accumulated objects into tables via software]]

Spawned in: [[000 To materialize grouped events and accumulated objects into tables via software#^spawn-infer-640f48|^spawn-infer-640f48]]

# 1 Journal

2025-10-03 Wk 40 Fri - 03:25 +03:00

As learned in [[002 Add event accumulation events through diesel]],

We cannot yet add views to diesel. This may change in the near future as [gh diesel-rs/diesel #4077](https://github.com/diesel-rs/diesel/pull/4077) mentions that it's on the roadmap from Aug 2025.

In [[003 Attempt to use partial view support branch of diesel]] we tried to use the feature branch for this but it does not yet generate views.