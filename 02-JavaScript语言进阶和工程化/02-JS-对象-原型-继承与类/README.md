# JS 对象、原型和类
> **基于 [现代 JavaScript 教程](https://zh.javascript.info/) 与 [MDN Web Docs](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object) 编写**
> 彻底搞懂 JavaScript 面向对象的核心三支柱：对象机制、原型链继承、ES6 类语法。

---

# 第一章 对象基础

## 1.1 什么是对象

对象是 JavaScript 的核心。它是**键值对**的集合，用于存储一组相关的数据和功能。

原始类型（number、string、boolean 等）只能保存单个值，而对象可以存储复杂的实体。

```javascript
let user = {
  name: '张三',          // 键(key): 值(value)
  age: 25,
  isAdmin: false,
  'likes birds': true,  // 多词属性键需要引号
};
```

## 1.2 属性的读取与设置

```javascript
// 点运算符（属性名是合法标识符时）
user.name;           // "张三"
user.age = 26;       // 修改

// 方括号运算符（任意字符串键、变量键）
user['likes birds']; // true
let key = 'name';
user[key];           // "张三"

// 添加属性
user.email = 'zhangsan@example.com';

// 删除属性
delete user.isAdmin;
```

## 1.3 计算属性

```javascript
let prop = 'age';

let user = {
  name: '张三',
  [prop]: 25,              // 等同于 age: 25
  ['say' + 'Hi']() {      // 方法名也可以计算
    return 'Hi!';
  }
};
```

## 1.4 属性值简写

当变量名与属性名相同时，可以简写：

```javascript
let name = '张三';
let age = 25;

// 等同于 { name: name, age: age }
let user = { name, age };
```

## 1.5 属性存在性检查

```javascript
let user = { name: '张三', age: undefined };

// in 运算符（推荐，能正确处理值为 undefined 的属性）
'name' in user;       // true
'email' in user;      // false
'age' in user;        // true

// 与 undefined 比较（不可靠）
user.age !== undefined; // false — 但属性确实存在！
```

## 1.6 遍历对象

```javascript
let user = { name: '张三', age: 25, city: '北京' };

// for...in — 遍历所有可枚举属性（包括继承的）
for (let key in user) {
  console.log(`${key}: ${user[key]}`);
}

// Object.keys / values / entries — 只遍历自身属性
Object.keys(user);    // ["name", "age", "city"]
Object.values(user);  // ["张三", 25, "北京"]
Object.entries(user); // [["name","张三"], ["age",25], ["city","北京"]]

// 配合解构遍历
for (let [key, value] of Object.entries(user)) {
  console.log(`${key}: ${value}`);
}
```

## 1.7 属性的排列顺序

整数属性会按照数值排序，其他属性按**创建顺序**排列：

```javascript
let codes = {
  '49': '德国',
  '41': '瑞士',
  '44': '英国',
  '1': '美国',
};

Object.keys(codes); // ["1", "41", "44", "49"] — 按数字排序
```

```javascript
let user = {
  name: '张三',    // 非整数键
  age: 25,
  1: 'one',        // 整数键排在前面
};

Object.keys(user); // ["1", "name", "age"]
```

---

# 第二章 对象引用与复制

## 2.1 原始值 vs 对象：值复制 vs 引用复制

原始值是按**值**复制的，对象是按**引用**复制的：

```javascript
// 原始值 — 独立副本
let a = '你好';
let b = a;
b = '世界';
console.log(a); // "你好"（不受影响）

// 对象 — 共享引用
let obj1 = { name: '张三' };
let obj2 = obj1;        // obj2 和 obj1 指向同一个对象
obj2.name = '李四';
console.log(obj1.name); // "李四"（被修改了！）
```

## 2.2 引用比较

```javascript
let a = { x: 1 };
let b = a;
let c = { x: 1 };

a === b;  // true  — 同一个对象
a === c;  // false — 不同对象，即使内容相同
```

## 2.3 浅拷贝

浅拷贝只复制第一层属性，嵌套对象仍然是共享引用：

```javascript
let user = {
  name: '张三',
  sizes: { height: 180, weight: 70 }
};

// 方式一：展开运算符（推荐）
let clone1 = { ...user };

// 方式二：Object.assign
let clone2 = Object.assign({}, user);

// 浅拷贝的陷阱
clone1.name = '李四';
clone1.sizes.height = 170;

console.log(user.name);         // "张三"（独立）
console.log(user.sizes.height); // 170（被修改了！嵌套对象是共享的）
```

## 2.4 深拷贝

```javascript
let user = {
  name: '张三',
  birthday: new Date(2000, 0, 1),
  sizes: { height: 180, weight: 70 },
  tags: ['admin', 'user'],
};

// 方式一：structuredClone（推荐，现代标准）
let deepClone = structuredClone(user);
deepClone.sizes.height = 170;
console.log(user.sizes.height); // 180（原对象不受影响）

// 方式二：JSON 序列化（有局限性）
let jsonClone = JSON.parse(JSON.stringify(user));
// ⚠️ 丢失 Date 对象（变成字符串）、不支持 undefined、function、Symbol、循环引用
```

**`structuredClone` 支持：**
- 嵌套对象和数组
- 循环引用
- Date、Map、Set、ArrayBuffer、RegExp 等
- **不支持**：函数、DOM 节点、`Symbol` 属性

---

# 第三章 对象方法与 this

## 3.1 对象方法

对象的属性值可以是函数，称为**方法**：

```javascript
let user = {
  name: '张三',

  // 方法简写（推荐）
  greet() {
    console.log(`你好，我是 ${this.name}`);
  },

  // 完整写法（等价）
  greetFull: function() {
    console.log(`你好，我是 ${this.name}`);
  }
};

user.greet(); // "你好，我是 张三"
```

## 3.2 this 的核心规则

`this` 是 JavaScript 中最让人困惑的概念之一。**关键原则：`this` 的值在运行时根据调用方式决定，而不是在定义时。**

```javascript
let user = { name: '张三' };
let admin = { name: '管理员' };

function greet() {
  console.log(`你好，${this.name}`);
}

// 同一个函数，不同的调用对象 → 不同的 this
user.greet = greet;
admin.greet = greet;

user.greet();   // "你好，张三"   — this = user
admin.greet();  // "你好，管理员"  — this = admin
```

## 3.3 this 在不同调用方式下的值

| 调用方式 | this 的值 | 示例 |
|----------|-----------|------|
| 对象方法调用 | 点号前的对象 | `obj.method()` → `obj` |
| 普通函数调用 | `undefined`（严格模式）/ `window`（非严格） | `func()` |
| `new` 调用 | 新创建的对象 | `new Func()` |
| `call/apply/bind` | 手动指定的对象 | `func.call(obj)` |
| 箭头函数 | 继承外层的 `this` | 无自己的 `this` |

```javascript
"use strict";

function standalone() {
  console.log(this); // undefined（严格模式下）
}
standalone();

let user = {
  name: '张三',
  greet() {
    console.log(this.name);
  }
};

let fn = user.greet;
fn(); // ❌ TypeError — this 是 undefined，不是 user
```

## 3.4 "丢失 this" 问题

当方法被赋值给变量或传递给回调时，`this` 会丢失：

```javascript
let user = {
  name: '张三',
  greet() {
    console.log(`你好，${this.name}`);
  }
};

setTimeout(user.greet, 1000); // "你好，undefined" — this 丢失！

// 解决方案一：包装函数
setTimeout(() => user.greet(), 1000); // ✅

// 解决方案二：bind
setTimeout(user.greet.bind(user), 1000); // ✅
```

## 3.5 箭头函数没有 this

箭头函数不创建自己的 `this`，它从外层词法环境继承：

```javascript
let group = {
  title: 'A组',
  students: ['张三', '李四', '王五'],

  showList() {
    // 箭头函数中的 this 继承自 showList 的 this，即 group
    this.students.forEach(student => {
      console.log(`${this.title}: ${student}`);
    });
  }
};

group.showList();
// A组: 张三
// A组: 李四
// A组: 王五
```

> **何时用箭头函数：** 在回调中需要访问外层 `this` 时用箭头函数。**何时不用：** 需要自己的 `this`（如对象方法、构造函数）时用普通函数。

---

# 第四章 构造函数与 new

## 4.1 构造函数

构造函数是普通函数，但有两个约定：
1. 函数名首字母大写
2. 只通过 `new` 来调用

```javascript
function User(name, age) {
  // this = {} (隐式创建)
  this.name = name;
  this.age = age;
  this.greet = function() {
    return `我是 ${this.name}`;
  };
  // return this (隐式返回)
}

let user = new User('张三', 25);
user.name;    // "张三"
user.greet(); // "我是 张三"
```

## 4.2 new 的执行过程

`new User(...)` 做了四件事：

1. 创建一个新的空对象
2. 将新对象的 `[[Prototype]]` 设置为 `User.prototype`
3. 以新对象作为 `this` 执行构造函数
4. 如果构造函数返回对象则使用它，否则返回步骤 1 的新对象

```javascript
// 手动模拟 new 的过程
function myNew(Constructor, ...args) {
  let obj = Object.create(Constructor.prototype);  // 步骤 1-2
  let result = Constructor.apply(obj, args);        // 步骤 3
  return result instanceof Object ? result : obj;   // 步骤 4
}
```

## 4.3 构造函数的 return

- 返回对象 → 使用该对象（`this` 被丢弃）
- 返回原始值或不返回 → 使用 `this`

```javascript
function BigUser() {
  this.name = '张三';
  return { name: '大对象' }; // 返回对象，this 被丢弃
}
new BigUser().name; // "大对象"

function SmallUser() {
  this.name = '张三';
  return '我是字符串'; // 返回原始值，被忽略
}
new SmallUser().name; // "张三"
```

## 4.4 new.target

在函数内部可以用 `new.target` 检查是否通过 `new` 调用：

```javascript
function User(name) {
  if (!new.target) {
    return new User(name); // 自动补上 new
  }
  this.name = name;
}

let user = User('张三'); // 即使不用 new 也能正常工作
```

---

# 第五章 属性标志与描述符

## 5.1 属性的三个标志

对象属性除了值之外还有三个隐藏标志：

| 标志 | 说明 | 默认值（字面量创建时） |
|------|------|----------------------|
| `writable` | 能否修改属性值 | `true` |
| `enumerable` | 能否在 `for...in` / `Object.keys` 中列出 | `true` |
| `configurable` | 能否删除属性 / 修改标志 | `true` |

## 5.2 获取与设置描述符

```javascript
let user = { name: '张三' };

// 获取单个属性的描述符
let descriptor = Object.getOwnPropertyDescriptor(user, 'name');
// { value: "张三", writable: true, enumerable: true, configurable: true }

// 获取所有属性的描述符
Object.getOwnPropertyDescriptors(user);

// 修改属性标志
Object.defineProperty(user, 'name', {
  writable: false
});
user.name = '李四'; // 严格模式报错，非严格模式静默失败
user.name;          // "张三"

// 定义新属性（通过 defineProperty 创建的属性，标志默认为 false）
Object.defineProperty(user, 'id', {
  value: 42,
  // writable: false, enumerable: false, configurable: false（全是默认值）
});
```

## 5.3 不可配置属性

一旦 `configurable: false`，就**不可逆转**：

```javascript
let user = {};
Object.defineProperty(user, 'name', {
  value: '张三',
  writable: true,
  configurable: false
});

// 还可以修改值（因为 writable 仍为 true）
user.name = '李四';

// 但不能删除
delete user.name; // 失败

// 不能再修改 configurable
// 也不能将 enumerable 从 false 改为 true
// 唯一能做的：将 writable 从 true 改为 false（单向）
```

## 5.4 对象整体保护

```javascript
let config = { apiUrl: '/api', timeout: 3000 };

// Object.preventExtensions — 禁止添加新属性
Object.preventExtensions(config);

// Object.seal — 禁止添加/删除属性，所有属性 configurable: false
Object.seal(config);

// Object.freeze — 最严格，额外将所有属性设为 writable: false
Object.freeze(config);

// 检测
Object.isExtensible(config); // false
Object.isSealed(config);     // true
Object.isFrozen(config);     // true
```

> **注意：** 以上方法都是**浅层**的，嵌套对象不受影响。

---

# 第六章 Getter 与 Setter

## 6.1 访问器属性

对象属性分两种：**数据属性**（有 `value`）和**访问器属性**（有 `get/set`）。

```javascript
let user = {
  firstName: '三',
  lastName: '张',

  // getter — 读取 user.fullName 时调用
  get fullName() {
    return `${this.lastName}${this.firstName}`;
  },

  // setter — 赋值 user.fullName = '...' 时调用
  set fullName(value) {
    [this.lastName, ...rest] = value;
    this.firstName = rest.join('');
  }
};

// 使用时看起来像普通属性
user.fullName;            // "张三"
user.fullName = '李四';
user.lastName;            // "李"
user.firstName;           // "四"
```

## 6.2 用 defineProperty 定义访问器

```javascript
let user = { _age: 25 };

Object.defineProperty(user, 'age', {
  get() {
    return this._age;
  },
  set(value) {
    if (typeof value !== 'number' || value < 0 || value > 150) {
      throw new RangeError('年龄无效');
    }
    this._age = value;
  },
  enumerable: true,
  configurable: true,
});

user.age;      // 25
user.age = 30; // ✅
user.age = -5; // ❌ RangeError
```

## 6.3 访问器的实用场景

**兼容性包装：** 当需要重命名属性但保持旧代码兼容时：

```javascript
function User(name, birthday) {
  this.name = name;
  this.birthday = birthday;

  Object.defineProperty(this, 'age', {
    get() {
      let today = new Date();
      let age = today.getFullYear() - this.birthday.getFullYear();
      return age;
    }
  });
}

let user = new User('张三', new Date(2000, 0, 1));
user.age; // 26（自动计算）
```

---

# 第七章 Symbol

## 7.1 Symbol 是什么

`Symbol` 是 ES6 引入的**原始类型**，每个 Symbol 都是**全局唯一**的：

```javascript
let id1 = Symbol('id');
let id2 = Symbol('id');
id1 === id2;             // false（即使描述相同）
id1.toString();          // "Symbol(id)"
id1.description;         // "id"
```

## 7.2 Symbol 作为属性键

Symbol 属性不会出现在常规遍历中，具有"隐藏性"：

```javascript
let id = Symbol('id');

let user = {
  name: '张三',
  [id]: 42
};

user[id];                // 42
Object.keys(user);       // ["name"]（不含 Symbol）
JSON.stringify(user);    // '{"name":"张三"}'（不含 Symbol）

// 获取 Symbol 属性
Object.getOwnPropertySymbols(user); // [Symbol(id)]
Reflect.ownKeys(user);              // ["name", Symbol(id)]
```

**使用场景：** 当多个库/模块操作同一对象时，Symbol 属性可以避免命名冲突。

## 7.3 全局 Symbol

`Symbol.for(key)` 在全局注册表中查找/创建 Symbol，实现跨模块共享：

```javascript
let id1 = Symbol.for('app.id'); // 创建
let id2 = Symbol.for('app.id'); // 从注册表取回
id1 === id2;                     // true

Symbol.keyFor(id1);              // "app.id"（反向查找）
```

## 7.4 内置 Symbol（Well-known Symbols）

JavaScript 定义了一系列内置 Symbol，用于自定义对象的内部行为：

| Symbol | 用途 |
|--------|------|
| `Symbol.iterator` | 使对象可被 `for...of` 遍历 |
| `Symbol.toPrimitive` | 自定义对象→原始值转换 |
| `Symbol.hasInstance` | 自定义 `instanceof` 行为 |
| `Symbol.toStringTag` | 自定义 `Object.prototype.toString` 的标签 |
| `Symbol.species` | 控制衍生对象的构造函数 |

```javascript
let price = {
  amount: 99.5,
  currency: 'CNY',

  [Symbol.toPrimitive](hint) {
    if (hint === 'number') return this.amount;
    if (hint === 'string') return `${this.amount} ${this.currency}`;
    return this.amount; // default
  }
};

+price;       // 99.5
`${price}`;   // "99.5 CNY"
price + 0;    // 99.5
```

---

# 第八章 原型继承

## 8.1 [[Prototype]] —— 隐藏的链接

每个 JavaScript 对象都有一个隐藏属性 `[[Prototype]]`，它指向另一个对象（或 `null`）。当访问一个属性时，如果对象自身没有，引擎会沿着 `[[Prototype]]` 链向上查找：

```javascript
let animal = {
  eats: true,
  walk() {
    console.log('走路');
  }
};

let rabbit = {
  jumps: true,
  __proto__: animal  // 设置原型（历史遗留的 getter/setter）
};

rabbit.jumps;   // true（自身属性）
rabbit.eats;    // true（从 animal 继承）
rabbit.walk();  // "走路"（从 animal 继承）
```

## 8.2 原型链

原型链可以有多层：

```javascript
let animal = { eats: true };

let rabbit = {
  jumps: true,
  __proto__: animal
};

let longEar = {
  earLength: 10,
  __proto__: rabbit
};

longEar.eats;   // true（沿链: longEar → rabbit → animal）
longEar.jumps;  // true（沿链: longEar → rabbit）
```

```
longEar  →  rabbit  →  animal  →  Object.prototype  →  null
```

**两个限制：**
1. 不能形成闭环
2. `__proto__` 的值只能是对象或 `null`

## 8.3 写操作不走原型

**读取**属性会沿原型链查找，但**写入/删除**操作直接作用于对象自身：

```javascript
let animal = {
  walk() {
    console.log('Animal walk');
  }
};

let rabbit = { __proto__: animal };

rabbit.walk = function() {
  console.log('Rabbit bounce!');
};

rabbit.walk(); // "Rabbit bounce!"（自身属性，不走原型）
animal.walk(); // "Animal walk"（原型不受影响）
```

**例外：** 访问器属性（getter/setter）由于是函数调用，赋值会触发 setter：

```javascript
let user = {
  _name: '张三',
  get name() { return this._name; },
  set name(value) { this._name = value; }
};

let admin = { __proto__: user, isAdmin: true };

admin.name = '管理员'; // 触发 user 的 setter，但 this = admin
admin._name;           // "管理员"（写入 admin 自身）
user._name;            // "张三"（user 不受影响）
```

## 8.4 this 始终指向调用者

无论方法在原型链哪一层被找到，`this` 始终是**点号前面的对象**：

```javascript
let animal = {
  sleep() {
    this.isSleeping = true;
  }
};

let rabbit = { __proto__: animal, name: '小白' };

rabbit.sleep();

rabbit.isSleeping;  // true（写入 rabbit）
animal.isSleeping;  // undefined（animal 没有这个属性）
```

> **核心理解：** 原型提供方法（共享），每个对象保管自己的状态（独立）。

## 8.5 for...in 与 hasOwnProperty

```javascript
let animal = { eats: true };
let rabbit = { jumps: true, __proto__: animal };

// for...in 遍历自身 + 继承的可枚举属性
for (let key in rabbit) {
  console.log(key); // jumps, eats
}

// 区分自身与继承
for (let key in rabbit) {
  if (rabbit.hasOwnProperty(key)) {
    console.log(`自身: ${key}`);    // jumps
  } else {
    console.log(`继承: ${key}`);    // eats
  }
}

// Object.keys 只返回自身的可枚举属性
Object.keys(rabbit); // ["jumps"]
```

## 8.6 经典陷阱：共享引用类型属性

```javascript
let hamster = {
  stomach: [],
  eat(food) {
    this.stomach.push(food); // ⚠️ this.stomach 从原型找到，push 修改了共享数组
  }
};

let speedy = { __proto__: hamster };
let lazy = { __proto__: hamster };

speedy.eat('苹果');
lazy.stomach; // ["苹果"]！两只仓鼠共享了同一个 stomach

// 修复方案一：赋值而非修改
let hamster = {
  stomach: [],
  eat(food) {
    this.stomach = [...this.stomach, food]; // 赋值会写入自身
  }
};

// 修复方案二：每个对象拥有自己的 stomach
let speedy = { __proto__: hamster, stomach: [] };
let lazy = { __proto__: hamster, stomach: [] };
```

> **原则：** 描述对象**状态**的属性应该直接定义在对象自身，不要依赖原型中的引用类型。

---

# 第九章 F.prototype —— 构造函数的原型

## 9.1 构造函数的 prototype 属性

每个函数都有一个 `prototype` 属性（默认是 `{ constructor: F }`）。当用 `new F()` 创建对象时，新对象的 `[[Prototype]]` 会被设为 `F.prototype`：

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(`${this.name} 在吃东西`);
};

