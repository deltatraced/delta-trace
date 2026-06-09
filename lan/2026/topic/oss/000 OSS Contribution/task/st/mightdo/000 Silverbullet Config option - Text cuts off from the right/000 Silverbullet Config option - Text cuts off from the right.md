
# Problem

The paths to the files I have are long. If the title includes the entire path, I can no longer see the title. I use those notes to know what I'm currently paying attention to, if I can't see the title clearly, then that mental benefit is gone.

We need an option to ensure that the title text can be displayed without the entire path.

# Journal

2026-06-08 Wk 24 Mon - 16:26 +03:00

```sh
# in /home/lan/src/cloned/gh/LanHikari22/forked/silverbulletmd/branches
git clone git@github.com:LanHikari22/silverbullet.git
mv silverbullet silverbullet@title-text-config
```

https://github.com/silverbulletmd/silverbullet/blob/main/CONTRIBUTING.md

```sh
# in /home/lan/src/cloned/gh/LanHikari22/forked/silverbulletmd/branches/silverbullet@title-text-config

git branch -m title-text-config
make setup
```

I already installed `air`, so we can already start the webapp with something like

```sh
# in /home/lan/src/cloned/gh/LanHikari22/forked/silverbulletmd/branches/silverbullet@title-text-config

air ~/src/cloned/gh/deltatraced/delta-trace/
```

Good practice would be to mention a needed feature before we set out to do the PR. Let's do that.

2026-06-08 Wk 24 Mon - 16:47 +03:00

https://github.com/silverbulletmd/silverbullet/issues/2016

2026-06-08 Wk 24 Mon - 16:55 +03:00

```ts
  openConfiguration:
    path: ./configuration.ts:openConfiguration
    command:
      name: "Configuration: Open"
```

CONFIG mentions:

```
This page holds configuration for your SilverBullet space. See [[^Library/Std/Config]] for all options and defaults.
```

