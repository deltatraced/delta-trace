---
status: todo
---

# 1 Objective

This is for reference. We need a basic template website infrastructure to see how processes like deployment integrate in with it.

This website should 
- [ ] Have a basic static website built out of html + css + javascript/typescript.
- [ ] Have a basic backend including http request capabilities over a simple REST API
- [ ] Have a basic database solution
- [ ] Can be deployed over wasmer

# 2 Journal

# 3 Tasks

## 3.1 Follow along wasmer documentation

- [ ] 

From [[#^spawn-task-229769]] in [[#6.1 Investigate Wasmer deployment]]

2025-08-26 Wk 35 Tue - 21:32

Following with [wasmer docs](https://docs.wasmer.io/).

Already installed this, but need to update

```sh
wasmer self-update
```

2025-08-26 Wk 35 Tue - 22:08

In [wasmer docs CLI](https://docs.wasmer.io/runtime/cli) they mention a `wasmer.toml` file that allows the folder to be recognized as a local package and be run with `wasmer run .` Looking at my [starter astro website](https://github.com/LanHikari22/astro-starter-website), we also have a `wasmer.toml`:

```toml
[dependencies]
"wasmer/static-web-server" = "*"

[fs]
public = "dist"
```

2025-08-26 Wk 35 Tue - 22:49

In [wasmer docs wasix](https://docs.wasmer.io/runtime/runners/wasix),

> WASIX was created by the Wasmer team to speed up the Wasmification of codebases around the world!

I guess Wasmification is a word!

We do want to try to make CGI executables, so [WCGI](https://docs.wasmer.io/runtime/runners/wcgi) is worth looking at.

2025-08-26 Wk 35 Tue - 22:53

In [registery getting-started](https://docs.wasmer.io/registry/get-started) they share the [wasmer.toml manifest](https://docs.wasmer.io/registry/manifest).

Spawn [[#7.1 wasmer.toml as reference for toml file documentation]] ^spawn-idea-24ee1a

2025-08-26 Wk 35 Tue - 23:10

In [build your package](https://docs.wasmer.io/registry/get-started#build-your-package) they show an example of creating a `*.wasm` file with Rust using their WASIX runner and also WASI.

2025-08-26 Wk 35 Tue - 23:19

In [wasmer architecture](https://docs.wasmer.io/edge/architecture),

So each node in the vertical stack uses a monolith capable of running an application itself. They explain that this means it's [vertically integrated](https://en.wikipedia.org/wiki/Vertical_integration).

It also uses a [shared-nothing architecture](https://en.wikipedia.org/wiki/Shared-nothing_architecture), so the nodes themselves will be able to continue to operate even if all others nodes are offline because they have no shared state that would cause failure.

2025-08-26 Wk 35 Tue - 23:41

Statelessness, CGI scripts as [idompotent](https://en.wikipedia.org/wiki/Idempotence) functions... Many cool functional principles here.

I'm wonder if by each node being stateless it would also mean that they are always able to fulfill their function and not store state themselves. Some state should exist in the broader application though. But by the shared nothing principle, no state will be accessible by all nodes. Where would the database go?

Spawn [[#6.2 Stateless nodes that share nothing but where does the database go and how is it integrated?]] ^spawn-invst-f3bfe8



## 3.2 Creating basic file structure for basic-ref-website-wasmer

- [ ] 

2025-08-26 Wk 35 Tue - 22:16

Right now in `/home/lan/src/unpub/gh/LanHikari22/basic-ref-website-wasmer`.

```sh
git init
git branch -m master main
cp ~/src/cloned/gh/LanHikari22/dbmint/README.md .
cp ~/src/cloned/gh/LanHikari22/dbmint/LICENSE .
wasmer init
```

Make edits to the README for this reference website.

We're just gonna have `backend`, `frontend`, and `backend/db` for now.

```sh
touch frontend/index.html
```

2025-08-26 Wk 35 Tue - 23:12

Running `wasmer init` basically creates this `wasmer.toml` in my case:

```toml
[package]
name = "lanhikari22/basic-ref-website-wasmer"
version = "0.1.0"
description = "Description for package basic-ref-website-wasmer"

# See more keys and definitions at https://docs.wasmer.io/registry/manifest

[[module]]
name = "basic-ref-website-wasmer"
source = "basic-ref-website-wasmer.wasm"
abi = "wasi"

[module.interfaces]
wasi = "0.1.0-unstable"

[[command]]
name = "basic-ref-website-wasmer"
module = "basic-ref-website-wasmer"
runner = "wasi"
```

# 4 Issues

# 5 HowTos

# 6 Investigations

## 6.1 Investigate Wasmer deployment

- [ ] 

2025-08-26 Wk 35 Tue - 21:32

Following with [wasmer docs](https://docs.wasmer.io/).

Spawn [[#3.1 Follow along wasmer documentation]] ^spawn-task-229769

## 6.2 Stateless nodes that share nothing but where does the database go and how is it integrated?

- [ ] 

From [[#^spawn-invst-f3bfe8]] in [[#3.1 Follow along wasmer documentation]]

2025-08-26 Wk 35 Tue - 23:45

In [wasmer architecture](https://docs.wasmer.io/edge/architecture),

Nodes follow the [shared-nothing architecture](https://en.wikipedia.org/wiki/Shared-nothing_architecture), so we expect that a request can be fulfilled by a single node, and each node can provide full independent service.

In [Wasmer Distributed Networking (DNET)](https://docs.wasmer.io/edge/architecture#wasmer-distributed-networking-dnet), 

They mention that its principles include being fully stateless:

> Control planes add complexity and create single pointers of failure thus if one is able to deliver the same functionality without a control plane then it is a better design.

So this may not apply to nodes specifically.

In fact each node is a distributed monolith, which is meant to include [Wasmer Storage](https://docs.wasmer.io/edge/architecture#wasmer-storage). But not much on this is explained at this time.

2025-08-27 Wk 35 Wed - 00:10

Spawn [[Drawing 2025-08-26 23.59.32.excalidraw]]

![[Pasted image 20250827001107.png]]

So this is one idea, where we create a cluster of nodes, each containing only one stateful db node they communicate with.  

This should not break the shared-nothing constraint, because each node can be assumed to have its own independent hardware. All state is only stored by the DB node. And there is no single point of failure database instance, because sync can happen between all db nodes. We could run some fault tolerance algorithms here, like best-2-of-3 of 3 duplicate processes fulfilling a user request that depends on the db. And all 3 node clusters must agree, or at least 2 of 3, or the operation is deemed a failure. On pass, information syncs between other triplet clusters.

Anyway we need to keep in mind that each node here is likely a fully functional service and we're trying to scale service here. Any further module break-up is on the application level, and not the service level.

Still I am trying to understand how we can go about ensuring that all spawned services give the user the same user account information for example without a single point of failure node. 

### 6.2.1 Pend

# 7 Ideas

## 7.1 wasmer.toml as reference for toml file documentation

From [[#^spawn-idea-24ee1a]] in [[#3.1 Follow along wasmer documentation]]

2025-08-26 Wk 35 Tue - 22:56

In [registery getting-started](https://docs.wasmer.io/registry/get-started) they share the [wasmer.toml manifest](https://docs.wasmer.io/registry/manifest).

When we create configuration for an app, we could use this as reference for how to write the documentation spec for that configuration file in specific.

Though it would be good if they put a quick summary of the entrypoints like `[fs]` what is fs? That document should make that clear.

I also like the github integrations. Basically they say you can edit the file in github, and github also offers editing which will fork the repository to edit the file. I guess this should make it more seamless for people to edit documentation.

# 8 Side Notes
# 9 External Links

# 10 References