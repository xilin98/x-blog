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

![](../images/quicker_2859eb70-7322-44a9-ad00-aa02bcbe2c18%201.png)
- `@vue/complier-sfc` 是用来解析单文件组件（Single File Component）
- 编译和运行时分别对应 `runtime` 模块和 `compiler` 模块
  - `runtime-dom` 和 `runtime-core` 解耦
  - `complier-dom` 和 `complier-core` 解耦
  - 这使得 vue 可是不局限于 DOM 平台，可以渲染在其他平台, 例如 canvas
- 响应式在 `reactivity` 模块中实现且不依赖其他包，可以独立使用
