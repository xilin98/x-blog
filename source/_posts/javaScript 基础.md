---
date:  2023-01-07
title: javaScript 基础
tags: 
  - javaScript 基础
categories:
  - javaScript 基础
---

## 模块化方案

// TODO 解释 `esm`
// TODO 解释 `cjs`

### UMD
UMD 是 Universal Module Definition 的缩写。这是一种模块定义的方式，它可以被各种模块加载器或者浏览器直接使用。

UMD 是为了解决 JavaScript 在不同环境下模块加载和使用的问题而创建的。它可以在各种模块加载器之间进行模块的加载和使用，也可以在没有模块加载器的环境下直接使用。

常见的模块加载器有 CommonJS 和 AMD。使用 UMD 可以使得你的模块在这两种模块加载器之间进行切换，同时也可以在没有模块加载器的环境下使用。

UMD 的一个缺点是代码可能会变得冗长，因为它需要支持多种不同的加载方式。
