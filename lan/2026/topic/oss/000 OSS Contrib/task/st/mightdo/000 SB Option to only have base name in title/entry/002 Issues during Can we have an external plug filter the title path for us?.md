---
context_type: entry
---

Parent: [[lan/2026/topic/oss/000 OSS Contrib/task/st/mightdo/000 SB Option to only have base name in title/000 SB Option to only have base name in title]]

Spawned by: [[lan/2026/topic/oss/000 OSS Contrib/task/st/mightdo/000 SB Option to only have base name in title/investigation/003 Can we have an external plug filter the title path for us?]]

Spawned in: [[lan/2026/topic/oss/000 OSS Contrib/task/st/mightdo/000 SB Option to only have base name in title/investigation/003 Can we have an external plug filter the title path for us?#^spawn-entry-dd47e7|^spawn-entry-dd47e7]]

# Journal

2026-06-11 Wk 24 Thu - 01:05 +03:00

	2026-06-11 Wk 24 Thu - 00:36 +03:00
	
	- As mentioned in [[000 Silverbullet How is the title rendered when a page is loaded?]],
		- `client/editor_ui.tsx > TopBar > pageName`
	
	Instead of 
	
	```tsx
	!viewState.current ? "" : getNameFromPath(pathToBasename(viewState.current.path))
	```
	
	We use
	
	```ts
	import {
	  renderPageTitle,
	} from "@silverbulletmd/silverbullet/lib/ref";
	
	// ...
	
	!viewState.current ? "" : renderPageTitle(viewState.current.path)
	```
	
	with `renderPageTitle` defined in `plug-api/lib/ref.ts` as

We run into an issue here. `renderPageTitle` will need to be async, since services are async. However, `ViewComponent()` is synchronous!