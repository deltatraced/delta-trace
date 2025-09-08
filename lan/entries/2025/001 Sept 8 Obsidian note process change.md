

2025-09-08 Wk 37 Mon - 18:07 +03:00

# 1 File Breakdown Proposal

## 1.1 On the old format

If you look at [[001 Note heading categories and method]] you will find that we created a whole new "type of file".

It always comes with pre-defined category headings, and subheadings are then entries. Like tasks, issues, investigations...

This made files grow without bound in our notes, and made them difficult to read or navigate.

Headings will no longer be used to fulfill file-system duties. Entries will strictly exist in their own files.

Each file must be contextually **atomic**. It will not include the content of multiples contexts in it.

We still want to preserve the fact that there exists a single big context file, and many attach small context files to it that are its tasks, issues, ideas, investigations, etc.

## 1.2 New folder structure

We propose the following folder structure:

```
NNN Task Name/
	tasks/
	issues/
	howtos/
	investigations/
	ideas/
	side-notes/
	NNN Task Name
```

For weekly entries, replace `NNN` with `Wk WW NNN`. 

It is important that the folder and main note name match. This is how we will automatically understand the meaning of this folder structure. 

Here's an example:

```
015 Do my math homework/
	tasks/
		000 Solve Problem 3.4
		001 Solve Problem 3.5
		002 Recap what we learned from material
		...
	issues/
		000 Do not understand book notation in page 65
		001 Contact someone about a confusion
		...
	howtos/
		000 Integrate by Parts
		...
	investigations/
		000 Historical origin and significance of Integration
		...
	ideas/
		000 Copying Wikipedia LaTeX equations
		...
	side-notes/
		000 Comment about the book author website
		...
	015 Do my math homework
```

## 1.3 Index heading

Big context notes should still maintain an **index** of all their small context notes to not require users to rely on folder structure. 

This means that `NNN Task Name` is required to have a heading `Index`:

```
# Index

## Tasks

[[000 First Task]]
[[001 Second Task]]
...

## Issues
```

This follows the same heading procedure detailed in [[001 Note heading categories and method]] and [[Note Headings]], except they are just for indexing.

This will allow us to decouple from the folder structure and navigate through the note content itself. We will also know by the note content that it is a big context by seeing whether it contains an Index heading.

In addition, we used to add `### Pend` to entries currently pending, and similar status segregation headings within one big file. These categories can instead be put as level-3 headings inside `Index/Category`. 

Let's do an Index file example for `015 Do my math homework`:

```
# Index

## Tasks

[[000 Solve Problem 3.4]]
[[002 Recap what we learned from material]]

### Pend

[[001 Solve Problem 3.5]]

## Issues

[[000 Do not understand book notation in page 65]]

### Watch

[[001 Contact someone about a confusion]]

## HowTos

[[000 Integrate by Parts]]

## Investigations

[[000 Historical origin and significance of Integration]]

## Ideas

[[000 Copying Wikipedia LaTeX equations]]

## Side Notes

[[000 Comment about the book author website]]

```

### 1.3.1 What this will acheieve

With this, we achieve the effect that every file is a single atom of context. 

This also opens our entries to the power of having tags.

Obsidian and other tools can treat each context note file as a first class citizens. So graph relations will work out of the box and they can have named relations to other notes.

# 2 Clarified Timestamp Proposal

In every note we've written, we've included timestamps like

```
2025-09-08 Wk 37 Mon 18:56
```

Ok, 18:56 in what timezone? It is unclear. So it makes correlating times with other artifacts like git commits difficult for others, and potentially even for me if I travel countries. 

We will no longer use `HH:mm` time but `HH:mm Z`

You can learn about Zulu time in [time.is Z](https://time.is/Z), and the particular time format string in [momentjs format](https://momentjs.com/docs/#/displaying/format/).

Now we include timestamps like

```
2025-09-08 Wk 37 Mon - 19:02 +03:00
```

to signify UTC+03:00. 

# 3 Consequences

(1)

Our time logging treated headings within a file as sub-entry logs naturally, which gave us a nice big context time logging + small context divisions.

This no longer works for us. We would like to preserve this presentation however. It is still the case that we create a big context, and many attached small contexts, although now they have their own files. Since they follow a predetermined file structure, this should be detected to treat them as sub-entries. 

We are not currently interested in logging time in a tree-fashion. A big/small context seperation is preferable. It's preferable for contexts to remain fairly flat and to leave the dependencies to be expresseed as relations between them.

(2)

Links throughout need to be updated, including inside time logs which are themselves in string representation.

The automatic heading IDs no longer apply, now there are unique categories triplet `NNN` IDs manually inserts like other files. Those are unique to `tasks/`, `issues/`, etc.

(3)

These are very central changes. Almost every note file we've written follows the old standard. This change needs to be made in an automatic fashion.

# 4 Automation

This process should also be automated to reduce human error. Here are some things that should be automated:

- [ ] Command to turn a note file into a big context file and makes it compliant with the category folders, index heading template, and expected folder structure.

- [ ] 
