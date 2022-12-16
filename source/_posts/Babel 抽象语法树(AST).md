---
title: Babel 抽象语法树(AST)
date: 2022-2-18 3:20:00
tag: 
  - babel
---

## javascript 的 AST标准 Estree

为了让机器能够理解代码的含义我们把代码转换为了抽象语法树（AST）, AST 的结构是有对应的标准的。在 javascript 中， AST 通常遵循 `estree` 标准.

ESTree（ECMAScript Tree）是一种用于表示 ECMAScript 源代码语法结构的抽象语法树（AST）的标准。ECMAScript 是 JavaScript 编程语言的标准，而 ESTree 标准则定义了如何表示 JavaScript 代码的语法结构，以方便编译器和其他工具对 JavaScript 代码进行处理。

ESTree 标准定义了一系列用于表示源代码结构的节点类型，并且每个节点都有一些属性来表示它所表示的结构的具体信息。例如，对于一个函数声明，ESTree 标准中定义了“FunctionDeclaration”节点来表示这个结构。“FunctionDeclaration”节点中会包含“id”属性表示函数名，“params”属性表示函数的参数，“body”属性表示函数主体等。

ESTree 标准的目的是让 JavaScript 编译器和工具之间能够交换源代码的语法信息，从而提高 JavaScript 代码的可移植性和可复用性。

==AST 中的节点类型涵盖了 javaScript 中的所有语法结构==
下面是常见的节点类型:

-   Program：表示整个源代码的结构。
-   BlockStatement：表示代码块，例如 if 语句中的代码块、函数主体等。
-   VariableDeclaration：表示变量声明的语句。
-   VariableDeclarator：表示变量的声明，包括变量名和初始值。
-   Identifier：表示标识符。
-   Literal：表示字面量，例如数字、字符串等。
-   ExpressionStatement：表示表达式语句，例如赋值语句、函数调用语句等。
-   BinaryExpression：表示两个操作数的二元表达式，例如加法、乘法等。
-   UnaryExpression：表示一个操作数的一元表达式，例如取反、自增等。
-   FunctionDeclaration：表示函数声明，包括函数名、参数列表和函数主体。
-   ReturnStatement：表示函数返回语句。
-   IfStatement：表示 if 语句，包括判断条件和执行的代码块。
-   SwitchStatement：表示 switch 语句，包括判断条件和多个 case 分支。
-   ForStatement：表示 for 循环语句，包括初始化语句、循环条件和更新语句。
-   WhileStatement：表示 while 循环语句，包括循环条件和执行的代码块。
-   DoWhileStatement：表示 do-while 循环语句，包括循环条件和执行的代码块。
-   BreakStatement：表示 break 语句。
-   ContinueStatement：表示 continue 语句。

在 AST 中，每个节点类型都是独立的，它们之间没有任何继承关系。例如，“FunctionDeclaration”节点类型并不是“BlockStatement”节点类型的子类型，它们之间没有任何关系。

不过，在 AST 中，一些节点类型可能会拥有其他节点类型作为它的子节点。例如，一个“FunctionDeclaration”节点可能会有一个“BlockStatement”节点作为它的子节点，表示函数主体。但这并不意味着“FunctionDeclaration”是“BlockStatement”的子类型，它们之间只是有一个父子关系，并不存在继承关系。

下面将介绍具体的语法结构。

## 语句(Statement + Declaration)

在 JavaScript 中，语句是指一个独立的执行单元。语句可以是赋值语句、函数调用语句、if 语句、for 语句、while 语句等。
在 JavaScript 中，每个语句都会在 AST 中对应一个节点。下面列出了每种语句对应的 AST 节点类型：

1.  声明语句：对应 AST 的 VariableDeclaration 节点，表示一个变量声明。
    
2.  表达式语句：对应 AST 的 ExpressionStatement 节点，表示一个表达式语句。
    
3.  控制语句：对应 AST 的 IfStatement、ForStatement、WhileStatement 等节点，分别表示 if、for、while 等控制语句。
    
4.  语句块：对应 AST 的 BlockStatement 节点，表示一个语句块。
    
5.  空语句：对应 AST 的 EmptyStatement 节点，表示一个空语句。
    

注意：AST 节点的类型是由 JavaScript 语言本身定义的，并且可能会在不同的 JavaScript 引擎中有所差异。

### 声明语句(Declaration)
#### 变量声明 (VariableDeclaration)
```js
const x = 123;
```

对应的 AST
```
VariableDeclaration
└── VariableDeclarator
    ├── Identifier (x)
    └── NumericLiteral (123)
```

