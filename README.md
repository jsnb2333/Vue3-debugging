# vuejs/core 源码调试 [![npm](https://img.shields.io/npm/v/vue.svg)](https://www.npmjs.com/package/vue) [![build status](https://github.com/vuejs/core/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/vuejs/core/actions/workflows/ci.yml)

## 前言
vue的开发者模式下的源码是被压缩的，很难理解运行逻辑，而我们需要未压缩的源码调试。

网上大部分方法都是不支持工程化项目中调试的，这样SFC文件就没有对应的调试方案了。但实际上可以使用工程化项目调试。

## 思路

让前端工程化项目可以实现源码调试，主要思路是从vue在npm包改为我们构建的vue包。
但是通过npm link的方式修改package.json，结果是失败的。个人猜测可能是我们引用的vue其实是vite依赖的依赖。对于依赖的依赖可以使用yarn去解决，但是也失败了，原因未知。

使用直接替换node_modules内的vue源码，结果也失败了。个人猜测可能是vue有个可以被静态解析的概念，其源码被vite缓存了。

博客中利用了monorepo优先安装本地的性质，结果是成功的。

## 使用
当前仓库下的代码是已经构建成功的。
```
pnpm install
pnpm build 或 pnpm dev
cd vue-project
pnpm install
pnpm dev
```

## 当前仓库的构建
**从Vue Github网上下载一份源码。并且用pnpm install安装依赖。**

**修改package.json文件：**
```
"dev": "node scripts/dev.js --sourcemap -t", 

"build": "node scripts/build.js --sourcemap -t",
```

--sourcemap 是输出 sourcemap，方便调试

-t 是输出 typescript 类型声明文件，也是方便调试

由于默认的 vue 打包构建，是不会生成 sourcemap 的，因此我们需要加上 --sourcemap，生成 sourcemap，以方便后面调试时，chrome 能够根据 sourcemap，将构建压缩过的代码，还原成源码。


**（这里可以修改源码后）打包构建：**

pnpm build 或 pnpm dev

dev 脚本，因为它可以 watch 监听修改，并自动重新构建。

构建 vue 源码，比如构建产物核心产物在packages\vue\dist\*。这里构建的结果就是npm网上下载到的内容。

其中packages\vue\dist\vue.global.js文件可以直接被html文件引用。但使用方式也是html中的JS脚本调试。

**可以先使用vite构建前端工程化项目。**

pnpm create vite
因为vue源码就是pnpm monorepo，所以不需要重新创建，直接将项目放入vue源码跟目录下。

pnpm-workspace.yaml文件加入“- 'vue-project'”：
```
packages:
  - 'packages/*'
  - 'vue-project'
```
项目目录下面使用pnpm install安装项目依赖。

**启动项目**

pnpm dev 

这时就可以在浏览器控制台中看到Vue未被压缩后的源码。

注意：不支持热更新，我们每次修改源码后都需要重新构建和重新启动项目。

## 参考
https://zhuanlan.zhihu.com/p/460681229 参考博客


