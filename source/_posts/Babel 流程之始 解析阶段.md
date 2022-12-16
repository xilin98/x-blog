---
title: Babel 流程之始 parse
date: 2022-2-17 3:20:00
---

## 转译的开始

本质上，babel 是一个 javascript 转译器 (javascript transpiler)。
为了理解代码第一部就是将它转换成抽象语法树 (Abstract Sytax Tree), 这就是 解析阶段(parse phase)。
Babel 的解析阶段主要负责将 JavaScript 代码解析成抽象语法树，为后续的转换和生成提供基础。它是 Babel 编译器的第一个阶段，为后续的转换和生成提供了重要的基础。

在解析阶段，Babel 会使用一个名为 `babylon` 的 JavaScript 解析器来读取 JavaScript 代码并解析成 AST。`babylon` 解析器会按照 JavaScript 语言的语法规则对代码进行分析，并将代码转换为一系列的节点，每个节点表示代码中的一个语法单元。
例如，对于如下 JavaScript 代码：
```js
function sum(a, b) {
  return a + b;
}
```
Babel 会将它解析成如下 AST：
```json
{
  "type": "Program",
  "body": [
    {
      "type": "FunctionDeclaration",
      "id": {
        "type": "Identifier",
        "name": "sum"
      },
      "params": [
        {
          "type": "Identifier",
          "name": "a"
        },
        {
          "type": "Identifier",
          "name": "b"
        }
      ],
      "body": {
        "type": "BlockStatement",
        "body": [
          {
            "type": "ReturnStatement",
            "argument": {
              "type": "BinaryExpression",
              "operator": "+",
              "left": {
                "type": "Identifier",
                "name": "a"
              },
              "right": {
                "type": "Identifier",
                "name": "b"
              }
            }
          }
        ]
      }
    }
  ]
}

```

从上面的 AST 可以看出，Babel 在解析阶段会将 JavaScript 代码按照语法规则分解成一系列的节点，每个节点表示代码中的一个语法单元。这些节点的组合组成了一棵抽象语法树，表示了整个 JavaScript 代码的结构和语法。

这个过程分为词法分析和语法分析

词法分析：

```js
function  // 关键字
sum       // 标识符
(         // 左圆括号
a         // 标识符
,         // 逗号
b         // 标识符
)         // 右圆括号
{         // 左大括号
   return  // 关键字
   a       // 标识符
   +       // 加法运算符
   b       // 标识符
   ;       // 分号
}         // 右大括号
```

语法分析:

“function”是一个关键字，表示定义函数。“sum”是一个标识符，表示函数的名称。圆括号中的“a”和“b”也是标识符，表示函数的参数。大括号中的“return”是关键字，表示返回值。“a”和“b”之间的“+”是加法运算符，表示两个参数的和。最后的分号表示语句结束。

综上，该代码定义了一个名为“sum”的函数，该函数接受两个参数“a”和“b”，并返回这两个参数的和。

最后生成 [[抽象语法树(AST)]]
```
FunctionDeclaration
├── Identifier (sum)
├── FormalParameters
│   ├── BindingIdentifier (a)
│   └── BindingIdentifier (b)
└── BlockStatement
    └── ReturnStatement
        └── BinaryExpression
            ├── Identifier (a)
            ├── "+"
            └── Identifier (b)
```

## `@babel/parser`
Babel/parser 是一个 JavaScript 解析器，可以将 JavaScript 代码解析成一个抽象语法树 (AST)。这个 AST 可以被其他工具使用，例如 Babel，一个 JavaScript 编译器，可以使用 AST 来将新版本的 JavaScript 代码转译成旧版本的 JavaScript 代码，以便在旧版本的浏览器或运行环境中运行。
Babel/parser 可以解析 ECMAScript 的所有版本，包括 ES5、ES6、ES7 等。它还支持解析 JSX，一种在 React 应用中使用的 JavaScript 语法扩展(默认不支持, 可以添加插件实现)。

