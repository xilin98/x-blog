---
title: Balel 转换阶段 
tags: 
  - babel
  - 前端工程化
categories:
  - babel 
---

在将源码转换为 AST 之后下一步就是对 AST 进行操作。 Babel 也为我们提供了对应的 api 让我们可以方便地对其进行遍历和修改.

## `@babel/travase`
Babel Traverse 是 Babel 库的一个模块，用于遍历和修改 JavaScript 代码的抽象语法树 (AST)。

使用 Babel Traverse 可以方便地对 AST 进行操作，例如查找、修改、删除指定的节点。

Babel Traverse 提供了一个 `traverse` 函数，用于遍历 AST。

`traverse` 函数的签名如下：
```ts
traverse(ast: Node, visitor: Visitor, state?: any)
```

其中，`ast` 参数是待遍历的 AST，`visitor` 参数是一个访问器对象，用于指定在遍历过程中要执行的操作。`state` 参数是可选的，用于在遍历过程中传递信息。

访问器对象包含了一组函数，每个函数对应一种 AST 节点类型。在遍历过程中，Babel Traverse 会调用访问器对象中对应节点类型的函数。

例如，下面是使用 Babel Traverse 遍历 AST 的示例：
```js
const visitor = {
  Identifier(path) {
    console.log(path.node.name);
  }
};
traverse(ast, visitor);
```

在这个示例中，访问器对象中的 Identifier 函数会在遍历过程中被调用，每当遇到标识符节点时都会执行。

其中的函数是可组合的，例如
```js
traverse(ast, {
  "FunctionDeclaration|VariableDeclaration"(path, state) {

  },
})
```