#### 函数声明(FunctionDeclaration)
```js
function add(x, y) {
  return x + y;
}
```
对应的 AST
```
FunctionDeclaration
├── Identifier (add)
├── FormalParameters
│   ├── Identifier (x)
│   └── Identifier (y)
└── BlockStatement
    ├── ReturnStatement
    │   └── Binary Expression
    │       ├── Identifier (x)
    │       └── Identifier (y)
    └── End
```
####  类声明(ClassDeclaration)
```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}
```
对应的 AST
```
ClassDeclaration
├── Identifier (Point)
└── ClassBody
    ├── MethodDefinition (constructor)
    │   ├── Identifier (constructor)
    │   ├── FormalParameters
    │   │   ├── Identifier (x)
    │   │   └── Identifier (y)
    │   └── BlockStatement
    │       ├── ExpressionStatement
    │       │   └── Assignment Expression
    │       │       ├── Member Expression
    │       │       │   ├── ThisExpression
    │       │       │   └── Identifier (x)
    │       │       └── Identifier (x)
    │       ├── Expression Statement
    │       │   └── Assignment Expression
    │       │       ├── Member Expression
    │       │       │   ├── ThisExpression
    │       │       │   └── Identifier (y)
    │       │       └── Identifier (y)
    │       └── End
    └── End
```
####  导入声明(ImportDeclaration) 
```js
import { sum } from './math';
```
对应的 AST
```
ImportDeclaration
├── ImportSpecifier
│   ├── Identifier (sum)
│   └── Identifier (sum)
└── StringLiteral ('./math')
```
####  命名导出语句(ExportNamedDeclaration)
```js
export function add(x, y) {
  return x + y;
}
```

对应的AST
```
ExportNamedDeclaration
├── FunctionDeclaration
│   ├── Identifier (add)
│   ├── FormalParameters
│   │   ├── Identifier (x)
│   │   └── Identifier (y)
│   └── Block Statement
│       ├── Return Statement
│       │   └── Binary Expression
│       │       ├── Identifier (x)
│       │       └── Identifier (y)
│       └── End
└── End
```

####  默认导出语句(ExportDefaultDeclaration)
```js
export default function add(x, y) {
  return x + y;
}
```
对应的 AST
```
ExportDefaultDeclaration
└── FunctionDeclaration
    ├── Identifier (add)
    ├── FormalParameters
    │   ├── Identifier (x)
    │   └── Identifier (y)
    └── Block Statement
        ├── Return Statement
        │   └── Binary Expression
        │       ├── Identifier (x)
        │       └── Identifier (y)
        └── End
```
### 表达式语句(ExpressionStatement)
在 JavaScript 中，表达式语句是指一种语句，其中包含表达式，并且在执行表达式时不会返回任何值。表达式语句通常用于对变量进行赋值、调用函数或执行其他操作。
注意：表达式和表达式语句是有区别的。
表达式是指一段代码，它可以被计算出一个值。表达式可以用于赋值、函数调用或其他操作。
```js
10 + 20
'Hello, World!'
{ x: 10, y: 20 }
function(x, y) { return x + y }
```
表达式语句是指一种语句，其中包含表达式，并且在执行表达式时不会返回任何值。表达式语句通常用于对变量进行赋值、调用函数或执行其他操作。
例如，下面是一些表达式语句的例子
```js
x = 10;
y = x + 20;
console.log('Hello, World!');
add(10, 20);
return x + y;
```

常见纠结问题：赋值语句会返回值，还是表达式语句吗？
在 JavaScript 中，赋值表达式会返回赋值后的值。但是，当赋值表达式作为一个独立的语句时，它就是一个表达式语句。
例如，下面的代码中的赋值表达式会返回赋值后的值：
```js
let x = 10;
let y = (x = 20);

console.log(y); // 20
```
但是，如果将赋值表达式放在独立的语句中，它就是一个表达式语句，在执行时不会返回任何值：
```js
let x = 10;
x = 20;
```
#### 赋值语句
```js
x = 456;
```
对应的 AST 结构如下：
```
ExpressionStatement
    └── AssignmentExpression
        ├── Identifier (x)
        └── NumericLiteral (456)
```
#### 函数调用语句
```
console.log(x);
```
对应的 AST 结构如下：
```
ExpressionStatement
└── CallExpression
    ├── MemberExpression
    │   ├── Identifier (console)
    │   └── Identifier (log)
    └── Identifier (x)
```
#### return 语句
```js
return x + y;
```
```
ReturnStatement
└── Binary Expression
    ├── Identifier (x)
    └── Identifier (y)
```
#### throw 语句
```js
throw new Error('Something went wrong');
```
```
ThrowStatement
└── NewExpression
    ├── Identifier (Error)
    └── StringLiteral ('Something went wrong')
```

