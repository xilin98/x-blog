---
date:  2023-01-06
title: 从0到1实现前端埋点 SDK
tags: 
  - 前端工程化
categories:
  - 前端工程化
  - 前端埋点
---

## 什么是前端埋点

前端埋点是指在网页的前端（即客户端）放置的记录用户行为的代码。通常，这些代码会将用户的操作记录下来，然后通过某种方式传回后端服务器，以便进行分析和统计。前端埋点常常被用来跟踪用户在网站上的行为，以便对网站进行优化并提供更好的用户体验。

要实现一个前端埋点系统，通常需要以下几个步骤：
1.  确定要跟踪哪些用户行为。这可能包括点击按钮、访问特定页面、填写表单等等。
2.  在网页中插入跟踪代码。这些代码通常使用 JavaScript 实现，并且需要在每个需要跟踪的行为发生时触发。
3.  设计后端服务器来接收并处理来自前端的埋点数据。这可能包括将数据存储到数据库中、使用分析工具对数据进行汇总和可视化等等。
4.  使用工具来可视化和分析埋点数据。这可能包括使用网络分析工具、数据可视化工具等来帮助您理解用户的行为模式。

## 技术选型

ts + rollup
- ts 可以提供更好的代码提示 
- rollup 配置比较干净和简洁，相比较 webpack 更适合开发工具类的库或框架，比方说 vue3 就是使用 rollup 打包的。

下面是 Rollup 和 Webpack 的一些主要区别：
-   Rollup 是一个模块打包器，而 Webpack 是一个模块打包工具。这意味着 Webpack 支持打包更多类型的文件，如图像和字体，以及更多的加载器（loaders）。
-   Rollup 具有更快的打包速度，因为它是专注于打包 JavaScript 模块的。Webpack 打包速度可能较慢，因为它可能需要处理更多类型的文件。
-   Rollup 使用树摇晃（tree-shaking）机制来删除未使用的代码。Webpack 也支持树摇晃，但需要使用 UglifyJS 插件。
-   Rollup 具有较少的配置选项，因此它可能更容易使用。Webpack 具有更多的配置选项，但也提供了更多的灵活性。

## 项目结构

```
src/
--core/
--types/
--utils/
rollup.config.js
tsconfig.json
```

## 安装相关依赖

```bash
npm install rollup -D
npm install rollup-plugin-dts -D
npm install rollup-plugin-typescript2 -D
npm install typescript -D
```
rollup-plugin-dts 是一个 Rollup 插件，用于将 TypeScript 声明文件转换为 JavaScript 代码，并生成相应的 `*.d.ts` 文件。这个插件可以帮助你在使用 TypeScript 编写的 JavaScript 库的时候更方便地生成声明文件。

## 配置 rollup 
📄 rollup.config.js
```js
import ts from "rollup-plugin-typescript2"
import dts from "rollup-plugin-dts"
export default [
  {
    //入口文件 相当于 webpack 里的 entry 选项
    input: "./src/core/index.ts",
    output: [
      //打包esModule
      {
        file: "./dist/index.esm.js",
        format: "es",
      },
      //打包common js
      {
        file: "./dist/index.cjs.js"
        format: "cjs",
      },
      //打包 AMD CMD UMD
      {
        file: "./dist/index.js",
        format: "umd",
      },
    ],
    //配置 ts 插件
    plugins: [ts()],
  },
  {
    //打包声明文件
    input: "./src/core/index.ts",
    output: {
      file: path.resolve(__dirname, "./dist/index.d.ts"),
      format: "es",
    },
    plugins: [dts()],
  },
]
```

## 配置一下 npm script

加上打包命令 `build`: `rollup -c`


