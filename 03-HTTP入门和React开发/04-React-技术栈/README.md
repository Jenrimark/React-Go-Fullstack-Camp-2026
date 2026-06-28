# 现代 React 参考教程
> **基于 [React 官方文档](https://react.dev/) 编写**
> 以 React 19 为基准，从零开始系统掌握 React 组件化 UI 开发。

---

# 目录

- [第一章 React 简介与环境搭建](#第一章-react-简介与环境搭建)
- [第二章 JSX 语法](#第二章-jsx-语法)
- [第三章 组件基础](#第三章-组件基础)
- [第四章 Props 与数据流](#第四章-props-与数据流)
- [第五章 State 与事件处理](#第五章-state-与事件处理)
- [第六章 条件渲染与列表](#第六章-条件渲染与列表)
- [第七章 State 管理进阶](#第七章-state-管理进阶)
- [第八章 副作用与 useEffect](#第八章-副作用与-useeffect)
- [第九章 Ref 与 DOM 操作](#第九章-ref-与-dom-操作)
- [第十章 Context 与跨层级通信](#第十章-context-与跨层级通信)
- [第十一章 useReducer 与复杂状态逻辑](#第十一章-usereducer-与复杂状态逻辑)
- [第十二章 自定义 Hook](#第十二章-自定义-hook)
- [第十三章 性能优化](#第十三章-性能优化)
- [第十四章 表单处理](#第十四章-表单处理)
- [第十五章 组件设计模式](#第十五章-组件设计模式)
- [第十六章 React 19 新特性](#第十六章-react-19-新特性)
- [第十七章 React 生态与工程化](#第十七章-react-生态与工程化)
- [附录 速查表](#附录-速查表)

---

# 第一章 React 简介与环境搭建

## 1.1 React 是什么

React 是由 Meta（原 Facebook）开发并开源的 **用于构建用户界面的 JavaScript 库**。它的核心理念是：

| 核心理念 | 说明 |
|----------|------|
| **组件化** | UI 被拆分成独立、可复用的组件，每个组件管理自己的状态和渲染逻辑 |
| **声明式** | 你只需描述 UI 应该「长什么样」，React 负责高效地更新 DOM |
| **单向数据流** | 数据从父组件通过 props 流向子组件，使数据流动可预测、易于调试 |
| **虚拟 DOM** | React 在内存中维护一棵虚拟 DOM 树，通过 diff 算法最小化真实 DOM 操作 |

## 1.2 React 的发展历程

| 版本 | 年份 | 关键特性 |
|------|------|----------|
| React 0.x | 2013 | 首次开源，`createClass` API |
| React 15 | 2016 | 稳定版本，Stack Reconciler |
| **React 16** | 2017 | Fiber 架构、Error Boundaries、Portals、`Fragment` |
| React 16.8 | 2019 | **Hooks** 正式发布 —— 里程碑式更新 |
| **React 18** | 2022 | 并发渲染、`useTransition`、`useDeferredValue`、自动批处理 |
| **React 19** | 2024 | `use` Hook、Server Components、Actions、`useFormStatus`、`useOptimistic` |

本教程以 **React 19** 为基准，全面使用函数组件 + Hooks 编写。

## 1.3 环境搭建

### 方式一：使用框架（推荐）

React 官方推荐使用全栈框架来启动新项目：

```bash
# Next.js（最流行的 React 框架）
npx create-next-app@latest my-app

# Remix
npx create-remix@latest

# Gatsby（静态站点生成）
npx gatsby new my-site
```

### 方式二：使用 Vite 创建纯客户端应用

```bash
npm create vite@latest my-react-app -- --template react

cd my-react-app
npm install
npm run dev
```

项目结构：

```
my-react-app/
├── index.html          # 入口 HTML
├── package.json
├── vite.config.js      # Vite 配置
├── public/             # 静态资源
└── src/
    ├── main.jsx        # 应用入口
    ├── App.jsx         # 根组件
    └── App.css         # 样式
```

### 入口文件解析

```jsx
// src/main.jsx
import { createRoot } from 'react-dom/client';
import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

`createRoot` 是 React 18+ 的渲染 API，它启用了并发特性。`render` 方法将 `<App />` 组件渲染到 `id="root"` 的 DOM 节点中。

## 1.4 第一个 React 组件

```jsx
// src/App.jsx
function App() {
  return (
    <div>
      <h1>你好，React！</h1>
      <p>这是我的第一个 React 应用。</p>
    </div>
  );
}

export default App;
```

打开浏览器，你会看到页面上显示了标题和段落。**恭喜，你的第一个 React 应用已经运行起来了！**

## 1.5 React 开发者工具

安装 **React Developer Tools** 浏览器扩展（Chrome / Firefox），它提供：

- **Components** 面板：查看组件树、props、state
- **Profiler** 面板：分析组件渲染性能

> **提示：** 在开发环境下，React 会输出有用的警告信息到浏览器控制台。务必留意这些警告，它们能帮你避免常见错误。

---

# 第二章 JSX 语法

## 2.1 什么是 JSX

JSX（JavaScript XML）是 JavaScript 的语法扩展，它允许你在 JavaScript 代码中编写类似 HTML 的标记：

```jsx
const element = <h1>Hello, world!</h1>;
```

JSX **不是** 字符串，也不是 HTML。它会被编译工具（Babel / SWC）转换为 `React.createElement()` 调用：

```js
// JSX
const element = <h1 className="title">Hello</h1>;

// 编译后等价于
const element = React.createElement('h1', { className: 'title' }, 'Hello');
```

## 2.2 JSX 基本规则

### 规则一：必须返回单个根元素

```jsx
// 错误：多个根元素
function App() {
  return (
    <h1>标题</h1>
    <p>段落</p>
  );
}

// 正确：用 <div> 或 Fragment 包裹
function App() {
  return (
    <div>
      <h1>标题</h1>
      <p>段落</p>
    </div>
  );
}

// 推荐：使用 Fragment（不会产生额外 DOM 节点）
function App() {
  return (
    <>
      <h1>标题</h1>
      <p>段落</p>
    </>
  );
}
```

`<>...</>` 是 `<React.Fragment>...</React.Fragment>` 的简写。

### 规则二：所有标签必须闭合

```jsx
// HTML 中可以不闭合
<img src="photo.jpg">
<br>
<input type="text">

// JSX 中必须闭合
<img src="photo.jpg" />
<br />
<input type="text" />
```

### 规则三：使用驼峰命名

JSX 中的属性名使用驼峰命名法（camelCase），因为 JSX 最终会转换为 JavaScript 对象：

| HTML 属性 | JSX 属性 |
|-----------|----------|
| `class` | `className` |
| `for` | `htmlFor` |
| `tabindex` | `tabIndex` |
| `onclick` | `onClick` |
| `readonly` | `readOnly` |
| `style="color: red"` | `style={{ color: 'red' }}` |

## 2.3 在 JSX 中使用 JavaScript 表达式

用**花括号 `{}`** 在 JSX 中嵌入任意 JavaScript 表达式：

```jsx
function UserGreeting() {
  const name = '张三';
  const age = 25;

  return (
    <div>
      {/* 变量 */}
      <h1>你好，{name}！</h1>

      {/* 表达式 */}
      <p>明年你就 {age + 1} 岁了。</p>

      {/* 函数调用 */}
      <p>当前时间：{new Date().toLocaleTimeString()}</p>

      {/* 三元表达式 */}
      <p>{age >= 18 ? '成年人' : '未成年人'}</p>
    </div>
  );
}
```

> **注意：** 花括号中只能放**表达式**（有返回值），不能放语句（`if`、`for`、`switch` 等）。

## 2.4 JSX 中的样式

### 内联样式

内联样式接收一个 JavaScript 对象，属性名使用驼峰命名：

```jsx
function StyledBox() {
  const boxStyle = {
    backgroundColor: '#f0f0f0',
    padding: '20px',
    borderRadius: '8px',
    fontSize: '16px',
  };

  return <div style={boxStyle}>这是一个带样式的盒子</div>;
}

// 也可以直接内联（注意双花括号）
<div style={{ color: 'red', fontWeight: 'bold' }}>红色加粗文字</div>
```

第一层 `{}` 表示进入 JavaScript 表达式，第二层 `{}` 是样式对象字面量。

### CSS 类名

```jsx
import './Button.css';

function Button() {
  return <button className="btn btn-primary">点击我</button>;
}
```

### 动态类名

```jsx
function Alert({ type, message }) {
  const className = `alert alert-${type}`;
  return <div className={className}>{message}</div>;
}
```

## 2.5 JSX 展开属性

使用展开运算符将对象的所有属性一次性传递给组件：

```jsx
function Profile(props) {
  return (
    <div>
      <img src={props.avatarUrl} alt={props.name} />
      <h2>{props.name}</h2>
    </div>
  );
}

const userData = { name: '张三', avatarUrl: '/avatar.jpg' };

// 展开传递所有属性
<Profile {...userData} />
// 等价于
<Profile name="张三" avatarUrl="/avatar.jpg" />
```

## 2.6 JSX 防注入攻击

React DOM 在渲染之前会对嵌入的值进行**转义**（escape），因此可以安全地嵌入用户输入：

```jsx
const userInput = '<script>alert("XSS")</script>';

// 安全：会被渲染为纯文本，不会执行脚本
<div>{userInput}</div>
```

如果你确实需要插入 HTML（谨慎使用），可以用 `dangerouslySetInnerHTML`：

```jsx
<div dangerouslySetInnerHTML={{ __html: '<b>加粗</b>' }} />
```

---

# 第三章 组件基础

## 3.1 函数组件

React 组件就是一个**返回 JSX 的 JavaScript 函数**：

```jsx
function Welcome() {
  return <h1>Welcome to React!</h1>;
}
```

**组件命名规则：**

- 必须以**大写字母**开头（`Welcome`、`UserProfile`）—— 小写字母开头会被当作 HTML 标签
- 推荐使用 PascalCase（大驼峰）命名

## 3.2 组件的使用

组件定义后，可以像 HTML 标签一样使用：

```jsx
function App() {
  return (
    <div>
      <Welcome />
      <Welcome />
      <Welcome />
    </div>
  );
}
```

## 3.3 组件的组合

React 应用就是组件的树形嵌套结构：

```jsx
function Header() {
  return (
    <header>
      <Logo />
      <Navigation />
    </header>
  );
}

function Logo() {
  return <img src="/logo.svg" alt="Logo" />;
}

function Navigation() {
  return (
    <nav>
      <a href="/">首页</a>
      <a href="/about">关于</a>
      <a href="/contact">联系</a>
    </nav>
  );
}

function Footer() {
  return <footer>© 2025 My App</footer>;
}

function App() {
  return (
    <>
      <Header />
      <main>
        <h1>欢迎访问我的网站</h1>
      </main>
      <Footer />
    </>
  );
}
```

## 3.4 组件的导入与导出

### 默认导出 / 导入

```jsx
// Button.jsx
export default function Button() {
  return <button>Click me</button>;
}

// App.jsx
import Button from './Button';
```

### 具名导出 / 导入

```jsx
// components.jsx
export function Button() {
  return <button>Click me</button>;
}

export function Input() {
  return <input type="text" />;
}

// App.jsx
import { Button, Input } from './components';
```

| 语法 | 导出 | 导入 |
|------|------|------|
| **默认导出** | `export default function Button() {}` | `import Button from './Button'` |
| **具名导出** | `export function Button() {}` | `import { Button } from './Button'` |

一个文件只能有一个默认导出，但可以有多个具名导出。

## 3.5 组件的纯粹性

React 假设你编写的每个组件都是**纯函数**：

- **相同的输入，相同的输出**：给定相同的 props，组件总是返回相同的 JSX
- **没有副作用**：组件在渲染过程中不应该修改任何外部变量

```jsx
// 纯组件 ✅
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// 不纯的组件 ❌（修改了外部变量）
let count = 0;
function Counter() {
  count++; // 副作用！每次渲染都会改变外部变量
  return <p>第 {count} 次渲染</p>;
}
```

> **为什么组件应该保持纯粹？** React 可能在任何时候重新渲染组件（包括多次渲染同一组件来进行优化）。不纯的组件会导致不可预测的 bug。

---

# 第四章 Props 与数据流

## 4.1 Props 基础

Props（Properties）是父组件传递给子组件的数据，相当于函数的参数：

```jsx
function UserCard({ name, age, avatar }) {
  return (
    <div className="user-card">
      <img src={avatar} alt={name} />
      <h2>{name}</h2>
      <p>年龄：{age}</p>
    </div>
  );
}

function App() {
  return (
    <UserCard
      name="张三"
      age={25}
      avatar="/avatars/zhangsan.jpg"
    />
  );
}
```

Props 是**只读的** —— 子组件不能修改接收到的 props。

## 4.2 Props 解构

推荐在参数列表中直接解构 props：

```jsx
// 方式一：直接解构（推荐）
function Button({ text, onClick, disabled = false }) {
  return (
    <button onClick={onClick} disabled={disabled}>
      {text}
    </button>
  );
}

// 方式二：使用 props 对象
function Button(props) {
  return (
    <button onClick={props.onClick} disabled={props.disabled}>
      {props.text}
    </button>
  );
}
```

## 4.3 默认值

```jsx
function Avatar({ src, size = 64 }) {
  return (
    <img
      src={src}
      alt="avatar"
      width={size}
      height={size}
    />
  );
}

// 不传 size，使用默认值 64
<Avatar src="/photo.jpg" />

// 传 size，覆盖默认值
<Avatar src="/photo.jpg" size={128} />
```

## 4.4 传递 JSX 作为 children

在组件标签之间放置的内容会作为特殊的 `children` prop 传递：

```jsx
function Card({ title, children }) {
  return (
    <div className="card">
      <h2 className="card-title">{title}</h2>
      <div className="card-body">
        {children}
      </div>
    </div>
  );
}

function App() {
  return (
    <Card title="用户信息">
      <p>姓名：张三</p>
      <p>邮箱：zhangsan@example.com</p>
    </Card>
  );
}
```

`children` 是构建**布局组件**和**容器组件**的关键模式。

## 4.5 Props 转发

有时候你需要把所有 props 透传给子元素：

```jsx
function FancyButton({ className, children, ...rest }) {
  return (
    <button className={`fancy-btn ${className}`} {...rest}>
      {children}
    </button>
  );
}

// 使用时，onClick、disabled 等都会被透传给内部的 <button>
<FancyButton className="primary" onClick={handleClick} disabled>
  提交
</FancyButton>
```

## 4.6 数据流动方向

React 中数据是**单向流动**的（自顶向下）：

```
  App          ← 数据源
   │
   ├── Header  ← 接收 props
   │    └── Nav
   │
   ├── Main
   │    ├── Sidebar  ← 接收 props
   │    └── Content  ← 接收 props
   │
   └── Footer
```

如果子组件需要改变父组件的数据，通过父组件传递的**回调函数**来实现（在第五章详细介绍）。

---

# 第五章 State 与事件处理

## 5.1 什么是 State

Props 是从外部传入的，而 **State** 是组件内部管理的数据。当 state 发生变化时，React 会**重新渲染**该组件。

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>当前计数：{count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  );
}
```

## 5.2 useState 详解

`useState` 是一个 **Hook**，它接收初始值，返回一个包含两个元素的数组：

```jsx
const [state, setState] = useState(initialValue);
//     ↑        ↑                      ↑
//   当前值  更新函数              初始值
```

**命名约定：** `[xxx, setXxx]` —— 例如 `[name, setName]`、`[items, setItems]`。

### 多个 state 变量

```jsx
function UserForm() {
  const [name, setName] = useState('');
  const [age, setAge] = useState(0);
  const [email, setEmail] = useState('');

  // ...
}
```

### 惰性初始化

如果初始值的计算成本很高，可以传一个函数：

```jsx
// 每次渲染都会调用 expensiveComputation()（即使结果会被忽略）
const [data, setData] = useState(expensiveComputation());

// 只在首次渲染时调用（推荐）
const [data, setData] = useState(() => expensiveComputation());
```

## 5.3 State 的更新机制

### State 更新是异步的（批量处理）

React 会将同一事件处理函数中的多个 `setState` 调用**批量处理**，只触发一次重新渲染：

```jsx
function handleClick() {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
  // count 只会 +1，而非 +3！
  // 因为三次调用都使用的是同一个 count 值
}
```

### 使用更新函数

要基于前一个值更新 state，传入一个**更新函数**：

```jsx
function handleClick() {
  setCount(prev => prev + 1);
  setCount(prev => prev + 1);
  setCount(prev => prev + 1);
  // count 会 +3 ✅
}
```

| 用法 | 含义 |
|------|------|
| `setCount(5)` | 替换为 5 |
| `setCount(prev => prev + 1)` | 基于上一个值 +1 |

## 5.4 事件处理

### 基本事件绑定

```jsx
function Button() {
  function handleClick() {
    alert('按钮被点击了！');
  }

  // 传递函数引用，不是调用函数
  return <button onClick={handleClick}>点击我</button>;
}
```

> **注意：** 是 `onClick={handleClick}` 而不是 `onClick={handleClick()}`。后者会在渲染时立即执行函数。

### 内联事件处理

```jsx
<button onClick={() => alert('点击了！')}>
  点击
</button>
```

### 事件对象

事件处理函数会接收一个**合成事件（SyntheticEvent）**对象：

```jsx
function InputField() {
  function handleChange(e) {
    console.log('输入值：', e.target.value);
  }

  function handleSubmit(e) {
    e.preventDefault(); // 阻止表单默认提交行为
    console.log('表单已提交');
  }

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" onChange={handleChange} />
      <button type="submit">提交</button>
    </form>
  );
}
```

### 传递参数给事件处理函数

```jsx
function ItemList({ items }) {
  function handleDelete(id) {
    console.log('删除项目：', id);
  }

  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>
          {item.name}
          {/* 用箭头函数包裹以传递参数 */}
          <button onClick={() => handleDelete(item.id)}>删除</button>
        </li>
      ))}
    </ul>
  );
}
```

## 5.5 事件冒泡与传播

JSX 中的事件会沿组件树向上冒泡（与 DOM 事件冒泡一致）：

```jsx
function Toolbar() {
  return (
    <div onClick={() => console.log('Toolbar 被点击')}>
      <button onClick={(e) => {
        e.stopPropagation(); // 阻止冒泡
        console.log('按钮被点击');
      }}>
        点击我
      </button>
    </div>
  );
}
```

## 5.6 State 提升（Lifting State Up）

当多个组件需要共享同一份数据时，将 state 提升到它们最近的**公共父组件**：

```jsx
import { useState } from 'react';

function TemperatureInput({ scale, temperature, onTemperatureChange }) {
  const scaleNames = { c: '摄氏', f: '华氏' };
  return (
    <fieldset>
      <legend>输入{scaleNames[scale]}温度：</legend>
      <input
        value={temperature}
        onChange={e => onTemperatureChange(e.target.value)}
      />
    </fieldset>
  );
}

function toCelsius(f) { return (f - 32) * 5 / 9; }
function toFahrenheit(c) { return c * 9 / 5 + 32; }

function tryConvert(temperature, convertFn) {
  const input = parseFloat(temperature);
  if (Number.isNaN(input)) return '';
  return Math.round(convertFn(input) * 1000) / 1000 + '';
}

function Calculator() {
  const [temperature, setTemperature] = useState('');
  const [scale, setScale] = useState('c');

  const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
  const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;

  return (
    <div>
      <TemperatureInput
        scale="c"
        temperature={celsius}
        onTemperatureChange={(temp) => {
          setScale('c');
          setTemperature(temp);
        }}
      />
      <TemperatureInput
        scale="f"
        temperature={fahrenheit}
        onTemperatureChange={(temp) => {
          setScale('f');
          setTemperature(temp);
        }}
      />
    </div>
  );
}
```

**数据流向：**

```
Calculator（拥有 state）
   ├── TemperatureInput (℃)  ← props: temperature, onChange 回调
   └── TemperatureInput (℉)  ← props: temperature, onChange 回调
```

---

# 第六章 条件渲染与列表

## 6.1 条件渲染

### if/else 语句

```jsx
function Greeting({ isLoggedIn }) {
  if (isLoggedIn) {
    return <h1>欢迎回来！</h1>;
  }
  return <h1>请先登录。</h1>;
}
```

### 三元运算符

适合在 JSX 中进行简单的条件判断：

```jsx
function StatusBadge({ isOnline }) {
  return (
    <span className={isOnline ? 'badge-online' : 'badge-offline'}>
      {isOnline ? '在线' : '离线'}
    </span>
  );
}
```

### 逻辑与（&&）

适合「有则显示，无则不显示」的场景：

```jsx
function Notification({ count }) {
  return (
    <div>
      <h1>通知中心</h1>
      {count > 0 && <span className="badge">{count}</span>}
    </div>
  );
}
```

> **陷阱：** `&&` 左边不要放数字 `0`，因为 `0 && <Component />` 会渲染出 `0` 而不是什么都不渲染。可以改写为 `count > 0 && ...`。

### 用变量存储 JSX

当逻辑较复杂时，可以在返回之前用变量组装 JSX：

```jsx
function Item({ name, isPacked }) {
  let content = name;
  if (isPacked) {
    content = <del>{name} ✓</del>;
  }
  return <li>{content}</li>;
}
```

### 返回 null

组件返回 `null` 表示不渲染任何内容：

```jsx
function WarningBanner({ show }) {
  if (!show) {
    return null;
  }
  return <div className="warning">警告！</div>;
}
```

## 6.2 列表渲染

使用 `Array.map()` 将数据数组转换为 JSX 数组：

```jsx
function TodoList() {
  const todos = [
    { id: 1, text: '学习 React', done: true },
    { id: 2, text: '写项目', done: false },
    { id: 3, text: '部署上线', done: false },
  ];

  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          {todo.done ? <del>{todo.text}</del> : todo.text}
        </li>
      ))}
    </ul>
  );
}
```

## 6.3 Key 的作用

`key` 是列表渲染中**必须**提供的特殊属性。它帮助 React 识别哪些元素发生了变化：

```jsx
// ✅ 使用唯一稳定的 ID 作为 key
{items.map(item => <li key={item.id}>{item.name}</li>)}

// ❌ 使用 index 作为 key（不推荐）
{items.map((item, index) => <li key={index}>{item.name}</li>)}
```

**Key 的规则：**

| 规则 | 说明 |
|------|------|
| **唯一性** | 在兄弟元素之间必须唯一（不需要全局唯一） |
| **稳定性** | 不应在重新渲染时改变（不要用 `Math.random()`） |
| **不可用 index** | 当列表会重新排序、插入或删除时，用 index 会导致 bug |
| **不会作为 prop** | `key` 不会传递给组件，需要额外传一个同值的 prop |

## 6.4 列表过滤与转换

```jsx
function FilteredList({ people }) {
  const scientists = people.filter(
    person => person.profession === '科学家'
  );

  return (
    <ul>
      {scientists.map(person => (
        <li key={person.id}>
          <strong>{person.name}</strong> — {person.field}
        </li>
      ))}
    </ul>
  );
}
```

## 6.5 嵌套列表

```jsx
function RecipeList({ recipes }) {
  return (
    <div>
      {recipes.map(recipe => (
        <div key={recipe.id}>
          <h2>{recipe.title}</h2>
          <ul>
            {recipe.ingredients.map(ingredient => (
              <li key={ingredient}>{ingredient}</li>
            ))}
          </ul>
        </div>
      ))}
    </div>
  );
}
```

---

# 第七章 State 管理进阶

## 7.1 State 设计原则

良好的 state 设计应遵循以下原则：

| 原则 | 说明 |
|------|------|
| **合并关联的 state** | 如果两个 state 总是一起更新，考虑合并为一个对象 |
| **避免矛盾的 state** | 不要出现 `isSending` 和 `isSent` 同时为 true 的情况 |
| **避免冗余的 state** | 能从 props 或其他 state 计算出来的值，不要存为 state |
| **避免重复的 state** | 同一份数据不应在多个 state 中重复存储 |
| **避免深层嵌套** | 扁平化的 state 更容易更新 |

```jsx
// ❌ 冗余的 state
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  const [fullName, setFullName] = useState(''); // 冗余！

  // ✅ fullName 应该在渲染时计算
  const fullName = firstName + ' ' + lastName;
}
```

## 7.2 更新对象类型的 State

State 中的对象是**不可变的**（immutable）。不要直接修改对象，而应该创建一个新对象：

```jsx
import { useState } from 'react';

function UserProfile() {
  const [user, setUser] = useState({
    name: '张三',
    email: 'zhangsan@example.com',
    address: {
      city: '北京',
      district: '海淀区',
    },
  });

  function handleNameChange(e) {
    // ❌ 直接修改（不会触发重新渲染）
    // user.name = e.target.value;

    // ✅ 创建新对象
    setUser({
      ...user,
      name: e.target.value,
    });
  }

  function handleCityChange(e) {
    // ✅ 更新嵌套对象
    setUser({
      ...user,
      address: {
        ...user.address,
        city: e.target.value,
      },
    });
  }

  return (
    <div>
      <input value={user.name} onChange={handleNameChange} />
      <input value={user.address.city} onChange={handleCityChange} />
    </div>
  );
}
```

## 7.3 更新数组类型的 State

与对象一样，数组 state 也应该保持不可变。以下是常见的数组操作对照表：

| 操作 | 避免使用（会修改原数组） | 推荐使用（返回新数组） |
|------|--------------------------|------------------------|
| 添加 | `push`、`unshift` | `[...arr, newItem]`、`concat` |
| 删除 | `splice`、`pop`、`shift` | `filter`、`slice` |
| 替换 | `splice`、`arr[i] = ...` | `map` |
| 排序 | `sort`、`reverse` | 先拷贝再排序 |

```jsx
import { useState } from 'react';

function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [text, setText] = useState('');
  let nextId = 0;

  function handleAdd() {
    setTodos([
      ...todos,
      { id: nextId++, text, done: false },
    ]);
    setText('');
  }

  function handleToggle(id) {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, done: !todo.done } : todo
    ));
  }

  function handleDelete(id) {
    setTodos(todos.filter(todo => todo.id !== id));
  }

  return (
    <div>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAdd}>添加</button>
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.done}
              onChange={() => handleToggle(todo.id)}
            />
            <span style={{
              textDecoration: todo.done ? 'line-through' : 'none'
            }}>
              {todo.text}
            </span>
            <button onClick={() => handleDelete(todo.id)}>删除</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## 7.4 使用 Immer 简化不可变更新

对于深层嵌套的数据，手动展开很繁琐。[Immer](https://immerjs.github.io/immer/) 库可以让你用「可变」的语法编写不可变更新：

```bash
npm install use-immer
```

```jsx
import { useImmer } from 'use-immer';

function UserProfile() {
  const [user, updateUser] = useImmer({
    name: '张三',
    address: {
      city: '北京',
      district: '海淀区',
    },
  });

  function handleCityChange(e) {
    updateUser(draft => {
      draft.address.city = e.target.value;
    });
  }

  // ...
}
```

Immer 在底层使用 Proxy，对 `draft` 的修改会自动生成一个新的不可变对象。

## 7.5 状态快照

每次渲染都有自己的 state「快照」。state 的值在一次渲染中是固定的：

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
    // 此时 count 仍然是 0（本次渲染的快照）
    console.log(count); // 0

    setTimeout(() => {
      // 3 秒后，打印的仍然是点击时的 count 值
      console.log(count); // 0
    }, 3000);
  }

  return <button onClick={handleClick}>{count}</button>;
}
```

这是因为 **state 的行为类似于一张快照**。设置 state 不会改变已有的变量，而是触发一次新的渲染。

---

# 第八章 副作用与 useEffect

## 8.1 什么是副作用

**纯粹的渲染逻辑**：根据 props 和 state 计算并返回 JSX。

**副作用（Side Effects）**：渲染之外的操作，例如：

- 发起网络请求
- 操作 DOM
- 设置定时器
- 订阅外部数据源
- 发送日志

## 8.2 useEffect 基础

`useEffect` 让你在组件渲染完成后执行副作用：

```jsx
import { useState, useEffect } from 'react';

function DocumentTitle() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `你点击了 ${count} 次`;
  });

  return (
    <div>
      <p>计数：{count}</p>
      <button onClick={() => setCount(count + 1)}>点击</button>
    </div>
  );
}
```

## 8.3 依赖数组

`useEffect` 的第二个参数是**依赖数组**，用来控制 effect 何时重新执行：

```jsx
// 每次渲染后都执行
useEffect(() => {
  console.log('每次渲染');
});

// 仅在挂载时执行一次
useEffect(() => {
  console.log('组件挂载');
}, []);

// 当 count 或 name 变化时执行
useEffect(() => {
  console.log('count 或 name 变了');
}, [count, name]);
```

| 依赖数组 | 执行时机 |
|----------|----------|
| 不传 | 每次渲染后 |
| `[]` | 仅挂载时 |
| `[a, b]` | 挂载时 + `a` 或 `b` 变化时 |

## 8.4 清理函数

Effect 可以返回一个**清理函数**，用于取消订阅、清除定时器等：

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log('tick');
  }, 1000);

  // 清理函数：在组件卸载或依赖变化前执行
  return () => {
    clearInterval(timer);
  };
}, []);
```

**清理函数的执行时机：**

1. 组件**卸载**时
2. 下一次 effect 执行**之前**（依赖变化时）

## 8.5 常见的 Effect 用例

### 数据获取

```jsx
import { useState, useEffect } from 'react';

function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let ignore = false;

    async function fetchUsers() {
      try {
        setLoading(true);
        const response = await fetch('https://api.example.com/users');
        const data = await response.json();
        if (!ignore) {
          setUsers(data);
        }
      } catch (err) {
        if (!ignore) {
          setError(err.message);
        }
      } finally {
        if (!ignore) {
          setLoading(false);
        }
      }
    }

    fetchUsers();

    return () => {
      ignore = true; // 防止竞态条件
    };
  }, []);

  if (loading) return <p>加载中...</p>;
  if (error) return <p>错误：{error}</p>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

`ignore` 标志用于处理**竞态条件**：如果组件在请求完成前卸载，或者依赖变化触发了新请求，旧请求的结果应被忽略。

### 监听浏览器事件

```jsx
function WindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    function handleResize() {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }

    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return <p>窗口大小：{size.width} × {size.height}</p>;
}
```

### 连接外部系统

```jsx
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.connect();

    return () => {
      connection.disconnect();
    };
  }, [roomId]); // roomId 变化时重新连接

  return <div>聊天室：{roomId}</div>;
}
```

## 8.6 你可能不需要 Effect

很多时候，你认为需要 `useEffect` 的场景实际上**不需要**：

```jsx
// ❌ 不必要的 Effect：根据 props/state 计算派生数据
function FilteredList({ items, query }) {
  const [filteredItems, setFilteredItems] = useState([]);

  useEffect(() => {
    setFilteredItems(items.filter(item => item.includes(query)));
  }, [items, query]);

  // ...
}

// ✅ 直接在渲染时计算
function FilteredList({ items, query }) {
  const filteredItems = items.filter(item => item.includes(query));
  // ...
}
```

```jsx
// ❌ 不必要的 Effect：响应事件
function Form() {
  const [data, setData] = useState(null);

  useEffect(() => {
    if (data) {
      submitToServer(data);
    }
  }, [data]);

  // ✅ 直接在事件处理函数中操作
  function handleSubmit() {
    const formData = getFormData();
    submitToServer(formData);
  }
}
```

**经验法则：**

- **计算派生值** → 在渲染时直接计算
- **响应用户事件** → 在事件处理函数中处理
- **同步外部系统** → 才需要 `useEffect`

---

# 第九章 Ref 与 DOM 操作

## 9.1 什么是 Ref

`useRef` 返回一个**可变的引用对象**，它有一个 `.current` 属性：

```jsx
import { useRef } from 'react';

function Counter() {
  const countRef = useRef(0);

  function handleClick() {
    countRef.current++;
    console.log('点击了', countRef.current, '次');
    // 注意：修改 ref 不会触发重新渲染！
  }

  return <button onClick={handleClick}>点击</button>;
}
```

**Ref vs State：**

| 特性 | `useRef` | `useState` |
|------|----------|------------|
| 返回值 | `{ current: value }` | `[value, setValue]` |
| 修改时触发渲染 | ❌ 不会 | ✅ 会 |
| 可变性 | 可以直接修改 `.current` | 必须用 `setState` |
| 渲染中可读 | 不建议（值可能不一致） | ✅ 安全 |

## 9.2 操作 DOM

Ref 最常见的用途是访问 DOM 元素：

```jsx
import { useRef } from 'react';

function TextInput() {
  const inputRef = useRef(null);

  function handleFocus() {
    inputRef.current.focus();
  }

  return (
    <div>
      <input ref={inputRef} type="text" placeholder="点击按钮聚焦" />
      <button onClick={handleFocus}>聚焦输入框</button>
    </div>
  );
}
```

### 滚动到指定元素

```jsx
function ScrollDemo() {
  const sectionRef = useRef(null);

  function scrollToSection() {
    sectionRef.current.scrollIntoView({ behavior: 'smooth' });
  }

  return (
    <div>
      <button onClick={scrollToSection}>滚动到底部</button>
      {/* 很多内容... */}
      <div ref={sectionRef} style={{ marginTop: '200vh' }}>
        <h2>目标区域</h2>
      </div>
    </div>
  );
}
```

### 管理列表中的多个 Ref

```jsx
import { useRef } from 'react';

function CatGallery() {
  const itemsRef = useRef(new Map());

  function scrollToId(itemId) {
    const node = itemsRef.current.get(itemId);
    node.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
  }

  return (
    <div>
      <nav>
        {catList.map(cat => (
          <button key={cat.id} onClick={() => scrollToId(cat.id)}>
            {cat.name}
          </button>
        ))}
      </nav>
      <div>
        {catList.map(cat => (
          <img
            key={cat.id}
            ref={node => {
              if (node) {
                itemsRef.current.set(cat.id, node);
              } else {
                itemsRef.current.delete(cat.id);
              }
            }}
            src={cat.imageUrl}
            alt={cat.name}
          />
        ))}
      </div>
    </div>
  );
}
```

## 9.3 转发 Ref（forwardRef）

默认情况下，自定义组件不会暴露其内部的 DOM 节点。使用 `forwardRef` 可以将 ref 转发到内部元素：

```jsx
import { forwardRef, useRef } from 'react';

const FancyInput = forwardRef(function FancyInput(props, ref) {
  return (
    <input
      ref={ref}
      className="fancy-input"
      {...props}
    />
  );
});

function App() {
  const inputRef = useRef(null);

  return (
    <div>
      <FancyInput ref={inputRef} placeholder="花哨的输入框" />
      <button onClick={() => inputRef.current.focus()}>
        聚焦
      </button>
    </div>
  );
}
```

## 9.4 useImperativeHandle

配合 `forwardRef` 使用，限制暴露给父组件的 DOM 操作：

```jsx
import { forwardRef, useImperativeHandle, useRef } from 'react';

const CustomInput = forwardRef(function CustomInput(props, ref) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => ({
    focus() {
      inputRef.current.focus();
    },
    scrollIntoView() {
      inputRef.current.scrollIntoView();
    },
    // 只暴露这两个方法，不暴露完整的 DOM 节点
  }));

  return <input ref={inputRef} {...props} />;
});
```

## 9.5 flushSync 与 DOM 更新时机

React 的 state 更新是批量异步的。如果你需要在 DOM 更新后立即执行操作：

```jsx
import { useState, useRef } from 'react';
import { flushSync } from 'react-dom';

function TodoList() {
  const [todos, setTodos] = useState([]);
  const listRef = useRef(null);

  function handleAdd(text) {
    flushSync(() => {
      setTodos([...todos, { id: Date.now(), text }]);
    });
    // flushSync 确保 DOM 已更新，现在可以安全滚动
    listRef.current.lastChild.scrollIntoView({ behavior: 'smooth' });
  }

  return (
    <ul ref={listRef}>
      {todos.map(todo => <li key={todo.id}>{todo.text}</li>)}
    </ul>
  );
}
```

---

# 第十章 Context 与跨层级通信

## 10.1 Props 逐层传递的问题

当数据需要跨越多层组件传递时，「Props 逐层传递」（prop drilling）会变得很麻烦：

```
App → Layout → Sidebar → Menu → MenuItem → Icon
                                              ↑
                                        需要 theme 数据
```

每一层中间组件都必须接收并转发 `theme`，即使它们自己并不使用这个数据。

## 10.2 创建和使用 Context

Context 可以让父组件的数据对**整棵子树**中的任意组件可用，无需逐层传递。

### 第一步：创建 Context

```jsx
// ThemeContext.js
import { createContext } from 'react';

export const ThemeContext = createContext('light'); // 默认值
```

### 第二步：提供 Context

```jsx
import { useState } from 'react';
import { ThemeContext } from './ThemeContext';

function App() {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={theme}>
      <Header />
      <Main />
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        切换主题
      </button>
    </ThemeContext.Provider>
  );
}
```

### 第三步：消费 Context

```jsx
import { useContext } from 'react';
import { ThemeContext } from './ThemeContext';

function ThemedButton() {
  const theme = useContext(ThemeContext);

  return (
    <button className={`btn-${theme}`}>
      当前主题：{theme}
    </button>
  );
}
```

无论 `ThemedButton` 在组件树的哪一层，只要它位于 `ThemeContext.Provider` 内部，就能获取到 `theme` 值。

## 10.3 Context 的工作原理

```
ThemeContext.Provider (value="dark")
   ├── Header
   │    └── Logo
   │         └── useContext(ThemeContext) → "dark"
   │
   ├── Main
   │    ├── Sidebar
   │    │    └── useContext(ThemeContext) → "dark"
   │    │
   │    └── ThemeContext.Provider (value="light")  ← 覆盖！
   │         └── Content
   │              └── useContext(ThemeContext) → "light"
   │
   └── Footer
        └── useContext(ThemeContext) → "dark"
```

`useContext` 会找到组件树中**最近的**对应 Provider 的值。

## 10.4 Context + State 完整示例

```jsx
import { createContext, useContext, useState } from 'react';

const AuthContext = createContext(null);

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  function login(username) {
    setUser({ name: username });
  }

  function logout() {
    setUser(null);
  }

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth 必须在 AuthProvider 内部使用');
  }
  return context;
}

function NavBar() {
  const { user, logout } = useAuth();

  return (
    <nav>
      {user ? (
        <>
          <span>欢迎，{user.name}</span>
          <button onClick={logout}>退出</button>
        </>
      ) : (
        <span>未登录</span>
      )}
    </nav>
  );
}

function LoginPage() {
  const { login } = useAuth();

  return (
    <button onClick={() => login('张三')}>
      登录
    </button>
  );
}

function App() {
  return (
    <AuthProvider>
      <NavBar />
      <LoginPage />
    </AuthProvider>
  );
}
```

## 10.5 何时使用 Context

| 适合 Context 的场景 | 不适合的场景 |
|---------------------|-------------|
| 主题（亮色/暗色） | 频繁更新的局部状态 |
| 当前登录用户 | 简单的父子通信 |
| 语言/国际化设置 | 只需传一两层的 props |
| 路由信息 | |
| 全局 UI 状态（Toast、Modal） | |

> **提示：** 在使用 Context 之前，先考虑能否通过组件组合（将组件作为 `children` 传递）来避免 prop drilling。

---

# 第十一章 useReducer 与复杂状态逻辑

## 11.1 为什么需要 useReducer

当组件的 state 逻辑变得复杂（多个相关联的状态、多种更新方式）时，`useState` 会显得力不从心。`useReducer` 允许你将状态更新逻辑集中到一个 **reducer 函数**中。

## 11.2 useReducer 基础

```jsx
import { useReducer } from 'react';

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return { count: 0 };
    default:
      throw new Error('未知 action: ' + action.type);
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <div>
      <p>计数：{state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+1</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-1</button>
      <button onClick={() => dispatch({ type: 'reset' })}>重置</button>
    </div>
  );
}
```

## 11.3 useReducer 的结构

```
const [state, dispatch] = useReducer(reducer, initialState);
```

- **`reducer(state, action)`**：一个纯函数，接收当前 state 和 action，返回新的 state
- **`dispatch(action)`**：触发状态更新的函数，参数是一个 action 对象
- **`action`**：描述「发生了什么」的对象，通常有 `type` 字段

## 11.4 实战：Todo 应用

```jsx
import { useReducer, useState } from 'react';

function todosReducer(todos, action) {
  switch (action.type) {
    case 'added':
      return [...todos, {
        id: action.id,
        text: action.text,
        done: false,
      }];
    case 'toggled':
      return todos.map(todo =>
        todo.id === action.id
          ? { ...todo, done: !todo.done }
          : todo
      );
    case 'deleted':
      return todos.filter(todo => todo.id !== action.id);
    default:
      throw new Error('未知 action: ' + action.type);
  }
}

let nextId = 0;

function TodoApp() {
  const [todos, dispatch] = useReducer(todosReducer, []);
  const [text, setText] = useState('');

  function handleAdd() {
    dispatch({ type: 'added', id: nextId++, text });
    setText('');
  }

  return (
    <div>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAdd}>添加</button>

      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.done}
              onChange={() => dispatch({ type: 'toggled', id: todo.id })}
            />
            <span style={{
              textDecoration: todo.done ? 'line-through' : 'none'
            }}>
              {todo.text}
            </span>
            <button onClick={() => dispatch({ type: 'deleted', id: todo.id })}>
              删除
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## 11.5 useReducer vs useState

| 特性 | `useState` | `useReducer` |
|------|-----------|-------------|
| **代码量** | 较少 | 较多（需要写 reducer） |
| **适合场景** | 简单状态 | 复杂状态逻辑 |
| **可读性** | 状态分散在事件处理中 | 更新逻辑集中在 reducer 中 |
| **可测试性** | 一般 | 好（reducer 是纯函数，易于单元测试） |
| **调试** | 一般 | 好（可以在 reducer 中打日志） |

## 11.6 Context + useReducer = 简版状态管理

结合 Context 和 useReducer，可以实现类似 Redux 的全局状态管理：

```jsx
import { createContext, useContext, useReducer } from 'react';

const TasksContext = createContext(null);
const TasksDispatchContext = createContext(null);

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added':
      return [...tasks, { id: action.id, text: action.text, done: false }];
    case 'changed':
      return tasks.map(t => t.id === action.task.id ? action.task : t);
    case 'deleted':
      return tasks.filter(t => t.id !== action.id);
    default:
      throw new Error('未知 action: ' + action.type);
  }
}

function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(tasksReducer, []);

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

function useTasks() {
  return useContext(TasksContext);
}

function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}

// 任何子组件都可以读取 tasks 和 dispatch actions
function AddTask() {
  const dispatch = useTasksDispatch();
  const [text, setText] = useState('');

  return (
    <div>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={() => {
        dispatch({ type: 'added', id: Date.now(), text });
        setText('');
      }}>
        添加
      </button>
    </div>
  );
}
```

---

# 第十二章 自定义 Hook

## 12.1 什么是自定义 Hook

自定义 Hook 是一个以 **`use`** 开头的函数，它内部可以调用其他 Hook。自定义 Hook 允许你将组件逻辑**提取到可复用的函数**中。

```jsx
// 自定义 Hook：追踪在线状态
import { useState, useEffect } from 'react';

function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() { setIsOnline(true); }
    function handleOffline() { setIsOnline(false); }

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);

    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return isOnline;
}

// 使用
function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ 在线' : '❌ 离线'}</h1>;
}
```

## 12.2 自定义 Hook 的命名规则

- **必须以 `use` 开头**，后接大写字母（如 `useOnlineStatus`、`useFormInput`）
- 这不仅是约定，也让 React 的 linter 能检查 Hook 的规则是否被正确遵守

## 12.3 常用自定义 Hook

### useLocalStorage

```jsx
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored !== null ? JSON.parse(stored) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// 使用
function Settings() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');

  return (
    <select value={theme} onChange={e => setTheme(e.target.value)}>
      <option value="light">亮色</option>
      <option value="dark">暗色</option>
    </select>
  );
}
```

### useDebounce

```jsx
import { useState, useEffect } from 'react';

function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// 使用：搜索输入防抖
function SearchBox() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 500);

  useEffect(() => {
    if (debouncedQuery) {
      fetchSearchResults(debouncedQuery);
    }
  }, [debouncedQuery]);

  return <input value={query} onChange={e => setQuery(e.target.value)} />;
}
```

### useFetch

```jsx
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    if (!url) return;

    let ignore = false;
    setLoading(true);

    fetch(url)
      .then(res => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
      })
      .then(json => {
        if (!ignore) {
          setData(json);
          setError(null);
        }
      })
      .catch(err => {
        if (!ignore) setError(err.message);
      })
      .finally(() => {
        if (!ignore) setLoading(false);
      });

    return () => { ignore = true; };
  }, [url]);

  return { data, loading, error };
}