### 控制语句
在 JavaScript 中，控制语句是指用于控制程序流程的语句。控制语句可以用于执行不同的操作、跳转到不同的位置或做出决策。
#### if 语句(IfStatement)
```js
if (x > 100) {
  console.log('x is larger than 100');
}
```
对应的 AST 结构如下：
```
IfStatement
├── BinaryExpression
│   ├── Identifier (x)
│   ├── >
│   └── NumericLiteral (100)
├── BlockStatement
│   └── Expression Statement
│       └── CallExpression
│           ├── MemberExpression
│           │   ├── Identifier (console)
│           │   └── Identifier (log)
│           └── StringLiteral ('x is larger than 100')
└── Null
```

####  switch 语句
```js
switch (x) {
  case 10:
    console.log('x is 10');
    break;
  case 20:
    console.log('x is 20');
    break;
  default:
    console.log('x is neither 10 nor 20');
}
```
对应的 AST
```
SwitchStatement
├── Identifier (x)
├── SwitchCase
│   ├── NumericLiteral (10)
│   └── ExpressionStatement
│       └── CallExpression
│           ├── MemberExpression
│           │   ├── Identifier (console)
│           │   └── Identifier (log)
│           └── StringLiteral ('x is 10')
├── SwitchCase
│   ├── NumericLiteral (20)
│   └── ExpressionStatement
│       └── CallExpression
│           ├── MemberExpression
│           │   ├── Identifier (console)
│           │   └── Identifier (log)
│           └── StringLiteral ('x is 20')
└── SwitchCase
    ├── null
    └── ExpressionStatement
        └── CallExpression
            ├── MemberExpression
            │   ├── Identifier (console)
            │   └── Identifier (log)
            └── StringLiteral ('x is neither 10 nor 20')
```

####  while 循环
```js
let i = 0;
while (i < 10) {
  console.log(i);
  i++;
}
```
对应的AST
```
While Statement
├── Binary Expression
│   ├── Identifier (i)
│   ├── <
│   └── NumericLiteral (10)
└── Block Statement
    ├── Expression Statement
    │   └── Call Expression
    │       ├── Member Expression
    │       │   ├── Identifier (console)
    │       │   └── Identifier (log)
    │       └── Identifier (i)
    └── Expression Statement
        └── Update Expression
            ├── Identifier (i)
            └── ++
```

#### for 循环

```js
for (let i = 0; i < 10; i++) {
  console.log(i);
}
```

对应的 AST 结构如下：
```
ForStatement
├── VariableDeclaration
│   ├── VariableDeclarator
│   │   ├── Identifier (i)
│   │   └── NumericLiteral (0)
│   └── ;
├── BinaryExpression
│   ├── Identifier (i)
│   ├── <
│   └── NumericLiteral (10)
├── UpdateExpression
│   ├── Identifier (i)
│   ├── ++
│   └── true
└── BlockStatement
    └── ExpressionStatement
        └── CallExpression
            ├── MemberExpression
            │   ├── Identifier (console)
            │   └── Identifier (log)
            └── Identifier (i)       
```

#### while循环
```js
while (x > 0) {
  console.log(x);
  x--;
}
```

对应的 AST 结构如下：
```
WhileStatement
├── BinaryExpression
│   ├── Identifier (x)
│   ├── >
│   └── NumericLiteral (0)
└── Block Statement
    ├── ExpressionStatement
    │   └── CallExpression
    │       ├── MemberExpression
    │       │   ├── Identifier (console)
    │       │   └── Identifier (log)
    │       └── Identifier (x)
    └── Expression Statement
        └── UpdateExpression
            ├── Identifier (x)
            ├── --
            └── true
```

####  do-while 循环
```js
let i = 0;
do {
  console.log(i);
  i++;
} while (i < 10);
```

AST:
```
DoWhile Statement
├── Block Statement
│   ├── Expression Statement
│   │   └── Call Expression
│   │       ├── Member Expression
│   │       │   ├── Identifier (console)
│   │       │   └── Identifier (log)
│   │       └── Identifier (i)
│   └── Expression Statement
│       └── Update Expression
│           ├── Identifier (i)
│           └── ++
└── Binary Expression
    ├── Identifier (i)
    ├── <
    └── NumericLiteral (10)
```

#### for-in 循环
```js
const obj = {x: 10, y: 20};
for (const key in obj) {
  console.log(key, obj[key]);
}
```
AST:
```
ForIn Statement
├── Variable Declaration
│   ├── Identifier (key)
│   └── Object Expression
│       ├── Property
│       │   ├── Identifier (x)
│       │   └── NumericLiteral (10)
│       └── Property
│           ├── Identifier (y)
│           └── NumericLiteral (20)
└── Block Statement
    ├── Expression Statement
    │   └── Call Expression
    │       ├── Member Expression
    │       │   ├── Identifier (console)
    │       │   └── Identifier (log)
    │       ├── Identifier (key)
    │       └── Member Expression
    │           ├── Identifier (obj)
    │           └── Identifier (key)
```

