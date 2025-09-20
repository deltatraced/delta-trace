---
parent: "[[002 Move credit_store_demo project to deltachives]]"
spawned_by: "[[002 Move credit_store_demo project to deltachives]]"
context_type: issue
status: skipped
---

Parent: [[002 Move credit_store_demo project to deltachives]]

Spawned by: [[002 Move credit_store_demo project to deltachives]] 

Spawned in: [[002 Move credit_store_demo project to deltachives#^spawn-issue-861a54|^spawn-issue-861a54]]

# 1 Journal

2025-09-19 Wk 38 Fri - 19:33 +03:00

This turned out to be extremely picky with its constraints:

```rust
pub trait UpdateHeadRec {
    fn update_head_rec<Model, Tab>(
        conn: &mut SqliteConnection,
        table: Tab,
        object: Model,
        tx_ea_msg: Sender<EaThreadMessage>,
    ) -> Result<usize, HeadRecOpError>
    where
        Model: AsChangeset<Target = Tab> + Insertable<Tab>,
        Tab: Identifiable
            + QueryFragment<Sqlite>
            + HasTable<Table = Tab>
            + diesel::Table
            + IntoUpdateTarget
            + AsChangeset
            + GetTableName,
        <Tab as QuerySource>::FromClause: QueryFragment<Sqlite>,
        <Tab as IntoUpdateTarget>::WhereClause: QueryFragment<Sqlite>,
        <Model as AsChangeset>::Changeset: QueryFragment<Sqlite>,
        UpdateStatement<
            Tab,
            <Tab as IntoUpdateTarget>::WhereClause,
            <Model as AsChangeset>::Changeset,
        >: AsQuery,
    {
        let written = diesel::update(table)
            .set(object)
            .execute(conn)?;

        tx_ea_msg.send(EaThreadMessage::DbWrite {
            table_name: table.get_table_name(),
        })?;

        Ok(written)
    }
}
```

I arrived here in a mixture of experimentation, using this [stackoverflow answer](https://stackoverflow.com/a/73461668/6944447) as template replacing Pg with Sqlite, and some LLM assistance, but they likely don't want this to be built explicitly like this as the answer suggests.

I was also not able to get it to work generically with returning records, but this can do.

A more sustainable way to do this might be to create derives or macros.

2025-09-19 Wk 38 Fri - 19:45 +03:00

Automation for Insert was much simpler:

```rust
pub trait InsertHeadRec {
    type NewRec;
    type Rec;

    fn insert_head_rec(
        conn: &mut SqliteConnection,
        table: impl Table,
        object: Self::NewRec,
        tx_ea_msg: Sender<EaThreadMessage>,
    ) -> Result<Self::Rec, HeadRecOpError> {
        let out = diesel::insert_into(table)
            .values(&object)
            .returning(CreditStoreHeadRec::as_returning())
            .get_result(conn)?;

        tx_ea_msg.send(EaThreadMessage::DbWrite {
            table_name: object.get_table_group_id(),
        })?;

        Ok(out)
    }
}
```

2025-09-19 Wk 38 Fri - 20:25 +03:00

Or maybe not so simple since it gives us this error

```rust
pub mod autogen;
^^^
```

```
error[E0275]: overflow evaluating the requirement `diesel::expression::operators::Eq<_, &_>: diesel::Insertable<_>`
  |
  = help: consider increasing the recursion limit by adding a `#![recursion_limit = "256"]` attribute to your crate (`credit_store_demo`)
  = note: required for `&diesel::expression::operators::Eq<_, _>` to implement `diesel::Insertable<_>`
  = note: 126 redundant requirements hidden
  = note: required for `&Option<Option<Option<Option<Option<Option<Option<Option<...>>>>>>>>` to implement `diesel::Insertable<_>`
  = note: the full name for the type has been written to '/home/lan/src/cloned/gh/deltachives/2025-002-credit-store-demo-rs/target/debug/deps/credit_store_demo-4fa9e0a64f9f4ad8.long-type-14634543152730437809.txt'
  = note: consider using `--verbose` to print the full type name to the console

```

2025-09-19 Wk 38 Fri - 20:26 +03:00

This happens for both the update and the insert generic traits.