// 使用
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(
    `https://api.example.com/users/${userId}`
  );

  if (loading) return <p>加载中...</p>;
  if (error) return <p>错误：{error}</p>;
  return <h1>{user.name}</h1>;
}
```

### useCounter

```jsx
import { useState, useEffect } from 'react';

function useCounter(interval = 1000) {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, interval);
    return () => clearInterval(id);
  }, [interval]);

  return count;
}

// 使用
function Timer() {
  const seconds = useCounter(1000);
  return <p>已过 {seconds} 秒</p>;
}
```

### useFormInput

```jsx
import { useState } from 'react';

function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  const inputProps = {
    value,
    onChange(e) {
      setValue(e.target.value);
    },
  };

  return inputProps;
}

// 使用：将返回值直接展开到 input 上
function RegistrationForm() {
  const nameProps = useFormInput('');
  const emailProps = useFormInput('');

  return (
    <form>
      <label>
        姓名：<input {...nameProps} />
      </label>
      <label>
        邮箱：<input {...emailProps} />
      </label>
      <p>你好，{nameProps.value}（{emailProps.value}）</p>
    </form>
  );
}
```

## 12.4 Hook 的规则

| 规则 | 说明 |
|------|------|
| **只在顶层调用** | 不能在循环、条件判断、嵌套函数中调用 Hook |
| **只在 React 函数中调用** | 只能在函数组件或自定义 Hook 中调用 |

```jsx
// ❌ 错误：在条件语句中调用 Hook
function Component({ show }) {
  if (show) {
    const [value, setValue] = useState(''); // 违反规则！
  }
}

