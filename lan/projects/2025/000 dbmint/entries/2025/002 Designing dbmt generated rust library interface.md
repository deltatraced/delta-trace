---
status: pend
---

#design

# 1 Objective

To capture design considerations for the generated rust library from a dbmt schema file.

# 2 Journal

# 3 Tasks

# 4 Issues

# 5 HowTos

# 6 Investigations

# 7 Ideas

## 7.1 Features we can add

### 7.1.1 Tablegroup to trait

2025-08-21 Wk 34 Thu - 10:45

dbml supports table groups. We need to be able to support this too. Suppose you have three tables, `A`, `B`, and `Common` in the relationship `A <- Common -> B`. This could be implemented by the pattern where `Common` joins with every table that uses it, but this won't scale well, and now it will be modified whenever a new user shows up.

You could also implement it as `{A, B} -> Common` and this is more scalable since `Common` remains untouched while new users `{A, B, C, ...}` join a primary key from it. But this introduced fragmentation, and the chains can grow complex in time. 

With table groups, we can get the modularity at the schema level while keeping the resulting database simpler, since we will have to worry about fewer join relationships. 

We can have the Rust generated library understand table groups for any table as well. The key idea is that groups should be akin to rust traits. 

We should be able to...
- accept multiple tables into a function sharing the same group. 
- iterate through all tables sharing a group
- Implement hooks on table groups that work on all tables that share it

Tablegroups can have data access settings. The strictest setting is applied when multiple are detected.

### 7.1.2 Relationships as Traits

2025-08-21 Wk 34 Thu - 11:06

Often we have relationships between entities. Users follow other users. Users post posts. 

One way to express a relationship is as `Users <- RelPosts -> Posts`.

But even though it's a user that posts a post, it's unclear here which is the source and which is the sink. We want the user to be the source, and the post to be the sink.

Another way is to do `Source -> Relationnship -> Sink`, so that `Users -> RelPosts -> Posts`.  But there is also an issue with this. Sure the source and sink are clear now, but it could be assumed that the material composition of the user's data is a relationship, which is logically false.

Really, using `Users <- RelPosts -> Posts` is the most enclosed option, this way the tables `Users` and `Posts` remain self-encapsulated and fully material (join structures in them express only additional data and not ways of relating between data).

For this, we propose two marks, `%relation` for a table, and optionally a `%relation_source` for a column in it to signify a directed relation. 

This can also work with [[#7.1.1 Tablegroup to trait]] in that relationship tables may be divided into table groups. For example, maybe both `Users` and `Robots` can post. so we can have a `GrRelPosts` with just the sink information, a foreign key to `Posts`, and we can then create `RelUserPosts` and `RelRobotPosts` which have `Users` or `Robots` as a source, and share a common `GrRelPosts` for sink. This way we could iterate over all such relationships, like all that can post.

# 8 Side Notes
# 9 External Links

# 10 References