let cat = new Animal('小猫');

// cat 的原型链
Object.getPrototypeOf(cat) === Animal.prototype; // true
cat.eat(); // "小猫 在吃东西"（从 Animal.prototype 获取）
```

```
cat  →  Animal.prototype  →  Object.prototype  →  null
          { constructor: Animal, eat: ƒ }
```

## 9.2 constructor 属性

`F.prototype` 默认有一个 `constructor` 属性指回函数本身：

```javascript
function User(name) {
  this.name = name;
}

User.prototype.constructor === User; // true

let user = new User('张三');
user.constructor === User;            // true（继承自 User.prototype）

// 通过 constructor 创建同类对象
let user2 = new user.constructor('李四');
```

## 9.3 覆盖 prototype 的陷阱

```javascript
function User(name) {
  this.name = name;
}

// ❌ 整体替换 prototype，constructor 丢失
User.prototype = {
  greet() { return `你好，${this.name}`; }
};

let user = new User('张三');
user.constructor === User; // false！constructor 指向 Object

// ✅ 正确做法：添加方法而非替换
User.prototype.greet = function() {
  return `你好，${this.name}`;
};

// ✅ 或者替换时手动保留 constructor
User.prototype = {
  constructor: User,
  greet() { return `你好，${this.name}`; }
};
```

---

# 第十章 原生原型

## 10.1 内置类型的原型链

JavaScript 所有内置类型都通过原型链共享方法：

```
let arr = [1, 2, 3];

