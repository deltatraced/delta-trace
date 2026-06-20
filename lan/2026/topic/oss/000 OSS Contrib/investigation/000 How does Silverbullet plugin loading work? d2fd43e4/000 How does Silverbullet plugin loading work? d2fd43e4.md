
# Journal

2026-06-18 Wk 25 Thu - 20:05 +03:00

https://github.com/silverbulletmd/silverbullet/tree/main

Sometimes our plug just doesn't load. Sometimes it takes multiple tries of the `Plugs: Reload` command. Another issue is that currently my plugin is not loading at all with the new change merging to rust. I need to learn about how this works and how I can test it.

1. `plugs/editor/editor.plug.yaml > reloadPlugsCommand`
2. `client/client.ts > fn loadPlugs`
3. `client/client_system.ts > fn reloadPlugsFromSpace`

Spawn [[lan/2026/topic/oss/000 OSS Contrib/investigation/000 How does Silverbullet plugin loading work? d2fd43e4/investigation/000 SB How are the plugin files fetched? d2fd43e4]] ^spawn-invst-9222a7

So this uses `WalkDir::new` which reports that it follows sym links. But my symlink plug was not found during debugging. 

Actually the gitignore filter in  `<DiskSpacePrimitives as impl SpacePrimitives>::fetch_file_list` explains why. I ignored my own plug. Before rust, it was not filter for gitignore but it is now.