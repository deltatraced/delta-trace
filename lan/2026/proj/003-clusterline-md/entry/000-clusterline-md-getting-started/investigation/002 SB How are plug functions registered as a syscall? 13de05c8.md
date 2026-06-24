---
context_type: investigation
status: todo
---

Parent: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/000-clusterline-md-getting-started]]

Spawned by: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text]]

Spawned in: [[lan/2026/proj/003-clusterline-md/entry/000-clusterline-md-getting-started/task/005 Add a custom fuzzy selector window with some text#^spawn-invst-618590|^spawn-invst-618590]]

# Solution

TODO

# Journal

2026-06-21 Wk 25 Sun - 19:16 +03:00

Registeration occurs through `client/plugos/system.ts > fn registerSyscalls`

`client/plugos/hooks/syscall.ts > fn <SyscallHook as impl Hook<SyscallHookT>>::registerSyscalls` registers sys calls per plug function.

These values come from `plug.manifest!.functions`

- https://www.typescriptlang.org/docs/
	- `?` for optional properties: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#optional-properties
	- `!` in an expression is a non-null assertion operator: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#non-null-assertion-operator-postfix-
		- not checked, it tells the type checker we know this is the case.
		- This is for terms and is NOT to be confused with definite assignment assertion operator `!`.
	- `!` in a type is a definite assignment assertion operator: https://www.typescriptlang.org/docs/handbook/2/classes.html#--strictpropertyinitialization
		- This means that a class field is initialized by means other than the class constructor

The manifest is loaded in `client/plugos/plug.ts > fn Plug<HookT>::createLazily<HookT>` via `system.options.manifestCache!.getManifest`

How are the functions loaded?

1. `client/plugos/plug.ts > fn Plug<HookT>::createLazily<HookT>`
2. `client/plugos/manifest_cache.ts > fn ManifestCache<T>::getManifest`
	- investigation
		- [x] How is it initialized for plugins?
			1. `client/plugos/system.ts > System<Hook> > fn constructor`
				- note
					- Initializes `client/plugos/manifest_cache.ts > InMemoryManifestCache<T>`
3. `client/plugos/manifest_cache.ts > fn <InMemoryManifestCache<T> as impl ManifestCache<T>>::getManifest`
	- investigation
		- [x]  How does this cache the manifest?
			- note
				- As an `XCache` it just caches the operation, which in this case is `getManifest` internally for performance.
			1. `client/plugos/system.ts > System<Hook> > fn constructor`
			2. `client/plugos/manifest_cache.ts > fn <InMemoryManifestCache<T> as impl ManifestCache<T>>::getManifest`