arr  →  Array.prototype    →  Object.prototype  →  null
         push, map,             toString,
         filter, reduce...      hasOwnProperty...

let str = "hello";

str  →  String.prototype   →  Object.prototype  →  null
         slice, trim,
         toUpperCase...

let num = 42;

num  →  Number.prototype   →  Object.prototype  →  null
         toFixed,
         toString...

let fn = function() {};

fn   →  Function.prototype →  Object.prototype  →  null
         call, apply,
         bind...
```

## 10.2 验证原型链

```javascript
let arr = [1, 2, 3];

arr.__proto__ === Array.prototype;                // true
arr.__proto__.__proto__ === Object.prototype;      // true
arr.__proto__.__proto__.__proto__ === null;         // true

// arr 可以使用 Array.prototype 的方法
arr.map(x => x * 2); // [2, 4, 6]

// 也可以使用 Object.prototype 的方法
arr.hasOwnProperty('length'); // true
arr.toString(); // "1,2,3"（Array.prototype.toString 覆盖了 Object 的版本）
```

## 10.3 原始值如何访问方法

原始值（string、number、boolean）本身不是对象，但当你访问它们的方法时，引擎会临时创建一个**包装对象**：

```javascript
'hello'.toUpperCase();
// 等同于：
// let temp = new String('hello');
// temp.toUpperCase();
// 然后 temp 被销毁
```

> `null` 和 `undefined` 没有包装对象，没有原型，也没有任何方法。

## 10.4 不要修改原生原型

```javascript
// ❌ 千万不要这样做（会影响所有字符串）
String.prototype.repeat = function(n) {
  return new Array(n + 1).join(this);
};

