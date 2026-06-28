# JavaScript 参考教程
> **基于 [现代 JavaScript 教程](https://zh.javascript.info/) 与 [MDN Web Docs](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript) 编写**
> 以最新 ECMAScript 标准为基准，从零开始系统掌握 JavaScript 语言本身。

---

# 第一章 JavaScript 简介与开发环境

## 1.1 JavaScript 是什么

JavaScript（简称 JS）最初是为了「让网页活起来」而创造的。如今它已远远超出浏览器范畴，成为全栈通用的编程语言：

| 运行环境 | 说明 |
|----------|------|
| **浏览器** | 操作 DOM、处理用户交互、发起网络请求 |
| **Node.js** | 后端服务、命令行工具、构建系统 |
| **Deno / Bun** | 新一代 JavaScript 运行时 |
| **Electron / Tauri** | 桌面应用 |
| **React Native / 小程序** | 移动端应用 |

## 1.2 语言特性概览

- **动态类型**：变量不需要声明类型，运行时自动确定
- **弱类型**：不同类型之间可以隐式转换
- **基于原型的面向对象**：通过原型链实现继承（也支持 `class` 语法）
- **头等函数**：函数是值，可以赋值给变量、作为参数传递、作为返回值
- **单线程 + 异步**：通过事件循环实现非阻塞并发
- **JIT 编译**：现代引擎（V8、SpiderMonkey）在运行时将 JS 编译为机器码，性能极高

## 1.3 ECMAScript 与版本

JavaScript 的语言标准叫 **ECMAScript**（简称 ES）。重要版本：

| 版本 | 年份 | 关键特性 |
|------|------|----------|
| ES5 | 2009 | `strict mode`、`JSON`、数组方法 |
| **ES6 / ES2015** | 2015 | `let/const`、箭头函数、类、Promise、模块、解构 |
| ES2016 | 2016 | `Array.includes`、`**` 幂运算 |
| ES2017 | 2017 | `async/await`、`Object.entries` |
| ES2020 | 2020 | `?.`可选链、`??`空值合并、`BigInt` |
| ES2022 | 2022 | 顶层 `await`、类私有字段 `#`、`.at()` |
| ES2023 | 2023 | 数组不可变方法 `toSorted()`、`toReversed()` |

ES6 是最大的一次飞跃，此后每年发布一个版本。本教程以最新标准为基准。

## 1.4 开发环境

**浏览器控制台：** 按 `F12` 打开开发者工具，切换到 Console 面板即可直接输入 JavaScript 代码。

**代码编辑器：** VS Code（推荐）、WebStorm。

**在 HTML 中使用 JavaScript：**

```html
<!DOCTYPE html>
<html>
<head>
  <title>JS 示例</title>
</head>
<body>
  <h1>你好</h1>

  <!-- 内联脚本 -->
  <script>
    console.log('Hello, World!');
  </script>

  <!-- 外部脚本（推荐） -->
  <script src="app.js"></script>
</body>
</html>
```

> **`<script>` 的位置：** 放在 `</body>` 之前可以确保 HTML 先加载完毕。也可以在 `<head>` 中使用 `defer` 属性：`<script src="app.js" defer></script>`，效果相同且更规范。

## 1.5 严格模式

在脚本或函数顶部添加 `"use strict"` 可以启用严格模式，它禁止一些不安全的语法，帮助你写出更可靠的代码：

```javascript
"use strict";

// 严格模式下，未声明的变量会报错
x = 10; // ReferenceError: x is not defined
```

> **注意：** ES6 模块（`import/export`）和 `class` 内部自动启用严格模式。现代开发中如果使用模块系统，通常不需要手动添加。

---

# 第二章 变量与数据类型

## 2.1 声明变量：let、const、var

| 关键字 | 作用域 | 可重新赋值 | 可重复声明 | 暂时性死区 | 推荐度 |
|--------|--------|------------|------------|------------|--------|
| `const` | 块级 | 否 | 否 | 有 | ★★★ 首选 |
| `let` | 块级 | 是 | 否 | 有 | ★★ 需要重新赋值时用 |
| `var` | 函数级 | 是 | 是 | 无 | ★ 遗留代码 |

```javascript
const name = '张三';    // 常量，不可重新赋值
let age = 25;           // 变量，可重新赋值
var legacy = 'old';     // 老写法，避免使用

name = '李四';          // ❌ TypeError: Assignment to constant variable
age = 26;               // ✅
```

> **最佳实践：** 默认使用 `const`，只在确实需要重新赋值时才用 `let`，永远不用 `var`。

## 2.2 命名规范

```javascript
// ✅ 合法命名
let userName = 'Alice';     // 驼峰命名（推荐）
let _private = true;        // 下划线开头（约定为私有）
let $element = null;        // $ 开头（jQuery 时代遗留风格）
let MAX_SIZE = 100;         // 全大写 + 下划线（约定为常量）

// ❌ 不合法
let 2name = 'x';            // 不能以数字开头
let my-name = 'x';          // 不能包含连字符
let let = 'x';              // 不能使用保留字
```

## 2.3 八种数据类型

JavaScript 有 **7 种原始类型** 和 **1 种引用类型**：

| 类型 | 分类 | 示例 | 说明 |
|------|------|------|------|
| `number` | 原始 | `42`、`3.14`、`Infinity`、`NaN` | 整数和浮点数（64 位双精度） |
| `bigint` | 原始 | `123456789012345678901234567890n` | 任意精度整数 |
| `string` | 原始 | `'hello'`、`"world"`、`` `模板` `` | 文本 |
| `boolean` | 原始 | `true`、`false` | 逻辑值 |
| `null` | 原始 | `null` | 刻意设置的"空值" |
| `undefined` | 原始 | `undefined` | 未赋值 |
| `symbol` | 原始 | `Symbol('id')` | 唯一标识符 |
| `object` | 引用 | `{}`、`[]`、`function(){}` | 对象、数组、函数、Date 等 |

```javascript
// typeof 运算符检查类型
typeof 42;           // "number"
typeof 'hello';      // "string"
typeof true;         // "boolean"
typeof undefined;    // "undefined"
typeof null;         // "object"   ← 历史遗留 bug，实际是 null
typeof Symbol();     // "symbol"
typeof 123n;         // "bigint"
typeof {};           // "object"
typeof [];           // "object"   ← 数组也是对象
typeof function(){}; // "function" ← 特殊显示，本质还是 object
```

## 2.4 Number

```javascript
// 整数和浮点数都是 number
let int = 42;
let float = 3.14;
let negative = -10;
let scientific = 2.5e6;  // 2500000
let hex = 0xFF;           // 255
let binary = 0b1010;      // 10
let octal = 0o777;        // 511

// 特殊值
Infinity;    // 正无穷
-Infinity;   // 负无穷
NaN;         // Not a Number（计算错误的结果）

// NaN 的特殊性
NaN === NaN;       // false（NaN 不等于任何值，包括自己）
isNaN(NaN);        // true
Number.isNaN(NaN); // true（推荐，更严格）

// 精度问题
0.1 + 0.2;                     // 0.30000000000000004
0.1 + 0.2 === 0.3;             // false!
Math.abs(0.1 + 0.2 - 0.3) < Number.EPSILON; // true（正确的比较方式）

// 数字方法
Number.isFinite(42);    // true
Number.isInteger(42.0); // true
Number.parseInt('42px');     // 42
Number.parseFloat('3.14m'); // 3.14
(255).toString(16);     // "ff"（转为十六进制字符串）
(3.14159).toFixed(2);   // "3.14"（保留两位小数，返回字符串）
```

## 2.5 String

JavaScript 中字符串是**不可变**的 —— 一旦创建就无法修改，所有"修改"操作都会返回新字符串。

```javascript
// 三种写法
let single = '单引号';
let double = "双引号";
let backtick = `反引号 — 模板字符串`;

// 模板字符串（ES6，推荐）
let name = '张三';
let greeting = `你好，${name}！`;              // 嵌入表达式
let multiline = `第一行
第二行
第三行`;                                        // 多行

// 转义字符
'\n';   // 换行
'\t';   // 制表符
'\\';   // 反斜杠
'\'';   // 单引号

// 常用属性和方法
let str = 'Hello, World!';

str.length;              // 13
str[0];                  // "H"（按索引访问）
str.at(-1);              // "!"（支持负索引，ES2022）

// 查找
str.indexOf('World');    // 7（返回位置，找不到返回 -1）
str.includes('World');   // true
str.startsWith('Hello'); // true
str.endsWith('!');       // true

// 截取
str.slice(7, 12);        // "World"（起始, 结束）
str.slice(-6);           // "orld!"（支持负索引）

// 转换
str.toUpperCase();       // "HELLO, WORLD!"
str.toLowerCase();       // "hello, world!"
str.trim();              // 去除首尾空白
str.trimStart();         // 去除开头空白
str.trimEnd();           // 去除结尾空白
str.repeat(3);           // 重复 3 次
str.padStart(20, '-');   // 左侧填充到 20 位
str.padEnd(20, '-');     // 右侧填充到 20 位

// 替换
str.replace('World', 'JS');     // "Hello, JS!"（替换第一个）
str.replaceAll('l', 'L');       // "HeLLo, WorLd!"（替换全部）

// 分割
'a,b,c'.split(',');      // ["a", "b", "c"]
```

## 2.6 Boolean

```javascript
let isActive = true;
let isDeleted = false;

// 以下值在布尔上下文中为 false（falsy 值）：
// false, 0, -0, 0n, "", null, undefined, NaN

// 其他所有值都为 true（truthy），包括：
// "0", " ", [], {}, function(){} — 这些都是 truthy！

Boolean(0);        // false
Boolean('');       // false
Boolean(null);     // false
Boolean('0');      // true（非空字符串）
Boolean([]);       // true（空数组也是 truthy）
```

## 2.7 null 与 undefined

```javascript
// undefined — "未赋值"
let x;
console.log(x);   // undefined（已声明但未赋值）

// null — "刻意设置的空值"
let user = null;   // 明确表示"此处没有值"

// 区别
typeof undefined;  // "undefined"
typeof null;       // "object"（历史 bug）
null == undefined; // true（宽松相等）
null === undefined;// false（严格相等）
```

## 2.8 Symbol（简介）

`Symbol` 是 ES6 引入的原始类型，每个 Symbol 都是**唯一的**，主要用作对象的属性键以避免命名冲突：

```javascript
const id = Symbol('id');
const anotherId = Symbol('id');
id === anotherId;  // false（即使描述相同，每个 Symbol 也是唯一的）

let user = {
  name: '张三',
  [id]: 42       // Symbol 属性
};
user[id];         // 42
```

> Symbol 属性不会出现在 `for...in` 循环和 `Object.keys()` 中，具有一定的"隐藏性"。

---

# 第三章 运算符与类型转换

## 3.1 算术运算符

```javascript
// 基本运算
5 + 3;     // 8（加）
5 - 3;     // 2（减）
5 * 3;     // 15（乘）
5 / 3;     // 1.6666...（除，结果为浮点数）
5 % 3;     // 2（取余）
5 ** 3;    // 125（幂运算，ES2016）

// + 的特殊行为：字符串拼接
'Hello' + ' ' + 'World';  // "Hello World"
'5' + 3;                   // "53"（字符串 + 数字 = 字符串）
5 + '3';                   // "53"
5 + 3 + '1';               // "81"（先算 5+3=8，再 "8"+"1"）
'1' + 5 + 3;               // "153"（从左到右，一旦遇到字符串就拼接）

// 一元 + 可以将值转为数字
+'42';     // 42
+true;     // 1
+false;    // 0
+'';       // 0
+'hello';  // NaN

// 自增/自减
let a = 5;
a++;       // 后置：返回 5，然后 a 变为 6
++a;       // 前置：a 先变为 7，然后返回 7
a--;       // 后置
--a;       // 前置

// 赋值运算符
let b = 10;
b += 5;    // b = b + 5 → 15
b -= 3;    // b = b - 3 → 12
b *= 2;    // b = b * 2 → 24
b /= 4;    // b = b / 4 → 6
b %= 4;    // b = b % 4 → 2
b **= 3;   // b = b ** 3 → 8
```

## 3.2 比较运算符

```javascript
// 值比较
5 > 3;      // true
5 < 3;      // false
5 >= 5;     // true
5 <= 4;     // false

// 相等比较 — 两种方式
// == 宽松相等（会做类型转换）
5 == '5';      // true
0 == false;    // true
'' == false;   // true
null == undefined; // true
null == 0;     // false（null 只与 undefined 宽松相等）

// === 严格相等（推荐，不做类型转换）
5 === '5';     // false
0 === false;   // false
null === undefined; // false

// 不等
5 != '5';      // false（宽松）
5 !== '5';     // true（严格，推荐）
```

> **最佳实践：** 始终使用 `===` 和 `!==` 进行比较。`==` 的隐式转换规则过于复杂，容易引发 bug。

## 3.3 逻辑运算符

```javascript
// && 与（返回第一个 falsy 值，或最后一个值）
true && true;       // true
true && false;      // false
'hello' && 42;      // 42（都是 truthy，返回最后一个）
'' && 42;           // ""（第一个是 falsy，直接返回）

// || 或（返回第一个 truthy 值，或最后一个值）
false || true;      // true
'' || 'default';    // "default"（常用于设置默认值）
0 || 42;            // 42

// ?? 空值合并运算符（ES2020，仅当左侧为 null/undefined 时返回右侧）
null ?? 'default';      // "default"
undefined ?? 'default'; // "default"
0 ?? 'default';         // 0（0 不是 null/undefined）
'' ?? 'default';        // ""（空字符串不是 null/undefined）

// || 与 ?? 的关键区别
0 || 100;   // 100 — 因为 0 是 falsy
0 ?? 100;   // 0   — 因为 0 不是 null/undefined

// ! 非
!true;      // false
!0;         // true
!!'hello';  // true（双重取反，转为布尔值）
```

## 3.4 可选链 ?.

当对象可能不存在时，安全地访问深层属性（ES2020）：

```javascript
let user = {
  name: '张三',
  address: {
    city: '北京'
  }
};

// 不使用可选链 — 需要层层判断
let street = user && user.address && user.address.street;

// 使用可选链 — 简洁安全
let street = user?.address?.street;  // undefined（不会报错）
let city = user?.address?.city;      // "北京"

// 可选链调用方法
user.greet?.();  // 如果 greet 方法存在则调用，否则返回 undefined

// 可选链访问数组元素
let arr = null;
arr?.[0];        // undefined（不会报错）
```

## 3.5 类型转换

```javascript
// 转为字符串
String(42);        // "42"
String(true);      // "true"
String(null);      // "null"
String(undefined); // "undefined"
(42).toString();   // "42"

// 转为数字
Number('42');      // 42
Number('');        // 0
Number(' ');       // 0
Number(true);      // 1
Number(false);     // 0
Number(null);      // 0
Number(undefined); // NaN
Number('hello');   // NaN
parseInt('100px'); // 100（解析到非数字字符为止）
parseFloat('3.14em'); // 3.14

// 转为布尔值
Boolean(1);        // true
Boolean(0);        // false
Boolean('hello');  // true
Boolean('');       // false
// 记住 6 个 falsy 值：false, 0, -0, 0n, "", null, undefined, NaN
```

## 3.6 其他运算符

```javascript
// 三元运算符（条件运算符）
let result = age >= 18 ? '成年' : '未成年';

// 逗号运算符（从左到右执行，返回最后一个值）
let a = (1 + 2, 3 + 4);  // a = 7

// typeof
typeof 42;         // "number"
typeof 'hello';    // "string"

// void（执行表达式但返回 undefined）
void 0;            // undefined
```

---

# 第四章 流程控制

## 4.1 条件语句：if...else

```javascript
let hour = 14;

if (hour < 12) {
  console.log('上午好');
} else if (hour < 18) {
  console.log('下午好');
} else {
  console.log('晚上好');
}
```

## 4.2 switch

`switch` 使用**严格相等** `===` 进行比较：

```javascript
let fruit = 'apple';

switch (fruit) {
  case 'apple':
    console.log('苹果');
    break;
  case 'banana':
  case 'orange':          // 多个 case 共享同一代码块
    console.log('香蕉或橘子');
    break;
  default:
    console.log('未知水果');
}
```

> **别忘了 `break`！** 没有 `break` 会"穿透"到下一个 case 继续执行。

## 4.3 while 与 do...while

```javascript
// while — 先判断再执行
let i = 0;
while (i < 5) {
  console.log(i);
  i++;
}

// do...while — 先执行再判断（至少执行一次）
let j = 0;
do {
  console.log(j);
  j++;
} while (j < 5);
```

## 4.4 for 循环

```javascript
// 经典 for
for (let i = 0; i < 5; i++) {
  console.log(i); // 0, 1, 2, 3, 4
}

// for...of — 遍历可迭代对象的值（数组、字符串、Map、Set 等）
let fruits = ['苹果', '香蕉', '橘子'];
for (let fruit of fruits) {
  console.log(fruit);
}

for (let char of 'Hello') {
  console.log(char); // H, e, l, l, o
}

// for...in — 遍历对象的可枚举属性键
let user = { name: '张三', age: 25 };
for (let key in user) {
  console.log(`${key}: ${user[key]}`);
}
```

> **for...of vs for...in：** `for...of` 遍历值，适合数组和可迭代对象；`for...in` 遍历键，适合对象。不要对数组使用 `for...in`，因为它还会遍历原型链上的属性，且顺序不可靠。

## 4.5 break、continue 与标签

```javascript
// break — 中断循环
for (let i = 0; i < 10; i++) {
  if (i === 5) break;
  console.log(i); // 0, 1, 2, 3, 4
}

// continue — 跳过当前迭代
for (let i = 0; i < 10; i++) {
  if (i % 2 === 0) continue;
  console.log(i); // 1, 3, 5, 7, 9
}

// 标签 — 用于跳出嵌套循环
outer: for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 3; j++) {
    if (i === 1 && j === 1) break outer;
    console.log(i, j);
  }
}
```

---

# 第五章 函数

## 5.1 函数声明

```javascript
function greet(name) {
  return `你好，${name}！`;
}

greet('张三'); // "你好，张三！"
```

**函数声明会被提升（hoisting）** —— 可以在声明之前调用：

```javascript
sayHi(); // ✅ 正常工作
function sayHi() {
  console.log('Hi!');
}
```

## 5.2 函数表达式

```javascript
const greet = function(name) {
  return `你好，${name}！`;
};

greet('李四'); // "你好，李四！"
```

函数表达式**不会被提升**：

```javascript
sayHi(); // ❌ ReferenceError
const sayHi = function() {
  console.log('Hi!');
};
```

## 5.3 箭头函数（ES6）

```javascript
// 完整写法
const add = (a, b) => {
  return a + b;
};

// 单表达式可省略 {} 和 return
const add = (a, b) => a + b;

// 单参数可省略 ()
const double = n => n * 2;

// 无参数必须写 ()
const greet = () => 'Hello!';

// 返回对象字面量需要用 () 包裹
const makeUser = (name, age) => ({ name, age });
```

**箭头函数 vs 普通函数的关键区别：**

| 区别 | 普通函数 | 箭头函数 |
|------|----------|----------|
| `this` | 调用时确定 | 继承外层作用域的 `this` |
| `arguments` | 有 | 没有 |
| `new` 调用 | 可以 | 不可以 |
| `prototype` | 有 | 没有 |

## 5.4 参数

```javascript
// 默认参数（ES6）
function greet(name = '访客', greeting = '你好') {
  return `${greeting}，${name}！`;
}
greet();              // "你好，访客！"
greet('张三');        // "你好，张三！"
greet('张三', '早上好'); // "早上好，张三！"

// 剩余参数（Rest parameters，ES6）
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
sum(1, 2, 3, 4);   // 10

// rest 必须是最后一个参数
function log(prefix, ...messages) {
  messages.forEach(msg => console.log(`[${prefix}] ${msg}`));
}
```

## 5.5 返回值

```javascript
// 函数没有 return 或 return 后没有值，返回 undefined
function doNothing() {}
doNothing(); // undefined

function earlyReturn() {
  return; // 返回 undefined
}

// 可以返回任何类型的值
function getUser() {
  return { name: '张三', age: 25 };
}
```

## 5.6 回调函数

函数作为参数传递给另一个函数：

```javascript
function ask(question, yes, no) {
  if (confirm(question)) {
    yes();
  } else {
    no();
  }
}

ask(
  '你同意吗？',
  () => console.log('同意了'),
  () => console.log('拒绝了')
);
```

## 5.7 立即调用函数表达式（IIFE）

```javascript
// 定义后立即执行（在 ES6 之前用于创建作用域，现在很少用）
(function() {
  let message = '私有变量';
  console.log(message);
})();
```

---

# 第六章 对象基础

## 6.1 创建对象

```javascript
// 对象字面量（推荐）
let user = {
  name: '张三',
  age: 25,
  isAdmin: false,
};

// 构造函数
let user2 = new Object();
user2.name = '李四';
```

## 6.2 访问属性

```javascript
// 点运算符
user.name;     // "张三"

// 方括号运算符（可以使用变量和任意字符串作为键）
user['name'];  // "张三"
let key = 'age';
user[key];     // 25

// 访问不存在的属性返回 undefined
user.email;    // undefined
```

## 6.3 添加、修改与删除属性

```javascript
user.email = 'zhangsan@example.com';  // 添加
user.age = 26;                         // 修改
delete user.isAdmin;                   // 删除
```

## 6.4 简写与计算属性

```javascript
// 属性值简写（变量名和属性名相同时）
let name = '张三';
let age = 25;
let user = { name, age };  // 等同于 { name: name, age: age }

// 方法简写
let user = {
  name: '张三',
  greet() {                // 等同于 greet: function() {}
    return `我是 ${this.name}`;
  }
};

// 计算属性名
let prop = 'name';
let user = {
  [prop]: '张三',          // 等同于 name: '张三'
  ['say' + 'Hi']() {      // 方法名也可以计算
    return 'Hi!';
  }
};
```

## 6.5 属性存在性检查

```javascript
let user = { name: '张三', age: 25 };

// in 运算符
'name' in user;   // true
'email' in user;  // false

// 与 undefined 比较（不完全可靠，因为属性值可能就是 undefined）
user.name !== undefined;  // true
```

## 6.6 遍历对象

```javascript
let user = { name: '张三', age: 25, city: '北京' };

// for...in
for (let key in user) {
  console.log(`${key}: ${user[key]}`);
}

// Object.keys / values / entries（ES2017）
Object.keys(user);    // ["name", "age", "city"]
Object.values(user);  // ["张三", 25, "北京"]
Object.entries(user);  // [["name","张三"], ["age",25], ["city","北京"]]

// 结合解构遍历
for (let [key, value] of Object.entries(user)) {
  console.log(`${key}: ${value}`);
}
```

## 6.7 对象引用与复制

对象通过**引用**赋值和传递，而不是复制：

```javascript
let a = { name: '张三' };
let b = a;            // b 和 a 指向同一个对象
b.name = '李四';
console.log(a.name);  // "李四"（a 也被修改了！）

// 比较：两个引用指向同一对象时相等
a === b;              // true

// 两个内容相同但独立的对象不相等
let c = { name: '李四' };
a === c;              // false

// 浅拷贝方式一：展开运算符（推荐）
let copy1 = { ...a };

// 浅拷贝方式二：Object.assign
let copy2 = Object.assign({}, a);

// 深拷贝（嵌套对象也完全独立）
let deep = { name: '张三', address: { city: '北京' } };
let deepCopy = structuredClone(deep); // 推荐，ES2022
deepCopy.address.city = '上海';
deep.address.city;    // "北京"（原对象未受影响）
```

## 6.8 this

`this` 的值取决于函数是**如何被调用**的，而不是在哪里定义的：

```javascript
let user = {
  name: '张三',
  greet() {
    console.log(`你好，我是 ${this.name}`);
  }
};

user.greet();  // "你好，我是 张三" — this 指向 user

let fn = user.greet;
fn();          // "你好，我是 undefined" — this 在非严格模式指向 window

// 箭头函数没有自己的 this，它从外层继承
let user = {
  name: '张三',
  greet() {
    let inner = () => {
      console.log(this.name); // this 继承自 greet，指向 user
    };
    inner();
  }
};
user.greet(); // "张三"
```

## 6.9 构造函数与 new

```javascript
function User(name, age) {
  // this = {} （隐式创建）
  this.name = name;
  this.age = age;
  this.greet = function() {
    return `我是 ${this.name}`;
  };
  // return this （隐式返回）
}

let user = new User('张三', 25);
user.name;     // "张三"
user.greet();  // "我是 张三"
```

`new` 操作符的执行步骤：
1. 创建一个新的空对象
2. 将 `this` 指向该对象
3. 执行函数体
4. 返回 `this`

## 6.10 可选链与对象

```javascript
let user = {};

// 安全访问嵌套属性
user?.address?.street;   // undefined
user?.greet?.();          // undefined（方法不存在，不会报错）
```

## 6.11 对象转原始值

当对象参与运算时，JavaScript 会自动调用转换方法：

```javascript
let user = {
  name: '张三',
  age: 25,
  toString() {
    return this.name;       // 字符串转换时调用
  },
  valueOf() {
    return this.age;        // 数字转换时调用
  }
};

String(user);  // "张三"（调用 toString）
+user;         // 25（调用 valueOf）
`${user}`;     // "张三"（调用 toString）
```

---

# 第七章 数组与数组方法

## 7.1 创建数组

```javascript
// 字面量（推荐）
let fruits = ['苹果', '香蕉', '橘子'];

// 构造函数
let arr = new Array(3);   // 创建长度为 3 的空数组（注意陷阱！）
let arr2 = Array.of(3);   // 创建包含元素 3 的数组 [3]

// Array.from — 将类数组或可迭代对象转为数组
Array.from('Hello');       // ["H", "e", "l", "l", "o"]
Array.from({ length: 3 }, (_, i) => i); // [0, 1, 2]
```

## 7.2 基本操作

```javascript
let arr = ['a', 'b', 'c', 'd', 'e'];

// 访问
arr[0];            // "a"
arr.at(-1);        // "e"（ES2022，支持负索引）
arr.length;        // 5

// 修改
arr[1] = 'B';      // 修改元素
arr.length = 3;    // 截断数组为 ["a", "B", "c"]

// 判断是否为数组
Array.isArray(arr); // true
Array.isArray({});  // false
```

## 7.3 增删元素

```javascript
let arr = [1, 2, 3];

// 末尾操作
arr.push(4, 5);        // 添加到末尾 → [1,2,3,4,5]，返回新长度
arr.pop();             // 移除末尾 → [1,2,3,4]，返回被移除的 5

// 开头操作
arr.unshift(0);        // 添加到开头 → [0,1,2,3,4]，返回新长度
arr.shift();           // 移除开头 → [1,2,3,4]，返回被移除的 0

// splice — 万能增删改（会修改原数组）
let arr = [1, 2, 3, 4, 5];
arr.splice(1, 2);          // 从索引 1 删除 2 个 → 返回 [2,3]，arr 为 [1,4,5]
arr.splice(1, 0, 'a', 'b'); // 从索引 1 删除 0 个，插入 'a','b'
                            // arr 为 [1,'a','b',4,5]

// toSpliced — 不修改原数组的版本（ES2023）
let newArr = arr.toSpliced(1, 2, 'x'); // 返回新数组，arr 不变
```

## 7.4 遍历

```javascript
let fruits = ['苹果', '香蕉', '橘子'];

// forEach
fruits.forEach((item, index, array) => {
  console.log(`${index}: ${item}`);
});

// for...of（推荐）
for (let fruit of fruits) {
  console.log(fruit);
}

// entries() — 同时获取索引和值
for (let [index, fruit] of fruits.entries()) {
  console.log(`${index}: ${fruit}`);
}
```

## 7.5 查找

```javascript
let arr = [1, 2, 3, 4, 5, 3];

// 查找值
arr.indexOf(3);         // 2（第一个出现的索引）
arr.lastIndexOf(3);     // 5（最后一个出现的索引）
arr.includes(3);        // true

// 条件查找
let users = [
  { name: '张三', age: 25 },
  { name: '李四', age: 30 },
  { name: '王五', age: 25 }
];

users.find(u => u.age === 25);       // { name: '张三', age: 25 }（第一个匹配）
users.findIndex(u => u.age === 25);  // 0
users.findLast(u => u.age === 25);   // { name: '王五', age: 25 }（最后一个匹配，ES2023）
users.findLastIndex(u => u.age === 25); // 2
```

## 7.6 转换

```javascript
let arr = [1, 2, 3, 4, 5];

// map — 对每个元素进行转换，返回新数组
let doubled = arr.map(n => n * 2);  // [2, 4, 6, 8, 10]

// filter — 过滤，返回满足条件的元素组成的新数组
let even = arr.filter(n => n % 2 === 0);  // [2, 4]

// reduce — 累积计算，将数组归纳为单个值
let sum = arr.reduce((acc, n) => acc + n, 0);  // 15
// acc:  0 → 1 → 3 → 6 → 10 → 15

// reduceRight — 从右往左归纳
let str = ['a','b','c'].reduceRight((acc, s) => acc + s, ''); // "cba"

// flat — 展平嵌套数组
[1, [2, [3, [4]]]].flat();      // [1, 2, [3, [4]]]（默认展平一层）
[1, [2, [3, [4]]]].flat(Infinity); // [1, 2, 3, 4]（全部展平）

// flatMap — map + flat(1)
['Hello World', 'Hi'].flatMap(s => s.split(' ')); // ["Hello", "World", "Hi"]
```

## 7.7 排序

```javascript
// sort — 原地排序（修改原数组）
let arr = [3, 1, 4, 1, 5];
arr.sort((a, b) => a - b);  // [1, 1, 3, 4, 5]（升序）
arr.sort((a, b) => b - a);  // [5, 4, 3, 1, 1]（降序）

// ⚠️ 默认 sort 按字符串排序！
[10, 9, 80].sort();          // [10, 80, 9] — 按字符串比较，不是数字！
[10, 9, 80].sort((a, b) => a - b); // [9, 10, 80] — 正确

// 字符串排序
['banana', 'apple', 'cherry'].sort(); // 字母序
// 中文排序
['张三', '李四', '王五'].sort((a, b) => a.localeCompare(b, 'zh-CN'));

// toSorted — 不修改原数组（ES2023）
let sorted = arr.toSorted((a, b) => a - b);

// reverse / toReversed
arr.reverse();                    // 原地反转
let reversed = arr.toReversed();  // 不修改原数组（ES2023）
```

## 7.8 其他实用方法

```javascript
// concat — 合并数组
[1, 2].concat([3, 4], [5]); // [1, 2, 3, 4, 5]
[...[1, 2], ...[3, 4]];     // [1, 2, 3, 4]（展开运算符替代方案）

// slice — 截取子数组（不修改原数组）
let arr = [0, 1, 2, 3, 4];
arr.slice(1, 3);   // [1, 2]
arr.slice(-2);     // [3, 4]
arr.slice();       // [0, 1, 2, 3, 4]（浅拷贝）

// join — 数组转字符串
['2026', '02', '25'].join('-'); // "2026-02-25"

// every / some — 条件测试
[2, 4, 6].every(n => n % 2 === 0);  // true（全部满足）
[1, 2, 3].some(n => n > 2);         // true（至少一个满足）

// fill — 用固定值填充
new Array(5).fill(0);  // [0, 0, 0, 0, 0]
[1,2,3,4].fill('x', 1, 3); // [1, "x", "x", 4]

// Array.from + keys — 生成序列
[...Array(5).keys()];  // [0, 1, 2, 3, 4]
```

## 7.9 解构赋值

```javascript
// 数组解构
let [a, b, c] = [1, 2, 3];    // a=1, b=2, c=3
let [first, , third] = [1, 2, 3]; // 跳过第二个
let [x, ...rest] = [1, 2, 3, 4]; // x=1, rest=[2,3,4]

// 默认值
let [a = 0, b = 0] = [1];     // a=1, b=0

// 交换变量
let a = 1, b = 2;
[a, b] = [b, a];              // a=2, b=1

// 对象解构
let { name, age } = { name: '张三', age: 25 };

// 重命名
let { name: userName, age: userAge } = { name: '张三', age: 25 };
// userName = "张三", userAge = 25

// 默认值
let { name, role = 'user' } = { name: '张三' };
// role = "user"

// 嵌套解构
let { address: { city } } = { address: { city: '北京' } };
// city = "北京"

// 函数参数解构
function createUser({ name, age = 18, role = 'user' } = {}) {
  return { name, age, role };
}
createUser({ name: '张三' }); // { name: '张三', age: 18, role: 'user' }
```

---

# 第八章 数据类型进阶

## 8.1 Map

`Map` 是键值对集合，与对象不同的是，它的**键可以是任意类型**（对象、函数、原始值等）：

```javascript
let map = new Map();

// 设置键值对
map.set('name', '张三');
map.set(42, '数字键');
map.set(true, '布尔键');
let objKey = { id: 1 };
map.set(objKey, '对象键');

// 读取
map.get('name');     // "张三"
map.get(42);         // "数字键"
map.get(objKey);     // "对象键"

// 检查与删除
map.has('name');     // true
map.delete(42);      // true
map.size;            // 3
map.clear();         // 全部清除

// 链式调用
let map2 = new Map()
  .set('a', 1)
  .set('b', 2)
  .set('c', 3);

// 遍历（保持插入顺序）
for (let [key, value] of map2) {
  console.log(`${key} → ${value}`);
}
map2.keys();     // MapIterator {"a", "b", "c"}
map2.values();   // MapIterator {1, 2, 3}
map2.entries();  // MapIterator {["a",1], ["b",2], ["c",3]}
map2.forEach((value, key) => console.log(key, value));

// 对象与 Map 互转
let obj = Object.fromEntries(map2);      // Map → Object
let map3 = new Map(Object.entries(obj));  // Object → Map
```

## 8.2 Set

`Set` 是值的集合，每个值**唯一**，不会重复：

```javascript
let set = new Set();
set.add(1);
set.add(2);
set.add(2);    // 重复，不会添加
set.add('hello');
set.size;       // 3

set.has(1);     // true
set.delete(2);  // true

// 常见用法：数组去重
let arr = [1, 2, 2, 3, 3, 3];
let unique = [...new Set(arr)]; // [1, 2, 3]

// 遍历
for (let value of set) {
  console.log(value);
}

// 集合运算（ES2025 提案，部分浏览器已支持）
let a = new Set([1, 2, 3]);
let b = new Set([2, 3, 4]);
a.union(b);        // Set {1, 2, 3, 4}
a.intersection(b); // Set {2, 3}
a.difference(b);   // Set {1}
```

## 8.3 WeakMap 与 WeakSet

它们的键（WeakMap）或值（WeakSet）必须是对象，且是**弱引用** —— 不阻止垃圾回收：

```javascript
let weakMap = new WeakMap();
let obj = { data: '重要数据' };
weakMap.set(obj, '附加信息');

// 当 obj 不再被其他变量引用时，weakMap 中的条目会自动被回收
obj = null; // 此时 weakMap 中的条目会在 GC 时被清除
```

> **使用场景：** 缓存计算结果、存储与 DOM 元素关联的数据（元素被移除时自动清理）。

## 8.4 可迭代对象与迭代器

实现了 `Symbol.iterator` 方法的对象就是可迭代对象，可以用 `for...of` 遍历：

```javascript
// 自定义可迭代对象
let range = {
  from: 1,
  to: 5,

  [Symbol.iterator]() {
    let current = this.from;
    let last = this.to;
    return {
      next() {
        return current <= last
          ? { value: current++, done: false }
          : { done: true };
      }
    };
  }
};

for (let num of range) {
  console.log(num); // 1, 2, 3, 4, 5
}

[...range]; // [1, 2, 3, 4, 5]（展开运算符也适用于可迭代对象）
```

## 8.5 JSON

`JSON`（JavaScript Object Notation）是最通用的数据交换格式：

```javascript
let user = {
  name: '张三',
  age: 25,
  hobbies: ['编程', '阅读'],
  address: { city: '北京' }
};

// 序列化（对象 → JSON 字符串）
let json = JSON.stringify(user);
// '{"name":"张三","age":25,"hobbies":["编程","阅读"],"address":{"city":"北京"}}'

// 格式化输出（第三个参数为缩进空格数）
JSON.stringify(user, null, 2);

// 过滤属性（第二个参数为 replacer）
JSON.stringify(user, ['name', 'age']); // 只序列化 name 和 age

// 反序列化（JSON 字符串 → 对象）
let parsed = JSON.parse(json);

// toJSON 自定义序列化行为
let meeting = {
  title: '周会',
  date: new Date(),
  toJSON() {
    return { title: this.title, date: this.date.toISOString() };
  }
};
JSON.stringify(meeting);
```

> **JSON 不支持的值：** `undefined`、`function`、`Symbol`、`Infinity`、`NaN`（变为 `null`）、循环引用（会报错）。

## 8.6 日期和时间

```javascript
// 创建日期
let now = new Date();                    // 当前时间
let date = new Date('2026-02-25');       // ISO 格式字符串
let date2 = new Date(2026, 1, 25);      // 年, 月(0-11), 日
let date3 = new Date(2026, 1, 25, 14, 30, 0); // 含时分秒

// 获取日期组件
now.getFullYear();    // 2026
now.getMonth();       // 0-11（0 = 一月）
now.getDate();        // 1-31
now.getDay();         // 0-6（0 = 周日）
now.getHours();       // 0-23
now.getMinutes();     // 0-59
now.getSeconds();     // 0-59
now.getTime();        // 时间戳（毫秒）
Date.now();           // 当前时间戳（不创建 Date 对象，性能更好）

// 设置日期组件
now.setFullYear(2027);
now.setMonth(5);

// 日期运算（自动调整溢出）
let date = new Date(2026, 0, 32); // 2026年2月1日（1月没有32号，自动进位）

// 格式化
now.toLocaleDateString('zh-CN'); // "2026/2/25"
now.toLocaleTimeString('zh-CN'); // "14:30:00"
now.toISOString();               // "2026-02-25T06:30:00.000Z"
```

## 8.7 展开运算符与剩余参数

```javascript
// 展开数组
let arr1 = [1, 2, 3];
let arr2 = [0, ...arr1, 4]; // [0, 1, 2, 3, 4]

// 展开对象
let defaults = { theme: 'light', lang: 'zh' };
let userSettings = { lang: 'en', fontSize: 14 };
let settings = { ...defaults, ...userSettings };
// { theme: 'light', lang: 'en', fontSize: 14 }（后面的覆盖前面的）

// 展开用于函数调用
Math.max(...[3, 1, 4, 1, 5]); // 5
```

---

# 第九章 函数进阶

## 9.1 闭包

**闭包**是函数与其词法环境的组合 —— 函数能"记住"它被创建时的外部变量：

```javascript
function makeCounter() {
  let count = 0;

  return function() {
    return count++;
  };
}

let counter = makeCounter();
counter(); // 0
counter(); // 1
counter(); // 2
// count 变量对外不可见，但函数"记住"了它
```

**每次调用外部函数都会创建新的词法环境：**

```javascript
let counter1 = makeCounter();
let counter2 = makeCounter();
counter1(); // 0
counter1(); // 1
counter2(); // 0（独立的计数器）
```

## 9.2 变量作用域

JavaScript 采用**词法作用域**（静态作用域）—— 变量的作用域在代码编写时就确定了：

```javascript
let globalVar = '全局';

function outer() {
  let outerVar = '外层';

  function inner() {
    let innerVar = '内层';
    console.log(globalVar); // ✅ 访问全局
    console.log(outerVar);  // ✅ 访问外层（闭包）
    console.log(innerVar);  // ✅ 访问自身
  }

  inner();
  console.log(innerVar);    // ❌ ReferenceError
}
```

**块级作用域（let / const）：**

```javascript
{
  let blockVar = '块级';
  const blockConst = '常量';
}
console.log(blockVar);  // ❌ ReferenceError

// if、for 等也创建块级作用域
for (let i = 0; i < 3; i++) {}
console.log(i); // ❌ ReferenceError
```

**var 的函数作用域（陷阱）：**

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// 输出 3, 3, 3（不是 0, 1, 2！因为 var 没有块级作用域）

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// 输出 0, 1, 2（let 有块级作用域，每次迭代创建新变量）
```

## 9.3 全局对象

```javascript
// 浏览器中全局对象是 window，Node.js 中是 global
// globalThis 是跨环境的统一引用（ES2020）
globalThis === window; // true（浏览器中）

// var 和 function 声明会成为全局对象的属性
var x = 10;
globalThis.x;  // 10

// let、const 不会
let y = 20;
globalThis.y;  // undefined
```

## 9.4 函数对象

函数本身就是对象，有自己的属性：

```javascript
function greet(name, greeting) {
  return `${greeting}, ${name}!`;
}

greet.name;    // "greet"（函数名）
greet.length;  // 2（期望的参数数量，不含 rest 和有默认值的参数）

// 可以给函数添加自定义属性
greet.callCount = 0;
```

## 9.5 调度：setTimeout 与 setInterval

```javascript
// 延时调用
let timerId = setTimeout(() => {
  console.log('3 秒后执行');
}, 3000);
clearTimeout(timerId); // 取消

// 周期调用
let intervalId = setInterval(() => {
  console.log('每秒执行');
}, 1000);
clearInterval(intervalId); // 停止

// 嵌套 setTimeout 替代 setInterval（更精确的间隔控制）
function tick() {
  console.log('tick');
  setTimeout(tick, 1000); // 上一次执行完毕后才开始计时
}
setTimeout(tick, 1000);

// 零延迟 setTimeout（不是真正的 0ms，最小约 4ms）
setTimeout(() => console.log('异步'), 0);
console.log('同步');
// 输出：同步 → 异步
```

## 9.6 装饰器与转发

装饰器模式：包装函数以添加额外功能：

```javascript
// 缓存装饰器
function cachingDecorator(fn) {
  let cache = new Map();

  return function(...args) {
    let key = JSON.stringify(args);
    if (cache.has(key)) {
      return cache.get(key);
    }
    let result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

function slowCalculation(x) {
  // 耗时计算...
  return x * x;
}

let cachedCalc = cachingDecorator(slowCalculation);
cachedCalc(5); // 计算并缓存
cachedCalc(5); // 直接从缓存返回
```

## 9.7 call、apply 与 bind

手动控制 `this` 的指向：

```javascript
function greet(greeting, punctuation) {
  return `${greeting}, ${this.name}${punctuation}`;
}

let user = { name: '张三' };

// call — 立即调用，参数逐个传递
greet.call(user, '你好', '！');   // "你好, 张三！"

// apply — 立即调用，参数作为数组
greet.apply(user, ['你好', '！']); // "你好, 张三！"

// bind — 返回新函数，永久绑定 this
let greetUser = greet.bind(user, '你好');
greetUser('。');   // "你好, 张三。"

// bind 的偏函数应用
function multiply(a, b) { return a * b; }
let double = multiply.bind(null, 2);
double(5);  // 10
```

---

# 第十章 对象属性配置

## 10.1 属性标志

对象的每个属性除了值之外，还有三个特殊标志（flag）：

| 标志 | 说明 | 默认值 |
|------|------|--------|
| `writable` | 是否可以修改值 | `true` |
| `enumerable` | 是否可以在循环中列出 | `true` |
| `configurable` | 是否可以删除/修改标志 | `true` |

```javascript
let user = { name: '张三' };

// 获取属性描述符
let descriptor = Object.getOwnPropertyDescriptor(user, 'name');
// { value: "张三", writable: true, enumerable: true, configurable: true }

// 定义/修改属性
Object.defineProperty(user, 'name', {
  writable: false  // 设为只读
});
user.name = '李四';  // 严格模式报错，非严格模式静默失败
user.name;           // "张三"

// 定义多个属性
Object.defineProperties(user, {
  age: { value: 25, writable: true, enumerable: true, configurable: true },
  role: { value: 'admin', writable: false, enumerable: false }
});
```

## 10.2 对象保护

```javascript
let config = { apiUrl: '/api', timeout: 3000 };

// 禁止添加新属性
Object.preventExtensions(config);
config.newProp = 'x'; // 静默失败或严格模式报错

// 禁止添加/删除属性（已有属性可以修改）
Object.seal(config);

// 禁止任何修改（最严格）
Object.freeze(config);
config.timeout = 5000; // 不生效

// 检测
Object.isExtensible(config); // false
Object.isSealed(config);     // true
Object.isFrozen(config);     // true
```

> **注意：** `Object.freeze` 是浅冻结，嵌套对象内部仍然可以修改。

## 10.3 Getter 与 Setter

访问器属性通过 `get` 和 `set` 函数实现，使用时看起来像普通属性：

```javascript
let user = {
  firstName: '三',
  lastName: '张',

  get fullName() {
    return `${this.lastName}${this.firstName}`;
  },

  set fullName(value) {
    [this.lastName, this.firstName] = value.split('');
  }
};

user.fullName;           // "张三"（调用 getter）
user.fullName = '李四';  // 调用 setter
user.lastName;           // "李"
user.firstName;          // "四"
```

**使用 defineProperty 定义访问器属性：**

```javascript
let user = { _age: 25 };

Object.defineProperty(user, 'age', {
  get() {
    return this._age;
  },
  set(value) {
    if (value < 0 || value > 150) {
      throw new Error('年龄无效');
    }
    this._age = value;
  },
  enumerable: true,
  configurable: true
});

user.age = 30;  // ✅
user.age = -5;  // ❌ Error: 年龄无效
```

---

# 第十一章 原型与继承

## 11.1 原型链

JavaScript 的继承基于**原型链** —— 每个对象都有一个隐藏的 `[[Prototype]]` 属性，指向另一个对象（称为"原型"）。当访问一个属性时，如果对象自身没有，就会沿着原型链向上查找：

```javascript
let animal = {
  eats: true,
  walk() {
    console.log('走路');
  }
};

let rabbit = {
  jumps: true,
  __proto__: animal    // 设置原型（不推荐直接这样写）
};

rabbit.jumps;   // true（自身属性）
rabbit.eats;    // true（从原型继承）
rabbit.walk();  // "走路"（从原型继承）
```

**推荐使用 `Object.create` 设置原型：**

```javascript
let rabbit = Object.create(animal);
rabbit.jumps = true;

Object.getPrototypeOf(rabbit) === animal; // true
```

## 11.2 原型链的查找过程

```
rabbit.eats
  1. rabbit 自身有 eats 属性？→ 没有
  2. rabbit.__proto__ (animal) 有 eats 属性？→ 有，返回 true

rabbit.toString()
  1. rabbit 自身有 toString？→ 没有
  2. animal 有 toString？→ 没有
  3. Object.prototype 有 toString？→ 有，调用它
  4. Object.prototype.__proto__ → null（链终止）
```

## 11.3 F.prototype

当使用 `new F()` 创建对象时，新对象的 `[[Prototype]]` 会被设置为 `F.prototype`：

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(`${this.name} 在吃东西`);
};

let cat = new Animal('小猫');
cat.eat();  // "小猫 在吃东西"

Object.getPrototypeOf(cat) === Animal.prototype; // true
cat.constructor === Animal;                        // true
```

## 11.4 原生原型

JavaScript 内置类型的原型链：

```
let arr = [1, 2, 3];

arr → Array.prototype → Object.prototype → null
       (push, map...)    (toString, hasOwnProperty...)

let str = "hello";
str → String.prototype → Object.prototype → null
       (slice, trim...)

let num = 42;
num → Number.prototype → Object.prototype → null
       (toFixed, toString...)
```

```javascript
// 可以扩展原生原型（但强烈不推荐！可能与未来标准冲突）
// String.prototype.repeat 就是这样被添加到语言中的
```

## 11.5 hasOwnProperty

区分自身属性和继承属性：

```javascript
let animal = { eats: true };
let rabbit = Object.create(animal);
rabbit.jumps = true;

rabbit.hasOwnProperty('jumps');  // true（自身的）
rabbit.hasOwnProperty('eats');   // false（继承的）

// for...in 会遍历继承的属性
for (let key in rabbit) {
  if (rabbit.hasOwnProperty(key)) {
    console.log(`自身: ${key}`);   // jumps
  } else {
    console.log(`继承: ${key}`);   // eats
  }
}

// Object.keys 只返回自身的可枚举属性
Object.keys(rabbit); // ["jumps"]
```

---

# 第十二章 类

## 12.1 基本语法

ES6 的 `class` 本质上是构造函数 + 原型的语法糖，但有一些细微差异：

```javascript
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `你好，我是 ${this.name}，${this.age} 岁`;
  }

  get info() {
    return `${this.name} (${this.age})`;
  }

  set nickname(value) {
    if (value.length < 2) throw new Error('昵称太短');
    this._nickname = value;
  }
}

let user = new User('张三', 25);
user.greet();  // "你好，我是 张三，25 岁"
user.info;     // "张三 (25)"
```

## 12.2 class 与 function 的区别

| 区别 | class | 构造函数 |
|------|-------|----------|
| 必须 `new` 调用 | 是 | 否（不加 new 不报错） |
| 方法不可枚举 | 是 | 手动设置 |
| 自动严格模式 | 是 | 否 |
| 提升 | 否（暂时性死区） | 是（函数声明） |

## 12.3 类继承

```javascript
class Animal {
  constructor(name) {
    this.name = name;
    this.speed = 0;
  }

  run(speed) {
    this.speed = speed;
    console.log(`${this.name} 以 ${this.speed} 的速度奔跑`);
  }

  stop() {
    this.speed = 0;
    console.log(`${this.name} 停下了`);
  }
}

class Rabbit extends Animal {
  constructor(name, earLength) {
    super(name);            // 必须先调用 super()
    this.earLength = earLength;
  }

  jump() {
    console.log(`${this.name} 跳了起来！`);
  }

  stop() {
    super.stop();           // 调用父类方法
    console.log(`${this.name} 躲了起来`);
  }
}

let rabbit = new Rabbit('小白', 10);
rabbit.run(5);   // "小白 以 5 的速度奔跑"（继承）
rabbit.jump();   // "小白 跳了起来！"（自身）
rabbit.stop();   // "小白 停下了" → "小白 躲了起来"（重写 + 调用 super）
```

> **关键规则：** 子类 `constructor` 中必须在使用 `this` 之前调用 `super()`。

## 12.4 静态属性与方法

```javascript
class MathHelper {
  static PI = 3.14159265;

  static add(a, b) {
    return a + b;
  }

  static isPositive(n) {
    return n > 0;
  }
}

MathHelper.PI;             // 3.14159265
MathHelper.add(2, 3);     // 5
MathHelper.isPositive(-1); // false

// 静态方法属于类本身，不属于实例
let m = new MathHelper();
m.add;                     // undefined
```

## 12.5 私有属性与方法

使用 `#` 前缀定义真正私有的成员（ES2022）：

```javascript
class BankAccount {
  #balance = 0;  // 私有属性

  constructor(owner, initialBalance) {
    this.owner = owner;
    this.#balance = initialBalance;
  }

  get balance() {
    return this.#balance;
  }

  deposit(amount) {
    this.#validate(amount);
    this.#balance += amount;
  }

  withdraw(amount) {
    this.#validate(amount);
    if (amount > this.#balance) {
      throw new Error('余额不足');
    }
    this.#balance -= amount;
  }

  #validate(amount) {  // 私有方法
    if (amount <= 0) throw new Error('金额必须大于 0');
  }
}

let account = new BankAccount('张三', 1000);
account.balance;      // 1000（通过 getter 访问）
account.deposit(500);
account.#balance;     // ❌ SyntaxError（外部无法访问私有属性）
```

## 12.6 instanceof

```javascript
class Animal {}
class Rabbit extends Animal {}

let rabbit = new Rabbit();
rabbit instanceof Rabbit;  // true
rabbit instanceof Animal;  // true
rabbit instanceof Object;  // true

// 也适用于构造函数
function Car() {}
let car = new Car();
car instanceof Car;        // true
```

## 12.7 Mixin 模式

JavaScript 不支持多重继承，但可以通过 mixin 将方法"混入"类中：

```javascript
let serializableMixin = {
  serialize() {
    return JSON.stringify(this);
  },
  toString() {
    return `[${this.constructor.name}] ${this.serialize()}`;
  }
};

let eventMixin = {
  on(event, handler) {
    if (!this._handlers) this._handlers = {};
    if (!this._handlers[event]) this._handlers[event] = [];
    this._handlers[event].push(handler);
  },
  emit(event, ...args) {
    if (this._handlers?.[event]) {
      this._handlers[event].forEach(h => h.apply(this, args));
    }
  }
};

class User {
  constructor(name) {
    this.name = name;
  }
}

Object.assign(User.prototype, serializableMixin, eventMixin);

let user = new User('张三');
user.serialize();           // '{"name":"张三"}'
user.on('login', () => console.log('登录了'));
user.emit('login');
```

---

# 第十三章 错误处理

## 13.1 try...catch...finally

```javascript
try {
  let result = riskyOperation();
  console.log(result);
} catch (error) {
  console.error('出错了:', error.message);
} finally {
  console.log('无论是否出错都会执行');
}
```

**catch 块接收的 Error 对象属性：**

```javascript
try {
  undeclaredVar;
} catch (err) {
  err.name;     // "ReferenceError"
  err.message;  // "undeclaredVar is not defined"
  err.stack;    // 完整调用栈（非标准但所有浏览器支持）
}
```

## 13.2 内置错误类型

| 类型 | 触发场景 |
|------|----------|
| `SyntaxError` | 语法错误（`JSON.parse('{bad}')` |
| `ReferenceError` | 引用不存在的变量 |
| `TypeError` | 值的类型不对（`null.prop`、`(1)()` |
| `RangeError` | 值超出范围（`new Array(-1)` |
| `URIError` | URI 函数参数不正确 |

## 13.3 throw 抛出错误

```javascript
function divide(a, b) {
  if (b === 0) {
    throw new Error('除数不能为零');
  }
  return a / b;
}

// 也可以抛出内置错误类型
function setAge(age) {
  if (typeof age !== 'number') {
    throw new TypeError('age 必须是数字');
  }
  if (age < 0 || age > 150) {
    throw new RangeError('age 超出有效范围');
  }
}
```

## 13.4 自定义错误类

```javascript
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}

class HttpError extends Error {
  constructor(statusCode, message) {
    super(message);
    this.name = 'HttpError';
    this.statusCode = statusCode;
  }
}

// 使用
try {
  throw new ValidationError('邮箱格式不正确', 'email');
} catch (err) {
  if (err instanceof ValidationError) {
    console.log(`字段 ${err.field}: ${err.message}`);
  } else if (err instanceof HttpError) {
    console.log(`HTTP ${err.statusCode}: ${err.message}`);
  } else {
    throw err; // 不认识的错误，继续向上抛出
  }
}
```

## 13.5 可选的 catch 绑定

如果不需要错误对象，可以省略（ES2019）：

```javascript
try {
  JSON.parse(data);
} catch {
  console.log('JSON 解析失败');
}
```

## 13.6 全局错误处理

```javascript
// 浏览器中捕获未处理的错误
window.addEventListener('error', (event) => {
  console.error('全局错误:', event.message);
});

// 捕获未处理的 Promise 拒绝
window.addEventListener('unhandledrejection', (event) => {
  console.error('未处理的 Promise 拒绝:', event.reason);
  event.preventDefault(); // 阻止默认的控制台报错
});
```

---

# 第十四章 Promise 与 async/await

## 14.1 回调的问题

传统的异步代码使用回调函数，多层嵌套会形成**回调地狱**：

```javascript
// ❌ 回调地狱
loadScript('a.js', function() {
  loadScript('b.js', function() {
    loadScript('c.js', function() {
      // 越来越深...
    });
  });
});
```

## 14.2 Promise 基础

`Promise` 是异步操作的容器，它有三种状态：

- **pending**（进行中）
- **fulfilled**（已成功）
- **rejected**（已失败）

```javascript
let promise = new Promise(function(resolve, reject) {
  // 异步操作...
  setTimeout(() => {
    let success = true;
    if (success) {
      resolve('操作成功');   // 标记为 fulfilled
    } else {
      reject(new Error('操作失败')); // 标记为 rejected
    }
  }, 1000);
});

// 消费 Promise
promise
  .then(result => console.log(result))   // 成功时
  .catch(error => console.error(error))  // 失败时
  .finally(() => console.log('完成'));    // 无论成功失败
```

## 14.3 Promise 链

`.then()` 返回新的 Promise，可以链式调用：

```javascript
fetch('/api/user/1')
  .then(response => {
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return response.json();               // 返回新 Promise
  })
  .then(user => {
    console.log(user.name);
    return fetch(`/api/posts?userId=${user.id}`);
  })
  .then(response => response.json())
  .then(posts => console.log(posts))
  .catch(error => console.error('链中任何一步出错:', error));
```

> **关键理解：** `.then` 中返回一个值，下一个 `.then` 收到该值；返回一个 Promise，下一个 `.then` 等待它完成。

## 14.4 Promise 静态方法

```javascript
// Promise.all — 全部成功才成功，有一个失败就立即失败
let results = await Promise.all([
  fetch('/api/users'),
  fetch('/api/posts'),
  fetch('/api/comments')
]);
// results 是三个 Response 对象的数组

// Promise.allSettled — 等待所有完成，不管成功失败
let results = await Promise.allSettled([
  fetch('/api/users'),
  fetch('/api/bad-url'),
]);
// [{ status: "fulfilled", value: Response },
//  { status: "rejected", reason: Error }]

// Promise.race — 返回第一个完成的（无论成功还是失败）
let fastest = await Promise.race([
  fetch('/api/server-a'),
  fetch('/api/server-b'),
]);

// Promise.any — 返回第一个成功的（忽略失败的）
let firstSuccess = await Promise.any([
  fetch('/api/server-a'),
  fetch('/api/server-b'),
]);

// Promise.resolve / Promise.reject — 快速创建已完成的 Promise
Promise.resolve(42);             // 立即成功，值为 42
Promise.reject(new Error('失败')); // 立即失败
```

## 14.5 async/await

`async/await` 是 Promise 的语法糖，让异步代码看起来像同步代码：

```javascript
// async 函数总是返回 Promise
async function fetchUser(id) {
  // await 等待 Promise 完成，拿到结果
  let response = await fetch(`/api/users/${id}`);

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  let user = await response.json();
  return user;
}

// 调用
let user = await fetchUser(1);
console.log(user.name);

// 错误处理
try {
  let user = await fetchUser(999);
} catch (error) {
  console.error('获取用户失败:', error.message);
}
```

## 14.6 并行执行

```javascript
// ❌ 串行执行（慢）
let user = await fetchUser(1);
let posts = await fetchPosts(1);
// 总耗时 = fetchUser 时间 + fetchPosts 时间

// ✅ 并行执行（快）
let [user, posts] = await Promise.all([
  fetchUser(1),
  fetchPosts(1)
]);
// 总耗时 = max(fetchUser 时间, fetchPosts 时间)
```

## 14.7 微任务

Promise 的回调（`.then`/`.catch`/`.finally`）在**微任务队列**中执行，优先级高于 `setTimeout` 等宏任务：

```javascript
console.log('1');

setTimeout(() => console.log('2'), 0);   // 宏任务

Promise.resolve().then(() => console.log('3')); // 微任务

console.log('4');

// 输出顺序：1 → 4 → 3 → 2
```

---

# 第十五章 Generator 与异步迭代

## 15.1 Generator 函数

Generator 是一种特殊函数，可以**暂停和恢复**执行：

```javascript
function* generateSequence() {
  yield 1;
  yield 2;
  yield 3;
}

let generator = generateSequence();

generator.next(); // { value: 1, done: false }
generator.next(); // { value: 2, done: false }
generator.next(); // { value: 3, done: false }
generator.next(); // { value: undefined, done: true }

// Generator 是可迭代的
for (let value of generateSequence()) {
  console.log(value); // 1, 2, 3
}

[...generateSequence()]; // [1, 2, 3]
```

## 15.2 yield 的双向传值

```javascript
function* calculator() {
  let result = 0;

  while (true) {
    let value = yield result;   // 向外产出 result，接收外部传入的 value
    result += value;
  }
}

let calc = calculator();
calc.next();     // { value: 0, done: false }（启动）
calc.next(10);   // { value: 10, done: false }（传入 10）
calc.next(20);   // { value: 30, done: false }（传入 20）
```

## 15.3 yield* 委托

```javascript
function* generateRange(start, end) {
  for (let i = start; i <= end; i++) {
    yield i;
  }
}

function* generateAll() {
  yield* generateRange(1, 3);   // 委托给另一个 generator
  yield* generateRange(7, 9);
}

[...generateAll()]; // [1, 2, 3, 7, 8, 9]
```

## 15.4 实用场景：无限序列

```javascript
function* fibonacci() {
  let a = 0, b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

// 取前 10 个斐波那契数
let fib = fibonacci();
let first10 = Array.from({ length: 10 }, () => fib.next().value);
// [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

## 15.5 异步迭代器

```javascript
// 异步可迭代对象使用 Symbol.asyncIterator
// 使用 for await...of 遍历

async function* fetchPages(baseUrl) {
  let page = 1;
  while (true) {
    let response = await fetch(`${baseUrl}?page=${page}`);
    let data = await response.json();
    if (data.length === 0) return;
    yield data;
    page++;
  }
}

// 消费异步 generator
for await (let pageData of fetchPages('/api/items')) {
  console.log('收到一页数据:', pageData);
}
```

---

# 第十六章 模块

## 16.1 为什么需要模块

模块化将代码分割到独立的文件中，每个文件有自己的作用域，通过 `import/export` 明确依赖关系。好处：

- 避免全局命名冲突
- 清晰的依赖关系
- 代码复用
- 按需加载

## 16.2 导出

```javascript
// math.js

// 命名导出（一个模块可以有多个）
export const PI = 3.14159265;

export function add(a, b) {
  return a + b;
}

export function multiply(a, b) {
  return a * b;
}

export class Calculator {
  // ...
}

// 也可以在底部统一导出
// export { PI, add, multiply, Calculator };
```

```javascript
// user.js

// 默认导出（一个模块只能有一个）
export default class User {
  constructor(name) {
    this.name = name;
  }
}
```

## 16.3 导入

```javascript
// 命名导入
import { add, multiply, PI } from './math.js';

// 重命名导入
import { add as sum } from './math.js';

// 导入全部（命名空间导入）
import * as math from './math.js';
math.add(1, 2);
math.PI;

// 默认导入（名字可以随便取）
import User from './user.js';

// 同时导入默认和命名
import User, { add, PI } from './module.js';
```

## 16.4 模块的特性

```html
<script type="module" src="app.js"></script>
```

| 特性 | 说明 |
|------|------|
| 自动严格模式 | 模块代码自动在严格模式下运行 |
| 独立作用域 | 每个模块有独立的顶层作用域 |
| 只执行一次 | 多次 import 同一模块，代码只执行一次 |
| 延迟执行 | 模块脚本默认 `defer`（等待 HTML 解析完毕） |
| `this` 为 `undefined` | 模块顶层的 `this` 不是 `window` |

## 16.5 动态导入

`import()` 表达式可以在运行时按需加载模块：

```javascript
// 按需加载
async function loadModule() {
  let module = await import('./heavy-module.js');
  module.doSomething();
}

// 条件加载
if (needsChart) {
  let { Chart } = await import('./chart.js');
  new Chart(data);
}

// 路由懒加载
let routes = {
  '/home': () => import('./pages/home.js'),
  '/about': () => import('./pages/about.js'),
};

async function navigate(path) {
  let module = await routes[path]();
  module.render();
}
```

## 16.6 重新导出

```javascript
// index.js（模块入口文件，汇总导出）
export { User } from './user.js';
export { Post } from './post.js';
export { default as Config } from './config.js';

// 导出全部
export * from './utils.js';
```

---

# 第十七章 其他重要特性

## 17.1 Proxy

`Proxy` 可以拦截对象的底层操作（读取、赋值、删除、函数调用等），实现自定义行为：

```javascript
let user = { name: '张三', age: 25 };

let proxy = new Proxy(user, {
  get(target, prop) {
    return prop in target ? target[prop] : `属性 ${prop} 不存在`;
  },

  set(target, prop, value) {
    if (prop === 'age' && typeof value !== 'number') {
      throw new TypeError('age 必须是数字');
    }
    target[prop] = value;
    return true;
  },

  deleteProperty(target, prop) {
    if (prop === 'name') {
      throw new Error('不允许删除 name');
    }
    delete target[prop];
    return true;
  }
});

proxy.name;       // "张三"
proxy.email;      // "属性 email 不存在"
proxy.age = 30;   // ✅
proxy.age = '30'; // ❌ TypeError
delete proxy.name; // ❌ Error
```

**常用 trap（拦截器）：**

| Trap | 拦截操作 |
|------|----------|
| `get(target, prop)` | 读取属性 |
| `set(target, prop, value)` | 设置属性 |
| `has(target, prop)` | `in` 运算符 |
| `deleteProperty(target, prop)` | `delete` 运算符 |
| `apply(target, thisArg, args)` | 函数调用 |
| `construct(target, args)` | `new` 运算符 |
| `ownKeys(target)` | `Object.keys` 等 |

## 17.2 Reflect

`Reflect` 提供与 Proxy trap 一一对应的静态方法，用于执行原始操作：

```javascript
let proxy = new Proxy(target, {
  get(target, prop, receiver) {
    console.log(`读取 ${prop}`);
    return Reflect.get(target, prop, receiver); // 委托给默认行为
  }
});
```

## 17.3 Symbol 进阶

Symbol 除了创建唯一键，还有一系列**内置 Symbol**，用于自定义对象的行为：

```javascript
// Symbol.iterator — 让对象可迭代
let range = {
  from: 1, to: 3,
  [Symbol.iterator]() {
    let current = this.from;
    let last = this.to;
    return {
      next() {
        return current <= last
          ? { value: current++, done: false }
          : { done: true };
      }
    };
  }
};

// Symbol.toPrimitive — 自定义类型转换
let money = {
  amount: 100,
  currency: 'CNY',
  [Symbol.toPrimitive](hint) {
    if (hint === 'number') return this.amount;
    if (hint === 'string') return `${this.amount} ${this.currency}`;
    return this.amount;
  }
};
+money;      // 100
`${money}`;  // "100 CNY"

// Symbol.for — 全局 Symbol 注册表
let id1 = Symbol.for('id');
let id2 = Symbol.for('id');
id1 === id2; // true（同名共享）
```

## 17.4 柯里化

**柯里化**将 `f(a, b, c)` 转换为 `f(a)(b)(c)`：

```javascript
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...moreArgs) {
      return curried.apply(this, [...args, ...moreArgs]);
    };
  };
}

