---
context_type: task
status: todo
---

Parent: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned by: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned in: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started#^spawn-task-3ab4dd|^spawn-task-3ab4dd]]

Overview: [[000 Overview clusterline getting started]]

Goal: [[000 Overview Goals for clusterlinemd#Version 0.1 Initial Functionality]]

# Journal

2026-06-15 Wk 25 Mon - 10:28 +03:00

We need to be able to open a menu similar to Ctrl+K

> **aside** 
> It would be nice if we could actually interact with the mentions.
>
> A hack I do for now is having vimium-c on firefox with the filter `^ f` to only allow f. To get the focus out of the editor, ctrl+, then escape, which opens config and leaves, and gives no focus to the editor. Then you can use f.
>
> Adding `^ f j k` gives even more smooth quick motion. You can get back to the editor with Ctrl+/ Escape.
>
> Of course, proper link hint following in a plug would be optimal.


2026-06-15 Wk 25 Mon - 12:43 +03:00

Let’s look at the command `Open Command Palette` in the silverbullet [source](https://github.com/silverbulletmd/silverbullet). This points to `client.startCommandPalette()`.

```ts
// in client/client.ts
  async startCommandPalette() {
    const commands = this.ui.viewState.commands;
    await this.commandAugmenter.augmentObjectMap(commands);
    this.ui.viewDispatch({
      type: "show-palette",
      commands,
      context: client.getContext(),
    });
  }
```

They also do not expose `this.ui` directly, instead they expose

```ts
// in plug-api/syscalls/editor.ts
/**
 * Dispatch a CodeMirror transaction: https://codemirror.net/docs/ref/#state.Transaction
 */
export function dispatch(change: any): Promise<void> {
  return syscall("editor.dispatch", change);
}

// in client/plugos/syscalls/editor.ts > fn editorSyscalls
    "editor.dispatch": (_ctx, change: Transaction) => {
      client.editorView.dispatch(change);
    },
```

So let's see how they actually implement `show-palette`. This is the action through the reducer for `viewDispatch`:

```ts
// in client/reducer.ts > fn reducer
    case "show-palette": {
      return {
        ...state,
        showCommandPalette: true,
        showPageNavigator: false,
        showFilterBox: false,
        showCommandPaletteContext: action.context,
        commands: action.commands,
      };
    }
```

It's simply a part of the `MainUI` generated html.

```ts
// in client/editor_ui.tsx > fn ViewComponent
        {viewState.showCommandPalette && (
          <CommandPalette
			  // ...
          />
        )}
```

So no hope for a plug to define its own fuzzy filter window through this. But is it possible for a plug to create its own html elements to add?

They also mention that we can create widgets with space-lua in the README.

https://silverbullet.md/Architecture

Some plugs we've seen make major changes to the UI itself. Let's take a look at

https://silverbullet.md/Plugs $\to$ https://github.com/MrMugame/silversearch

This obviously has a fuzzy window, so how did they do it?

https://github.com/MrMugame/silversearch/blob/main/modal/modal.ts

So they have access to `document` directly, and they mount to `#container` which we can find in silverbullet `plugs/image-viewer/viewer.ts`. 

This mounting action is described here: https://svelte.dev/docs/svelte/imperative-component-api

2026-06-15 Wk 25 Mon - 18:07 +03:00

But I don't have access to `document` in typescript via the plug:

```ts
// in src/hello.ts
export async function helloWorld() {
  console.log(`AAA00 (doc ${JSON.stringify(document.URL)})`);
}
```

```
[hello plug] An exception was thrown as a result of invoking function helloWorld error: document is not defined
```

Actually in [here](https://github.com/MrMugame/silversearch/blob/b33127a907db5a46926f9b569f2e3ae4f2d61f64/worker/silversearch.ts#L50) they're using `editor.showPanel` to pass in the html:

```ts
export async function openSearch(defaultQuery: string  = ""): Promise<void> {
    await editor.showPanel(
        "modal",
        // We can't have a falsy value (0) here, because of some silverbullet oddities
        1,
        html,
        defaultQuery ? `globalThis.DEFAULT_QUERY = ${JSON.stringify(defaultQuery)};` + script : script,
    );
}
```

`html` and `script` being prebuilt from `modal` we've seen before.

So we need to be able to build `HTMLElements` in rust too.

https://book.leptos.dev/web_sys.html

This seems to be fairly similar to preact with defining components.

https://github.com/leptos-rs/leptos

https://book.leptos.dev/appendix_reactive_graph.html

2026-06-15 Wk 25 Mon - 22:43 +03:00

The basic example doesn't work out of the box, but shouldn't jump to make an issue for this. This seems to be a nightly vs stable semantics issue: https://github.com/leptos-rs/book/issues/54

```rust
use leptos::prelude::*;

#[component]
fn App() -> impl IntoView {
    let (count, set_count) = signal(0);

    view! {
        <button
            on:click=move |_| set_count.set(3)
        >
            "Click me: "
            {count}
        </button>
        <p>
            "Double count: "
            {move || count.get() * 2}
        </p>
    }
}
```

2026-06-17 Wk 25 Wed - 10:50 +03:00

Ran into [[000 WESR leptos mismatches types on lower edition]]

But changing edition to `2021` now is giving us a lot of errors in the API, so I guess at least that's good. We should be compatible with newest.

2026-06-18 Wk 25 Thu - 13:36 +03:00

Actually the errors were just because with changing `Cargo.toml`, I had set wasm-bindgen as optional, reverted back to required and all is good.

