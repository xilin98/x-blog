---
date:  2023-01-06
title: 从0到1实现前端埋点 SDK
tags: 
  - 前端工程化
categories:
  - 前端工程化
  - 前端埋点
---

## 什么是前端埋点

前端埋点是指在网页的前端（即客户端）放置的记录用户行为的代码。通常，这些代码会将用户的操作记录下来，然后通过某种方式传回后端服务器，以便进行分析和统计。前端埋点常常被用来跟踪用户在网站上的行为，以便对网站进行优化并提供更好的用户体验。

要实现一个前端埋点系统，通常需要以下几个步骤：
1. 确定要跟踪哪些用户行为。这可能包括点击按钮、访问特定页面、填写表单等等。
2. 在网页中插入跟踪代码。这些代码通常使用 JavaScript 实现，并且需要在每个需要跟踪的行为发生时触发。
3. 设计后端服务器来接收并处理来自前端的埋点数据。这可能包括将数据存储到数据库中、使用分析工具对数据进行汇总和可视化等等。
4. 使用工具来可视化和分析埋点数据。这可能包括使用网络分析工具、数据可视化工具等来帮助您理解用户的行为模式。

## 技术选型

ts + rollup
- ts 可以提供更好的代码提示 
- rollup 配置比较干净和简洁，相比较 webpack 更适合开发工具类的库或框架，比方说 vue3 就是使用 rollup 打包的。

下面是 Rollup 和 Webpack 的一些主要区别：
- Rollup 是一个模块打包器，而 Webpack 是一个模块打包工具。这意味着 Webpack 支持打包更多类型的文件，如图像和字体，以及更多的加载器（loaders）。
- Rollup 具有更快的打包速度，因为它是专注于打包 JavaScript 模块的。Webpack 打包速度可能较慢，因为它可能需要处理更多类型的文件。
- Rollup 使用树摇晃（tree-shaking）机制来删除未使用的代码。Webpack 也支持树摇晃，但需要使用 UglifyJS 插件。
- Rollup 具有较少的配置选项，因此它可能更容易使用。Webpack 具有更多的配置选项，但也提供了更多的灵活性。

## 项目结构

```
src/
--core/
--types/
--utils/
rollup.config.js
tsconfig.json
```

## 初始化项目
```shell
npm init -y
```

因为 rollup 使用 esm, 所以在 package.json 中加上
`"type": module`

## 安装相关依赖

```bash
npm install rollup -D
npm install rollup-plugin-dts -D
npm install rollup-plugin-typescript2 -D
npm install typescript -D
```
其中 rollup-plugin-dts 是一个 Rollup 插件，用于将 TypeScript 声明文件转换为 JavaScript 代码，并生成相应的 `*.d.ts` 文件。这个插件可以帮助你在使用 TypeScript 编写的 JavaScript 库的时候更方便地生成声明文件。

## 配置 rollup 
📄 rollup.config.js
```js
import ts from "rollup-plugin-typescript2"
import dts from "rollup-plugin-dts"
export default [
  {
    //入口文件 相当于 webpack 里的 entry 选项
    input: "./src/core/index.ts",
    output: [
      //打包esModule
      {
        file: "./dist/index.esm.js",
        format: "es",
      },
      //打包common js
      {
        file: "./dist/index.cjs.js"
        format: "cjs",
      },
      //打包 AMD CMD UMD
      {
        file: "./dist/index.js",
        format: "umd",
      },
    ],
    //配置 ts 插件
    plugins: [ts()],
  },
  {
    //打包声明文件
    input: "./src/core/index.ts",
    output: {
      file: path.resolve(__dirname, "./dist/index.d.ts"),
      format: "es",
    },
    plugins: [dts()],
  },
]
```

## 配置一下 npm script

加上打包命令 `build`: `rollup -c`

## 初始化 Tracker 类

### 定义选项类型
我们给用户提供一个默认选项，并且用户可以传入个性化地配置。

默认选项的类型如下，
📃 /types/index.ts
```ts
export interface DefaultOpitons {
  // 唯一标识符
  uuid?: string,

  // 上报地址
  requestUrl?: string,

  // 是否监听 hash 路由
  hashTracker: boolean,

  // 是否监听 history 路由
  historyTracker: boolean,

  // 是否监听 dom
  domTracker: boolean,

  // 其他配置信息
  extra?: Record<string, any>,

  // 版本号
  sdkVersion: string | number,
} 
```

