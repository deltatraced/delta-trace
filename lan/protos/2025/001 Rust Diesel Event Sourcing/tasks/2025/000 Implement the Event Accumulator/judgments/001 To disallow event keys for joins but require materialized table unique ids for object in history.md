---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[004 Choosing accumulations or events as keys in events]]"
context_type: judgment
status: done
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[004 Choosing accumulations or events as keys in events]]

Spawned in: [[004 Choosing accumulations or events as keys in events#^spawn-jdgmt-fe09db|^spawn-jdgmt-fe09db]]

# 1 Judgment

In order to prevent [[003 Use of event keys in events would result in complex partial accumulations|(1) complex partial accumulations]] and ensure joins are standard and correct where one record refers to a [[004 IDs in events must be to a unique object, which cannot be object id for a historic table|(2) unique historical record]], all IDs in events must either be to a non-historical table or a historical table that is the product of event accumulation and not events themselves.

# 2 Reasons

(1)

2025-10-03 Wk 40 Fri - 05:06 +03:00

Spawn [[003 Use of event keys in events would result in complex partial accumulations]] ^spawn-infer-c52a0d

(2)

2025-10-03 Wk 40 Fri - 05:11 +03:00

Spawn [[004 IDs in events must be to a unique object, which cannot be object id for a historic table]] ^spawn-infer-0bd9ad


# 3 Related

This judgment relies on having made the judgment [[000 To materialize grouped events and accumulated objects into tables via software]]

# 4 Journal