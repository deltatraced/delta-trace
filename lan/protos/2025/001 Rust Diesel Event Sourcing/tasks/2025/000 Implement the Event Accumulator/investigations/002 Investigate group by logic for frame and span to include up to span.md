---
parent: "[[000 Implement the Event Accumulator]]"
spawned_by: "[[001 Create coin table events to experiment with aggregation being in views]]"
context_type: investigation
status: done
---

Parent: [[000 Implement the Event Accumulator]]

Spawned by: [[001 Create coin table events to experiment with aggregation being in views]] 

Spawned in: [[001 Create coin table events to experiment with aggregation being in views#^spawn-invst-bd7e4d|^spawn-invst-bd7e4d]]

# 1 Related

[[001 Use of views and CTEs with sqlite3 and diesel-rs]]

# 2 Journal

2025-09-26 Wk 39 Fri - 04:43 +03:00

The aggregate view will show unique objects within a respective frame, but the scales will differ. Let's use the notation `sNfM` to mean `scale N frame M`. 

Suppose we have `s0f0`, `s1f1`, and `s1f2`. 

We need to show 3 separate states of afairs, so object ids can duplicate potentially 3 times, but are guaranteed to be unique within a frame. 

All objects in frame 1 need to be `f1`, but they may be `s0` or `s1`. 

Remember that only frames are "separate worlds", but all scales mix. Previously, we used an analogy that `s0` could mean the monthly aggregates, while `s1` could be the individual transactions. This doesn't work. Scales refer to layers of *persistence* and not literally different timescales. 

I can't rename to `lifetime` at least because it would be confusing given it lights up blue in sql indicating it's a keyword somewhere. Let's call it `span`. 

2025-09-26 Wk 39 Fri - 07:14 +03:00

So what we want to do is group by 

So for all frames, we want to group by:

1. All Items within the current frame and span; unioned with
2. All items whose span is $\lt$ the frame span AND their timestamp $\le$ the frame latest update time

This will give us the property that every frame is self-contained with its own set of objects, even from lower spans (or higher persistence layers). This way objects in all past contexts are accessible for querying. They should be given a unique identifier, like group_frame and frame_updated_on. 

2025-09-26 Wk 39 Fri - 07:25 +03:00

One problem this decomposes to is the following:

Spawn [[003 Create a natural numbers table and group by divisibility up to N]] ^spawn-invst-3a334f

2025-09-27 Wk 39 Sat - 03:50 +03:00

So we should be able to achieve property-based groups with a filtered duplicator.

2025-09-27 Wk 39 Sat - 04:05 +03:00

Checked [stackoverflow answer](https://stackoverflow.com/a/35067383/6944447) for getting unix timestamp in shell with `date +%s.%N`

2025-09-27 Wk 39 Sat - 04:16 +03:00

Checked [sqlite.org datefunc subseq](https://sqlite.org/lang_datefunc.html#subsec) for `unixepoch('subsec');`

2025-09-27 Wk 39 Sat - 04:24 +03:00

Creating some events for our experiment

```sql
-- in ~/tmp/del/events.sql
CREATE TABLE coin_store_diffs (
  id INTEGER NOT NULL PRIMARY KEY,
  obj_id INTEGER NOT NULL,
  person TEXT NOT NULL,
  coins INTEGER NOT NULL
);

CREATE TABLE coin_store_events (
  id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
  opt_diff_id INTEGER NULL REFERENCES coin_store_diffs(id),
  transactions INTEGER NOT NULL,
  ev_action TEXT CHECK(ev_action IN ('insert', 'update', 'delete', 'frame')) NOT NULL,
  span INTEGER NOT NULL,
  frame INTEGER NOT NULL,
  created_on_ts REAL NOT NULL
);

INSERT INTO coin_store_diffs (id, obj_id, person, coins) VALUES 
	(1, 1, 'person001', 10),
	(2, 1, 'person001', 15),
	(3, 1, 'person001', 5),
	(4, 2, 'person002', 7),
	(5, 2, 'person002', 14),
	(6, 3, 'person003', 8)
;

INSERT INTO coin_store_events (opt_diff_id, transactions, ev_action, span, frame, created_on_ts) VALUES
	(null, 0, 'frame', 1, 1, 1758935304.168087602),
	(1, 1, 'insert', 1, 1, 1758935465.865269566),
	(2, 1, 'update', 1, 1, 1758935520.052601247),
	(3, 1, 'update', 1, 1, 1758935543.867800435),
	(null, 0, 'frame', 2, 1, unixepoch('subsec')),
	(4, 1, 'insert', 2, 1, unixepoch('subsec')),
	(5, 1, 'update', 2, 1, unixepoch('subsec')),
	(6, 1, 'insert', 1, 1, unixepoch('subsec')),
	(7, -3, 'delete', 1, 1, unixepoch('subsec'))
;

.save "events.db"
```

Here we use floating point timestamps, which can be generated via SQL itself, or by ourselves via `date +%s.+%N`

2025-09-27 Wk 39 Sat - 04:32 +03:00

Checked [post](https://towardsdatascience.com/sql-insert-values-with-joined-ids-from-another-table-83ff7f149296/) for inserting values into joined tables

2025-09-27 Wk 39 Sat - 06:02 +03:00

Checked [post](https://learnsql.com/cookbook/how-to-count-the-number-of-rows-in-a-table-in-sql/) for getting the number of rows in a query using `SELECT COUNT(*) as count FROM table;`

We can also filter these, like with

```sql
SELECT COUNT(*)
	FROM coin_store_events
	WHERE ev_action = 'update'
;
```


2025-09-27 Wk 39 Sat - 07:29 +03:00

It works! We can now group and then accumulate events! Multiple problems had to be solved to achieve this. [[001 Creating a basic counter with a recursive CTE in sqlite3|Creating a counter]], [[002 Creating a basic table duplicator with recursive CTE in sqlite3|Duplicating tables N times generically]],  [[003 Create a natural numbers table and group by divisibility up to N|Grouping by property]] as we did with divisibly-by-n groups, fitting extra information on with each duplication for property filtering, and finally putting all together to create the proper groups. And then we grouped by the `grp_id` that's now an enumerable for each unique state of affairs of interest for our objects.

```sql
-- events.db
CREATE TABLE coin_store_diffs (
  id INTEGER NOT NULL PRIMARY KEY,
  obj_id INTEGER NOT NULL,
  person TEXT NOT NULL,
  coins INTEGER NOT NULL
);

CREATE TABLE coin_store_events (
  id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
  opt_diff_id INTEGER NULL REFERENCES coin_store_diffs(id),
  transactions INTEGER NOT NULL,
  ev_action TEXT CHECK(ev_action IN ('insert', 'update', 'delete', 'frame')) NOT NULL,
  span INTEGER NOT NULL,
  frame INTEGER NOT NULL,
  created_on_ts REAL NOT NULL
);

INSERT INTO coin_store_diffs (id, obj_id, person, coins) VALUES 
	(1, 1, 'person001', 10),
	(2, 1, 'person001', 15),
	(3, 1, 'person001', 5),
	(4, 2, 'person002', 7),
	(5, 2, 'person002', 14),
	(6, 3, 'person003', 8),
	(7, 1, 'person001', 0)
;

INSERT INTO coin_store_events (opt_diff_id, transactions, ev_action, span, frame, created_on_ts) VALUES
	(null, 0, 'frame', 1, 1, 1758935304.168087602),
	(1, 1, 'insert', 1, 1, 1758935465.865269566),
	(2, 1, 'update', 1, 1, 1758935520.052601247),
	(3, 1, 'update', 1, 1, 1758935543.867800435),
	(null, 0, 'frame', 2, 1, unixepoch('subsec')),
	(4, 1, 'insert', 2, 1, unixepoch('subsec')),
	(5, 1, 'update', 2, 1, unixepoch('subsec')),
	(6, 1, 'insert', 1, 1, unixepoch('subsec')),
	(7, -3, 'delete', 1, 1, unixepoch('subsec'))
;

CREATE VIEW v_coin_store_events_grouped AS
	WITH RECURSIVE duplicator(dup, obj_id, person, coins, transactions, span, frame, created_on_ts) AS (
		SELECT 1, t2.obj_id, t2.person, t2.coins, t1.transactions, t1.span, t1.frame, t1.created_on_ts
			FROM coin_store_events AS t1
			INNER JOIN coin_store_diffs AS t2
				ON t1.opt_diff_id = t2.id
			WHERE ev_action != 'frame'
		UNION
		SELECT dup + 1, obj_id, person, coins, transactions, span, frame, created_on_ts
			FROM duplicator
			WHERE 
				(dup + 1) <= (
					SELECT COUNT(*) 
						FROM coin_store_events
						WHERE ev_action = 'frame'
				)
	)
	SELECT *
		FROM duplicator AS t1
		JOIN 
			(SELECT ROW_NUMBER() OVER () AS grp_id, * 
				FROM (
					SELECT u1.span AS grp_span, u1.frame AS grp_frame, u1.created_on_ts AS grp_created_on_ts
						FROM coin_store_events AS u1
						WHERE ev_action = 'frame'
				)
			) AS t2
		ON t1.dup = t2.grp_id
	  WHERE
			(t1.frame == t2.grp_frame AND t1.span == t2.grp_span) OR
			(t1.span < t2.grp_span AND t1.created_on_ts < t2.grp_created_on_ts)
	;


CREATE VIEW v_coin_store_objects AS
	SELECT
		obj_id,
		grp_id,
		grp_span,
		grp_frame,
		SUM(transactions) AS transactions,
		MAX(person) AS person,
		SUM(coins) AS coins
	FROM v_coin_store_events_grouped
	GROUP BY 
		obj_id, grp_id, grp_span, grp_frame, grp_created_on_ts
;

.save "events.db"
```

```sh
cat events.sql | sqlite3 && vd events.db
```

This is `v_coin_store_events_grouped`. We can filter this by `grp_id` to get the events that accumulate for that group. This way we can also search events by discrete states of affairs much more easily than if we had filtered the events table directly.

![[Pasted image 20250927073355.png]]

This is `v_coin_store_objects` which accumulates all events into unique object ids per group id. `transactions` will show 0 for deleted objects, otherwise will show how many times the object has been modified. Other properties, such as `coins` now show the accumulated amount of coins the person has!

![[Pasted image 20250927073411.png]]
