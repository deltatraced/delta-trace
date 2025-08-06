---
status: todo
---
#external #docs


# 1 Objective

Notes on `dbmt`, which is a language derivative of `dbml`.

# 2 Journal

## 2.1 Initial Planned Features

2025-07-22 Wk 30 Tue - 18:58

We want the following features in addition to what's in the [dbml syntax docs](https://dbml.dbdiagram.io/docs/) [[#^1]]:

- [ ] Syntax to include other dbmt files

```dbmt
.include "some/path/to/other.dbmt"
```


- [ ] Custom data types

```dbmt
.type HP integer
```

Signals associated with types apply wherever the types are used.

Note that even tables may have custom datatypes:

```dmbn
.type SpecialTable Table
```

In case we want to include repetitiveness in the tables, they may be further specified

```dmbn
.type SpecialTable Table {
	id integer [primary key]
	created_on string
}

SpecialTable MySpecialTable {
	myfield integer
}
```

`MySpecialTable` here will automatically have the same fields `id` and `created_on`. 

- [ ] Signals with trigger type on table and column

Triggers include: 
- insert
- update
- delete
- write (insert, update, and delete)
- read
- init
- all
- cust(...)

```dbmt
%signal(my_signal_name, on=[insert, delete])
```

This applies to the next item, which could be a table or a column.

User should be able to subscribe to these signals and perform some logic on those events.

In addition, signals can be `gated`, meaning that user action is expected before the operation is confirmed. Operation will wait for user to signal OK or ERR.

`gated` signals are specified under `then=` rather than `on=`:

```dbmt
%signal(my_signal_name, then=[insert, delete])
```

In addition, we can use `via=` instead of `on=` and `then=`. `via` signals override the event values themselves which are useful for computed fields:

```dmbn
%signal(get_current_date, via=[write])
created_on string [computed]
```

`cust` triggers are custom. The application handles how those events trigger

```
%signal(clear_temporary, on=[cust(on_cache_clear)])
```

The generated application will subscribe to these signals, when the user publishes to them, they will triggered the configured behavior.

- [ ] Fields computed on trigger

Some fields shouldn't be manually created. They should be computed based on the context on some signal trigger. 

If the field really shouldn't have a value in the database, and is just a placeholder until it is computed on read, mark it `[computed]`. 

```
Table MyTable {
	%signal(calculate_my_field, via=[read])
	my_read_only_field integer [computed]
	%signal(get_current_date, via=[write])
	created_on string
}
```

- [ ] Data access configuration

Those include
- fixed
- immutable
- mutable
- ephemeral

fixed cannot be written at all. immutable only allows inserts. mutable allows inserts and updates but not deletes. and ephemeral allows inserts, updates, and deletes.

Configuration only applies to tables:

```dmbn
[access: immutable]
Table MyTable {
	...
}
```

They also apply to table types.

- [ ] Not null by default

All fields cannot be null unless marked explicitly as `[null]`. 

- [ ] Uniqueness checks

Fields marked as `[unique]` will be flagged for that check when a table verification process starts. 

This can be overriden for an expensive check always on read/write with `[always-check]` for that specific field.

- [ ] One-to-many or one-to-one

References can be marked `[otm]` for one to many or `[oto]` for one to one. You can also do `[mto]` for many to one. These will be flagged for that check when a table verification process starts.

This can be overriden for an expensive check always on read/write with `[always-check]` for that specific relation.

# 3 References
1. [dbml syntax docs](https://dbml.dbdiagram.io/docs/) ^1