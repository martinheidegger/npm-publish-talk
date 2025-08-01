---
title: Publishing a React module to npm
sub_title: Share your code!
author: Martin Heidegger - https://github.com/martinheidegger/npm-publish-talk
date: 2025-08-01
theme:
  name: terminal-light
  override:
    code:
      theme_name: Solarized (light)
    footer:
      style: progress_bar
---

When you write React...
リアクトを書く時…
===


<!-- incremental_lists: true -->

- Do you use Node.js? ノード使っていますか？
  - _Maybe you want to start?_ 使ったらどう？
- Do you use JavaScript? JS で書いている？
  - _Or TypeScript?_ または TS?
- Do you Copy & Paste? コピペー良くある？
  - _Maybe you want to stop?_ もしかして辞めたら良いかも？

<!-- end_slide -->

Basics
基本
===

- a **_module_** in JavaScript is any file: `index.js`
  「モジュール」はただのＪＳファイルになります。
  - hint: `.mjs` ... "module"-js
  - hint: "ESM" ... "EcmaScript Module"

---

- a **_package_** is one or more modules with extra information
  「パッケージ」だとモジュール集めと他のデータをまとめて
  - hint: "package.json"

<!-- end_slide -->

NPM
===


- node
- **_package_**
- manager

<!-- incremental_lists: true -->

---

- NPM is a server that stores a lot of packages
- Owned by Microsoft
- Biggest code deployment platform

---

<!-- incremental_lists: false -->

- `npm` is a command line tool that comes with Node.js to download the packages


<!-- end_slide -->

# NPM

_published with node_

```bash +exec
npm --version
```


<!-- end_slide -->

# NPM

_getting the latest version_

```bash +exec
npm i npm@latest -g
```

<!-- end_slide -->

# React getting started

_from the documentation_

```bash +exec
npm create \
  vite@latest \
  01_where_it_starts \
  -- --template react
```

<!-- end_slide -->

# No installation?

```bash +exec
cd 01_where_it_starts  

# skip...                      
# npm install

npm run dev
```

<!-- end_slide -->

# Fake vite

```bash +exec
cd 01_where_it_starts

# 1. create folder
mkdir -p node_modules/.bin

# 2. create fake vite command
<<VITE cat > node_modules/.bin/vite
#!/bin/bash
echo "fake vite"
VITE

# 3. make it executable
chmod +x node_modules/.bin/vite

# 4. show
find node_modules

# 5. try again
npm run dev
```

<!-- end_slide -->

# Real vite

```bash +exec
cd 01_where_it_starts

# 1. cleanup
rm -rf node_modules

# 2. install default dependencies
npm i
```

<!-- end_slide -->

# Mini Component

```file {9,11,18-21,28-34}
path: patch/01_my_component
language: diff
```

<!-- end_slide -->

# Apply that diff

```bash +exec
cd 01_where_it_starts

# Let's add that component
patch -p1 < ../patch/01_my_component
```

<!-- end_slide -->

# Extract to file

```file {6,10,16-23,25,28-40}
path: patch/02_component_file
language: diff
```

<!-- end_slide -->

# Apply...

```bash +exec
cd 01_where_it_starts

# Let's add that component
patch -p1 < ../patch/02_component_file
```

<!-- end_slide -->

# Our first package!

```file
path: patch/03_move_to_pkg
language: diff
```

---

```bash +exec
cd 01_where_it_starts

# 1. create a my-package ... package
mkdir node_modules/my-package

# 2. move the component in there
mv src/MyComponent.jsx node_modules/my-package

# 3. create the smallest package.json
echo '{ "module": "MyComponent.jsx" }' \
  > node_modules/my-package/package.json

# 4. apply the patch
patch -p1 < ../patch/03_move_to_pkg
```

<!-- end_slide -->

# out of `node_modules`

```bash +exec
# move into own folder
mv 01_where_it_starts/node_modules/my-package \
   02_my-package

cd 02_my-package

# add a name
npm pkg set name=my-package

# install it in the first folder 🤯
cd ../01_where_it_starts

npm i ../02_my-package
```

<!-- end_slide -->

# Quick Setup

```bash +exec
cd 02_my-package

# 1. Readme for .description
<<_ cat > readme.md
# MyComponent

Small and sweet
_

# 2. initialize npm
npm init -y \
  --init-version 0.0.0 \
  --init-author-name "Martin Heidegger" \
  --init-author-url "https://leichtgewicht.at" \
  --init-type module

# 3. Add license
npx license MIT

# 4. react as a peer dependency
npm i 'react@>=18' --save-peer

# 5. include a linter
npm add -D prettier
npm pkg set "scripts.lint=prettier -c ."
npm pkg set "scripts.format=prettier -w ."
npm run format

# 6. add version control
<<_ cat > .gitignore
node_modules
package-lock.json
.tgz
_

git init && git add . && git commit -m "init"
```

<!-- end_slide -->

# Simple publish

```bash +exec
cd 02_my-package

npm pack
```

<!-- end_slide -->

# Simple install

```bash +exec
cd 01_where_it_starts

# Remove the currently linked package
npm r my-package

# Install from .tgz file
npm i ../02_my-package/my-package-0.0.0.tgz
```

<!-- end_slide -->

# Publish options

### USB drive

`npm i /Volumes/stick/my-package-0.0.0.tgz`

### Web Server

