---
parent: "[[000 Learning]]"
spawned_by: "[[000 Learning]]"
context_type: entry
---

Parent: [[000 Learning]]

Spawned by: [[000 Learning]]

Spawned in: [[000 Learning#^spawn-entry-ddd17e|^spawn-entry-ddd17e]]

[[Mn 10 October]]

# 1 Purpose

Capturing highlights of practices and lessons learned this month!

# 2 Journal

2025-10-09 Wk 41 Thu - 19:15 +03:00

(1)

We can use windows in Rust iterators to process a moving window of values with tuple size up to 12!

Here's an example from dbmint:

```rust
let settings_of_the_same_table_are_consecutive = settings
	.iter()
	.enumerate()
	.chunk_by(|(_, k)| {
		match k {
			Setting::TableDeriveEventSourcing(setting) => &setting.table_line.table_name,
			Setting::ColumnESSetting(setting) => &setting.table_line.table_name,
		}
	})
	.into_iter()
	.all(|(_, group)| {
		group
			.into_iter()
			.tuple_windows::<(_, _)>()
			.all(|((i0, _), (i1, _))| i1 - i0 == 1)
	});
```

In order to prove consecutive indices, I have to check that any two indices only differ by 1. Using `tuple_windows` we can do that. A moving window should always include two values to do the comparison moving through the iterator. If the iterator has less than 2 elements, then no windows are produced.

We're also using `chunk_by`! All items having the same key end up in the same group!

2025-10-16 Wk 42 Thu - 10:08 +03:00

(2)

During [[001 Reading through lukaswirth.dev decl-macros]],

I have been thinking about specification writing and the this [rust reference point](https://doc.rust-lang.org/reference/macros-by-example.html#r-macro.decl.transcription.fragment) provides a good example of how specification should be in a sense excutable. They provide specific examples to mark exceptions or invariants. This can be a way that tests and specification interact, with tests providing stimulus, ie. concrete examples to demonstrate expected errors and behaviors. The spec then can, in natural language, provide a minimal reproduction of that example to illustrate its logical point.

I also incorporate errors in the types of the functions in Rust. Some errors are due to the function input domain varying in unintended ways and are meant to be handled externally. Others are Invariant Errors, which should never trigger in a correct implementation, but yet are technically possible given the compiler context.

Wherever we can, invariants should be encoded through computation by constructing new types whose values are always valid. This can help to write better tests, but it also helps clarify how our software can fail, which goes back to spec writing as well.