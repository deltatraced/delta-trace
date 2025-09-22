---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[000 Implement the Event Accumulator]]"
context_type: investigation
status: todo
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[000 Implement the Event Accumulator]] 

Spawned in: [[000 Implement the Event Accumulator#^spawn-invst-3617a0|^spawn-invst-3617a0]]

# 1 Objective

Since events are differential and hard to view directly, we would like to see if we can create an aggregate view in the sqlite3 database.

# 2 Journal

2025-09-21 Wk 38 Sun - 22:39 +03:00

I wrote projection initially, but I think we're looking for views here. Projections seem to do more with distinct selections (as explored in [stackoverflow answer](https://stackoverflow.com/questions/3461099/what-is-a-projection)).

There's information about the [sqlite architecture](https://sqlite.org/arch.html) and [sqlite overview](https://sqlite.org/howitworks.html).

2025-09-21 Wk 38 Sun - 22:53 +03:00

They have information specifically on creating Sqlite views in [sqlite createview](https://sqlite.org/lang_createview.html).

