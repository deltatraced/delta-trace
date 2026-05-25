---
parent: "[[000 Learning]]"
spawned_by: "[[000 Learning]]"
context_type: entry
---

Parent: [[000 Learning]]

Spawned by: [[000 Learning]]

Spawned in: [[000 Learning#^spawn-entry-0d3d79|^spawn-entry-0d3d79]]

[[Mn 09 September]]

# 1 Purpose

Capturing highlights of practices and lessons learned this month!

# 2 Journal

(1)

2025-09-05 Wk 36 Fri - 13:48

Spawn [[001 Streamlined environment variables in Rust]] ^spawn-entry-07b270

(2)

2025-09-19 Wk 38 Fri - 09:56 +03:00

From [[001 Getting many debugging logs from rustyline while using shi#^learning-6c9d3b]],

We actually can do

```rust
#[error("Got ShiError: {0:?}")]
ShiError(#[from] ShiError),
```

And then we can do `?` on a `ShiError` directly! No need to map in the case it's a one to one correspondence.