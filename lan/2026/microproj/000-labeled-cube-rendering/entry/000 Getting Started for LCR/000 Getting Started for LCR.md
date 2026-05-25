# 1 Journal

2026-05-09 Wk 19 Sat - 18:24 +03:00

Hmm. I might want to be able to do this in Rust and also in Agda. We wanna use [OpenGL](https://www.opengl.org/).

2026-05-09 Wk 19 Sat - 19:47 +03:00

Currently trying to compile `hello-world-prog.agda` from [a-taste-of-agda](https://agda.readthedocs.io/en/latest/getting-started/a-taste-of-agda.html). Need the standard library.

From https://github.com/agda/agda-stdlib,

```sh
sh -c "$(curl --proto '=https' --tlsv1.2 -s https://raw.githubusercontent.com/agda/agda-stdlib/refs/heads/master/stdlib-install.sh)"
```

```sh
# in /home/lan/src/cloned/gh/LanHikari22/lan-exp-scripts/microproj/2026/000-LabeledCubeRendering/agda
agda --compile hello-world-prog.agda
./hello-world-prog
```

2026-05-09 Wk 19 Sat - 22:20 +03:00

Spawn [[000 Compile cubical agda to an executable using ctqs sources]] ^spawn-task-5385cc

2026-05-13 Wk 20 Wed - 00:35 +03:00

Spawn [[002 Setup openGL rendering app in rust and do some dev]] ^spawn-task-69c8c6

2026-05-13 Wk 20 Wed - 18:32 +03:00

Alright since we got things going on multiple front here, let's move this all to its own repository:

```sh
# in /home/lan/src/cloned/gh/deltachives
mkdir labeled-cube-rendering-2026-m000

# in /home/lan/src/cloned/gh/deltachives/labeled-cube-rendering-2026-m000
mv ~/src/cloned/gh/LanHikari22/lan-exp-scripts/microproj/2026/000-LabeledCubeRendering .
mv 000-LabeledCubeRendering/* .
rmdir 000-LabeledCubeRendering
cp rs/README.md rs/FUNDING.yml rs/CONTRIBUTING.md rs/LICENSE .
# update LICENSE
```

Code now in [gh deltachives/labeled-cube-rendering-2026-m000](https://github.com/deltachives/labeled-cube-rendering-2026-m000)

`m000` has `m` for microproject, since their indices are different from projects here.


# 2 Spawn Trees

- [[000 Getting Started for LCR]]
    - entry [[000 LCR Side Activity]]
    - todo task [[000 Compile cubical agda to an executable using ctqs sources]]
        - entry [[001 Full content for agda build 1]]
        - issue [[000 Unable to compile hello world with cubical agda flag GHC-49196]]
            - entry [[002 Updates for Unable to compile hello world with cubical agda flag GHC-49196]]
        - todo task [[001 Enable agda-mode in vim and any other intellisense]]
    - todo task [[002 Setup openGL rendering app in rust and do some dev]]

# 3 Index

**entry**

[[000 LCR Side Activity]]

[[001 Full content for agda build 1]]

[[002 Updates for Unable to compile hello world with cubical agda flag GHC-49196]]

**issue**

[[000 Unable to compile hello world with cubical agda flag GHC-49196]]

**task**

todo [[000 Compile cubical agda to an executable using ctqs sources]]

todo [[001 Enable agda-mode in vim and any other intellisense]]

todo [[002 Setup openGL rendering app in rust and do some dev]]