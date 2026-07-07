---
context_type: entry
---

Parent: [[lan/2026/topic/concept/000 Atomic/wiki/003 Wiki Clusterline Concepts/003 Wiki Clusterline Concepts]]

Spawned by: [[lan/2026/topic/concept/000 Atomic/wikiproc/003 Wiki Proc Clusterline Concepts/003 Wiki Proc Clusterline Concepts]]

Spawned in: [[lan/2026/topic/concept/000 Atomic/wikiproc/003 Wiki Proc Clusterline Concepts/003 Wiki Proc Clusterline Concepts#^spawn-entry-a0e76f|^spawn-entry-a0e76f]]

---

# Benefits of schematized filesystems

A schema-less filesystem imposes many decisions on the note user. Where should we put a note? How should we group notes? One may opt for emergant organization via linking alone and act on a flat list, but the emergent links may not always be optimal for navigation. 

# Specifics of clusterline as an example

For the case of clusterline, 

the note filesystem is a deterministic schema. In the notes root, one may have many users. `lan/` is my user, you may add another, we may have a `shared/` space of collaboration in writing.

Within `/{user}/` we have  `/{user}/YYYY` and `/{user}/archived`. This decision was made so that we get a fresh space every year. We can of course continue working and linking to what was already started in prior years. Inevitably, systems change, and we want to archive things. We track this ahead of time under `/{user}/archived/{archive-id}` and `/{user}/archived/archive-reason` explains why.

So within `/{user}/YYYY` we are now ready to define primary subspaces of operation. 

We have the `main` subspace: `/{user}/YYYY/main`. Every subspace has `cluster types` within it. Think of a `cluster` as the key intents within the subspace. We can have entries, tasks, wikis, etc as cluster types, so that we have clusters under them: `/{user}/YYYY/main/task/000 My First Task/`. Clusters can spawn an arbitrary number of subnotes of specified types, like `task`, `entry`, `investigation`, `issue`, etc. The subspace also mirrors all categories of the subnote types, since clusters are just key starting notes, but notes nonetheless.

We also have other subspaces with different meanings. The meaning is denoted by a `subspace type`. Experiments go to subspace type `/{user}/YYYY/microproj/`. For example: `/{user}/YYYY/microproj/003 My Third Experiment/`. Every subspace like `003 My Third Experiment/` is similar in operation to the `main` subspace. It mirrors key cluster subnote types and includes some others, like the `wiki/` cluster subtype.

Normal projects go under subspace type `/{user}/YYYY/proj/`. These are the more long-term or user-facing projects for a programmer for example.

We have a special subspace type `/{user}/YYYY/topic`. It allows the user to create an arbitrary number of subspace types under it which are interpreted to be a key grouping of subspaces. This allows us to have something like `/{user}/YYYY/topic/book-notes` which we then define to include one subspace per author, and multiple clusters per work for example. For example, `/{user}/YYYY/topic/book-notes/015 Some Author/wiki/002 Wiki Some Work/`.

subspaces, clusters, and subnotes all begin with a triplet number `NNN` which helps giving them a stable point of reference. We can refer to them by number, and they are more stable in case of title renames.

There are some other organizational additions, both cluster type and subspace type folders support a `/st/{status}/` folder which can use the filesystem to indicate that some content is todo, done, pending, idea, or mightdo which supports use cases like [[004 Use status mightdo for works we arent yet fully commited to doing]]

It would be tedious to maintain all this manually, so we ought to use some tooling that respects this schema. This is the purpose of the `clusterline-md` set of tools: [[001 Goals for clusterlinemd]]

# Generalizing principles from clusterline

I made many choices that fit my way of working in the previous section. What's important is that I can immediately know where to put a new note. Is it a new originating intent? It's a cluster. Am I reading a new book? It's a subspace under the broad activity of `book-notes`. This schema does not go on forever, it only has a few levels, but they all have meaning. They assign types of things, or act as key groupings.

Within a note, it may link to any other note, even outside its own cluster, but the cluster tracks the origin and often key intents that brought those notes to be. These may be projects, named activities, a field of study, an intent we want to realize independent of the strategy we may execute, and so on.

When I created the semantic grouping of `book-notes`, I already decided where all book notes indexed by author and title would go once, and so the decision to add notes for a new author are easier later. I opted for a flat index of semantic groups like `book-notes` and `studying-math` because allowing them to be tree-like again reintroduces that tension of deciding where to put new notes. Navigating an arbitrarily created tree by a user is difficult both from a tooling point of view and just general use, so this encourages the user to think in a more indexing type of way, which lowers the cognitive load of adding new content but also remains organized and easy to navigate.