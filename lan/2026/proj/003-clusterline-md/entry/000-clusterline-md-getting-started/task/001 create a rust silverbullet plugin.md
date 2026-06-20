---
context_type: task
status: done
---

Parent: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned by: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/000 Create a silverbullet plugin]]

Spawned in: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/000 Create a silverbullet plugin#^spawn-task-fa5f84|^spawn-task-fa5f84]]

# Journal

2026-05-23 Wk 22 Sat - 10:33 +03:00

Spawn [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/002 Figure out how to get a simple rust plugin to work with Silverbullet]] ^spawn-task-eac5da

2026-05-25 Wk 22 Mon - 04:40 +03:00

Spawn [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/003 Create a Rust FFI for Silverbullet plug api]] ^spawn-task-f6aec1

2026-06-04 Wk 23 Thu - 16:08 +03:00

Spawn [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/issue/001 Using async for js extern instead of future leads to wrong pkg generation via wasm-pack]] ^spawn-issue-7ed0f8

2026-06-04 Wk 23 Thu - 07:28 +03:00

Spawn [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/004 File an issue for improving documentation for async js rust integration with wasm-pack]] ^spawn-task-5426a0

## Write command for making notes absolute

2026-06-08 Wk 24 Mon - 13:34 +03:00

### So how do I open the Ctrl+/ menu and put options for the user? Needed to give the user a list of notes to select which to open.

`editor::open_page_navigator` is unclear of what possible modes there are.

In the source of silverbullet, we see:

```ts
    "editor.openPageNavigator": (
      _ctx,
      mode: "page" | "meta" | "document" | "all" = "page",
```

It’s actually clear. I did not transmit over the type:

```ts
/**
 * Opens the page navigator
 * @param mode the mode to open the navigator in
 */
export function openPageNavigator(
  mode: "page" | "meta" | "document" | "all" = "page",
): Promise<void> {
  return syscall("editor.openPageNavigator", mode);
}
```

That just opens the page navigator. I cannot customize it with my own content for the user and act accordingly.

The implementation uses `this.ui.viewDispatch({ type: "start-navigate", mode });`, so we need to understand this next.

Similar command exposed:

```ts
    "editor.dispatch": (_ctx, change: Transaction) => {
      client.editorView.dispatch(change);
    },
```


```rs
/**
 * Dispatch a CodeMirror transaction: https://codemirror.net/docs/ref/#state.Transaction
 */
pub async fn dispatch(change: JsValue) {
    imported::dispatch(change).await.unwrap()
}
```


Specifically for `editorView`: https://codemirror.net/docs/ref/#view.EditorView.constructor^config.dispatchTransactions

Checking how silverbullet does `new EditorView(...)` $\to$ `createEditorState`

## End