// ✅ 正确：Hook 始终在顶层调用
function Component({ show }) {
  const [value, setValue] = useState('');

  if (!show) return null;
  return <input value={value} onChange={e => setValue(e.target.value)} />;
}
```

---

# 第十三章 性能优化

## 13.1 React 的渲染机制

当组件的 state 或 props 变化时，React 会重新渲染该组件及其**所有子组件**。大多数情况下这不是问题（React 很快），但在某些场景下需要优化。

## 13.2 React.memo

`memo` 让组件在 props 未变化时跳过重新渲染：

```jsx
import { memo } from 'react';

const ExpensiveList = memo(function ExpensiveList({ items }) {
  console.log('ExpensiveList 渲染了');
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
});

function App() {
  const [count, setCount] = useState(0);
  const [items] = useState([
    { id: 1, name: 'Apple' },
    { id: 2, name: 'Banana' },
  ]);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
      {/* items 没变，ExpensiveList 不会重新渲染 */}
      <ExpensiveList items={items} />
    </div>
  );
}
```

## 13.3 useMemo

`useMemo` 缓存**计算结果**，避免每次渲染都重新计算：

```jsx
import { useMemo, useState } from 'react';

function FilteredList({ items, query }) {
  const filteredItems = useMemo(() => {
    console.log('正在过滤...');
    return items.filter(item =>
      item.name.toLowerCase().includes(query.toLowerCase())
    );
  }, [items, query]); // 只在 items 或 query 变化时重新计算

  return (
    <ul>
      {filteredItems.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}
```

## 13.4 useCallback

`useCallback` 缓存**函数引用**，常与 `memo` 配合使用：

```jsx
import { useCallback, memo, useState } from 'react';

const TodoItem = memo(function TodoItem({ todo, onToggle }) {
  console.log('TodoItem 渲染:', todo.text);
  return (
    <li>
      <input
        type="checkbox"
        checked={todo.done}
        onChange={() => onToggle(todo.id)}
      />
      {todo.text}
    </li>
  );
});

function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: '学习 React', done: false },
    { id: 2, text: '写项目', done: false },
  ]);

  const handleToggle = useCallback((id) => {
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id ? { ...todo, done: !todo.done } : todo
      )
    );
  }, []); // 空依赖，函数引用永远不变

  return (
    <ul>
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} onToggle={handleToggle} />
      ))}
    </ul>
  );
}
```

## 13.5 何时需要优化

**不要过早优化！** 只在以下情况考虑性能优化：

| 情况 | 优化手段 |
|------|----------|
| 组件渲染很慢（可通过 Profiler 确认） | `useMemo` 缓存计算 |
| 子组件被不必要地重渲染 | `memo` + `useCallback` |
| 超长列表 | 虚拟化（react-window / react-virtual） |
| 低优先级更新阻塞了高优先级交互 | `useTransition` / `useDeferredValue` |

## 13.6 useTransition

`useTransition` 将某些状态更新标记为**低优先级**，不阻塞用户交互：

```jsx
import { useState, useTransition } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('home');
  const [isPending, startTransition] = useTransition();

  function handleTabClick(nextTab) {
    startTransition(() => {
      setTab(nextTab); // 低优先级更新
    });
  }

  return (
    <div>
      <nav>
        <button onClick={() => handleTabClick('home')}>首页</button>
        <button onClick={() => handleTabClick('posts')}>文章</button>
        <button onClick={() => handleTabClick('settings')}>设置</button>
      </nav>
      <div style={{ opacity: isPending ? 0.6 : 1 }}>
        {tab === 'home' && <HomeTab />}
        {tab === 'posts' && <PostsTab />}
        {tab === 'settings' && <SettingsTab />}
      </div>
    </div>
  );
}
```

## 13.7 useDeferredValue

`useDeferredValue` 延迟更新一个值，让输入保持响应：

```jsx
import { useState, useDeferredValue, memo } from 'react';

