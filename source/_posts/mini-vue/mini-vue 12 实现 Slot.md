---
date: 2022/12/19
title: mini-vue 12 实现 Slot
tags: 
  - mini-vue
categories:
  - mini-vue
---

## Slots 为何而在
有这样一种场景，就是说我们需要在一个模板中插入自己定制话的内容。比方说在一个弹窗中加入定制化的标题。
抽象一下，就是说在一个组件中加入来自外部的渲染逻辑。

## 设计实现
类似于 HTML `<div> 内容 </div>` 
只不过这里把 `div` 换成了组件而已。

例如我们这里有一个 Foo 组件 并且有 `I'm Slots` 字符串作为它的 Slots
在 template 表示如下：
```html
<Foo>I'm Slots</Foo>
```

编译成渲染函数以渲染函数的
```js
const Foo = {
  render() {
    return h("div", {}, 'foo');
  },
...
};

const FooWithSlots= {
  render(){
    return h(Foo, {}, "I'm a Slots")
  }
...
}
```

为了在 Foo 组件中正确地显示 Slots，我们希望可以以 `this.$slots` 方式拿到传入的 slots。
我们在作以下处理：
首先我们在新建组件实例的时候新建一个空的 slots。
📁 component.ts
```js
export function createComponentInstance(vnode) {
  const instance = {
...
    slots: {},
...
  };
...
  return instance;
}
```

📁 componentPublicInstance.ts
```js
const publicPropsMap = {
...
  $slots: (i) => i.slots, 
...
};
```

然后我们就可以在 Foo 组件中通过 this.$slot 拿到传入的 slots。


