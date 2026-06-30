---
context_type: issue
status: todo
---

Parent: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned by: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text]]

Spawned in: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text#^spawn-issue-a3158e|^spawn-issue-a3158e]]

# Journal

2026-06-30 Wk 27 Tue - 21:04 +03:00

We get the error

```
Uncaught Error: Unregistered syscall clusterline.cmd_aaa
```

and also

```
Uncaught Error: Unregistered syscall clusterline.ts_aaa
```

as we vary tying `cmd_aaa` and `ts_aaa` via this rust code:

```rust
#[wasm_bindgen]
pub async fn greet() {
    init();

    flash_notification("(0) Rust greets you!", NotificationType::Info).await;

    let html = r#"
<div>
  <p>Hi!</p>
  <button id="btn">Click me: <span id="count">0</span></button>
</div>
"#;

    let script = r#"
let count = 0;
document.getElementById('btn').onclick = () => {
    count++;
    document.getElementById('count').textContent = count;
    syscall('clusterline.ts_aaa');
};
"#;

    editor::show_panel(PanelLocation::Lhs, /*mode*/ inv(U32Nz::new(1)), html, script).await;
}

#[wasm_bindgen]
pub async fn test() {
    init();

    flash_notification("Test test!", NotificationType::Info).await;
}
```

and this `clusterline.plug.yaml` portion:

```sh
  greet:
    path: src/clusterline.ts:ts_greet
    command:
      name: "Clusterline: greet"
  cmd_aaa:
    path: src/clusterline.ts:ts_aaa
    command:
      name: "Clusterline: aaa"
```

2026-06-30 Wk 27 Tue - 21:22 +03:00

Trying

```rust
#[wasm_bindgen]
pub async fn greet() {
    init();

    flash_notification("(1) Rust greets you!", NotificationType::Info).await;

    system::invoke_function("ts_aaa", &vec![]).await;
```