我们再定义一下可以选择性的配置类型 Options, 它是默认选项的一部分，所以我们通过 `Partial` 来实现：
📃 /types/index.ts
```ts
export interface Opitons extends Partial<DefaultOpitons>{
  requestUrl: string,
}
```

### 定义 Tracker 类
📃 /core/index.ts
```ts
import {Options, DefaultOPtions} from './types'
class Tracker {
  public data: Options;
  constructor(opitons: Options){
    this.data = Object.assign(this.initDef(), options);
  }
  private initDef(): DefaultOptions{
     return <defaultOpitons> {
        hashTracker: false,
        historyTracker: false,
        domTracker: false,
     }
  }
}
```

## 实现 page view

- 使 pushState 和 replaceState 可以被监听,
因为 `addEventListener` 监听不到 History API 中的 pushState 和 replaceState, 可以采用装饰器模式对其重写在 📄utils/pv.ts 里面实现一个
```ts
export function createHistoryEvent<T extends keyof History>(type: T){
    origin = window.history[type];
    return function(this: any){
      const e = new Event(type);
      const window.dispatchEvent(e)
      const res = origin.apply(this, arguments);
      return res
    }
}
```

- 实现监听
在 core/index.ts 中实现监听逻辑

```ts
class Tracker {
  ...
  constructor(){
    ...
    this.installTracker();
  }
  ...
  private installTracker(){
    if(this.data.historyTracker){
      this.captureEvent(["pushState", "replaceState", "popState"], "history-page-view")
    }
    if(this.date.hashTracker){
      this.captureEvent(['hashChange'], "hash-page-view")
    }
  }

  private captureEvent(mouseEventList: string [], targetKey: string, date?: any){
     mouseEventList.forEach(e=>{
       window.addEventListener(e, (e)=>{
          console.log("页面跳转已被监听", e)
       })
     })
  }
}
```

## 实现 unique visitor 

未登陆的访客(一台计算机作为一个访客)如何进行记录呢
解决方案一：
将一个 uuid 存在 localStorage 里

解决方案二：
使用 canvas 指纹追踪技术

这里使用第一种方法，在 Tracker 类中暴露两个公共类来设置 uuid 和 extra 字段

```ts
class Tracker{
  ...
  public  setUserId<T extends DefaultOpitons["uuid"]>(uuid: T){
    this.data["uuid"] =  uuid;
  }

  public setExtra<T extends DefaultOpitons["extra"]>(extra: T){
    this.data["extra"] = extra;
  }
  ...
}
```

## 上报

对监听到的信息进行上报

### XMLHttpRequest vs Navigator.sendBeacon
- Navigator.sendBeacon 方法在页面卸载时发送数据，XMLHttpRequest 可以在任何时间发送数据。
- XMLHttpRequest 可以发送任何类型的 HTTP 请求，而 Navigator.sendBeacon 只能发送 HTTP POST 请求。
- XMLHttpRequest 还可以处理请求和响应的头部信息，而 Navigator.sendBeacon 不能。
- Navigator.sendBeacon 方法可以将数据发送到服务器，即使页面已经卸载了，而XMLHttpRequest 无法保证数据在页面卸载后发送到服务器。
- 浏览器对于XMLHttpRequest的支持度高于Navigator.sendBeacon，所以XMLHttpRequest在兼容性上会更好。
- Navigator.sendBeacon 和 XMLHttpRequest 在发送数据的类型上有所不同。
  Navigator.sendBeacon 只能发送 ArrayBufferView、Blob、USVString 类型的数据。
  XMLHttpRequest 可以发送多种数据类型，包括 ArrayBufferView、Blob、Document、DOMString、FormData、URLSearchParams 等。
  因为 Navigator.sendBeacon 可以在页面关闭后继续发送，所以我们采用 Navigator.sendBeacon

### 实现细节

我们分两层，首先我们实现以下一个私有的方法用来发送数据。
```js
private reportTracker(data){
  navigate.sendBeacon(this.data.reportUrl, data);
}
```


  ``