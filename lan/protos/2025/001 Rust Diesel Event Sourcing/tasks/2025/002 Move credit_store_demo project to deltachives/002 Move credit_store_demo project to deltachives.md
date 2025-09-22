---
status: done
---
# 1 Journal

2025-09-18 Wk 38 Thu - 18:09 +03:00

```sh
# in /home/lan/src/cloned/gh/deltachives/2025-002-credit-store-demo-rs

```

2025-09-18 Wk 38 Thu - 19:46 +03:00

We need to reorganize how we spawn a shell. We can have many experiments which require the same shell infrastructure but with different commands. 

Spawn [[000 Modularize shi shell use in credit store demo]] ^spawn-task-dfb856

2025-09-19 Wk 38 Fri - 19:32 +03:00

Spawn [[002 Getting a generic diesel update all function to work]] ^spawn-issue-861a54

2025-09-19 Wk 38 Fri - 20:27 +03:00

Complex to automate through types, so we might have to look for other methods.

2025-09-19 Wk 38 Fri - 21:50 +03:00

Spawn [[001 Register tables to process for event accumulator]] ^spawn-task-0a20b1

2025-09-20 Wk 38 Sat - 04:38 +03:00

We still need to be able to write events in an append-only fashion, and signal work to the event accumulator, but this now looks to be in a good state. So let's try to create the repository.

2025-09-20 Wk 38 Sat - 04:45 +03:00

Handling some lints, which include:
- module level docs not having `//!` and instead having `///`. 
- prefer to impl `Display` instead of `ToString`.
- use `Vec::new` instead of `|| vec![]`

2025-09-20 Wk 38 Sat - 04:51 +03:00

All lints passed!