// 唯一可接受的场景：polyfill
if (!String.prototype.includes) {
  String.prototype.includes = function(search) {
    return this.indexOf(search) !== -1;
  };
}
```

## 10.5 从原型借用方法

```javascript
// 类数组对象没有数组方法
let arrayLike = { 0: 'a', 1: 'b', length: 2 };

// 借用 Array.prototype 的方法
let result = Array.prototype.join.call(arrayLike, ', ');
// "a, b"

// 更现代的写法
Array.from(arrayLike).join(', ');
```

---

# 第十一章 原型方法与无原型对象

## 11.1 现代原型操作方法

`__proto__` 是历史遗留的访问方式，现代代码应使用：

```javascript
// 获取原型
Object.getPrototypeOf(obj);

// 设置原型
Object.setPrototypeOf(obj, proto);

// 以指定原型创建新对象（推荐）
let rabbit = Object.create(animal, {
  jumps: {
    value: true,
    writable: true,
    enumerable: true,
    configurable: true
  }
});
```

## 11.2 Object.create 的妙用

**精确克隆对象（包含属性描述符和原型）：**

```javascript
let exactClone = Object.create(
  Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj)
);
```

**创建原型链：**

```javascript
let animal = {
  eats: true,
  walk() { console.log('走路'); }
};

