---
date: 2022/10/15
title: mini-vue 0 vue 3 模块之间的依赖关系
tags:
  - mini-vue
categories:
  - mini-vue
---

## 概览

vue 3 采用 monorepo 的形式组织代码，所有的模块都在 `packages` 目录下。
包与包之间的依赖关系明确, 依赖关系如下：

![](../../images/quicker_2859eb70-7322-44a9-ad00-aa02bcbe2c18%201.png)

- `@vue/complier-sfc` 是用来解析单文件组件（Single File Component）
- 编译和运行时分别对应 `runtime` 模块和 `compiler` 模块
  - `runtime-dom` 和 `runtime-core` 解耦
  - `complier-dom` 和 `complier-core` 解耦
  - 这使得 vue 可是不局限于 DOM 平台，可以渲染在其他平台, 例如 canvas
- 响应式在 `reactivity` 模块中实现且不依赖其他包，可以独立使用

总的来说，vue 可以大致的分为三块:
- Reactivity Module
让我们可以创建响应式对象
- Compiler Module
把模板文件转换成 render 函数（可以在浏览器的运行时执行，也可以在构建项目的时候执行）
- Renderer Moudle
可以大致分为三个阶段：
1. Render 阶段：
render function --> 虚拟 DOM 节点
2. Mount 阶段:
虚拟 DOM -->生成 DOM 节点，在网页上显示出来
3. Patch 阶段:
比较新旧虚拟 DOM 节点，更新变化的部分

## 整体流程
首先我们有 template，其中我们可能会使用一些响应式的对象。然后我们把会将模板转换为render function。render function 会创建虚拟 DOM，通过虚拟 DOM 创建真实的DOM 节点。如果响应式对象发生改变就会生成新的虚拟 DOM，比较新旧虚拟 DOM，在真实DOM 上对需要更新的部分进行更新。