// 使用
function add(a, b, c) {
  return a + b + c;
}

let curriedAdd = curry(add);
curriedAdd(1)(2)(3);     // 6
curriedAdd(1, 2)(3);     // 6
curriedAdd(1)(2, 3);     // 6

// 实用场景：创建偏函数
let addTo10 = curriedAdd(10);
addTo10(5)(3); // 18
```

## 17.5 BigInt

用于表示任意精度的整数（ES2020）：

```javascript
// 超出 Number 安全范围
Number.MAX_SAFE_INTEGER;          // 9007199254740991
9007199254740991 + 2;             // 9007199254740992（精度丢失！）

// BigInt 没有精度限制
let big = 9007199254740991n + 2n; // 9007199254740993n

// 创建方式
let a = 123456789012345678901234567890n; // 字面量（后缀 n）
let b = BigInt("123456789012345678901234567890"); // 构造函数

// 注意事项
// BigInt 不能与 Number 混合运算
2n + 3;  // ❌ TypeError
2n + 3n; // ✅ 5n

// 比较可以混合
2n === 2;  // false（类型不同）
2n == 2;   // true（值相同）
2n > 1;    // true

// 不能用于 Math 方法
Math.max(1n, 2n); // ❌ TypeError
```

## 17.6 eval

`eval` 执行字符串形式的代码（**极度不推荐使用**）：

```javascript
let code = '1 + 2';
eval(code); // 3

// 安全风险：永远不要 eval 用户输入
// 性能问题：引擎无法优化 eval 代码
// 替代方案：JSON.parse、new Function 等
```

## 17.7 Unicode 与字符串内部

```javascript
// JavaScript 字符串使用 UTF-16 编码
'A'.codePointAt(0);    // 65
String.fromCodePoint(65); // "A"

// emoji 等字符占用两个"码元"（surrogate pair）
'😀'.length;           // 2（不是 1！）
[...'😀'].length;      // 1（展开运算符正确处理）

// 遍历包含 emoji 的字符串
for (let char of '你好😀') {
  console.log(char); // "你", "好", "😀"（正确）
}
```

---

> **参考资料：**
> - [现代 JavaScript 教程](https://zh.javascript.info/)
> - [MDN Web Docs — JavaScript](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)
> - [ECMAScript 规范](https://tc39.es/ecma262/)