let rabbit = Object.create(animal);
rabbit.jumps = true;

Object.getPrototypeOf(rabbit) === animal; // true
```

## 11.3 "纯字典"对象

```javascript
// 普通对象从 Object.prototype 继承了 toString、hasOwnProperty 等
let dict = {};
dict['__proto__']; // [Object: null prototype] {}（不是你想要的值）

// 创建没有原型的"纯净"对象
let safeDict = Object.create(null);
safeDict['__proto__'] = '自定义值';
safeDict['__proto__']; // "自定义值"（完全安全）

// 纯字典对象不继承任何方法
safeDict.toString; // undefined
// 需要用 Object.keys 而不是 for...in
Object.keys(safeDict); // ["__proto__"]
```

**使用场景：** 当对象作为纯粹的键值存储（如缓存、配置映射）时，使用 `Object.create(null)` 避免键名与继承属性冲突。

---

# 第十二章 Class 基本语法

## 12.1 class 声明

```javascript
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `你好，我是 ${this.name}`;
  }

  get info() {
    return `${this.name} (${this.age}岁)`;
  }

  set nickname(value) {
    if (value.length < 2) throw new Error('昵称太短');
    this._nickname = value;
  }
}

let user = new User('张三', 25);
user.greet(); // "你好，我是 张三"
user.info;    // "张三 (25岁)"
```

## 12.2 class 的真面目

`class` 本质上是函数+原型的语法糖：

```javascript
typeof User; // "function"
User === User.prototype.constructor; // true

