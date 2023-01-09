---
date: 2022/10/16
title: Babel 流程整合
tags: 
  - babel
  - 前端工程化
categories:
  - babel
---

## `@babel/core`
babel 有一个把解析、转换、生成阶段整合在一起的包， 就是 `@babel/core` 
@babel/core 是 Babel 的核心库，它提供了用于编译 JavaScript 代码的基本功能。你可以使用 @babel/core 包来将源代码转换为更新的、更兼容的 JavaScript 代码，这样你就可以在更多的环境中运行你的代码。

你可以使用 `transform` 函数来编译代码，它有以下几个参数：

-   `code`：要编译的代码
-   `options`：编译选项

`transform` 函数会返回一个对象，其中包含编译后的代码、源映射 (source map) 和抽象语法树 (AST)。你可以使用这些信息来调试代码或者进一步处理代码。

下面是一个例子：
```js
const { transform } = require('@babel/core');

const { code, map, ast } = transform(`const foo = "bar"; console.log(foo)`, {
  sourceType: 'module',
});

console.log(code);
// 输出: "const foo = 'bar';\nconsole.log(foo);"
console.log(map);
// 输出: { version: 3, sources: [...], names: [...], mappings: "..." }
console.log(ast);
// 输出: { type: "Program", body: [...], sourceType: "module" }
```

文档 [@babel/core · Babel](https://babeljs.io/docs/en/babel-core)
