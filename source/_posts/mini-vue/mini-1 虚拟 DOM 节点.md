---
date:  2022-12-31
title: mini-1 虚拟 DOM 节点
tags: 
  - mini-vue
categories:
  - mini-vue
---

## 虚拟 DOM 节点的好处
把渲染逻辑和真实的 DOM 进行解耦。有以下的几个好处：
- 使得这种渲染逻辑更容易在非浏览器环境中进行重用
  - 渲染为字符串（SSR）
  - 渲染到 canvas 、 webGL 等平台
  - 渲染到原生的移动端平台(IOS 、Android)
- 提供了一种拥有 javascript 全部表现力的方式去操作衍生结构（derivative structure, 例如 DOM） 的方式。（UI 编程（使用模板语法）约束更强，有时候用 javascript 可以更好的表达渲染逻辑）

## Render function
如果我们需要用 render function 表达一个 `<div> hello <span>world</span></div>`, 可以安装下面的代码。
```js
const App = {
  render(){
    return h("div", {}, ["hello", h("div",  {}, "world")])
  }
}
```

条件渲染可以直接通过三元运算符或者条件语句实现：
```js
const app = {
  render(){
    return condition ? h("div", "foo") : h("div", "bar");
  }
}
```

## 何时直接写 Render function 比模板语法方便
### 当我们时候 slots 且逻辑较多的时候
比方说我们需要一个会自动缩进的的元素 `stack`

```js
const Stack = {
  render(){
    const slots = this.$slots.default ? this.$slots.default() : [];
    return h("div",{class: "stack"}, slots.map(child => {
      return h("div", {class: `mt-${this.props.size}`}, child)
    }))
  }
}
```