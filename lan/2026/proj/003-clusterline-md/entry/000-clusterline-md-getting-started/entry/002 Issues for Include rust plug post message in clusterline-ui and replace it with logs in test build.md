---
context_type: entry
---

Parent: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned by: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/009 Include rust plug post message in clusterline-ui and replace it with logs in test build]]

Spawned in: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/009 Include rust plug post message in clusterline-ui and replace it with logs in test build#^spawn-entry-d49eda|^spawn-entry-d49eda]]

# Journal

## syscall any type rejected by eslint no-explicit-any

- [x] 

2026-07-05 Wk 27 Sun - 18:17 +03:00

```sh
# in /home/lan/src/cloned/cb/lan22h/clusterlinemd/clusterline-ui
INDEX=sb_options_filter_list npm run build

# out (relevant)
/home/lan/src/cloned/cb/lan22h/clusterlinemd/clusterline-ui/src/ts/utils/silverbullet.ts
  1:49  error  Unexpected any. Specify a different type  @typescript-eslint/no-explicit-any
  1:66  error  Unexpected any. Specify a different type  @typescript-eslint/no-explicit-any
  3:57  error  Unexpected any. Specify a different type  @typescript-eslint/no-explicit-any
  3:75  error  Unexpected any. Specify a different type  @typescript-eslint/no-explicit-any

✖ 4 problems (4 errors, 0 warnings)
```

1. https://stackoverflow.com/questions/58467000/how-to-bypass-warning-unexpected-any-specify-a-different-type-typescript-eslin
2. https://stackoverflow.com/a/58575280/6944447
	- note: general disable of eslint rules
3. https://stackoverflow.com/a/63884537/6944447
	- note: specific disable of no-explicit-any

Add ``// eslint-disable-next-line``

```ts
/* eslint-disable @typescript-eslint/no-explicit-any */
// code ...
/* eslint-enable @typescript-eslint/no-explicit-any */
```

```sh
# in /home/lan/src/cloned/cb/lan22h/clusterlinemd/clusterline-ui
INDEX=sb_options_filter_list npm run build

# out (relevant)
/home/lan/src/cloned/cb/lan22h/clusterlinemd/clusterline-ui/src/ts/utils/silverbullet.js
   1:1  error  Definition for rule '@typescript-eslint/no-explicit-any' was not found  @typescript-eslint/no-explicit-any
   2:1  error  Definition for rule '@typescript-eslint/no-explicit-any' was not found  @typescript-eslint/no-explicit-any
  11:1  error  Definition for rule '@typescript-eslint/no-explicit-any' was not found  @typescript-eslint/no-explicit-any

✖ 3 problems (3 errors, 0 warnings)
```

2026-07-05 Wk 27 Sun - 18:26 +03:00

Using `// eslint-disable-next-line` bypasses the `@typescript-eslint/no-explicit-any` rule not being found, but the warning is shown again since we technically lint twice:

```
src/ts/utils/silverbullet.ts:2:49 lint/suspicious/noExplicitAny ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ Unexpected any. Specify a different type.

    1 │ // eslint-disable-next-line
  > 2 │ declare function syscall(name: string, ...args: any[]) : Promise<any>;
      │                                                 ^^^
    3 │
    4 │ // eslint-disable-next-line

  ℹ any disables many type checking rules. Its use should be avoided.
```

https://stackoverflow.com/a/69449844/6944447 suggests we can certify that we simply do not *know* the type with type `unknown`, and this would avoid the need to bypass lint.

That builds with no issues!

We can use `unknown` when we simply don't have a choice in the type. This is the case here, we are not in control of the type of a function owned by silverbullet.

OK