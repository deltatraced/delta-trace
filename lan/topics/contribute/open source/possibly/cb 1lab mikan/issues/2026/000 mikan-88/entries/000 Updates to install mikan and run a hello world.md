---
parent: "[[000 mikan-88]]"
spawned_by: "[[000 Install mikan and run a hello world]]"
context_type: entry
---

Parent: [[000 mikan-88]]

Spawned by: [[000 Install mikan and run a hello world]]

Spawned in: [[000 Install mikan and run a hello world#^spawn-entry-618136|^spawn-entry-618136]]

# 1 Journal

2026-05-10 Wk 19 Sun - 11:14 +03:00

---

(updating)

2026-05-10 Wk 19 Sun - 10:56 +03:00

https://codeberg.org/1lab/mikan/issues/88

```sh
# in /home/lan/src/cloned/cb/1lab
git clone ssh://git@codeberg.org/1lab/mikan.git
```

From `HACKING.md` instructions,

```sh
cabal install --package-env mikan --lib Mikan ieee754
```

(/updating)

---

Actually,  in `HACKING.md` it is mentioned that we should be cloning the submodules also:

```sh
git clone --recurse-submodules https://codeberg.org/1lab/mikan.git
```

So we should have

```sh
# in /home/lan/src/cloned/cb/1lab
git clone --recurse-submodules ssh://git@codeberg.org/1lab/mikan.git
```