const SlowList = memo(function SlowList({ text }) {
  const items = [];
  for (let i = 0; i < 500; i++) {
    items.push(<li key={i}>{text} - 项目 {i}</li>);
  }
  return <ul>{items}</ul>;
});

function App() {
  const [text, setText] = useState('');
  const deferredText = useDeferredValue(text);

  return (
    <div>
      <input value={text} onChange={e => setText(e.target.value)} />
      {/* 输入框即时响应，列表延迟更新 */}
      <SlowList text={deferredText} />
    </div>
  );
}
```

## 13.8 lazy 与 Suspense（代码分割）

`React.lazy` 实现组件的按需加载，配合 `Suspense` 显示加载状态：

```jsx
import { lazy, Suspense } from 'react';

const HeavyChart = lazy(() => import('./HeavyChart'));

function Dashboard() {
  return (
    <div>
      <h1>仪表盘</h1>
      <Suspense fallback={<div>图表加载中...</div>}>
        <HeavyChart />
      </Suspense>
    </div>
  );
}
```

`import('./HeavyChart')` 会将该组件打包为单独的 chunk，只在实际需要时才加载。

---

# 第十四章 表单处理

## 14.1 受控组件

在受控组件中，表单数据由 React 的 state 驱动：

```jsx
import { useState } from 'react';

