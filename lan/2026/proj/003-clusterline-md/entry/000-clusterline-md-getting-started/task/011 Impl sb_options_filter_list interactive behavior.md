---
context_type: task
status: todo
---

Parent: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned by: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text]]

Spawned in: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text#^spawn-task-3c7bf1|^spawn-task-3c7bf1]]

# Journal

2026-07-07 Wk 28 Tue - 18:17 +03:00

2026-07-07 Wk 28 Tue - 21:04 +03:00

We implemented confirmed selection and now post an `on_selected` message besides `on_canceled`.

There are some events that are not triggering:

- `keyDown` and `keyUp` for `sb_input1`.
- `mouseMove` for one of the option items.

https://www.w3schools.com/tags/ref_eventattributes.asp


