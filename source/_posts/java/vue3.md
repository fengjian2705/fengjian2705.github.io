---
title: 玩转 vue3
tags: 
    - vue3
    - elementPlus
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/vue3/vue3_001.jpeg
# excerpt: vue3全掌握
---

## 项目搭建

### 版本要求

vue3.2 版本：vue-cli 版本 4.5.13 以上

### 首次安装

安装 node，版本要求 v12以上，安装 node 后自带 npm

安装 nrm，镜像源管理，nrm use xxx 切换镜像源

npm install nrm -g

npm install -g @vue/cli

### 版本更新

vue -V 检查版本

vue update -g @vue/cli

### vue-cli 创建项目

vue create vue3-demo

step 1:

```bash
Vue CLI v5.0.1
? Please pick a preset:
  Default ([Vue 3] babel, eslint)
  Default ([Vue 2] babel, eslint)
❯ Manually select features // 手动配置
```

step 2:

```bash
Vue CLI v5.0.1
? Please pick a preset: Manually select features
? Check the features needed for your project: (Press <space> to select, <a> to t
oggle all, <i> to invert selection, and <enter> to proceed)
 ◉ Babel // 使用 babel
 ◯ TypeScript
 ◯ Progressive Web App (PWA) Support
 ◉ Router // 添加 vue-router
 ◉ Vuex // 添加 vuex
 ◉ CSS Pre-processors // 使用 css 预处理器
 ◉ Linter / Formatter // 代码格式化
 ◯ Unit Testing
❯◯ E2E Testing
```

step 3:

```bash
Vue CLI v5.0.1
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, CSS Pre-proce
ssors, Linter
? Choose a version of Vue.js that you want to start the project with (Use arrow
keys)
❯ 3.x // 选择 vue 3.0 版本
  2.x
```

step 4:

```bash

Vue CLI v5.0.1
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, CSS Pre-proce
ssors, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Use history mode for router? (Requires proper server setup for index fallback
in production) (Y/n) n // 不使用 history 模式的路由

```

step 5:

```bash
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported
by default): (Use arrow keys)
❯ Sass/SCSS (with dart-sass) // 使用基于 dart-sass 的 scss 预处理器
  Less
  Stylus

```

step 6:

```bash
? Pick a linter / formatter config: (Use arrow keys)
  ESLint with error prevention only
  ESLint + Airbnb config
❯ ESLint + Standard config 使用 ESLint 标准化格式代码方案
  ESLint + Prettier
```

step 7:

```bash
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i
> to invert selection, and <enter> to proceed)
 ◉ Lint on save
❯◉ Lint and fix on commit // 保存时 && 提交时，都进行 lint
```

step 8:

```bash
? Where do you prefer placing config for Babel, ESLint, etc.? (Use arrow keys)
❯ In dedicated config files // 单独的配置文件
  In package.json

```

step 9:

```bash
? Where do you prefer placing config for Babel, ESLint, etc.? In dedicated confi
g files
? Save this as a preset for future projects? (y/N) n // 不存储预设
```

step 10: 

```bash
npm run serve // 运行项目
```