使用 Babel/parser 可以很方便地进行代码分析、转换和优化。你可以使用它来检查你的代码中是否有语法错误，或者提取你的代码中的某些信息，例如函数调用、变量声明等。你还可以使用它来构建代码生成器或代码修改器。
Babel/parser 是一个开源项目，你可以在 GitHub 上找到它的源代码：[https://github.com/babel/babel/tree/master/packages/babel-parser](https://github.com/babel/babel/tree/master/packages/babel-parser)

你可以使用 npm 来安装 Babel/parser：
```bash
npm install @babel/parser
```

然后就可以在你的 JavaScript 代码中使用它了：
```js
const parser = require('@babel/parser');

const ast = parser.parse('1 + 2');
console.log(ast);
```

结果如下: 
```js
Node {
  type: 'File',
  start: 0,
  end: 5,
  loc: SourceLocation {
    start: Position { line: 1, column: 0, index: 0 },
    end: Position { line: 1, column: 5, index: 5 },
    filename: undefined,
    identifierName: undefined
  },
  errors: [],
  program: Node {
    type: 'Program',
    start: 0,
    end: 5,
    loc: SourceLocation {
      start: [Position],
      end: [Position],
      filename: undefined,
      identifierName: undefined
    },
    sourceType: 'script',
    interpreter: null,
    body: [ [Node] ],
    directives: []
  },
  comments: []
}
```

`parse` 函数签名如下
```ts
parse(code: string, options?: ParserOptions): {
  type: 'Program',
  body: Array<Node>,
  sourceType: 'script' | 'module',
  ...
}
```

其中，`code` 参数是待解析的 JavaScript 代码字符串，`options` 参数是可选的，用于配置解析过程的选项。

`ParserOptions` 类型包含以下属性：

-   `sourceType`: 值为 `"script"` 或 `"module"` 或 `unambiguous`，用于指定代码的模块化方式。
     - `"script"`: 不使用 ES module 模块化方式
     - `"module"`: 使用 ES module 模块化方式
     - `unambiguous`: 根据语法是否使用 ES module 模块化方式
-   `allowImportExportEverywhere`: 值为布尔值，用于指定是否允许在任何地方使用 `import` 和 `export` 语句。
    
-   `allowReturnOutsideFunction`: 值为布尔值，用于指定是否允许在函数外使用 `return` 语句。
    
-   `allowSuperOutsideMethod`: 值为布尔值，用于指定是否允许在方法外使用 `super` 关键字。
    
-   `strictMode`: 值为布尔值，用于指定是否启用严格模式。
    
-   `plugins`: 一个字符串数组，用于指定要启用的插件。

- `ranges`: 值为布尔值，用于指定是否在解析后的 AST 中包含源代码中每个节点的起始和结束位置。

- `locations`: 值为布尔值，用于指定是否在解析后的 AST 中包含源代码中每个节点的行号和列号。

- `onToken`: 值为函数，用于在解析每个词法单元时调用的回调函数。

- `onComment`: 值为函数，用于在解析每个注释时调用的回调函数。

- `tolerant`: 值为布尔值，用于指定是否启用宽容模式。在宽容模式下，解析器会忽略语法错误，并尽量生成完整的 AST。

- `ecmaVersion`: 值为数字，用于指定要解析的 ECMAScript 版本。默认值为当前环境支持的最高版本。

- `sourceFilename`: 值为字符串，用于指定要解析的代码文件名。

- `preserveParens`: 值为布尔值，用于指定是否在解析后的 AST 中保留括号。

- `errorRecovery`: 用于控制解析过程中遇到语法错误时的行为。它有两个可能的值：
  -   `true`: 表示在遇到语法错误时，解析器会尽量生成完整的 AST，并忽略语法错误。
  -   `false`: 表示在遇到语法错误时，解析器会立即抛出异常，终止

当然，你也可以使用 `parseExpression` 函数来解析 JavaScript 表达式。它的签名如下：

```ts
parseExpression(code: string, options?: ParserOptions): Expression | Directive
```
其中，`code` 参数是待解析的 JavaScript 表达式字符串，`options` 参数是可选的，用于配置解析过程的选项。

Babel Parser 还提供了一些其他的函数，如 `parseStatement` 和 `parseBlock`，用于解析 JavaScript 语句和代码块。

另外，你也可以使用 `parse` 函数的变体来解析 ECMAScript 模块，如 `parseModule` 和 `parseModuleExpression`。

`babel/parser` 还支持通过插件机制来扩展其功能。你可以通过在 `ParserOptions` 中指定插件名称来启用插件。

例如，如果你想使用 Babel Parser 支持 JSX 语法，可以在 `ParserOptions` 中指定 `"jsx"` 插件：
```js
const ast = parse(code, {
  sourceType: 'module',
  plugins: ['jsx']
});
```