`npm i http://mydomain.com/my-package-0.0.0.tgz`

### Git Repository

`npm i git+https://github.com/martinheidegger/my-package.git`

`npm i github:martinheidegger/my-package`

### NPM

`npm i my-package`

<!-- end_slide -->

# Publish to NPM

<!-- incremental_lists: true -->

- You need an account: https://npmjs.com
　　　アカウントが必須
- NPM hosts your package (no cost)
    無料のパッケージホスティング
- NPM may block you from publishing 😈
    パブリッシュブロック可能
- NPM may remove your versions 🦠
    自動バーション削除可能
- NPM may block you from deleting 🥇
    削除ブロック可能あり

<!-- end_slide -->

# Publish to NPM

```bash +exec
cd 02_my-package
# Already logged in (skipping): npm login
npm publish
```

<!-- end_slide -->

# Detour: Use private!

```bash +exec
cd 02_my-package

npm pkg set private=true --json
```


<!-- end_slide -->

# Real-Life

https://martinheidegger.github.io/react-now-Hook

```bash +exec
git clone https://github.com/martinheidegger/react-now-hook 03_react-now-hook
```

<!-- end_slide -->

# Github Actions

```bash +exec
# Added workflows
cd 03_react-now-hook

# Automation folder!
ls -1 .github/workflows
```

<!-- end_slide -->

# Github Actions

_Automatic deployments_
_自動デプロイ_

```yaml
name: Publish Package to npmjs
on:
  push:
    tags:
      - 'v*'

permissions:
  id-token: write  # Required for OIDC
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      id-token: write
    environment:
      name: npm-deploy
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '24.x'
          registry-url: 'https://registry.npmjs.org'
      - name: Update npm
        run: npm install -g npm@latest
      - run: npm ci
      - run: npm publish
```

<!-- end_slide -->

# Github Actions

```bash +exec
cd 03_react-now-hook
npm version patch
git push --follow-tags
```

<!-- end_slide -->

# Github Actions (workflow)

- Do: [Approve Deployment](https://github.com/martinheidegger/react-now-hook/actions/workflows/deploy.yml)
- Prep: [Setup Environment](https://github.com/martinheidegger/react-now-hook/settings/environments)
- Prep: [Allow OIDC](https://www.npmjs.com/package/react-now-hook/access)

<!-- end_slide -->

# Github Actions (OIDC)

- No Password!
- No Token!
- Control in Github!

<!-- end_slide -->

# React: .jsx -> js

```file
path: patch/04_end_jsx_to_js
language: diff
```

```bash +exec
cd 02_my-package

patch -p1 < ../patch/04_end_jsx_to_js
mv MyComponent.jsx MyComponent.js
```

<!-- end_slide -->

# React: .jsx -> js

```file
path: patch/05_jsx_to_js
language: diff
```

```bash +exec
cd 02_my-package

patch -p1 < ../patch/05_jsx_to_js
```

<!-- end_slide -->

# React: peer dependency

```bash +exec
cd 02_my-package

npm i --save-peer 'react@>=18'
```

<!-- end_slide -->

# Detour: License

- `MIT` … very open!
- `MPL` … some limitations.
- `GPL` … fork same license required!
- `UNLICENSED` … private!

more: https://bit.ly/4l7TKD2

詳しく → http://bit.ly/4mlpIfZ

<!-- end_slide -->

# Detour: Semver

Example: `1.1.1`

`<major>.<minor>.<patch>`

- `major` … If a dependent code might break
  利用するライブラリーが壊れる可能
- `minor` … Feature added to code that might help
  新しいフィーチャー追加になったとき　※
- `patch` … Code fix.
  コード出征だけ　※²

---

※ Its okay-ish if a dependent breaks because of a bug. 被扶養者関がバグの問題で動かなくなるなら修正だと私ならだともしかしてオケ。

※² Its okay-ish if a dependent breaks because of a security bug. 被扶養者はセキュリティーの問題で動かなくなるならもしかしてオケ。

<!-- end_slide -->

# Detour: cjs/mjs/jsx/ts

- No `.ts` in `.tgz` - fast compile and install!
- Prefer `"type": "module"` _(or both)_
- Prefer `.mjs` over `.jsx` - fast, error-free compile!

<!-- end_slide -->

# Detour: `exports default`

```js
 🛑

exports default function MyComponent {
  //...
}
```

```js
import MyComponent from 'my-package'
```

```js
 ✅

exports function MyComponent {
  //...
}
```

```js
import { MyComponent } from 'my-package'
```

<!-- end_slide -->

# Detour: .npmignore

`my-package_0.0.0.tgz` - 🐘

```ignore
.github
.vscode
dist
node_modules
*.ts
!src/useNow.d.ts
src/example/*
*.tsx
.prettierrc
.gitingore
tsconfig*
*.tgz
server.mjs
index.html
```

<!-- end_slide -->

# Why deploy packages?

- Pride! プライド 💪
- Independence! 自由のために ⛓️‍💥
- Collaboration! コラボ練習 🤝
- Skill-up! スキルアップ 📚
- Give-back! 恩返し 🙇

<!-- end_slide -->

# More topics

- Testing / テスト
- TypeScript / タイプスクリプト
- Maintenance / メンテ
- Auditing / 監査

<!-- end_slide -->

Thank you very much!
===

### Questions?

### Slides:

https://github.com/martinheidegger/npm-publish-talk

