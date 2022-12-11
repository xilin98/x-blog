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
