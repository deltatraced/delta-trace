---
status: todo
---

# 1 Objective

This is for reference. We need a basic template website infrastructure to see how processes like deployment integrate in with it.

This website should
- [x] Have a basic static website built out of html + css + javascript/typescript.
- [ ] Have a basic backend including http request capabilities over a simple REST API
- [ ] Have a basic database solution
- [x] Can be deployed over wasmer

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

## 3.3 Follow with wweb static npm website tutorial

- [ ]

2025-09-01 Wk 36 Mon - 21:08

The tutorial is [here](https://wweb.dev/blog/how-to-create-static-website-npm-scripts).

There is a finished template in [gh wwebdev/static-website-template](https://github.com/wwebdev/static-website-template).

```sh
# in /tmp/del/tut

npm version

# out
11.3.0
```

From `npm init` we get:

```
npm notice
npm notice New minor version of npm available! 11.3.0 -> 11.5.2
npm notice Changelog: https://github.com/npm/cli/releases/tag/v11.5.2
npm notice To update run: npm install -g npm@11.5.2
npm notice
```

```sh
npm install -g npm@11.5.2
```

```sh
# in /tmp/del/tut

npm init
```

This gives us some choices to go through and creates a `package.json`. The schema documentation is [here](https://docs.npmjs.com/cli/v10/configuring-npm/package-json?v=true). But I can't find `type` there...

[stackoverflow answer](https://stackoverflow.com/a/62554884/6944447) $\to$ [nodejs docs esm_enabling](https://nodejs.org/docs/latest-v13.x/api/esm.html#esm_enabling)

2025-09-01 Wk 36 Mon - 22:15

```json
// in /tmp/del/tut
cat package.json

// out
{
  "name": "static-website",
  "version": "1.0.0",
  "description": "An ephemeral project",
  "license": "ISC",
  "author": "Lan",
  "type": "module",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

Then we include a similar `index.html` in the tut:

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<title>Some website!</tite>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1" />
	</head>
	
	<body>
		<h1>Hi! I am a heading</h1>
	</body>
</html>
```

For some reason `open index.html` is not able to find the existing file at `file:///tmp/del/tut/index.html`... But we can open it in `~/tmp/del/tut`
2025-09-01 Wk 36 Mon - 22:25

Right now the content says `<body></body>` despite us having some content...

But we do see a body with the following content:

```html
<!DOCTYPE html>
<html lang="en">
	<head>
	</head>
	<body>
		<h1>Hi! I am a heading</h1>
	</body>
</html>
```

Even including `<title>Some website!</tite>` inside `<head></head>` would break this...

Oh oops, `</title>` not `</tite>`!

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<title>Some website!</title>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1" />
	</head>
	
	<body>
		<h1>Hi! I am a heading</h1>
	</body>
</html>
```

You can use `view-source:` before a url in firefox to also view it in the browser, or right click and do `View Page Source`.

2025-09-01 Wk 36 Mon - 22:39

Spawn [[#7.2 SASS language extension inspiration for dbmt]] ^spawn-idea-3f98c7

2025-09-01 Wk 36 Mon - 22:57

```diff
{
  "name": "static-website",
  "version": "1.0.0",
  "description": "An ephemeral project",
  "license": "ISC",
  "author": "Lan",
  "type": "module",
  "main": "index.js",
  "scripts": {
-    "test": "echo \"Error: no test specified\" && exit 1"
+    "css:scss": "node-sass --output-style compressed -o dist src/scss"
  }
}
```

Let's get [Sass](https://sass-lang.com/) for CSS:

```sh
npm i -D node-sass
```

```diff
{
  "name": "static-website",
  "version": "1.0.0",
  "description": "An ephemeral project",
  "license": "ISC",
  "author": "Lan",
  "type": "module",
  "main": "index.js",
  "scripts": {
    "css:scss": "node-sass --output-style compressed -o dist src/scss"
  },
+  "devDependencies": {
+    "node-sass": "^9.0.0"
+  }
}

```

2025-09-01 Wk 36 Mon - 23:30

```sh
# in ~/tmp/del/tut
mkdir -p src/scss
touch src/scss/index.scss
```

```diff
	<head>
+		<link rel="stylesheet", type="text/css", href="dist/index.css">
	</head>
```

2025-09-01 Wk 36 Mon - 23:36

```scss
// in src/scss/_variables.scss
$primary: #16a085;

// in src/scss/index.scss
@import 'variables.scss';

body {
	color: $primary;
}
```

Seems even though it's called `_variables.scss`, we can import it as `variables.scss`, maybe related to it being a compilation flag to ignore the file...

```sh
npm run css:scss
```

2025-09-01 Wk 36 Mon - 23:50

Had to fix a missing semicolon to `@import 'variables.scss'` , but otherwise we're able to build!

It's green!

![[Pasted image 20250901235009.png]]

2025-09-01 Wk 36 Mon - 23:54

Now we're using [npmjs autoprefixer](https://www.npmjs.com/package/autoprefixer) ([gh postcss/autoprefixer](https://github.com/postcss/autoprefixer)) and [npmjs postcss-cli](https://www.npmjs.com/package/postcss-cli) to manage it.

Install:

```sh
# in ~/tmp/del/tut
npm i -D autoprefixer postcss-cli
```

And also include the new scripts.

```diff
# in ~/tmp/del/tut/package.json
  "scripts": {
-    "css:scss": "node-sass --output-style compressed -o dist src/scss"
+    "css:scss": "node-sass --output-style compressed -o dist src/scss",
+    "css:autoprefixer": "postcss -u autoprefixer -r dist/*.css",
+    "build:css": "npm run css:scss && npm run css:autoprefixer"
  },
  "devDependencies": {
+    "autoprefixer": "^10.4.21",
    "node-sass": "^9.0.0",
+    "postcss-cli": "^11.0.1"
  }
```

2025-09-02 Wk 36 Tue - 00:16

This post [What is Autoprefixer and Why Should You Use It?](https://www.codu.co/articles/what-is-autoprefixer-and-why-should-you-use-it-hwzzvb6i) explains why autoprefixer is needed for cross-browser compatibility where vendor prefixes otherwise would have to be managed manually to ensure a consistent UX across different browsers.

2025-09-02 Wk 36 Tue - 00:25

For linting, get [npmjs stylelint](https://www.npmjs.com/package/stylelint) ([lint rules](https://stylelint.io/user-guide/rules/))

Also getting [npmjs postcss-scss](https://www.npmjs.com/package/postcss-scss)

```sh
npm i -D stylelint postcss-scss
```

2025-09-02 Wk 36 Tue - 05:23

This [post](https://www.sitepoint.com/postcss-sass-configurable-alternative/) explores using postcss-scss for automated configuration.

```diff
# in ~/tmp/del/tut/package.json
  "scripts": {
-	 "build:css": "npm run css:scss && npm run css:autoprefixer"
+    "css:lint": "stylelint src/scss/*.scss  --custom-syntax postcss-scss",
+    "build:css": "npm run css:lint && npm run css:scss && npm run css:autoprefixer",
  },
  "devDependencies": {
+    "postcss-scss": "^4.0.9",
+    "stylelint": "^16.23.1"
  }
```

Create a `.stylelintrc`,

```json
// in ~/tmp/del/tut/.stylelintrc

"rules": {
    "block-no-empty": true,
    "color-hex-case": "lower",
    "color-hex-length": "short",
    "color-no-invalid-hex": true,
    "declaration-colon-space-after": "always",
    "max-empty-lines": 2
}
```

2025-09-02 Wk 36 Tue - 05:33

Adding rebuild automation on file change with [npmjs onchange](https://www.npmjs.com/package/onchange).

```sh
npm i -D onchange
```

```diff
# in ~/tmp/del/tut/package.json
  "scripts": {
-    "build:css": "npm run css:lint && npm run css:scss && npm run css:autoprefixer"
+    "build:css": "npm run css:lint && npm run css:scss && npm run css:autoprefixer",
+    "watch:css": "onchange \"src/scss\" -- npm run build:css"
  },
  "devDependencies": {
+    "onchange": "^7.1.0",
  }
```

Automate away browser refresh with [npmjs browser-sync](https://www.npmjs.com/package/browser-sync),

```sh
npm i -D browser-sync
```

```diff
# in ~/tmp/del/tut/package.json
  "scripts": {
-    "watch:css": "onchange \"src/scss\" -- npm run build:css"
+    "watch:css": "onchange \"src/scss\" -- npm run build:css",
+    "serve": "browser-sync start --server \"dist\" --files \"dist\""
  },

  "devDependencies": {
+    "browser-sync": "^3.0.4",
  }
```

For now, moving `index.html` to `dist` as it is being watched for changes, and make the change

```diff
-		<link rel="stylesheet", type="text/css", href="dist/index.css">
+		<link rel="stylesheet", type="text/css", href="/index.css">
```

2025-09-02 Wk 36 Tue - 05:46

Following some configuration update recommendations,

Use `npm i -D sass` instead of `npm i -D node-sass`

```diff
# in ~/tmp/del/tut/package.json
  "scripts": {
-    "css:scss": "node-sass --output-style compressed -o dist src/scss",
+    "css:scss": "sass --style compressed src/scss:dist",
  },
  "devDependencies": {
-    "node-sass": "^9.0.0",
+    "sass": "^1.91.0",
  }
 
  
# in src/scss/index.scss
-@import 'variables.scss';
+@use 'variables.scss';

body {
-  color: $primary;
+  color: variables.$primary;
}
```

Spawn [[#7.3 Post Comment integration via utterance.es]] ^spawn-idea-bb2c9a

2025-09-02 Wk 36 Tue - 06:07

Also we'll use [npmjs concurrently](https://www.npmjs.com/package/concurrently) instead of [npmjs npm-run-all](https://www.npmjs.com/package/npm-run-all)

```sh
npm i -D concurrently
```

And the script

```diff
-"watch": "run-p serve watch:css"
+"watch": "concurrently 'npm run serve' 'npm run watch:css'"
```

```diff
# in ~/tmp/del/tut/package.json
  "scripts": {
-    "serve": "browser-sync start --server \"dist\" --files \"dist\""
+    "serve": "browser-sync start --server \"dist\" --files \"dist\"",
+    "watch": "concurrently 'npm run serve' 'npm run watch:css'"
  },
  "devDependencies": {
+    "concurrently": "^9.2.1",
  }

```

2025-09-02 Wk 36 Tue - 06:27

We run into some lint errors when updating scss:

```
[1] src/scss/_variables.scss
[1]   1:1  ✖  Unknown rule color-hex-case. Did you mean color-hex-alpha, color-hex-length?  color-hex-case
[1]   1:1  ✖  Unknown rule declaration-colon-space-after                                    declaration-colon-space-after
[1]   1:1  ✖  Unknown rule max-empty-lines                                                  max-empty-lines
```

Relevant:
- [stylelint #7400: 4 Rule color-hex-case unexpectedly removed](https://github.com/stylelint/stylelint/issues/7400)

They added migration guides for deprecated rules: [to-16](https://stylelint.io/migration-guide/to-16/#removed-deprecated-stylistic-rules), [to-15](https://stylelint.io/migration-guide/to-15/#deprecated-stylistic-rules)

For deprecation of rules like `color-hex-case`, `declaration-colon-space-after`, `max-empty-lines`  they write:

> When we created these rules, pretty printers (like [Prettier](https://prettier.io/)) didn't exist. They now offer a better way to consistently format code, especially whitespace. Linters and pretty printers are complementary tools that work together to help you write consistent and error-free code.

We need to add formatting automation instead. Remove these lints.

```sh
npm install --save-dev --save-exact prettier
```

```diff
# in ~/tmp/del/tut/package.json


  "scripts": {
+    "format": "npx prettier . --check",
-    "build:css": "npm run css:lint && npm run css:scss && npm run css:autoprefixer",
+    "build:css": "npm run format && npm run css:lint && npm run css:scss && npm run css:autoprefixer",
  },
  "devDependencies": {
+    "prettier": "^3.6.2",
  }
```

2025-09-02 Wk 36 Tue - 08:37

Let's use this with pre-commit hooks

Install it if you haven't:

```sh
uv tool install pre-commit
```

Hmm, this [post](https://medium.com/@danielangelesangelestoribio/configure-pre-commit-for-nextjs-project-d511aa6a1f7b) with a recommended pre-commit configuration recommends [biome](https://biomejs.dev/) which integrates formatting and linting

This provides full code linting.  Let's replace prettier with it

```sh
npm i -D --save-exact @biomejs/biome
```

```diff
# in ~/tmp/del/tut/package.json

  "scripts": {
    "css:lint": "stylelint src/scss/*.scss  --custom-syntax postcss-scss",
-    "format": "npx prettier . --check",
+    "biome-check": "npx @biomejs/biome check --write ./src",
    "build:css": "npm run biome-check && npm run css:lint && npm run css:scss && npm run css:autoprefixer",
  },
  "devDependencies": {
+    "@biomejs/biome": "2.2.2",
-    "prettier": "3.6.2",
  }
```

`css:lint` with `stylelint` seems necessary still because it doesn't seem biome handles scss.

2025-09-02 Wk 36 Tue - 09:20

```sh
pre-commit sample-config > .pre-commit-config.yaml
```

Add some extra config

```yaml
# See https://pre-commit.com for more information
# See https://pre-commit.com/hooks.html for more hooks
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v3.2.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
	  - id: check-json
      - id: check-added-large-files
  - repo: local
    hooks:
      - id: local-biome-check
        name: biome check
        entry: npx biome check --write --files-ignore-unknown=true --no-errors-on-unmatched
        language: system
        types: [text]
        files: "\\.(jsx?|tsx?|c(js|ts)|m(js|ts)|d\\.(ts|cts|mts)|jsonc?|svelte|vue|astro|graphql|gql)$"
  - repo: https://github.com/codespell-project/codespell
	rev: v2.4.1
	hooks:
      - id: codespell
```

The biome check config is as recommended in [gh biomejs/pre-commit](https://github.com/biomejs/pre-commit).

And the codespell config is latest as of this writing from [gh codespell-project/codespell](https://github.com/codespell-project/codespell).

2025-09-02 Wk 36 Tue - 09:43

Needed to make sure that `-id: check-json` is not too indented, or `pre-commit run` would give an error. Also needed git repo initialized and node_modules in .gitiginore and files staged to test.

Currently the compression done to css is overridden by biome making the css file pretty. Let's remove `css` from the files to format.

```
codespell................................................................Failed
- hook id: codespell
- exit code: 65

package-lock.json:229: COo ==> coup
package-lock.json:3061: vEw ==> view, vow, vex
package-lock.json:3989: Nd ==> And, 2nd
```

Why is it failing on urls... I guess we need `args: [--skip, package-lock.json]` as recommended by the [post](https://medium.com/@danielangelesangelestoribio/configure-pre-commit-for-nextjs-project-d511aa6a1f7b).

2025-09-02 Wk 36 Tue - 09:58

Back to the [tutorial](https://wweb.dev/blog/how-to-create-static-website-npm-scripts)!

Getting [npmjs imagemin-cli](https://www.npmjs.com/package/imagemin-cli) to minify images

```sh
npm i -D imagemin-cli
```

```diff
# in ~/tmp/del/tut/package.json
	"scripts": {
+		"build:images": "imagemin src/images/**/* --out-dir=dist/images",
+		"watch:images": "onchange \"src/images\" -- npm run build:images",
	},

```

2025-09-02 Wk 36 Tue - 10:04

Spawn [[#4.1 npm audit reports security vulnerabilities for tutorial template project]] ^spawn-issue-e23534

2025-09-02 Wk 36 Tue - 10:15

We ran some audit fixes to reduce vulnerablities. This is what we have so far:

```json
	"scripts": {
		"css:scss": "sass --style compressed src/scss:dist",
		"css:autoprefixer": "postcss -u autoprefixer -r dist/*.css",
		"css:lint": "stylelint src/scss/*.scss  --custom-syntax postcss-scss",
		"format": "npx prettier . --check",
		"biome-check": "npx @biomejs/biome check --write ./src",
		"build:css": "npm run biome-check && npm run css:lint && npm run css:scss && npm run css:autoprefixer",
		"build:images": "imagemin src/images/**/* --out-dir=dist/images",
		"build": "npm run build:*",
		"watch:css": "onchange \"src/scss\" -- npm run build:css",
		"watch:images": "onchange \"src/images\" -- npm run build:images",
		"serve": "browser-sync start --server \"dist\" --files \"dist\"",
		"watch": "concurrently 'npm run serve' 'npm run watch:*'"
	},
	"devDependencies": {
		"@biomejs/biome": "2.2.2",
		"autoprefixer": "^10.4.21",
		"browser-sync": "^3.0.4",
		"concurrently": "^9.2.1",
		"imagemin-cli": "^8.0.0",
		"onchange": "^7.1.0",
		"postcss-cli": "^11.0.1",
		"postcss-scss": "^4.0.9",
		"prettier": "3.6.2",
		"sass": "^1.91.0",
		"stylelint": "^16.23.1"
	}
```

2025-09-02 Wk 36 Tue - 11:00

We can use [webpack](https://webpack.js.org/) for bundling and resolving dependencies, and [babeljs](https://babeljs.io/) for writing latest javascript and yet being browser-compatible.

```sh
npm i -D webpack webpack-cli babel-loader @babel/preset-env
```

```
added 224 packages, and audited 865 packages in 33s
```

That's a lot of dependencies.

Let's create the `webpack.config.js` config file in the project root like the [tutorial](https://wweb.dev/blog/how-to-create-static-website-npm-scripts) and add

```json
"build:js": "webpack --mode=production",
"watch:js": "onchange \"src/js\" -- webpack --mode=development",
```

and include the script into the html at the end of the `<body>`:

```html
<script src="./bundle.js"></script>
```

The tutorial uses [eslint](https://www.npmjs.com/package/eslint) but we're already using biome.

Make sure to also have `src/js/main.js`.

2025-09-02 Wk 36 Tue - 11:21

Now let's add the rest of the configuration for building html pages into dist and minifying.

```sh
npm i -D posthtml posthtml-cli posthtml-modules htmlnano
```

Add `posthtml.json` as in the tutorial.

Add build and watch for html:

```json
"build:html": "posthtml -c posthtml.json",
"watch:html": "onchange \"src/views\" -- npm run build:html",
```

Move `dist/index.html` into `src/views/index.html` and split the head into its own file, `src/views/components/head.html` then import it with

```html
<module href="/components/head.html"></module>
```

2025-09-02 Wk 36 Tue - 11:35

```sh
npm run build:html
> static-website@1.0.0 build:html
> posthtml -c posthtml.json

You have to install "cssnano" in order to use htmlnano's "minifyCss" module
You have to install "svgo" in order to use htmlnano's "minifySvg" module
The file /home/lan/tmp/del/tut/src/views/index.html has been saved!
```

2025-09-02 Wk 36 Tue - 11:39

Awesome! We got a lot of tools for modular html, css, js, images, linting, formatting, pre-commit hooks...

One last thing! Let's add to this tutorial by deploying with wasmer!

Let's move this template somewhere more permanent. You can view it in [gh lan-exp-scripts tut](https://github.com/LanHikari22/lan-exp-scripts/tree/main/tutorials/2025/topics/frontend/wwebdev/tut).

Spawn [[#3.4 Deploy wwebdev tutorial project with wasmer]] ^spawn-task-7ad6e5

2025-09-02 Wk 36 Tue - 12:19

It's all deployed now!

Let's make add a comment to the tutorial with the process we went through here.

There are a few differences:
- We use [biome](https://biomejs.dev/) instead of eslint for linting and non-scss formatting
- Following [stylelint migration-guide to-15](https://stylelint.io/migration-guide/to-15/#deprecated-stylistic-rules), we removed deprecated rules `color-hex-case`, `declaration-colon-space-after`, `max-empty-lines` since they recommend to just run auto-formatting like with prettifier. We use biome.
- Added pre-commit hooks for formatting and linting
- Added configuration for wasmer deployment

The comment ended up being routed to [Vincenius/wwebdev-comments #3](https://github.com/Vincenius/wwebdev-comments/issues/3).

## 3.4 Deploy wwebdev tutorial project with wasmer

- [x]

From [[#^spawn-task-7ad6e5]] in [[#3.3 Follow with wweb static npm website tutorial]]

2025-09-02 Wk 36 Tue - 11:53

We can use wasmer's [static website example](https://github.com/wasmer-examples/static-website) on github.

This uses their [WASIX](https://docs.wasmer.io/runtime/runners/wasix) runner over `wasmer-static-web-site`. Let's copy their [wasmer.toml](https://raw.githubusercontent.com/wasmer-examples/static-website/refs/heads/main/wasmer.toml):

(update)
```toml
[dependencies]
"wasmer/static-web-server" = "^1"

[fs]
"/public" = "public"
"/settings" = "settings"

[[command]]
name = "script"
module = "wasmer/static-web-server:webserver"
runner = "https://webc.org/runner/wasi"

[command.annotations.wasi]
main-args = ["-w", "/settings/config.toml"]
```

<details>
<summary>errata</summary>

2025-09-02 Wk 36 Tue - 12:15
Seems it requires us to use `/public`:

```sh
2025-09-02T09:10:09.424496Z ERROR static_web_server::server: server failed to start up: root directory was not found or inaccessible

Caused by:

    path /public was not found or inaccessible
```

Move everything `/dist` to `/public`.

Then

```sh
wasmer app delete
wasmer app create # and deploy
```

</details>

(/update)

Copy their [settings/config.toml](https://raw.githubusercontent.com/wasmer-examples/static-website/refs/heads/main/settings/config.toml).

Copy [app.yaml](https://raw.githubusercontent.com/wasmer-examples/static-website/refs/heads/main/app.yaml).

Then, we can deploy with

```sh
wasmer deploy
```

2025-09-02 Wk 36 Tue - 12:19

And it works!

## 3.5 Find and follow process to deploy multiple npm projects under wasmer

- [ ]

2025-09-02 Wk 36 Tue - 13:05

We are able to deploy two different projects now, but they do not share the same domain name. We have a single domain name, and would like to dedicate different pages to different npm projects. This tutorial might help.

Tutorial is [here](https://turbocloud.dev/book/deploying-node.js-under-one-domain-with-caddy/).

2025-09-02 Wk 36 Tue - 13:07

Let's make a temporary tutorial project.

```sh
mkdir -p ~/src/tmp/del/tut
```

2025-09-02 Wk 36 Tue - 13:44

They use [Caddy](https://caddyserver.com/).

We might need some integration to use this with wasmer. There is [valpackett/caddy-wasm-wcgi](https://codeberg.org/valpackett/caddy-wasm-wcgi)

2025-09-02 Wk 36 Tue - 14:56

I asked on wasmer discord, and the founder said that it's not an available feature yet, but it is possible yet with 3 apps but it is possible with a custom router in a single app?

2025-09-02 Wk 36 Tue - 16:21

You can also find some of the language projects implemented under WASIX [here](https://github.com/wasix-org).

2025-09-02 Wk 36 Tue - 16:38

In [cowsay wasmer.toml](https://github.com/wapm-packages/cowsay/blob/master/wasmer.toml) they are feeding a `cowsay.wasm` executable directly, but this is for a command rather than a service.

2025-09-02 Wk 36 Tue - 16:55

Spawn [[#4.2 Open an issue to Wasmer docs for broken links]] ^spawn-issue-fe8e6b

2025-09-02 Wk 36 Tue - 17:18

It's pretty streamlined on rust: [wasmer.io rust-wcgi](https://wasmer.io/templates/rust-wcgi?intent=at_ynm3Iet1Cr2Z). Just gotta add the right build target!

### 3.5.1 Pend

# 4 Issues

## 4.1 npm audit reports security vulnerabilities for tutorial template project

From [[#^spawn-issue-e23534]] in [[#3.3 Follow with wweb static npm website tutorial]]

2025-09-02 Wk 36 Tue - 10:07

Seems due to the versions being used.

```
23 vulnerabilities (1 moderate, 22 high)
```

```sh
npm audit fix --force
```

```
49 vulnerabilities (11 moderate, 28 high, 10 critical)
```

That just got worse!

```sh
npm audit fix
```

```
32 vulnerabilities (11 moderate, 17 high, 4 critical)
```

```sh
npm audit fix --force
```

```
21 vulnerabilities (1 moderate, 20 high)
```

This is strange how this is changing. It seems each force switches us to a different configuration with its own vulnerabilities. We definitely don't want the one with critical issues.

## 4.2 Open an issue to Wasmer docs for broken links

- [x]

From [[#^spawn-issue-fe8e6b]] in [[#3.5 Find and follow process to deploy multiple npm projects under wasmer]]

2025-09-02 Wk 36 Tue - 16:56

I found these again!

The problem itself is not in docs.wasmer.io, but in the website. [wasmer.io](https://wasmer.io/)

Hi, there are broken links in [wasmer.io](https://wasmer.io/) (404):
- Developers > C: https://github.com/wasmerio/wasmer-c
- Developers > Rust: https://github.com/wasmerio/wasmer-rust

```sh
git clone git@github.com:LanHikari22/docs.wasmer.io.git ~/src/cloned/gh/LanHikari22/forked/wasmerio/docs.wasmer.io 
```

2025-09-02 Wk 36 Tue - 17:15

Since this isn't the docs website, can't file an issue nor PR. Just informed them on discord.
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

### 6.2.1 Backlog

# 7 Ideas

## 7.1 wasmer.toml as reference for toml file documentation

From [[#^spawn-idea-24ee1a]] in [[#3.1 Follow along wasmer documentation]]

2025-08-26 Wk 35 Tue - 22:56

In [registery getting-started](https://docs.wasmer.io/registry/get-started) they share the [wasmer.toml manifest](https://docs.wasmer.io/registry/manifest).

When we create configuration for an app, we could use this as reference for how to write the documentation spec for that configuration file in specific.

Though it would be good if they put a quick summary of the entrypoints like `[fs]` what is fs? That document should make that clear.

I also like the github integrations. Basically they say you can edit the file in github, and github also offers editing which will fork the repository to edit the file. I guess this should make it more seamless for people to edit documentation.

## 7.2 SASS language extension inspiration for dbmt

From [[#^spawn-idea-3f98c7]] in [[#3.3 Follow with wweb static npm website tutorial]]

2025-09-01 Wk 36 Mon - 22:52

the [Sass](https://sass-lang.com/guide/) guide here reviews some of the added features to the language. This is possibly a documentation and feature expansion inspiration for the dbmt language which extends dbml.  You can learn more about that project [here](https://github.com/dbmint/dbmint-notes).

## 7.3 Post Comment integration via utterance.es

From [[#^spawn-idea-bb2c9a]] in [[#3.3 Follow with wweb static npm website tutorial]]

Commenters added critical updates to this post which raised its quality. This was possible because of adding comments via github. And it's all from a standalone widget! [utteranc.es](https://utteranc.es/)

It just maps comments to github issues!

Added to [[000 Mn 09 Ideas]]

# 8 Side Notes
# 9 External Links

| Internal                                                                                                                                                                                                                                                        | External                                                                                |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| [link](https://github.com/deltatraced/delta-trace/blob/webview/lan/projects/2025/002%20obsidian-sourced-website/tasks/2025/001%20Create%20a%20reference%20basic%20website%20and%20host%20it%20with%20wasmer.md#33-follow-with-wweb-static-npm-website-tutorial) | [Vincenius/wwebdev-comments #3](https://github.com/Vincenius/wwebdev-comments/issues/3) |

# 10 References