#### for-of 循环
```js
const arr = [1, 2, 3];
for (const value of arr) {
  console.log(value);
}
```
```
ForOf Statement
├── Variable Declaration
│   ├── Identifier (value)
│   └── Array Expression
│       ├── NumericLiteral (1)
│       ├── NumericLiteral (2)
│       └── NumericLiteral (3)
└── Block Statement
    └── Expression Statement
        └── Call Expression
            ├── Member Expression
            │   ├── Identifier (console)
            │   └── Identifier (log)
            └── Identifier (value)

```
AST:

## 字面量
字面量是指直接在程序中写出来的常量值，JavaScript 支持五种基本的字面量：数字、字符串、布尔值、对象和数组。

对应在 JavaScript 抽象语法树（AST）中，这些字面量会用到以下几种节点：

-   数字字面量对应 `NumericLiteral` 节点，表示一个数字字面量。
-   字符串字面量对应 `StringLiteral` 节点，表示一个字符串字面量。
-   布尔值字面量对应 `BooleanLiteral` 节点，表示一个布尔值字面量。
-   对象字面量对应 `ObjectExpression` 节点，表示一个对象字面量。对象字面量中的每个属性对应一个 `Property` 节点。
-   数组字面量对应 `ArrayExpression` 节点，表示一个数组字面量。数组字面量中的每个元素对应一个 `ArrayExpression` 的子节点。
例如，以下代码：
```js
const x = 123;
const y = 'hello';
const z = true;
const obj = { a: 1, b: 2 };
const arr = [1, 2, 3];
```
对应的 AST 可能长这样：
```
Program
├── VariableDeclaration
│   ├── VariableDeclarator
│   │   ├── Identifier (x)
│   │   └── NumericLiteral (123)
│   └── ;
├── VariableDeclaration
│   ├── VariableDeclarator
│   │   ├── Identifier (y)
│   │   └── StringLiteral ('hello')
│   └── ;
├── VariableDeclaration
│   ├── VariableDeclarator
│   │   ├── Identifier (z)
│   │   └── BooleanLiteral (true)
│   └── ;
├── VariableDeclaration
│   ├── VariableDeclarator
│   │   ├── Identifier (obj)
│   │   └── ObjectExpression
│   │       ├── Property
│   │       │   ├── Identifier (a)
│   │       │   └── NumericLiteral (1)
│   │       └── Property
│   │           ├── Identifier (b)
│   │           └── NumericLiteral (2)
│   └── ;
└── VariableDeclaration
    ├── VariableDeclarator
    │   ├── Identifier (arr)
    │   └── ArrayExpression
    │       ├── NumericLiteral (1)
    │       ├── NumericLiteral (2)
    │       └── NumericLiteral (3)
    └── ;
```

##  标识符 Identifier 

在上面的例子中也可以看到不少 Identifier, 标识符也是最小的语法单元之一。
在 JavaScript 中，标识符是指变量、常量、函数名或属性名。标识符是由一个或多个字母、数字或下划线组成的，且必须以字母或下划线开头。

标识符的作用是在程序中标记变量、常量、函数或属性的名字，便于程序中的变量、常量、函数或属性能够被引用和使用。

例如，以下代码中的 `x`、`y` 和 `add` 都是标识符：

```js
let x = 123;
const y = 'hello';

function add(a, b) {
  return a + b;
}

console.log(add(x, y));
```

JavaScript 的标识符遵循一些命名规则，例如：

-   不能使用关键字作为标识符名。
-   不能使用保留字作为标识符名。
-   不能使用特殊字符作为标识符名。

标识符在 JavaScript 抽象语法树（AST）中对应的节点为 `Identifier`。
例如，在以下代码的 AST 中：
```js
const x = 123;
```

变量 `x` 对应的节点是：
```
VariableDeclaration
└── VariableDeclarator
    ├── Identifier (x)
    └── NumericLiteral (123)
```

##  对 AST 进行处理

在生成 AST 以后就可以对 AST 进行处理

- 分析 AST 结构进行规范性检查 -> lint 工具的本质 
- 从 AST 中抽取注释 -> 文档自动生成工具
- 分析类型信息 -> 类型工具
- 直接执行语句 -> js 解释器

## 总结
AST 中的每个节点都对应 javascript 中的一个语法结构。 也就是说将源码转为 AST 以后我们就可以方便地对特定的语法结构进行操作。
##  链接
[AST explorer](https://astexplorer.net/) 是探索 AST 的好地方。
