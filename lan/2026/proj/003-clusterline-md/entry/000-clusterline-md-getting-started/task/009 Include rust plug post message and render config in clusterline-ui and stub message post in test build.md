---
context_type: task
status: todo
---

Parent: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned by: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text]]

Spawned in: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text#^spawn-task-1070ec|^spawn-task-1070ec]]

Overview: [[000 Overview clusterline getting started]]

Issues encountered: [[002 Issues for Include rust plug post message in clusterline-ui and replace it with logs in test build]]

# Decision

1. `syscall` is declared in typescript. It is tested for existence with a try catch for `ReferenceError`. This way we know if we are in the test environment and we're able to vary the meaning of `post_message` accordingly.
2. The UI expects the consumer (rust) to replace a hidden tag in HTML with a JSON string config. This is used to initialize the widget with the data it needs, like the service so that rust remains stateless and the options for the `sb_options_filter_list`:

```html
<head>
	<span id="render_config_json" hidden>REPLACE_RENDER_CONFIG_JSON</span>
</head>
```

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
    } catch (_) {
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
	<span id="render_config_json" hidden>REPLACE_RENDER_CONFIG_JSON</span>
</head>
```

We can also use this to pass the necessary information like the items to be displayed which can only be known by rust precisely at the time of service invocation rather than later by message repsonse.

We could have requires that the functions interfacing with the UI be built during service invocation, but this should be more flexible and not require this. Just pass the needed information right from the start, as we did when we generated the html with exactly the context it needs earlier in rust hardcoded html/script strings.

2026-07-06 Wk 28 Mon - 03:38 +03:00

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse

```ts
public static from_untyped_array(arr: Array<unknown>): Array<SbOption> | DetailedError<SbOptionFromUntypedArrayError> {
```

We're not able to use `arr[i]` here because we get an error `Object is of type 'unknown'.ts(18046)`. Better have it as an `Array<object>`.

No, even with `Array<object>` it complains even though we are checking if the property is defined. That seems to only be permitted for type `any`, which the linters won't let us use.

1. https://stackoverflow.com/questions/49707327/typescript-check-if-property-in-object-in-typesafe-way
2. https://stackoverflow.com/a/49707533/6944447

We can use the string check `'prop' in obj`. Once we are in a branch where this is true, intellisense and typescript will acknowledge that the property now exists.

2026-07-06 Wk 28 Mon - 04:49 +03:00

For some reason `typeof` recognizes `undefined` but not `null`.

But we can do `(elem.opt_hint == null || typeof(elem.opt_hint) === 'string') &&` and now it recognizes `elem.opt_hint` as `(property) opt_hint: string | null | undefined`.

Instead of `from_untyped_array` we will just implement `from_obj` which is more fundamental/simple.

2026-07-06 Wk 28 Mon - 05:38 +03:00

```rust
    let html = include_str!("./inc/index_sb_options_filter_list.html")
        .replace(modal_selector::DEFAULT_RENDER_CONFIG_VALUE, &format!(r#" {{
            \"service\": \"greet\",
            \"items\": [
                {{ 
                    \"name\":           \"Apple!\",
                    \"opt_hint\":       \"Juicy Hint\",
                    \"desc\":           \"Apple Juice is good for you\",
                    \"active_hint\":    true,
                    \"selected\":       false,
                }},

                {{ 
                    \"name\":           \"Rocks...\",
                    \"opt_hint\":       \"Earthy Hint\",
                    \"desc\":           \"Chewing rocks is bad for you\",
                    \"active_hint\":    true,
                    \"selected\":       true,
                }},

                {{ 
                    \"name\":           \"Ice cream...\",
                    \"opt_hint\":       \"Cold Hint\",
                    \"desc\":           \"Chocolate Mint Ice cream for 5 dollars\",
                    \"active_hint\":    false,
                    \"selected\":       false,
                }}
            ]
        }}"#))
    ;
```

This ended up passing with the escapes over to javascript. Need to remove the escapes. We also need to make sure there's no comma at the end of each object.

![[Pasted image 20260706054417.png]]

It works! We're able to pass in the options from rust as JSON to javascript which are parsed and validated according to the schema and then used to render the items from the initial effect!

```
[clusterline plug] panicked at src/plug/message_post.rs:20:5: Received unsubsribed message on sb_options_filter_list - onCancel - { service: "greet" }
```

We need to also make sure we subscribe to these test events, but this proves that we're able to receive the messages as well with the correct service tagged.

However, when we cancel, and we use the greet command again to test bring the modal selector back up, it won't work a second time until a refresh.

2026-07-06 Wk 28 Mon - 06:55 +03:00

Proper semantics for creating the UI bundle:

```rust
    let sb_options = inv(sb_options_filter_list::SbOptionVec::new(
        vec![
            SbOption { 
                name: "Apple!".to_owned(), 
                opt_hint: Some("Juicy Hint".to_owned()), 
                desc: "Apple Juice is good for you".to_owned(), 
                active_hint: true, 
                selected: false,
            },

            SbOption { 
                name: "Rocks...".to_owned(), 
                opt_hint: Some("Earthy Hint".to_owned()), 
                desc: "Chewing rocks is bad for you".to_owned(), 
                active_hint: true, 
                selected: true,
            },

            SbOption { 
                name: "Ice cream...".to_owned(), 
                opt_hint: Some("Cold Hint".to_owned()), 
                desc: "Chocolate Mint Ice cream for 5 dollars".to_owned(), 
                active_hint: false, 
                selected: false,
            },

            SbOption { 
                name: "Secret~".to_owned(), 
                opt_hint: None, 
                desc: "".to_owned(), 
                active_hint: false, 
                selected: false,
            },
        ]
    ));

    let ui_bundle = widgets::sb_options_filter_list::new_ui_bundle("greet", sb_options);

    widgets::sb_options_filter_list::show_panel(&ui_bundle).await
```

This shows up alright, and we do respond to the cancel event now:

```
# javascript
[sb_dialog1 > on:cancel]
# rust
[clusterline plug] [rust|on_canceled] You posted a message! { service: "greet" }
[clusterline plug] (service some_service) (event on_canceled)
# javascript
message response for cancel: on_canceled OK
```

We do not yet parse the json message, so rust is reporting `(service some_service)` but we can see that the JSON message with the correct service is there.

[[003 Modal loaded to silverbullet as ui bundle fails to deallocate on cancel due to rust future async ignored]]