function LoginForm() {
  const [formData, setFormData] = useState({
    username: '',
    password: '',
    remember: false,
  });

  function handleChange(e) {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value,
    }));
  }

  function handleSubmit(e) {
    e.preventDefault();
    console.log('提交数据：', formData);
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">用户名：</label>
        <input
          id="username"
          name="username"
          value={formData.username}
          onChange={handleChange}
        />
      </div>
      <div>
        <label htmlFor="password">密码：</label>
        <input
          id="password"
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
        />
      </div>
      <div>
        <label>
          <input
            name="remember"
            type="checkbox"
            checked={formData.remember}
            onChange={handleChange}
          />
          记住我
        </label>
      </div>
      <button type="submit">登录</button>
    </form>
  );
}
```

## 14.2 非受控组件

非受控组件使用 `ref` 直接访问 DOM 值：

```jsx
import { useRef } from 'react';

function FileUpload() {
  const fileInputRef = useRef(null);

  function handleSubmit(e) {
    e.preventDefault();
    const file = fileInputRef.current.files[0];
    console.log('选择的文件：', file?.name);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" ref={fileInputRef} />
      <button type="submit">上传</button>
    </form>
  );
}
```

| 特性 | 受控组件 | 非受控组件 |
|------|---------|-----------|
| 数据存储 | React state | DOM 自身 |
| 即时验证 | ✅ 容易 | ❌ 较难 |
| 条件禁用提交 | ✅ 容易 | ❌ 较难 |
| 动态输入 | ✅ 容易 | ❌ 较难 |
| 文件输入 | ❌ 不支持 | ✅ 必须用 |

## 14.3 各种表单元素

### select 下拉框

```jsx
function FruitPicker() {
  const [fruit, setFruit] = useState('apple');

  return (
    <select value={fruit} onChange={e => setFruit(e.target.value)}>
      <option value="apple">苹果</option>
      <option value="banana">香蕉</option>
      <option value="orange">橙子</option>
    </select>
  );
}
```

### textarea 文本域

```jsx
function Essay() {
  const [text, setText] = useState('请输入你的文章...');

  return (
    <textarea value={text} onChange={e => setText(e.target.value)} />
  );
}
```

### 单选按钮

```jsx
function GenderSelect() {
  const [gender, setGender] = useState('male');

  return (
    <div>
      <label>
        <input
          type="radio"
          value="male"
          checked={gender === 'male'}
          onChange={e => setGender(e.target.value)}
        />
        男
      </label>
      <label>
        <input
          type="radio"
          value="female"
          checked={gender === 'female'}
          onChange={e => setGender(e.target.value)}
        />
        女
      </label>
    </div>
  );
}
```

## 14.4 表单验证

```jsx
import { useState } from 'react';