// class User { ... } 做了什么：
// 1. 创建函数 User，代码来自 constructor
// 2. 将方法存入 User.prototype
// 3. 方法默认 enumerable: false
```

```javascript
// 用构造函数等价实现
function User(name, age) {
  this.name = name;
  this.age = age;
}
User.prototype.greet = function() {
  return `你好，我是 ${this.name}`;
};
```

## 12.3 class 不只是语法糖

`class` 与手写构造函数有几个重要区别：

| 区别 | class | 构造函数 |
|------|-------|----------|
| 不用 `new` 调用 | 报错 | 不报错（this 可能指向 window） |
| 方法可枚举 | `enumerable: false` | `enumerable: true` |
| 严格模式 | 自动启用 | 需要手动 `"use strict"` |
| 提升 | 不提升（暂时性死区） | 函数声明会提升 |

```javascript
// 必须用 new
User(); // ❌ TypeError: Class constructor User cannot be invoked without 'new'

// 不提升
let u = new User(); // ❌ ReferenceError
class User {}
```

## 12.4 类表达式

```javascript
// 匿名类表达式
let User = class {
  greet() { return '你好'; }
};

// 命名类表达式（名字仅类内部可见）
let User = class MyClass {
  greet() { return MyClass.name; } // "MyClass"
};
typeof MyClass; // "undefined"（外部不可见）

// 动态创建类
function createClass(greeting) {
  return class {
    greet() { return greeting; }
  };
}
let ChineseUser = createClass('你好');
new ChineseUser().greet(); // "你好"
```

## 12.5 类字段

类字段声明的属性会被设在**实例对象**上（不是 prototype）：

```javascript
class User {
  name = '默认用户';    // 类字段
  role = 'user';

  constructor(name) {
    if (name) this.name = name;
  }
}

let user = new User('张三');
user.name;                 // "张三"
User.prototype.name;       // undefined（不在原型上）
```

## 12.6 用类字段解决 this 丢失

```javascript
class Button {
  constructor(value) {
    this.value = value;
  }

  // 类字段 + 箭头函数 = 每个实例独立的方法，this 永远正确
  click = () => {
    console.log(this.value);
  }
}

let button = new Button('提交');
setTimeout(button.click, 1000); // "提交"（this 不会丢失）
```

---

# 第十三章 类继承

## 13.1 extends

```javascript
class Animal {
  constructor(name) {
    this.name = name;
    this.speed = 0;
  }

  run(speed) {
    this.speed = speed;
    console.log(`${this.name} 以 ${speed} 速度奔跑`);
  }

  stop() {
    this.speed = 0;
    console.log(`${this.name} 停下了`);
  }
}

class Rabbit extends Animal {
  hide() {
    console.log(`${this.name} 躲起来了`);
  }
}

