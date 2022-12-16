---
date: 2022/10/15
title: Babel 生成阶段
tags: 
  - babel 
  - 前端工程hx
categories:
  - babel
---

在对 AST 进行修改后, 就可以生成目标代码了。

##  `@babel/generate`
@babel/generator 模块提供了一个默认的函数，该函数接受一个抽象语法树 (AST) 并生成代码。这个函数的签名如下：
```ts
generate(ast: Node, options?: GeneratorOptions, code?: string): GenerateResult
```
-   `ast` 是要生成代码的抽象语法树，这是一个 JavaScript 对象。
    
-   `options` 是可选的，是一个配置对象，用于控制生成的代码的格式。可用的选项包括：
    
  -   `comments`: 是否在生成的代码中包含注释。默认为 `true`。
      
  -   `compact`: 是否生成紧凑的代码。如果设为 `true`，则会尽可能减少生成的代码的行数。默认为 `false`。
      
  -   `filename`: 生成的代码的文件名。这可以用于生成的代码的调试信息。
      
  -   `retainLines`: 是否保留源代码中的行号。如果设为 `true`，则生成的代码的行号与源代码的行号相同。默认为 `false`。
      
  -   `sourceMaps`: 是否生成源映射。如果设为 `true`，则会生成一个源映射文件，用于调试生成的代码。默认为 `false`。
      
  -   `sourceRoot`: 生成的源映射文件中源代码的根目录。
      
  -   `startLine`: 生成的代码的起始行号。默认为 `1`。
      
  -   `endLine`: 生成的代码的结束行号。

  下面是一个示例:
```js
const options = {
  compact: true,
  retainLines: true,
  sourceMaps: true,
  sourceRoot: '/src',
  startLine: 1,
  endLine: 10,
  comments: true,
  minified: true,
  concise: true,
  quotes: 'double',
  retainFunctionParens: true,
  auxiliaryCommentBefore: 'some comment',
  auxiliaryCommentAfter: 'some other comment',
  shouldPrintComment: comment => /^\**!|@preserve|@license|@cc_on/i.test(comment),
  environments: {
    browser: {
      presets: ['@babel/preset-env']
    }
  }
};

const generatedCode = generate(ast, options).code;

```

我们也可以拿到 sourcemap, 
```js
const { code, map } = generate(ast, { sourceMaps: true })
```

## `@babel/code-frame`
@babel/code-frame 是一个 Babel 库的一部分，主要用于在报告错误时在代码中生成格式化的代码框。它接受代码字符串和错误对象，并生成包含错误信息的代码框，可以方便用户查看和修复错误。

例如，如果在您的代码中有语法错误，则可以使用 @babel/code-frame 生成格式化的代码框，以方便您找到并修复错误
它的用法非常简单，你可以在你的代码中这样使用它：
```js
import { codeFrameColumns } from '@babel/code-frame';

try {
  // 在这里执行一些代码
} catch (error) {
  console.log(codeFrameColumns(error.code, { start: error.loc }));
}
```
在上面的代码中，我们在 `try` 块中执行一些代码，如果代码执行出错，就会捕获到 `error` 对象，然后我们调用 `codeFrameColumns` 函数，将 `error.code` 和 `error.loc` 传递给它。`error.code` 是一段代码，`error.loc` 是错误发生的位置（行号和列号）。

`codeFrameColumns` 函数会返回一段可读的代码片段，其中会用特殊的颜色标记出错误的位置，并在下面输出错误信息。这样，你就可以快速定位到错误的位置，并解决问题。