function RegistrationForm() {
  const [form, setForm] = useState({ email: '', password: '' });
  const [errors, setErrors] = useState({});

  function validate() {
    const newErrors = {};
    if (!form.email) {
      newErrors.email = '邮箱不能为空';
    } else if (!/\S+@\S+\.\S+/.test(form.email)) {
      newErrors.email = '邮箱格式不正确';
    }
    if (!form.password) {
      newErrors.password = '密码不能为空';
    } else if (form.password.length < 6) {
      newErrors.password = '密码至少 6 个字符';
    }
    return newErrors;
  }

  function handleSubmit(e) {
    e.preventDefault();
    const newErrors = validate();
    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }
    setErrors({});
    console.log('验证通过，提交数据：', form);
  }

  function handleChange(e) {
    setForm(prev => ({ ...prev, [e.target.name]: e.target.value }));
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          name="email"
          placeholder="邮箱"
          value={form.email}
          onChange={handleChange}
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>
      <div>
        <input
          name="password"
          type="password"
          placeholder="密码"
          value={form.password}
          onChange={handleChange}
        />
        {errors.password && <span className="error">{errors.password}</span>}
      </div>
      <button type="submit">注册</button>
    </form>
  );
}
```

## 14.5 异步表单提交

```jsx
import { useState } from 'react';

