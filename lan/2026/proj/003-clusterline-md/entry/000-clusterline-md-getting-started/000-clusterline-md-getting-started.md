
# Journal

2026-05-22 Wk 21 Fri - 08:39 +03:00

Coming from [[lan/2026/entry/000-2026-may-restructure/000-2026-may-restructure]]

We want to migrate from obsidian.md to silverbullet.md which is open source and which we are currently running from a local build!

Features, sorted by high priority for use:

- [x] Add a command to put the current timestamp
- [ ] Add a command to copy the link of the current page in a format directly used in `[[]]`
- [ ] Add a command to navigate to all subnotes, and one to all main notes.
- [ ] Ask for a user, and a project category, spawn a project.
- [ ] Ask for a user, and a project category, spawn a document.
- [ ] Ask for a project, spawn a cluster.
- [ ] Spawn notes within a cluster
- [ ] Detect when the cursor is on an aspiring note `[[note]]`, and use the name when user asks to spawn a note
- [ ] Ask for a project, spawn both a wiki cluster and a dual process cluster, have them reference one another.
- [ ] Add a command to automatically fix non-complete spawn note lines under cursor from obsidian
- [ ] Autogenerate `project-index` for all projects in the space, ‘cluster-index`, for all clusters in the space, and `note-index` for all notes in the space, all alphabetically sorted
- [ ] For each cluster, create the following in an `autogen/` folder, where `X` is the number of the cluster: `000 X Context Index`, `001 X Spawn Tree`.
- [ ] Add a command to paste and move images to attachment/ after paste

Let’s try to develop the plugin with rust/webasm rather than ts if possible.

2026-05-22 Wk 21 Fri - 21:11 +03:00

Spawn [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/000 Create a silverbullet plugin]] ^spawn-task-c536a0


