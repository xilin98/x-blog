---
date:  2023-01-03
title: 编程小技巧（little trick）
tags: 
  - trick
categories:
  - 编程小技巧
---

q: 将未知长度的字符串分成长度为 K 的 n 段，如果字符串不够则补 '0'
a: 直接先补 K 个 '0' 然后开始截取

q: 防止表达式值溢出
a: value = Math.max([expression], limit_value)

q: 区间开闭对应的元素个数
a:
```js
[a, b) (a, b]
// 这个区间共有b - a个数

[a, b] 
// 共有 b - a + 1个数
```

q: 求两个数的平均数且不能溢出
%%防溢出%%
a: a + (b - a) >> 1

q: 求十位，个位
```js
// 十位
n / 10 % 10

// 个位
n % 10
```

q: 不用额外的变量交换两个数
```js
// 以下代码限于二进制
a = a ^ b;
b = a ^ b;
a = a ^ b;

// 最右边的1 
const rightMostOne = a & (1 + ~a)
```

q: 如何在字符串中套变量, 该变量的值是字符串
a: 
```js
// 1
`'${str}'`

// 2
"'" +  str + "'"
```

q: 当用二进制补码(two's compelement)表示负数是怎样的
a:

**Flip the bits and add one** 
Example :  +7 =  0b 0000 0111, -7 =  0b 1111 1001