这些函数也是有别名的，可以在[babel/index.ts at main · babel/babel · GitHub](https://github.com/babel/babel/blob/main/packages/babel-types/src/ast-types/generated/index.ts#L2059) 查看

`state` 是一个可选的参数，用于在遍历过程中传递信息。

你可以使用 `state` 参数在遍历过程中传递变量或其他信息。例如，你可以使用它来记录遍历过程中遇到的节点数量：
```js
traverse(
  ast,
  {
    enter(path, state) {
      state.nodeCount++
    },
  },
  { nodeCount: 0 }
)
```
另外，你还可以使用 `state` 参数来传递变量或者其他信息，例如：

```js
traverse(ast, {
  enter(path, state) {
    if (path.node.type === 'Identifier') {
      console.log(`Found identifier: ${path.node.name} in file ${state.filename}`);
    }
  },
}, { filename: 'example.js' });

```

在这个示例中，`state` 参数是一个对象，包含了一个 `filename` 属性。在遍历过程中，如果遇到标识符节点，就会使用 `state.filename` 输出文件名。

`Path` 参数是一个对象，包含了有关当前节点的信息以及操作当前节点的方法。它有以下几个常用属性：

-   `node`：当前节点。
-   `parent`：当前节点的父节点。
-   `parentPath`：当前节点的父节点的 `Path` 对象。
-   `context`：遍历过程中的上下文信息。
-   `opts`：传递给 `traverse` 函数的配置选项。

`Path` 对象还包含了许多方法，可以用于操作当前节点和遍历过程。例如：

-   `replaceWith(node)`：用指定的节点替换当前节点。
-   `remove()`：删除当前节点。
-   `skip()`：跳过当前节点的子节点的遍历。
-   `stop()`：停止整个遍历过程。
-   `insertBefore(node)`：在当前节点之前插入一个节点。
-   `insertAfter(node)`：在当前节点之后插入一个节点。
-   `replaceWithMultiple(nodes)`：用一组节点替换当前节点。
-   `traverse(visitor, state)`：在当前节点的子树中进行遍历，并使用指定的 `visitor` 回调处理节点。
-   `getSibling(key)`：获取当前节点的兄弟节点。
-   `getValue()`：获取当前节点的值。
在 `visitor` 回调中，你可以使用 `Path` 对象的信息和方法来处理当前节点，并通过返回值控制遍历的流程。

下面是一个示例，展示了如何在遍历过程中使用 `Path` 对象：
例如，你可以使用 `replaceWith` 函数替换 AST 节点：
```js
path.replaceWith(newNode);
```
`remove` 函数可以删除 AST 节点：
```js
path.remove();
```
还有一些其他的函数，如 insertAfter 和 insertBefore，可以在 AST 节点的前面或后面插入新的节点：
```js
path.insertAfter(newNode);
path.insertBefore(newNode);
```

另外，Babel Traverse 还提供了一些函数，用于获取 AST 节点的信息。例如，你可以使用 `get` 函数获取 AST 节点的指定属性的值：
```js
const name = path.get('name');
```
还有一些其他的函数，如 `isIdentifier`、`isFunctionDeclaration` 等，可以用于判断 AST 节点的类型。

例如，下面是使用 `isIdentifier` 函数判断 AST 节点是否为标识符节点的示例：
```js
if (path.isIdentifier()) {
  console.log('This node is an identifier.');
}
```
同样的，你还可以使用 `isFunctionDeclaration` 函数判断 AST 节点是否为函数声明节点：
```js
if (path.isFunctionDeclaration()) { console.log('This node is a function declaration.'); }
```

###  在进入节点和离开节点执行回调
在遍历 AST 的过程中，Babel Traverse 会为每个节点调用两个回调函数：`exit` 和 `enter`。

-   `exit` 函数在遍历到每个节点时被调用，在遍历该节点的子节点之前调用。
    
-   `enter` 函数在遍历到每个节点时被调用，在遍历该节点的子节点之后调用。
    

这两个回调函数都可以用于指定在遍历过程中要执行的操作。

例如，下面是使用 `exit` 和 `enter` 函数遍历 AST 的示例：
```js
const visitor = {
  Identifier: {
    exit(path) {
      console.log('Exiting identifier:', path.node.name);
    },
    enter(path) {
      console.log('Entering identifier:', path.node.name);
    },
  },
};
traverse(ast, visitor);

```
在这个示例中，访问器对象中的 `exit` 函数和 `enter` 函数会在遍历过程中被调用，分别在遍历标识符节点之前和之后执行。

## `@babel/types`

`@babel/types` 是一个 JavaScript 库，用于操作和生成 JavaScript 的抽象语法树（AST）。它提供了一组函数，可以用于创建、修改和验证 AST 节点。

例如，你可以使用 `@babel/types` 的 `isIdentifier(node)` 函数来验证一个节点是否是标识符节点，或者使用 `@babel/types` 的 `numericLiteral(value)` 函数来创建一个数字字面量节点。

下面是一些示例，展示了如何使用 `@babel/types` 库 :
创建一个 AST 节点:
```js
const t = require('@babel/types');

// 创建一个数字字面量节点
const node = t.numericLiteral(42);

console.log(t.isNumericLiteral(node));  // true
console.log(node.value);  // 42
```

创建一个函数声明：
```js
const t = require('@babel/types');

// 创建一个函数声明节点
const node = t.functionDeclaration(
  t.identifier('add'),  // 函数名
  [t.identifier('a'), t.identifier('b')],  // 参数列表
  t.blockStatement([
    t.returnStatement(t.binaryExpression('+', t.identifier('a'), t.identifier('b')))  // 返回 a + b
  ])
);
```

判断一个节点是否是某种类型：
```js
const t = require('@babel/types');

const node = t.binaryExpression('+', t.numericLiteral(1), t.numericLiteral(2));

console.log(t.isBinaryExpression(node));  // true
console.log(t.isNumericLiteral(node));  // false

```

-   修改一个节点的属性：

```js
const t = require('@babel/types');

let node = t.numericLiteral(42);

// 修改节点的值
node = t.numericLiteral(123);

console.log(node.value);  // 123
```

api 文档 -> [@babel/types · Babel](https://babeljs.io/docs/en/babel-types#api)

## `@babel/template`
`@babel/template` 是一个 JavaScript 库，用于生成 JavaScript 代码的抽象语法树（AST）。它使用一种类似于模板字符串的语法，允许你通过简单的字符串构建复杂的 AST。

例如，你可以使用 `@babel/template` 的 `template.expression` 函数来生成一个表达式的 AST：

```js
const t = require('@babel/types');
const template = require('@babel/template').default;

// 使用 template.expression 函数生成 AST
const ast = template.expression('a + b')({
  a: t.identifier('x'),
  b: t.identifier('y')
});

console.log(ast);
// Output:
// {
//   "type": "BinaryExpression",
//   "operator": "+",
//   "left": {
//     "type": "Identifier",
//     "name": "x"
//   },
//   "right": {
//     "type": "Identifier",
//     "name": "y"
//   }
// }
```

你还可以使用 `@babel/template` 的 `template.program` 函数生成一个完整的代码块的 AST：

```js
const t = require('@babel/types');
const template = require('@babel/template').default;

// 使用 template.program 函数生成 AST
const ast = template.program([
  'function add(a, b) {',
  '  return a + b;',
  '}'
])({
  a: t.identifier('x'),
  b: t.identifier('y')
});

console.log(ast);
// Output:
// {
//   "type": "Program",
//   "body": [
//     {
//       "type": "FunctionDeclaration",
//       "id": {
//         "type": "Identifier",
//         "name": "add"
//       },
//       "params": [
//         {
//           "type": "Identifier",
//           "name": "a"
//         },
//         {
//           "type": "Identifier",
//           "name": "b"
//         }
//       ],
//       "body": {
//         "type": "BlockStatement",
//         "body": [
//           {
//             "type": "ReturnStatement",
//             "argument": {
//               "type": "BinaryExpression",
//               "operator": "+",
//               "left": {
//                 "type": "Identifier",
```
下面是一些常用的 API：

-   `template.program(strings, ...expressions)`：生成一个完整的代码块的 AST。`strings` 参数是一个字符串数组，表示代码块的各个部分。`expressions` 参数是一组表达式，用于插入到 `strings` 中。
    
-   `template.expression(strings, ...expressions)`：生成一个表达式的 AST。`strings` 和 `expressions` 参数的用法同上。
    
-   `template.statement(strings, ...expressions)`：生成一个语句的 AST。`strings` 和 `expressions` 参数的用法同上。