function ContactForm() {
  const [status, setStatus] = useState('idle');
  const [error, setError] = useState(null);

  async function handleSubmit(e) {
    e.preventDefault();
    setStatus('submitting');
    setError(null);

    const formData = new FormData(e.target);

    try {
      await fetch('/api/contact', {
        method: 'POST',
        body: formData,
      });
      setStatus('success');
    } catch (err) {
      setStatus('idle');
      setError(err.message);
    }
  }

  if (status === 'success') {
    return <p>消息已发送！</p>;
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" placeholder="姓名" required />
      <textarea name="message" placeholder="留言" required />
      <button disabled={status === 'submitting'}>
        {status === 'submitting' ? '发送中...' : '发送'}
      </button>
      {error && <p className="error">{error}</p>}
    </form>
  );
}
```

---

# 第十五章 组件设计模式

## 15.1 容器组件与展示组件

将逻辑与展示分离：

```jsx
// 展示组件：只负责 UI 渲染
function UserList({ users, onDelete }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>
          {user.name}
          <button onClick={() => onDelete(user.id)}>删除</button>
        </li>
      ))}
    </ul>
  );
}

// 容器组件：负责数据获取和状态管理
function UserListContainer() {
  const { data: users, loading } = useFetch('/api/users');

  function handleDelete(id) {
    fetch(`/api/users/${id}`, { method: 'DELETE' });
  }

  if (loading) return <p>加载中...</p>;
  return <UserList users={users} onDelete={handleDelete} />;
}
```

## 15.2 组合模式（Composition）

通过 `children` 和插槽实现灵活的组件组合：

```jsx
function Layout({ sidebar, children }) {
  return (
    <div className="layout">
      <aside className="sidebar">{sidebar}</aside>
      <main className="content">{children}</main>
    </div>
  );
}

function App() {
  return (
    <Layout sidebar={<Navigation />}>
      <h1>主内容区域</h1>
      <p>这里是页面的主要内容。</p>
    </Layout>
  );
}
```

## 15.3 Render Props

通过函数 prop 将渲染逻辑委托给使用者：

```jsx
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    function handleMouseMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    window.addEventListener('mousemove', handleMouseMove);
    return () => window.removeEventListener('mousemove', handleMouseMove);
  }, []);

  return render(position);
}

// 使用
function App() {
  return (
    <MouseTracker
      render={({ x, y }) => (
        <p>鼠标位置：{x}, {y}</p>
      )}
    />
  );
}
```

> **提示：** 在 Hooks 时代，Render Props 很多时候可以被自定义 Hook 替代（如上例可以用 `useMousePosition()`）。

## 15.4 高阶组件（HOC）

高阶组件是一个函数，接收组件，返回增强后的新组件：

```jsx
function withLogger(WrappedComponent) {
  return function LoggerComponent(props) {
    useEffect(() => {
      console.log(`${WrappedComponent.name} 已挂载`);
      return () => console.log(`${WrappedComponent.name} 已卸载`);
    }, []);

    return <WrappedComponent {...props} />;
  };
}

function UserProfile({ name }) {
  return <h1>{name}</h1>;
}

const UserProfileWithLogger = withLogger(UserProfile);
```

> **提示：** HOC 在 Hooks 出现前很常用，现在更推荐使用自定义 Hook 来复用逻辑。

## 15.5 复合组件模式

让一组相关的组件共享隐式状态：

```jsx
import { createContext, useContext, useState } from 'react';

const TabsContext = createContext();

function Tabs({ children, defaultTab }) {
  const [activeTab, setActiveTab] = useState(defaultTab);

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div className="tab-list">{children}</div>;
}

function Tab({ value, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  return (
    <button
      className={activeTab === value ? 'tab active' : 'tab'}
      onClick={() => setActiveTab(value)}
    >
      {children}
    </button>
  );
}

function TabPanel({ value, children }) {
  const { activeTab } = useContext(TabsContext);
  if (activeTab !== value) return null;
  return <div className="tab-panel">{children}</div>;
}

// 使用：API 清晰、灵活
function App() {
  return (
    <Tabs defaultTab="tab1">
      <TabList>
        <Tab value="tab1">选项卡 1</Tab>
        <Tab value="tab2">选项卡 2</Tab>
        <Tab value="tab3">选项卡 3</Tab>
      </TabList>
      <TabPanel value="tab1">内容 1</TabPanel>
      <TabPanel value="tab2">内容 2</TabPanel>
      <TabPanel value="tab3">内容 3</TabPanel>
    </Tabs>
  );
}
```

## 15.6 错误边界（Error Boundary）

错误边界可以捕获子组件树中的 JavaScript 错误，防止整个应用崩溃：

```jsx
import { Component } from 'react';

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('错误边界捕获：', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <h1>出了点问题。</h1>;
    }
    return this.props.children;
  }
}

// 使用
function App() {
  return (
    <ErrorBoundary fallback={<p>该模块加载失败</p>}>
      <RiskyComponent />
    </ErrorBoundary>
  );
}
```

> **注意：** 目前错误边界只能用类组件实现。推荐使用 [react-error-boundary](https://github.com/bvaughn/react-error-boundary) 库来简化。

---

# 第十六章 React 19 新特性

## 16.1 use Hook

`use` 是一个特殊的 Hook，可以在渲染过程中读取 Promise 或 Context 的值：

```jsx
import { use, Suspense } from 'react';

function UserProfile({ userPromise }) {
  const user = use(userPromise);
  return <h1>{user.name}</h1>;
}

function App() {
  const userPromise = fetch('/api/user').then(r => r.json());

  return (
    <Suspense fallback={<p>加载中...</p>}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}
```

`use` 与其他 Hook 不同，它**可以在条件语句中调用**。

### 用 use 读取 Context

```jsx
import { use } from 'react';
import { ThemeContext } from './ThemeContext';

function Button() {
  const theme = use(ThemeContext);
  return <button className={theme}>主题按钮</button>;
}
```

## 16.2 Actions 与表单

React 19 为表单引入了原生的 Actions 支持：

```jsx
function UpdateNameForm() {
  async function updateName(formData) {
    'use server';
    const name = formData.get('name');
    await db.users.update({ name });
  }

  return (
    <form action={updateName}>
      <input name="name" placeholder="输入新名称" />
      <button type="submit">更新</button>
    </form>
  );
}
```

## 16.3 useFormStatus

获取父级 `<form>` 的提交状态：

```jsx
import { useFormStatus } from 'react-dom';

function SubmitButton() {
  const { pending } = useFormStatus();

  return (
    <button type="submit" disabled={pending}>
      {pending ? '提交中...' : '提交'}
    </button>
  );
}

function MyForm() {
  async function handleSubmit(formData) {
    await saveToServer(formData);
  }

  return (
    <form action={handleSubmit}>
      <input name="title" />
      <SubmitButton />
    </form>
  );
}
```

## 16.4 useOptimistic

在异步操作完成前乐观地更新 UI：

```jsx
import { useOptimistic } from 'react';

function MessageList({ messages, sendMessage }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (currentMessages, newMessage) => [
      ...currentMessages,
      { text: newMessage, sending: true },
    ]
  );

  async function handleSend(formData) {
    const message = formData.get('message');
    addOptimisticMessage(message);
    await sendMessage(message);
  }

  return (
    <div>
      {optimisticMessages.map((msg, i) => (
        <p key={i} style={{ opacity: msg.sending ? 0.5 : 1 }}>
          {msg.text}
          {msg.sending && ' (发送中...)'}
        </p>
      ))}
      <form action={handleSend}>
        <input name="message" />
        <button type="submit">发送</button>
      </form>
    </div>
  );
}
```

## 16.5 useActionState

管理表单 action 的状态：

```jsx
import { useActionState } from 'react';

function AddToCart({ itemId }) {
  const [message, formAction, isPending] = useActionState(
    async (previousState, formData) => {
      const result = await addItemToCart(itemId);
      if (result.success) {
        return '已添加到购物车！';
      }
      return '添加失败，请重试。';
    },
    null // 初始 message
  );

  return (
    <form action={formAction}>
      <button type="submit" disabled={isPending}>
        {isPending ? '添加中...' : '加入购物车'}
      </button>
      {message && <p>{message}</p>}
    </form>
  );
}
```

## 16.6 ref 作为 prop

React 19 中，函数组件可以直接接收 `ref` 作为 prop，不再需要 `forwardRef`：

```jsx
// React 19：直接接收 ref prop
function FancyInput({ ref, ...props }) {
  return <input ref={ref} className="fancy" {...props} />;
}

