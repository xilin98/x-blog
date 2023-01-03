---
date:  2023-01-03
title: 深度优先遍历(DFS)和广度优先遍历(BFS)
tags: 
  - algorithm
categories:
  - algorithm
---

深度优先遍历(Depth-First Search, DFS)是按照树的深度遍历树的节点，优先遍历节点的子树。

广度优先遍历(Breadth-First Search, BFS)是按照树的宽度遍历树的节点，优先遍历节点的同层节点。

下面是用 JavaScript 实现的 DFS 和 BFS 算法的例子。这里我们假设我们有一棵树，树上的每个节点都是一个 JavaScript 对象，有一个 `value` 属性表示节点的值，一个 `children` 属性表示节点的子节点数组。
```js
const tree = {
  value: 1,
  children: [
    {
      value: 2,
      children: [
        { value: 4, children: [] },
        { value: 5, children: [] }
      ]
    },
    {
      value: 3,
      children: [
        { value: 6, children: [] },
        { value: 7, children: [] }
      ]
    }
  ]
};
```
下面是 DFS 的实现：
```js
function DFS(tree) {
  console.log(tree.value);
  for (const child of tree.children) {
    DFS(child);
  }
}

DFS(tree);
```
输出结果为：
```shell
1
2
4
5
3
6
7
```
下面是 BFS 的实现：
```js
function BFS(tree) {
  const queue = [tree];
  while (queue.length > 0) {
    const node = queue.shift();
    console.log(node.value);
    queue.push(...node.children);
  }
}

BFS(tree);
```
输出结果为：
```shell
1
2
3
4
5
6
7
```