let rabbit = new Rabbit('小白');
rabbit.run(5);  // "小白 以 5 速度奔跑"（继承自 Animal）
rabbit.hide();  // "小白 躲起来了"（自身方法）
```

`extends` 在原型链上做了什么：

```
rabbit  →  Rabbit.prototype  →  Animal.prototype  →  Object.prototype  →  null
```

```javascript
// extends 后面可以是任意表达式
class Cat extends Animal {}

// 甚至可以是函数调用
function getBaseClass() {
  return Animal;
}
class Dog extends getBaseClass() {}
```

## 13.2 方法重写与 super

```javascript
class Rabbit extends Animal {
  constructor(name, earLength) {
    super(name);            // 必须在使用 this 之前调用
    this.earLength = earLength;
  }

  stop() {
    super.stop();           // 调用父类的 stop
    this.hide();
  }

  hide() {
    console.log(`${this.name} 躲起来了`);
  }
}

let rabbit = new Rabbit('小白', 10);
rabbit.stop();
// "小白 停下了"（来自 super.stop()）
// "小白 躲起来了"（来自 this.hide()）
```

## 13.3 constructor 的必须规则

**子类如果定义了 `constructor`，必须在使用 `this` 之前调用 `super()`：**

```javascript
class Rabbit extends Animal {
  constructor(name, earLength) {
    // this.earLength = earLength; // ❌ ReferenceError！
    super(name);                   // ✅ 先调用 super
    this.earLength = earLength;    // ✅ 然后才能用 this
  }
}
```

如果子类没有定义 `constructor`，会自动生成：

```javascript
class Rabbit extends Animal {
  // 隐式生成：
  // constructor(...args) {
  //   super(...args);
  // }
}
```

## 13.4 箭头函数中的 super

箭头函数没有自己的 `super`，它从外层函数获取：

```javascript
class Rabbit extends Animal {
  stop() {
    setTimeout(() => super.stop(), 1000); // ✅ 箭头函数继承外层 super
  }
}
```

---

# 第十四章 静态属性与方法

## 14.1 static 方法

静态方法属于**类本身**，不属于实例：

```javascript
class MathHelper {
  static add(a, b) {
    return a + b;
  }

  static isPositive(n) {
    return n > 0;
  }
}

MathHelper.add(2, 3);     // 5
MathHelper.isPositive(-1); // false

let m = new MathHelper();
m.add; // undefined（实例没有静态方法）
```

## 14.2 静态方法的实际用途

**工厂方法：**

```javascript
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  static fromJSON(json) {
    let data = JSON.parse(json);
    return new User(data.name, data.age);
  }

  static compare(a, b) {
    return a.age - b.age;
  }
}

let user = User.fromJSON('{"name":"张三","age":25}');
let users = [new User('张三', 30), new User('李四', 20)];
users.sort(User.compare);
```

## 14.3 static 属性

```javascript
class Config {
  static defaultTimeout = 3000;
  static apiUrl = '/api';
  static version = '1.0.0';
}

Config.defaultTimeout; // 3000
```

## 14.4 静态成员的继承

静态方法和属性会被子类继承：

```javascript
class Animal {
  static planet = '地球';

  static create(name) {
    return new this(name); // this 指向调用者（Animal 或其子类）
  }

  constructor(name) {
    this.name = name;
  }
}

class Rabbit extends Animal {}

Rabbit.planet;              // "地球"（继承自 Animal）
let rabbit = Rabbit.create('小白'); // Rabbit { name: '小白' }
rabbit instanceof Rabbit;  // true
```

这是因为 `extends` 不仅连接了 `Rabbit.prototype → Animal.prototype`，还连接了 `Rabbit → Animal`（静态方法的原型链）。

---

# 第十五章 私有与受保护的属性

## 15.1 受保护的属性（约定）

以 `_` 开头的属性是一种**命名约定**，表示"仅供内部使用"。JavaScript 不强制执行，但所有开发者都理解这个约定：

```javascript
class CoffeeMachine {
  _waterAmount = 0;

  set waterAmount(value) {
    if (value < 0) throw new Error('水量不能为负');
    this._waterAmount = value;
  }

  get waterAmount() {
    return this._waterAmount;
  }

  constructor(power) {
    this._power = power;
  }

  get power() {
    return this._power; // 只读：只有 getter 没有 setter
  }
}
```

## 15.2 私有属性（语言级别）

ES2022 引入了 `#` 前缀的**真正私有**字段：

```javascript
class BankAccount {
  #balance = 0;         // 私有属性
  #transactionLog = []; // 私有属性

  constructor(owner, initialBalance) {
    this.owner = owner;
    this.#balance = initialBalance;
  }

  get balance() {
    return this.#balance;
  }

  deposit(amount) {
    this.#validateAmount(amount);
    this.#balance += amount;
    this.#log('存入', amount);
  }

  withdraw(amount) {
    this.#validateAmount(amount);
    if (amount > this.#balance) throw new Error('余额不足');
    this.#balance -= amount;
    this.#log('取出', amount);
  }

  #validateAmount(amount) {  // 私有方法
    if (typeof amount !== 'number' || amount <= 0) {
      throw new TypeError('金额无效');
    }
  }

  #log(type, amount) {     // 私有方法
    this.#transactionLog.push({ type, amount, date: new Date() });
  }

  getHistory() {
    return [...this.#transactionLog]; // 返回副本
  }
}

let account = new BankAccount('张三', 1000);
account.deposit(500);
account.balance;        // 1500
account.#balance;       // ❌ SyntaxError（外部无法访问）
account.#validateAmount; // ❌ SyntaxError
```