// 之前需要 forwardRef：
// const FancyInput = forwardRef(function FancyInput(props, ref) { ... });
```

## 16.7 其他改进

| 特性 | 说明 |
|------|------|
| **自动批处理增强** | 所有更新（包括 setTimeout、Promise 中的）都自动批处理 |
| **文档元数据支持** | 组件中可以直接渲染 `<title>`、`<meta>`、`<link>` |
| **样式表优先级** | `<link rel="stylesheet" precedence="...">` 控制加载顺序 |
| **资源预加载** | `prefetchDNS`、`preconnect`、`preload`、`preinit` API |
| **ref 清理函数** | `ref` 回调可以返回清理函数 |

---

# 第十七章 React 生态与工程化

## 17.1 框架选择

React 官方推荐使用框架来构建生产应用：

| 框架 | 特点 | 适用场景 |
|------|------|----------|
| **Next.js** | 全栈框架，SSR/SSG/ISR，App Router | 大多数 Web 应用 |
| **Remix** | 全栈框架，嵌套路由，渐进增强 | 注重 Web 标准的应用 |
| **Gatsby** | 静态站点生成，GraphQL 数据层 | 博客、文档站点 |
| **Expo** | React Native 框架 | 移动端应用 |

## 17.2 路由

React 本身不包含路由，常用的路由库：

```jsx
// React Router v6+ 示例
import { BrowserRouter, Routes, Route, Link, useParams } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">首页</Link>
        <Link to="/about">关于</Link>
        <Link to="/users/1">用户 1</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users/:id" element={<UserDetail />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

function UserDetail() {
  const { id } = useParams();
  return <h1>用户 ID：{id}</h1>;
}
```

## 17.3 状态管理

| 方案 | 定位 | 特点 |
|------|------|------|
| **useState + Context** | 轻量 | 内置方案，适合中小型应用 |
| **Zustand** | 轻量 | API 简洁，无 Provider，包体积小 |
| **Jotai** | 原子化 | 自底向上的原子化状态管理 |
| **Redux Toolkit** | 重量级 | 生态完善，适合大型应用 |
| **TanStack Query** | 服务端状态 | 专注于异步数据获取和缓存 |

### Zustand 示例

```jsx
import { create } from 'zustand';

const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
}));

function Counter() {
  const { count, increment, decrement } = useStore();

  return (
    <div>
      <p>{count}</p>
      <button onClick={increment}>+1</button>
      <button onClick={decrement}>-1</button>
    </div>
  );
}
```

### TanStack Query 示例

```jsx
import { useQuery, QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient();

function UserList() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(res => res.json()),
  });

  if (isLoading) return <p>加载中...</p>;
  if (error) return <p>错误：{error.message}</p>;

  return (
    <ul>
      {data.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <UserList />
    </QueryClientProvider>
  );
}
```

## 17.4 样式方案

| 方案 | 特点 |
|------|------|
| **CSS Modules** | 局部作用域，零运行时，构建工具原生支持 |
| **Tailwind CSS** | 原子化 CSS，开发效率高 |
| **styled-components** | CSS-in-JS，组件级样式 |
| **Vanilla Extract** | 类型安全的零运行时 CSS-in-TS |

```jsx
// CSS Modules 示例
import styles from './Button.module.css';

function Button({ children }) {
  return <button className={styles.primary}>{children}</button>;
}
```

```jsx
// Tailwind CSS 示例
function Button({ children }) {
  return (
    <button className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
      {children}
    </button>
  );
}
```

## 17.5 TypeScript 与 React

React 项目推荐使用 TypeScript：

```tsx
// 定义 Props 类型
interface UserCardProps {
  name: string;
  age: number;
  email?: string;          // 可选属性
  onEdit: (id: number) => void;
  children: React.ReactNode;
}

function UserCard({ name, age, email, onEdit, children }: UserCardProps) {
  return (
    <div>
      <h2>{name}（{age} 岁）</h2>
      {email && <p>{email}</p>}
      {children}
    </div>
  );
}

// 定义 State 类型
interface Todo {
  id: number;
  text: string;
  done: boolean;
}

function TodoApp() {
  const [todos, setTodos] = useState<Todo[]>([]);
  const inputRef = useRef<HTMLInputElement>(null);

  // ...
}
```

常用的 React + TypeScript 类型：

| 类型 | 用途 |
|------|------|
| `React.ReactNode` | 可渲染的任意内容 |
| `React.ReactElement` | JSX 元素 |
| `React.FC<Props>` | 函数组件类型（不推荐，直接用函数声明更好） |
| `React.ChangeEvent<HTMLInputElement>` | input 的 change 事件 |
| `React.FormEvent<HTMLFormElement>` | 表单事件 |
| `React.MouseEvent<HTMLButtonElement>` | 按钮点击事件 |
| `React.CSSProperties` | 内联样式对象 |

## 17.6 测试

### React Testing Library

```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

test('点击按钮后计数增加', () => {
  render(<Counter />);

  const button = screen.getByText('+1');
  const display = screen.getByText('计数：0');

  fireEvent.click(button);

  expect(screen.getByText('计数：1')).toBeInTheDocument();
});

test('输入框可以输入文本', () => {
  render(<SearchBox />);

  const input = screen.getByPlaceholderText('搜索...');
  fireEvent.change(input, { target: { value: 'React' } });

  expect(input.value).toBe('React');
});
```

## 17.7 项目结构建议

```
src/
├── assets/              # 静态资源（图片、字体等）
├── components/          # 通用/共享组件
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.module.css
│   │   └── Button.test.tsx
│   └── ...
├── features/            # 功能模块（按业务划分）
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── api.ts
│   └── ...
├── hooks/               # 全局自定义 Hook
├── contexts/            # Context 定义
├── utils/               # 工具函数
├── types/               # TypeScript 类型定义
├── App.tsx
└── main.tsx
```

---

# 附录 速查表

## Hooks 速查

| Hook | 用途 | 示例 |
|------|------|------|
| `useState` | 状态管理 | `const [v, setV] = useState(init)` |
| `useEffect` | 副作用 | `useEffect(() => { ... }, [deps])` |
| `useContext` | 消费 Context | `const val = useContext(MyCtx)` |
| `useRef` | 可变引用 / DOM 访问 | `const ref = useRef(null)` |
| `useReducer` | 复杂状态逻辑 | `const [s, dispatch] = useReducer(fn, init)` |
| `useMemo` | 缓存计算结果 | `const val = useMemo(() => compute(), [deps])` |
| `useCallback` | 缓存函数引用 | `const fn = useCallback(() => {}, [deps])` |
| `useId` | 生成唯一 ID | `const id = useId()` |
| `useTransition` | 低优先级更新 | `const [pending, start] = useTransition()` |
| `useDeferredValue` | 延迟值更新 | `const deferred = useDeferredValue(val)` |
| `useImperativeHandle` | 自定义 ref 暴露 | `useImperativeHandle(ref, () => ({ ... }))` |
| `useLayoutEffect` | 同步 DOM 副作用 | 用法同 `useEffect`，在 DOM 更新后同步执行 |
| `use` (React 19) | 读取 Promise/Context | `const data = use(promise)` |
| `useFormStatus` (React 19) | 表单提交状态 | `const { pending } = useFormStatus()` |
| `useOptimistic` (React 19) | 乐观更新 | `const [opt, addOpt] = useOptimistic(state, fn)` |
| `useActionState` (React 19) | Action 状态 | `const [state, action, pending] = useActionState(fn, init)` |

## JSX 速查

```jsx
{/* 变量 */}          {name}
{/* 表达式 */}        {a + b}
{/* 三元 */}          {ok ? <A /> : <B />}
{/* 逻辑与 */}        {show && <Modal />}
{/* 列表 */}          {arr.map(x => <li key={x.id}>{x.name}</li>)}
{/* 样式对象 */}      <div style={{ color: 'red' }} />
{/* CSS 类名 */}      <div className="box" />
{/* 展开属性 */}      <Input {...props} />
{/* Fragment */}      <>{a}{b}</>
```

## 事件速查

| 事件 | 触发时机 |
|------|----------|
| `onClick` | 点击 |
| `onChange` | 输入值变化 |
| `onSubmit` | 表单提交 |
| `onFocus` / `onBlur` | 获得 / 失去焦点 |
| `onMouseEnter` / `onMouseLeave` | 鼠标进入 / 离开 |
| `onKeyDown` / `onKeyUp` | 键盘按下 / 松开 |
| `onScroll` | 滚动 |
| `onDrag` / `onDrop` | 拖拽 |

## 不可变更新速查

```jsx
// 对象
setObj({ ...obj, key: newValue })
setObj({ ...obj, nested: { ...obj.nested, key: newValue } })

// 数组
setArr([...arr, newItem])                        // 添加到末尾
setArr([newItem, ...arr])                        // 添加到开头
setArr(arr.filter(x => x.id !== id))             // 删除
setArr(arr.map(x => x.id === id ? newX : x))    // 替换
setArr([...arr].sort(compareFn))                 // 排序
```

## 常用社区库速查

| 类别 | 库 |
|------|-----|
| 路由 | React Router, TanStack Router |
| 状态管理 | Zustand, Jotai, Redux Toolkit |
| 数据获取 | TanStack Query, SWR |
| 表单 | React Hook Form, Formik |
| UI 组件 | shadcn/ui, Ant Design, MUI, Radix UI |
| 动画 | Framer Motion, React Spring |
| 图表 | Recharts, Visx |
| 国际化 | react-i18next, next-intl |
| 测试 | React Testing Library, Vitest |
| 文档 | Storybook |

---

> **学习建议：** React 官方文档（https://react.dev）是最好的学习资源。本教程覆盖了从入门到进阶的核心内容，建议按章节顺序学习，每个概念都动手编写代码实践。掌握 React 的关键在于理解**组件化思维**、**单向数据流**和 **Hooks 的工作原理**。
