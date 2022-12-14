---
date:  2022-12-20
title: monorepo
tags: 
categories:
---

## 什么是 monorepo
Monorepo 是一种版本控制和软件开发方式，其中所有项目都存储在同一个代码仓库中。这意味着，所有的项目都共享相同的版本控制系统（例如 Git），并且代码、文档和资源都存储在同一个位置。

## monorepo 的优点和缺点
Monorepo 的优点包括：

-   减少了代码库管理的复杂度：因为所有项目都在同一个仓库中，所以无需管理多个代码仓库，便于统一管理。
-   便于代码共享：因为所有项目都在同一个仓库中，所以可以轻松地在项目之间共享代码，减少代码冗余。
-   提高了代码的一致性：因为所有项目都在同一个仓库中，所以可以方便地维护代码的一致性。

Monorepo 的缺点包括：

-   增加了代码库的大小：因为所有项目都在同一个仓库中，所以代码库的大小会变得更大，可能会增加代码管理的复杂度。
-   增加了代码冲突的风险：因为所有项目都在同一个仓库中，所以在多人协作开发时可能会出现冲突，需要更多的协调和沟通。

## 使用 pnpm 建立一个 monorepo
```shell
pnpm create monorepo
```

执行这个命令会在当前目录中创建一个名为 `package.json` 的文件，并将其中的 `private` 字段设置为 `true`。这样可以确保 monorepo 中的包不会被发布到 npm 上。
