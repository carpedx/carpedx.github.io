---
layout: post
title: JavaScript代码的各种骚操作
categories: javascript
description: JavaScript代码的各种骚操作
keywords: js, javascript
---



#### 1. 使用左移运算符 `<<` 迅速得出2的次方

```javascript
1 << 2;  // 4, 即 2的2次方
1 << 10;  // 1024, 即 2的10次方
```



#### 2. 使用 `^` 切换变量 0 或 1

变量toggle如果为1则变为0，如果为0则变为1

```javascript
// toggle从1变为0
let toggle = 1;
toggle ^= 1;  // 0 并赋值给toggle

// toggle从0变为1
let toggle = 0;
toggle ^= 1;  // 1 并赋值给toggle
```



#### 3. 使用 `&` 判断奇偶性

1为奇数，0为偶数

```javascript
console.log(7 & 1);  // 1 奇数
console.log(8 & 1);  // 0 偶数
```



#### 4. 使用 `!!` 将数字转为布尔值

所有非0的值都是true，包括负数、浮点数
```javascript
console.log(!!7);  // true
console.log(!!0);  // false
console.log(!!-1);  // true
console.log(!!0.71);  // true
```



#### 5. 使用 `~、>>、<<、>>>、|` 来取整

相当于使用了Math.floor()
```javascript
console.log(~~11.71);  // 11
console.log(11.71 >> 0);  // 11
console.log(11.71 << 0);  // 11
console.log(11.71 | 0);  // 11
console.log(11.71 >>> 0);  // 11 不可对负数取整
```



#### 6. 使用 `^` 来完成值交换

这个符号的用法前面提到过，下面介绍一些高级的用法，在 ES6 的解构赋值出来之前，用这种方式会更快(但必须是整数)：
```javascript
let a = 1;
let b = 7;
a ^= b;
b ^= a;
a ^= b;
console.log(a);  // 7
console.log(b);  // 1
```



#### 7. 使用 `^` 判断是否都是正数还是负数

```javascript
let x = -1;
let y = 11;
(x ^ y) >= 0;  // false 不相同，因为一个是负数一个是正数

let x = 2;
let y = 11;
(x ^ y) >= 0;  // true 相同，因为两个都是正数
```



#### 8. 使用 `^` 来检查数字是否不相等
```javascript
let a = 1171;
if (a ^ 1171) {
  console.log('1171');
}
if (a ^ 7777) {
  console.log('7777');  // 7777 不相等
}
```



#### 9. `n & (n - 1)` 如果为 0，说明 n 是 2 的整数幂

2的整数幂：1，2，4，8，16，32，64......

```javascript
let n = 24;
n & (n - 1);	// 16

let n = 64;
n & (n - 1);	// 0
```



#### 10. 使用 `+ 0.5 | 0` 来替代 `Math.round()`

```javascript
let a = 24.7;
console.log(Math.round(a));	// 25
console.log(a + 0.5 | 0);	// 25

let a = 24.1;
console.log(Math.round(a));	// 24
console.log(a + 0.5 | 0);	// 24
```
如果是负数，只需要 `- 0.5 | 0`
```javascript
let a = -24.7;
console.log(Math.round(a));	// -25
console.log(a - 0.5 | 0);	// -25
```


#### 11. 使用 `toString(16)` 取随机字符串
最多可取13位（数字和字母组成）
```javascript
Math.random().toString(16).substring(2, 15);
```



#### 12. 使用 `link()` 创建链接

```javascript
let b = `<a herf="www.google.com">google</a>`;
可以改为：
let b = 'google'.link('www.google.com');
```



#### 13. 复制重复字符

```javascript
for (let a = "", i = 7; i--;) {
  a += 'a';  // aaaaaaa
}

let b = Array(7).join('a');  // aaaaaaa

// ES6
let d = "a".repeat(7);  // aaaaaaa
```


#### 14. 使用当前时间创建一个随机数

不要在快速循环中使用，因为毫秒可能没有变化

```javascript
let a = (new Date()).getTime() % 100; // 两位随机数
let a = (new Date()).getTime() % 1000; // 三位随机数
let a = (new Date()).getTime() % 10000; // 四位随机数...依次类推
```



#### 15. 一些可以替代 undefined 的操作

```javascript
let a = ''._;  // undefined
let b = 1.._;  // undefined
let c = 0[0];  // undefined
```
void 0 会比写undefined要快一些
```javascript
let d = void 0;  // undefined
```



#### 16. 使用 1/0 来替代 Infinity

```javascript
let a = [1/0, -1/0];  // [Infinity, -Infinity]
```



#### 17. 使用 `Array.length = 0` 来清空数组

```javascript
let arr = [0,1,2,3,4,5];  // [0,1,2,3,4,5]
arr.length = 0;	// []
```



#### 18. 使用 `Array.slice(0)` 实现数组浅拷贝

```javascript
let arr = [0,1,2,3,4,5];
let b = arr.slice(0);
```



#### 19. 使用 `!+"1"` 快速判断 IE8 以下的浏览器

```javascript
let isIE8 = !+"\v1";  // false
```



#### 20. for 循环条件的简写

```javascript
for(let i = 0; i < arr.length; i++) {...}
可以简写为：
for(let i = arr.length; i--;) {...}  // 注意 i-- 后面的分号别漏了
```
