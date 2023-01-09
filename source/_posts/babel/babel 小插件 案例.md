
---
date: 2022/12/16
title: babel 小插件 案例
tags: 
  - babel
  - 前端工程化
categories:
  - babel
---

## 需求
给所有 `console.log` 带上 行号和列号
把
```
console.log('hello')
```

自动转换为

```js
console.log("line: <line>, column: <column>)", 'hello')
```

先搭好流程: 
```js
const parser = require('@babel/parser');
const traverse = require('@babel/traverse').default;
const generate = require('@babel/generator').default;
const types = require('@babel/types');

const sourceCode = `console.log('hello');`;

const ast = parser.parse(sourceCode, {
  sourceType: 'unambiguous'
});

traverse(ast, {
    CallExpression(path, state) {
      if(path.node.callee.object.name === 'console' 
            && ['log', 'info', 'error', 'debug'].includes(path.node.callee.property.name)){
             const { line, column } = path.node.loc.start; 
              path.node.arguments.unshift(types.StringLiteral(`line: ${line}, column: ${columm}` ))

            }
    }
});

const { code, map } = generate(ast);
console.log(code);
```

打印结果为:
```js
console.log("line: 1, column: 0", 'hello');
```

## 美化
如果决定行号和列好放在同一个 `console.log` 里面不好看的话, 可以把位置信息单独放在一个
`console.log` 中,
即将 
```js
console.log("hello")
```

转换为
```js
console.log('line: 1, column')
console.log("hello")
```

并且需要处理 JSX, 因为 JSX 只支持单个表达式， 所以考虑使用数组包裹

## 代码实现
```js
const parser = require('@babel/parser');
const traverse = require('@babel/traverse').default;
const generate = require('@babel/generator').default;
const types = require('@babel/types');
const template = require('@babel/template').default;

const ast = parser.parse(sourceCode, {
    sourceType: 'unambiguous',
    plugins: ['jsx']
});

const targetCalleeName = ['log', 'info', 'error', 'debug'].map(item => `console.${item}`);

traverse(ast, {
    CallExpression(path, state) {
        if (path.node.isNew) {
            return;
        }
        const calleeName = generate(path.node.callee).code;
         if (targetCalleeName.includes(calleeName)) {
            const { line, column } = path.node.loc.start;

            const newNode = template.expression(`console.log("filename: (${line}, ${column})")`)();
            newNode.isNew = true;

            if (path.findParent(path => path.isJSXElement())) {
                path.replaceWith(types.arrayExpression([newNode, path.node]))
                path.skip();
            } else {
                path.insertBefore(newNode);
            }
        }
    }
});
```

这段代码使用 @babel/traverse 遍历 AST，并在遇到 CallExpression 节点时执行一些操作。

CallExpression 节点表示函数调用表达式，例如 `foo()` 或 `obj.method()`。

首先，代码中使用 `path.node.isNew` 检查 CallExpression 节点是否是新的，如果是，就跳过当前节点。

然后，代码使用 `generate(path.node.callee).code` 获取调用的函数的名称。

接下来，使用 `targetCalleeName.includes(calleeName)` 判断函数名是否在目标名称列表中，如果在，就执行一些操作。

具体来说，首先使用 `path.node.loc.start` 获取调用行和列的位置信息。然后使用 `template.expression` 创建一个新的节点，该节点表示输出 "filename: (line, column)" 的 console.log 调用。

接下来，使用 `path.findParent` 查找 CallExpression 节点的父节点是否为 JSXElement 类型，如果是，就使用 `path.replaceWith` 将 CallExpression 节点替换为一个新的数组表达式，该表达式包含新的节点和原始的 CallExpression 节点。否则，就使用 `path.insertBefore` 在 CallExpression 节点之前插入新的节点。

最后，使用 `path.skip()` 跳过当前节点。

将这个功能改造成一个插件：
```js
module.exports = function(api, options) {
  return {
    visitor: {
      Identifier(path, state) {},
    },
  };
}
```

如上所示, babel 插件就是个函数, 可以通过第一个参数拿到 babel 的api, 返回一个对象，其中包含一个或多个转换器函数。转换器函数是用于在 AST 上进行转换的函数，它接受一个节点和一个转换器对象作为参数，并返回转换后的节点。

这里的 visitor 属性就相当于是在 traverse 中的第二个选项。
```js
const { generate }= require('@babel/generator');
const targetCalleeName = ['log', 'info', 'error', 'debug'].map(item => `console.${item}`);

module.exports = function({types, template}) {
    return {
        visitor: {
            CallExpression(path, state) {
                if (path.node.isNew) {
                    return;
                }
  
                const calleeName = generate(path.node.callee).code;

                 if (targetCalleeName.includes(calleeName)) {
                    const { line, column } = path.node.loc.start;
                    
                    const newNode = template.expression(`console.log("${state.filename || 'unkown filename'}: (${line}, ${column})")`)();
                    newNode.isNew = true;

                    if (path.findParent(path => path.isJSXElement())) {
                        path.replaceWith(types.arrayExpression([newNode, path.node]))
                        path.skip();
                    } else {
                        path.insertBefore(newNode);
                    }
                }
            }
        }
    }
}
```

自己调用该插件
```js
const { transformFileSync } = require('@babel/core');
const insertParametersPlugin = require('./plugin/parameters-insert-plugin');
const path = require('path');

const { code } = transformFileSync(path.join(__dirname, './sourceCode.js'), {
    plugins: [insertParametersPlugin],
    parserOpts: {
        sourceType: 'unambiguous',
        plugins: ['jsx']       
    }
});

console.log(code);
```

或者我们也可以借助 babel/cli 进行配置
```bash
mkdir my-babel-plugin
cd my-babel-plugin
npm init -y
npm install --save-dev @babel/core @babel/cli
```

然后在 `package.json` 中配置
```json
{
  "name": "my-babel-plugin",
  "devDependencies": {
    "@babel/core": "^7.0.0",
    "@babel/cli": "^7.0.0"
  },
  "babel": {
    "plugins": ["my-babel-plugin"]
  }
}
```

使用 babel 进行转换
```bash
npx babel src --out-dir lib
```