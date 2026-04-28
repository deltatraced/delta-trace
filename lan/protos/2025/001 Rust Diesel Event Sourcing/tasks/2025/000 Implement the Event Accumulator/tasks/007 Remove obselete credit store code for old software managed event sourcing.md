---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[005 Model coin_store_hist and related in diesel]]"
context_type: task
status: done
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[005 Model coin_store_hist and related in diesel]]

Spawned in: [[005 Model coin_store_hist and related in diesel#^spawn-task-e6701d|^spawn-task-e6701d]]

# 1 Journal

2025-10-20 Wk 43 Mon - 22:23 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-002-credit-store-demo-rs
git commit -m "remove event accumulator rust code since it is done in SQL"

# out
Trim Trailing Whitespace.................................................Passed
Check Yaml...........................................(no files to check)Skipped
Check for added large files..............................................Passed
Check formatting.........................................................Passed
Run tests................................................................Passed
Check clippy lints.......................................................Passed
[main 14c4b2e] remove event accumulator rust code since it is done in SQL
 6 files changed, 9 insertions(+), 684 deletions(-)
 delete mode 100644 src/db/event_accumulator.rs
 delete mode 100644 src/db/event_accumulator_actions.rs
 delete mode 100644 src/db/watcher.rs
```

All of this is managed in SQL, we don't need manual rust code that is thread and lock managed just to manipulate the database.

The only place we may still need to use locking is for guaranteeing atomicity of partial history reads.