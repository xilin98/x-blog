---
title: Babel 流程之始 parse
date: 2022-2-18 3:20:00
tag: 
  - babel
---


## Estree

为了让机器能够理解代码的含义我们把代码转换为了抽象语法树（AST）, AST 的结构是有对应的标准的。在 javascript 中， AST 通常遵循 `estree` 标准.
ESTree（ECMAScript Tree）是一种用于表示 ECMAScript 源代码语法结构的抽象语法树（AST）的标准。ECMAScript 是 JavaScript 编程语言的标准，而 ESTree 标准则定义了如何表示 JavaScript 代码的语法结构，以方便编译器和其他工具对 JavaScript 代码进行处理。

ESTree 标准定义了一系列用于表示源代码结构的节点类型，并且每个节点都有一些属性来表示它所表示的结构的具体信息。例如，对于一个函数声明，ESTree 标准中定义了“FunctionDeclaration”节点来表示这个结构。“FunctionDeclaration”节点中会包含“id”属性表示函数名，“params”属性表示函数的参数，“body”属性表示函数主体等。

ESTree 标准的目的是让 JavaScript 编译器和工具之间能够交换源代码的语法信息，从而提高 JavaScript 代码的可移植性和可复用性。

AST 中的节点类型涵盖了 javaScript 中的所有语法结构
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