## 15.3 私有 vs 受保护的对比

| 特性 | `_` 受保护（约定） | `#` 私有（语言级） |
|------|-------------------|-------------------|
| 外部访问 | 可以（仅靠约定） | 不可以（语法错误） |
| 子类访问 | 可以 | **不可以** |
| `for...in` 可见 | 是 | 否 |
| 性能 | 无开销 | 极小开销 |

```javascript
class Parent {
  #secret = '秘密';
  _convention = '约定';
}

class Child extends Parent {
  tryAccess() {
    // this.#secret;    // ❌ SyntaxError
    this._convention;   // ✅ "约定"
  }
}
```

---

# 第十六章 类型检查与 Mixin

## 16.1 instanceof

```javascript
class Animal {}
class Rabbit extends Animal {}

let rabbit = new Rabbit();

rabbit instanceof Rabbit;  // true
rabbit instanceof Animal;  // true
rabbit instanceof Object;  // true
```

`instanceof` 沿原型链检查 `Constructor.prototype` 是否存在于对象的原型链上。

## 16.2 自定义 instanceof 行为

```javascript
class PowerArray extends Array {
  static [Symbol.hasInstance](instance) {
    return Array.isArray(instance);
  }
}

[] instanceof PowerArray; // true
```

## 16.3 Object.prototype.toString 的类型标签

```javascript
let s = Object.prototype.toString;

s.call(123);            // "[object Number]"
s.call('hello');        // "[object String]"
s.call(true);           // "[object Boolean]"
s.call(null);           // "[object Null]"
s.call(undefined);      // "[object Undefined]"
s.call([1, 2]);         // "[object Array]"
s.call(new Map());      // "[object Map]"

// 自定义标签
class MyClass {
  get [Symbol.toStringTag]() {
    return 'MyClass';
  }
}
s.call(new MyClass());  // "[object MyClass]"
```

## 16.4 各类型检查方式对比

| 方法 | 适用场景 | 返回 |
|------|----------|------|
| `typeof` | 原始类型 | 字符串（`"number"`等） |
| `instanceof` | 对象的类层次 | 布尔值 |
| `Array.isArray()` | 检查数组 | 布尔值 |
| `Object.prototype.toString.call()` | 万能方案 | `"[object Type]"` |

## 16.5 Mixin —— 多继承的替代

JavaScript 不支持多重继承，但 Mixin 模式可以将方法"混入"任意类：

```javascript
// Mixin：可序列化
let serializeMixin = {
  serialize() {
    return JSON.stringify(this);
  },
  deserialize(json) {
    return Object.assign(this, JSON.parse(json));
  }
};

// Mixin：事件系统
let eventMixin = {
  _handlers: null,

  on(event, handler) {
    if (!this._handlers) this._handlers = {};
    if (!this._handlers[event]) this._handlers[event] = [];
    this._handlers[event].push(handler);
    return this;
  },

  off(event, handler) {
    let handlers = this._handlers?.[event];
    if (handlers) {
      this._handlers[event] = handlers.filter(h => h !== handler);
    }
    return this;
  },

  emit(event, ...args) {
    this._handlers?.[event]?.forEach(handler => handler.apply(this, args));
    return this;
  }
};

// 将 Mixin 应用到类
class User {
  constructor(name) {
    this.name = name;
  }
}

Object.assign(User.prototype, serializeMixin, eventMixin);

let user = new User('张三');

user.on('login', function() {
  console.log(`${this.name} 登录了`);
});

user.emit('login');        // "张三 登录了"
user.serialize();          // '{"name":"张三"}'
```

## 16.6 Mixin 的继承

Mixin 之间也可以继承：

```javascript
let baseMixin = {
  log(message) {
    console.log(`[${this.constructor.name}] ${message}`);
  }
};

let advancedMixin = {
  __proto__: baseMixin,

  logError(message) {
    this.log(`ERROR: ${message}`);
  }
};

class Service {}
Object.assign(Service.prototype, advancedMixin);

let service = new Service();
service.logError('连接失败'); // "[Service] ERROR: 连接失败"
```

---

> **参考资料：**
> - [现代 JavaScript 教程 — 对象基础](https://zh.javascript.info/object-basics)
> - [现代 JavaScript 教程 — 原型与继承](https://zh.javascript.info/prototypes)
> - [现代 JavaScript 教程 — 类](https://zh.javascript.info/classes)
> - [MDN — Object](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object)
> - [MDN — Classes](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes)
> - [MDN — 继承与原型链](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
