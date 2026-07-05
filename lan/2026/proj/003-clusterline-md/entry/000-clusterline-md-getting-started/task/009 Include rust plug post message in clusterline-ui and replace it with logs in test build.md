---
context_type: task
status: todo
---

Parent: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned by: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text]]

Spawned in: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text#^spawn-task-1070ec|^spawn-task-1070ec]]

Overview: [[000 Overview clusterline getting started]]

Issues encountered: [[002 Issues for Include rust plug post message in clusterline-ui and replace it with logs in test build]]

# Journal

2026-07-05 Wk 27 Sun - 07:59 +03:00

```ts
document.getElementById('btn_reset').onclick = () => {{
    const resp_promise = syscall('system.invokeFunction', 'clusterline.post_message', ['{MODULE_TOPIC}', 'btn_reset_onclick', '{{' +
        '"service": {service}' +
    '}}']);

    resp_promise.then((resp) => {{
        console.log("btn_reset awaited response: " + resp);
    }})
}};
```

This is how we had javascript exchange messages with the rust plug. Now we want it to work with `clusterline-ui`.

We should be able to simply check if syscall exists under a wrapper, or if not, just stub it with console.log.

> **aside** `??` syntax:  https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#nullish-coalescing

https://stackoverflow.com/questions/43335962/purpose-of-declare-keyword-in-typescript

```ts
declare function syscall(name: string, ...args: any[]) : Promise<any>;
```

This should remove the `cannot find Name` typescript error. Even though it's declared, it might still not exist, so we should check.

2026-07-05 Wk 27 Sun - 18:09 +03:00

Spawn [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/entry/002 Issues for Include rust plug post message in clusterline-ui and replace it with logs in test build]] ^spawn-entry-d49eda

[[002 Issues for Include rust plug post message in clusterline-ui and replace it with logs in test build#syscall any type rejected by eslint no-explicit-any]]

2026-07-05 Wk 27 Sun - 18:35 +03:00

```ts
export function get_syscall():
	| ((name: string, ...args: unknown[]) => Promise<unknown>)
	| null {
	if (syscall === undefined) {
		return null;
	} else {
		return syscall;
	}
}
```

This results in an exception in the test build:

```
Uncaught (in promise) ReferenceError: syscall is not defined
    get_syscall http://localhost:8002/index.js:147  
    post_message http://localhost:8002/index.js:154  
    main_sb_options_filter_list http://localhost:8002/index.js:393
```

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch

```ts
export function get_syscall():
	| ((name: string, ...args: unknown[]) => Promise<unknown>)
	| null {
    try {
        return syscall;
    } catch (e) {
        return null;
    }
}
```

Now we can use this to know whether we're in a test environment in runtime.

2026-07-05 Wk 27 Sun - 18:42 +03:00

Another thing to resolve about post message is that we would like to know the service that initiated the UI. The rust plug is stateless and we would prefer not to have any global state.

We did this prior by adding it directly via rust-rendering of javascript. 

https://www.w3schools.com/TAGS/att_hidden.asp

We could do something like this that is replaced by rust: 

```html
<head>
	<span id="render_config_json" hidden>{}</span>
</head>
```