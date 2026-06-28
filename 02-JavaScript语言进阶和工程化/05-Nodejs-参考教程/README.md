# 现代 Node.js 参考教程
# 现代 Node.js 教程

> **基于 [Node.js 官方文档](https://nodejs.org/zh-cn) 编写**
> 以 Node.js 最新 LTS 版本为基准，从零开始系统掌握 Node.js 后端开发。

---

# 第一章 Node.js 简介

## 1.1 什么是 Node.js

Node.js 是一个基于 Chrome V8 引擎的 **JavaScript 运行时环境**。它让 JavaScript 脱离了浏览器，可以在服务器端、命令行、桌面应用等任何地方运行。

> Node.js® 是一个免费、开源、跨平台的 JavaScript 运行时环境，它让开发人员能够创建服务器、Web 应用、命令行工具和脚本。 —— [nodejs.org](https://nodejs.org/zh-cn)

## 1.2 诞生背景

2009 年，Ryan Dahl 希望用高级语言编写高性能 Web 服务。他选择了 JavaScript，原因很巧妙：

1. **单线程天性**：JavaScript 天生只能做异步 I/O，不会像其他语言那样让开发者"偷懒"使用同步 I/O
2. **V8 引擎开源**：Google 投入大量资源优化 V8，性能极高
3. **开发者基数大**：全世界已有海量 JavaScript 前端开发者

于是 Node.js 诞生了——它第一次把 JavaScript 带入后端服务器开发领域。

## 1.3 核心优势

| 特性 | 说明 |
|------|------|
| **事件驱动** | 基于事件循环的非阻塞 I/O 模型，天然适合高并发场景 |
| **V8 引擎** | JIT 编译，JavaScript 性能堪比 C++ |
| **单线程模型** | 无需担心多线程竞争条件，编程模型简单 |
| **npm 生态** | 全球最大的包注册中心，超过 200 万个包 |
| **全栈统一** | 前后端同一语言，共享代码与类型定义 |
| **跨平台** | Windows、macOS、Linux 全面支持 |

## 1.4 适用场景

- **Web 服务器与 API**：RESTful API、GraphQL 服务
- **实时应用**：即时通讯、协作编辑、在线游戏
- **微服务**：轻量、启动快，适合容器化部署
- **命令行工具**：webpack、ESLint、Prettier 都是 Node.js 开发的
- **服务端渲染（SSR）**：Next.js、Nuxt.js 等框架
- **IoT 与嵌入式**：运行在资源受限的设备上

## 1.5 不适用场景

- **CPU 密集型计算**：大量数学运算、视频编码等（可用 Worker Threads 缓解）
- **需要强类型保障**：大型团队建议配合 TypeScript 使用

---

# 第二章 安装与环境配置

## 2.1 下载与安装

访问 [Node.js 官网](https://nodejs.org/zh-cn) 下载安装包：

- **LTS 版本**（推荐）：长期支持，适合生产环境
- **Current 版本**：包含最新特性，适合尝鲜

**Windows 安装要点：**
- 选择 Prebuilt Installer，根据操作系统和 CPU 架构下载
- 安装时务必勾选 **Add to Path**
- 安装完成后打开 PowerShell 验证

**macOS / Linux：**
- 可使用官方安装包
- 推荐使用版本管理器（见 2.3 节）

## 2.2 验证安装

打开终端，输入以下命令：

```bash
node -v
# 输出类似：v24.14.0

npm -v
# 输出类似：10.8.0
```

如果看到版本号，说明安装成功。

## 2.3 使用版本管理器（推荐）

在实际开发中，不同项目可能需要不同版本的 Node.js。推荐使用版本管理器：

**nvm（macOS / Linux）：**

```bash
# 安装 nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash

# 安装指定版本
nvm install 24
nvm install 22

# 切换版本
nvm use 24

# 查看已安装版本
nvm ls
```

**fnm（跨平台，Rust 编写，更快）：**

```bash
# Windows (PowerShell)
winget install Schniz.fnm

# macOS / Linux
curl -fsSL https://fnm.vercel.app/install | bash

# 使用
fnm install 24
fnm use 24
fnm default 24
```

**项目级版本锁定：**

在项目根目录创建 `.node-version` 文件：

```
24
```

fnm 和 nvm 都会自动识别并切换到指定版本。

## 2.4 Node.js REPL

直接在终端输入 `node` 即可进入交互式解释器环境（REPL）：

```bash
$ node
Welcome to Node.js v24.14.0.
Type ".help" for more information.
> 1 + 2
3
> 'Hello' + ' ' + 'Node'
'Hello Node'
> Math.max(10, 20, 30)
30
> .exit
```

REPL 常用命令：

| 命令 | 说明 |
|------|------|
| `.help` | 显示帮助 |
| `.exit` | 退出（也可按两次 `Ctrl+C`） |
| `.editor` | 进入多行编辑模式 |
| `.clear` | 清除当前上下文 |

---

# 第三章 第一个 Node.js 程序

## 3.1 Hello World

创建文件 `hello.mjs`：

```javascript
console.log('Hello, Node.js!');
```

在终端运行：

```bash
node hello.mjs
# 输出：Hello, Node.js!
```

就这么简单！`.mjs` 扩展名表示这是一个 ESM（ECMAScript Module）模块文件。

## 3.2 命令行模式 vs 交互模式

这是初学者最容易混淆的两种模式：

**命令行模式** —— 执行整个文件：

```bash
PS C:\workspace> node hello.mjs
Hello, Node.js!
PS C:\workspace>
```

**交互模式（REPL）** —— 逐行执行：

```bash
PS C:\workspace> node
> console.log('Hello')
Hello
undefined
>
```

交互模式会自动打印每一行表达式的返回值（`console.log` 返回 `undefined`），而命令行模式只打印你主动输出的内容。

## 3.3 接收命令行参数

Node.js 通过 `process.argv` 数组获取命令行参数：

```javascript
// args.mjs
const args = process.argv.slice(2);
console.log('参数列表:', args);
console.log('第一个参数:', args[0]);
```

```bash
node args.mjs hello world
# 参数列表: [ 'hello', 'world' ]
# 第一个参数: hello
```

`process.argv` 的前两个元素分别是 `node` 可执行文件路径和当前脚本路径，所以用 `slice(2)` 跳过。

## 3.4 使用 process 对象

`process` 是 Node.js 的全局对象，提供了当前进程的信息和控制能力：

```javascript
// process-info.mjs

// 当前工作目录
console.log('CWD:', process.cwd());

// Node.js 版本
console.log('Node:', process.version);

// 操作系统平台
console.log('Platform:', process.platform);

// CPU 架构
console.log('Arch:', process.arch);

// 环境变量
console.log('PATH:', process.env.PATH);

// 进程 ID
console.log('PID:', process.pid);

// 内存使用
console.log('Memory:', process.memoryUsage());
```

## 3.5 退出进程

```javascript
// 正常退出（退出码 0）
process.exit(0);

// 异常退出（退出码 1）
process.exit(1);

// 更优雅的方式：设置退出码，让进程自然结束
process.exitCode = 1;
```

---

# 第四章 搭建开发环境

## 4.1 编辑器选择

**VS Code**（强烈推荐）：
- 微软出品，免费开源
- 原生支持 JavaScript / TypeScript
- 内置终端、调试器、Git
- 海量扩展插件

推荐安装的 VS Code 扩展：

| 扩展 | 说明 |
|------|------|
| ESLint | 代码检查 |
| Prettier | 代码格式化 |
| Error Lens | 行内显示错误 |
| REST Client | 在编辑器中测试 API |

## 4.2 项目初始化

每个 Node.js 项目都应该有一个 `package.json` 文件：

```bash
# 创建项目目录
mkdir my-project
cd my-project

# 初始化 package.json
npm init -y
```

生成的 `package.json`：

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

**启用 ESM 模块：** 在 `package.json` 中添加 `"type": "module"`：

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "type": "module"
}
```

这样项目中所有 `.js` 文件都会被视为 ESM 模块，无需使用 `.mjs` 扩展名。

## 4.3 调试 Node.js 程序

**方法一：VS Code 内置调试器**

在 VS Code 中打开 `.js` 文件，按 `F5` 选择 "Node.js" 即可启动调试。可以设置断点、单步执行、查看变量。

**方法二：Chrome DevTools**

```bash
node --inspect hello.mjs
```

然后在 Chrome 中访问 `chrome://inspect`，点击 "inspect" 即可使用 Chrome DevTools 调试。

**方法三：`console` 调试**

最简单直接的方式：

```javascript
console.log('变量值:', someVariable);
console.dir(complexObject, { depth: null });
console.time('操作耗时');
// ... 操作
console.timeEnd('操作耗时');
console.table([{ name: 'Alice', age: 25 }, { name: 'Bob', age: 30 }]);
```

## 4.4 使用 --watch 模式

Node.js 18+ 内置了文件监视功能，修改文件后自动重启：

```bash
node --watch app.mjs
```

这替代了以前需要 `nodemon` 等第三方工具的场景。

---

# 第五章 模块系统

Node.js 有两套模块系统：早期的 **CommonJS (CJS)** 和现代的 **ECMAScript Modules (ESM)**。现代项目推荐使用 ESM。

## 5.1 ESM 模块（推荐）

ESM 是 JavaScript 语言原生支持的模块系统，使用 `export` 和 `import` 关键字。

**导出模块：**

```javascript
// math.mjs

// 命名导出
export function add(a, b) {
  return a + b;
}

export function multiply(a, b) {
  return a * b;
}

// 也可以集中导出
const PI = 3.14159;
const E = 2.71828;
export { PI, E };
```

**默认导出：**

```javascript
// logger.mjs

export default function log(message) {
  console.log(`[${new Date().toISOString()}] ${message}`);
}
```

**导入模块：**

```javascript
// app.mjs

// 导入命名导出
import { add, multiply, PI } from './math.mjs';

// 导入默认导出
import log from './logger.mjs';

// 导入全部为命名空间
import * as math from './math.mjs';

// 重命名导入
import { add as sum } from './math.mjs';

console.log(add(1, 2));       // 3
console.log(multiply(3, 4));   // 12
console.log(PI);               // 3.14159
log('程序启动');
console.log(math.E);           // 2.71828
console.log(sum(10, 20));      // 30
```

**ESM 特点：**

- 默认启用严格模式，无需 `'use strict'`
- `import` 语句会被提升到文件顶部
- 静态分析，支持 Tree Shaking
- 使用 `.mjs` 扩展名，或在 `package.json` 中设置 `"type": "module"` 后使用 `.js`

## 5.2 CommonJS 模块

CommonJS 是 Node.js 最初的模块系统，至今仍被大量使用。

**导出：**

```javascript
// hello.js (CommonJS)

function greet(name) {
  console.log(`Hello, ${name}!`);
}

function hi(name) {
  console.log(`Hi, ${name}!`);
}

// 导出对象
module.exports = { greet, hi };

// 或导出单个值
// module.exports = greet;
```

**导入：**

```javascript
// main.js (CommonJS)

const { greet, hi } = require('./hello');

greet('World');  // Hello, World!
hi('Node');      // Hi, Node!
```

**模块原理：** Node.js 将每个文件包装在一个函数中执行：

```javascript
(function(exports, require, module, __filename, __dirname) {
  // 你的代码在这里
});
```

这就是为什么每个模块中的变量是私有的——它们都是函数内部的局部变量。

## 5.3 `module.exports` vs `exports`

- `exports` 是 `module.exports` 的一个引用
- 可以往 `exports` 上添加属性：`exports.foo = bar`
- **不能**直接给 `exports` 赋值：`exports = { foo: bar }` 无效
- 最终导出的始终是 `module.exports`

**结论：** 始终使用 `module.exports = xxx` 来导出模块，不会出错。

## 5.4 导入 Node.js 内置模块

内置模块推荐使用 `node:` 协议前缀：

```javascript
// ESM（推荐）
import fs from 'node:fs';
import { readFile } from 'node:fs/promises';
import path from 'node:path';

// CommonJS
const fs = require('node:fs');
const path = require('node:path');
```

`node:` 前缀的好处是明确区分内置模块和第三方模块，避免同名包攻击。

## 5.5 ESM vs CJS 对比

| 特性 | ESM | CJS |
|------|-----|-----|
| 语法 | `import` / `export` | `require()` / `module.exports` |
| 加载方式 | 静态（编译时解析） | 动态（运行时加载） |
| 顶层 `await` | 支持 | 不支持 |
| 严格模式 | 默认开启 | 需手动声明 |
| `__dirname` | 不可用（需替代方案） | 可用 |
| Tree Shaking | 支持 | 不支持 |
| 浏览器兼容 | 原生支持 | 不支持 |

**ESM 中获取 `__dirname`：**

```javascript
import { fileURLToPath } from 'node:url';
import { dirname } from 'node:path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
```

Node.js 21+ 提供了更简洁的方式：

```javascript
const __dirname = import.meta.dirname;
const __filename = import.meta.filename;
```

## 5.6 动态导入

ESM 支持使用 `import()` 函数进行动态导入，它返回一个 Promise：

```javascript
async function loadModule(name) {
  const module = await import(`./plugins/${name}.mjs`);
  module.default();
}

// 条件导入
if (process.platform === 'win32') {
  const { windowsUtils } = await import('./win-utils.mjs');
}
```

---

# 第六章 npm 包管理

## 6.1 npm 是什么

npm（Node Package Manager）是 Node.js 的包管理工具，随 Node.js 一起安装。它解决了以下问题：

- 从 [npmjs.com](https://www.npmjs.com/) 注册中心下载第三方包
- 管理项目依赖关系和版本
- 发布自己的包供他人使用
- 运行项目脚本

## 6.2 安装依赖

```bash
# 安装生产依赖
npm install lodash
npm install express koa

# 安装开发依赖
npm install --save-dev eslint prettier

# 简写
npm i lodash
npm i -D eslint

# 全局安装
npm install -g npm-check-updates

# 安装特定版本
npm install lodash@4.17.21

# 根据 package.json 安装所有依赖
npm install
```

## 6.3 package.json 详解

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "一个示例 Node.js 应用",
  "type": "module",
  "main": "src/index.js",
  "scripts": {
    "start": "node src/index.js",
    "dev": "node --watch src/index.js",
    "test": "node --test",
    "lint": "eslint src/"
  },
  "dependencies": {
    "express": "^4.18.2"
  },
  "devDependencies": {
    "eslint": "^9.0.0"
  },
  "engines": {
    "node": ">=20.0.0"
  }
}
```

**版本号语义（SemVer）：**

| 符号 | 含义 | 示例 |
|------|------|------|
| `^4.18.2` | 兼容补丁和次版本更新 | `>=4.18.2 <5.0.0` |
| `~4.18.2` | 仅兼容补丁更新 | `>=4.18.2 <4.19.0` |
| `4.18.2` | 锁定精确版本 | 只使用 `4.18.2` |
| `*` | 任意版本 | 不推荐使用 |

## 6.4 npm 脚本

`package.json` 的 `scripts` 字段定义了可运行的脚本：

```bash
npm run dev      # 执行 "dev" 脚本
npm start        # 执行 "start" 脚本（可省略 run）
npm test         # 执行 "test" 脚本（可省略 run）
npm run lint     # 执行 "lint" 脚本
```

## 6.5 package-lock.json

`package-lock.json` 记录了依赖树中所有包的精确版本，确保团队成员和 CI 环境安装完全一致的依赖。

- **必须提交到 Git 仓库**
- 不要手动编辑
- 使用 `npm ci` 在 CI 环境中安装（比 `npm install` 更快更严格）

## 6.6 npx 执行命令

`npx` 用于执行 npm 包中的命令，无需全局安装：

```bash
# 执行本地安装的包
npx eslint src/

# 临时下载并执行（不安装到本地）
npx create-vite my-app

# 指定版本
npx node@20 --version
```

## 6.7 常用 npm 命令速查

```bash
npm init -y              # 初始化项目
npm install              # 安装所有依赖
npm install <pkg>        # 安装并保存到 dependencies
npm install -D <pkg>     # 安装并保存到 devDependencies
npm uninstall <pkg>      # 卸载包
npm update               # 更新所有依赖
npm outdated             # 查看过期依赖
npm ls                   # 查看依赖树
npm audit                # 安全审计
npm audit fix            # 自动修复安全问题
npm cache clean --force  # 清除缓存
```

---

# 第七章 内置模块 — fs 文件系统

`fs` 是 Node.js 最常用的内置模块之一，用于读写文件和目录操作。它提供了三套 API：回调版、同步版和 Promise 版。

## 7.1 三种 API 风格

```javascript
// 1. 回调风格（传统）
import { readFile } from 'node:fs';
readFile('file.txt', 'utf-8', (err, data) => {
  if (err) throw err;
  console.log(data);
});

// 2. 同步风格
import { readFileSync } from 'node:fs';
const data = readFileSync('file.txt', 'utf-8');
console.log(data);

// 3. Promise 风格（推荐）
import { readFile } from 'node:fs/promises';
const data = await readFile('file.txt', 'utf-8');
console.log(data);
```

**现代 Node.js 开发推荐使用 `node:fs/promises`**，配合 `async/await` 使用，代码最简洁。

## 7.2 读取文件

```javascript
import { readFile } from 'node:fs/promises';

// 读取文本文件
const text = await readFile('config.json', 'utf-8');
console.log(text);

// 读取二进制文件（不指定编码，返回 Buffer）
const buffer = await readFile('image.png');
console.log(buffer.length, '字节');

// 解析 JSON 文件
const config = JSON.parse(await readFile('config.json', 'utf-8'));
```

## 7.3 写入文件

```javascript
import { writeFile, appendFile } from 'node:fs/promises';

// 写入文件（覆盖）
await writeFile('output.txt', 'Hello, Node.js!\n', 'utf-8');

// 追加内容
await appendFile('log.txt', `[${new Date().toISOString()}] 日志条目\n`);

// 写入 JSON
const data = { name: 'Alice', age: 25 };
await writeFile('data.json', JSON.stringify(data, null, 2));

// 写入 Buffer
const buf = Buffer.from('二进制数据');
await writeFile('binary.dat', buf);
```

## 7.4 文件信息

```javascript
import { stat, access, constants } from 'node:fs/promises';

// 获取文件信息
const st = await stat('sample.txt');
console.log('是文件:', st.isFile());
console.log('是目录:', st.isDirectory());
console.log('大小:', st.size, '字节');
console.log('创建时间:', st.birthtime);
console.log('修改时间:', st.mtime);

// 检查文件是否存在
async function fileExists(path) {
  try {
    await access(path, constants.F_OK);
    return true;
  } catch {
    return false;
  }
}
```

## 7.5 目录操作

```javascript
import { mkdir, readdir, rm, rename, cp } from 'node:fs/promises';

// 创建目录（recursive 允许创建多层目录）
await mkdir('a/b/c', { recursive: true });

// 读取目录
const files = await readdir('.');
console.log(files);

// 读取目录（包含文件类型）
const entries = await readdir('.', { withFileTypes: true });
for (const entry of entries) {
  const type = entry.isDirectory() ? '目录' : '文件';
  console.log(`${type}: ${entry.name}`);
}

// 重命名 / 移动
await rename('old.txt', 'new.txt');

// 删除文件
await rm('temp.txt');

// 删除目录（递归）
await rm('temp-dir', { recursive: true, force: true });

// 复制文件
await cp('source.txt', 'dest.txt');

// 复制目录（递归）
await cp('src-dir', 'dest-dir', { recursive: true });
```

## 7.6 监听文件变化

```javascript
import { watch } from 'node:fs/promises';

const watcher = watch('./src', { recursive: true });
for await (const event of watcher) {
  console.log(`${event.eventType}: ${event.filename}`);
}
```

## 7.7 异步 vs 同步的选择

| 场景 | 推荐方式 |
|------|----------|
| 服务器运行时的业务逻辑 | `fs/promises`（异步） |
| 程序启动时读取配置 | `readFileSync`（同步） |
| CLI 工具的简单操作 | 同步或异步均可 |
| 大量 async 函数中 | `fs/promises`（异步） |

---

# 第八章 内置模块 — path 路径处理

## 8.1 为什么需要 path 模块

不同操作系统的路径分隔符不同（Windows 用 `\`，其他系统用 `/`）。直接拼接字符串容易出错，`path` 模块帮你正确处理跨平台路径。

## 8.2 常用方法

```javascript
import path from 'node:path';

// 拼接路径
path.join('/usr', 'local', 'bin');
// POSIX: '/usr/local/bin'
// Windows: '\\usr\\local\\bin'

// 解析为绝对路径
path.resolve('src', 'index.js');
// 相对于当前工作目录：'/home/user/project/src/index.js'

// 获取目录名
path.dirname('/home/user/file.txt');  // '/home/user'

// 获取文件名
path.basename('/home/user/file.txt');         // 'file.txt'
path.basename('/home/user/file.txt', '.txt'); // 'file'

// 获取扩展名
path.extname('index.html');  // '.html'
path.extname('app.test.js'); // '.js'

// 解析路径
path.parse('/home/user/file.txt');
// { root: '/', dir: '/home/user', base: 'file.txt', ext: '.txt', name: 'file' }

// 格式化路径对象
path.format({ dir: '/home/user', name: 'file', ext: '.txt' });
// '/home/user/file.txt'

// 相对路径
path.relative('/home/user/a', '/home/user/b/c');
// '../b/c'

// 规范化路径
path.normalize('/foo/bar//baz/../qux');
// '/foo/bar/qux'

// 路径分隔符
path.sep;       // '/' 或 '\\'
path.delimiter; // ':' 或 ';'
```

## 8.3 ESM 中的路径处理

ESM 中没有 `__dirname` 和 `__filename`，需要从 `import.meta` 获取：

```javascript
import { fileURLToPath } from 'node:url';
import path from 'node:path';

// Node.js 21+ 的简洁写法
const __dirname = import.meta.dirname;
const __filename = import.meta.filename;

// 兼容旧版本的写法
const __filename2 = fileURLToPath(import.meta.url);
const __dirname2 = path.dirname(__filename2);

// 实际使用
const configPath = path.join(__dirname, 'config', 'default.json');
```

---

# 第九章 内置模块 — stream 流

## 9.1 什么是流

流（Stream）是一种抽象的数据结构，用于处理连续的数据。就像水管中的水流一样，数据可以从一个地方源源不断地流向另一个地方。

流的核心优势是**不需要一次性将所有数据加载到内存中**，特别适合处理大文件。

## 9.2 四种基本流类型

| 类型 | 说明 | 示例 |
|------|------|------|
| **Readable** | 可读流 | 文件读取、HTTP 请求 |
| **Writable** | 可写流 | 文件写入、HTTP 响应 |
| **Duplex** | 双工流（可读可写） | TCP Socket |
| **Transform** | 转换流 | gzip 压缩、加密 |

## 9.3 读取流

```javascript
import { createReadStream } from 'node:fs';

const rs = createReadStream('large-file.txt', 'utf-8');

rs.on('data', (chunk) => {
  console.log('收到数据块，长度:', chunk.length);
});

rs.on('end', () => {
  console.log('读取完毕');
});

rs.on('error', (err) => {
  console.error('读取出错:', err.message);
});
```

`data` 事件可能触发多次，每次传递的 `chunk` 是流的一部分数据。

## 9.4 写入流

```javascript
import { createWriteStream } from 'node:fs';

const ws = createWriteStream('output.txt', 'utf-8');
ws.write('第一行\n');
ws.write('第二行\n');
ws.write('第三行\n');
ws.end();

ws.on('finish', () => {
  console.log('写入完毕');
});
```

## 9.5 pipe 管道

管道可以把读取流直接连接到写入流，数据自动流转：

```javascript
import { createReadStream, createWriteStream } from 'node:fs';

const rs = createReadStream('input.txt');
const ws = createWriteStream('output.txt');

// 将输入流导入输出流 —— 实现文件复制
rs.pipe(ws);
```

## 9.6 pipeline（推荐）

`pipeline` 是 `pipe` 的增强版，支持错误处理和多个转换流：

```javascript
import { createReadStream, createWriteStream } from 'node:fs';
import { pipeline } from 'node:stream/promises';
import { createGzip, createGunzip } from 'node:zlib';

// 压缩文件
async function compress(src, dest) {
  await pipeline(
    createReadStream(src),
    createGzip(),
    createWriteStream(dest)
  );
  console.log('压缩完成');
}

// 解压文件
async function decompress(src, dest) {
  await pipeline(
    createReadStream(src),
    createGunzip(),
    createWriteStream(dest)
  );
  console.log('解压完成');
}

await compress('big-file.txt', 'big-file.txt.gz');
await decompress('big-file.txt.gz', 'restored.txt');
```

## 9.7 流式处理大文件

对比两种方式处理 1GB 文件：

```javascript
import { readFile, writeFile } from 'node:fs/promises';
import { createReadStream, createWriteStream } from 'node:fs';
import { pipeline } from 'node:stream/promises';

// 方式一：整体读取（可能内存溢出）
const data = await readFile('1gb-file.txt');

// 方式二：流式处理（内存占用很小）
await pipeline(
  createReadStream('1gb-file.txt'),
  createWriteStream('copy.txt')
);
```

---

# 第十章 内置模块 — http 网络

## 10.1 HTTP 服务器基础

Node.js 内置的 `http` 模块可以直接创建 HTTP 服务器：

```javascript
import http from 'node:http';

const server = http.createServer((req, res) => {
  console.log(`${req.method} ${req.url}`);
  res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
  res.end('<h1>Hello, Node.js!</h1>');
});

server.listen(3000, () => {
  console.log('服务器运行在 http://localhost:3000/');
});
```

启动后在浏览器访问 `http://localhost:3000/`，即可看到 "Hello, Node.js!"。

## 10.2 处理不同路由

```javascript
import http from 'node:http';

const server = http.createServer((req, res) => {
  const url = new URL(req.url, `http://${req.headers.host}`);

  res.setHeader('Content-Type', 'text/html; charset=utf-8');

  switch (url.pathname) {
    case '/':
      res.writeHead(200);
      res.end('<h1>首页</h1>');
      break;
    case '/about':
      res.writeHead(200);
      res.end('<h1>关于</h1>');
      break;
    case '/api/time':
      res.setHeader('Content-Type', 'application/json');
      res.writeHead(200);
      res.end(JSON.stringify({ time: new Date().toISOString() }));
      break;
    default:
      res.writeHead(404);
      res.end('<h1>404 Not Found</h1>');
  }
});

server.listen(3000, () => {
  console.log('服务器运行在 http://localhost:3000/');
});
```

## 10.3 处理请求体

```javascript
import http from 'node:http';

const server = http.createServer(async (req, res) => {
  if (req.method === 'POST' && req.url === '/api/data') {
    // 收集请求体数据
    const chunks = [];
    for await (const chunk of req) {
      chunks.push(chunk);
    }
    const body = Buffer.concat(chunks).toString();
    const data = JSON.parse(body);

    console.log('收到数据:', data);

    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ status: 'ok', received: data }));
  } else {
    res.writeHead(404);
    res.end('Not Found');
  }
});

server.listen(3000);
```

## 10.4 静态文件服务器

```javascript
import http from 'node:http';
import path from 'node:path';
import { createReadStream } from 'node:fs';
import { stat } from 'node:fs/promises';

const MIME_TYPES = {
  '.html': 'text/html',
  '.css':  'text/css',
  '.js':   'application/javascript',
  '.json': 'application/json',
  '.png':  'image/png',
  '.jpg':  'image/jpeg',
  '.gif':  'image/gif',
  '.svg':  'image/svg+xml',
  '.ico':  'image/x-icon',
  '.txt':  'text/plain',
};

const wwwRoot = path.resolve('public');

const server = http.createServer(async (req, res) => {
  const url = new URL(req.url, `http://${req.headers.host}`);
  let filepath = path.join(wwwRoot, url.pathname);

  try {
    const st = await stat(filepath);

    // 如果是目录，尝试 index.html
    if (st.isDirectory()) {
      filepath = path.join(filepath, 'index.html');
      await stat(filepath);
    }

    const ext = path.extname(filepath);
    const mime = MIME_TYPES[ext] || 'application/octet-stream';

    res.writeHead(200, { 'Content-Type': `${mime}; charset=utf-8` });
    createReadStream(filepath).pipe(res);
  } catch {
    res.writeHead(404, { 'Content-Type': 'text/html; charset=utf-8' });
    res.end('<h1>404 Not Found</h1>');
  }
});

server.listen(3000, () => {
  console.log('静态文件服务器运行在 http://localhost:3000/');
});
```

## 10.5 发起 HTTP 请求

Node.js 18+ 内置了全局 `fetch` API（与浏览器一致）：

```javascript
// GET 请求
const response = await fetch('https://api.github.com/users/octocat');
const user = await response.json();
console.log(user.login, user.name);

// POST 请求
const res = await fetch('https://httpbin.org/post', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'Node.js', version: 24 }),
});
const data = await res.json();
console.log(data);
```

---

# 第十一章 内置模块 — crypto 加密

`crypto` 模块提供了加密和哈希功能，支持常见的加密算法。

## 11.1 哈希（Hash）

哈希是将任意长度的数据转换为固定长度摘要的单向函数，常用于密码存储、数据校验：

```javascript
import { createHash } from 'node:crypto';

// MD5（不推荐用于安全场景）
const md5 = createHash('md5').update('Hello').digest('hex');
console.log('MD5:', md5);
// 8b1a9953c4611296a827abf8c47804d7

// SHA-256（推荐）
const sha256 = createHash('sha256').update('Hello').digest('hex');
console.log('SHA-256:', sha256);

// 多次 update
const hash = createHash('sha256');
hash.update('Hello');
hash.update(' World');
console.log(hash.digest('hex'));

// 计算文件哈希
import { createReadStream } from 'node:fs';
import { pipeline } from 'node:stream/promises';

async function fileHash(filepath) {
  const hash = createHash('sha256');
  const stream = createReadStream(filepath);
  for await (const chunk of stream) {
    hash.update(chunk);
  }
  return hash.digest('hex');
}
```

## 11.2 HMAC

HMAC（Hash-based Message Authentication Code）使用密钥 + 哈希生成消息认证码：

```javascript
import { createHmac } from 'node:crypto';

const hmac = createHmac('sha256', 'my-secret-key');
hmac.update('需要签名的消息');
console.log(hmac.digest('hex'));
```

## 11.3 对称加密（AES）

```javascript
import { randomBytes, createCipheriv, createDecipheriv } from 'node:crypto';

const algorithm = 'aes-256-gcm';
const key = randomBytes(32);
const iv = randomBytes(16);

// 加密
function encrypt(text) {
  const cipher = createCipheriv(algorithm, key, iv);
  let encrypted = cipher.update(text, 'utf-8', 'hex');
  encrypted += cipher.final('hex');
  const authTag = cipher.getAuthTag();
  return { encrypted, authTag: authTag.toString('hex') };
}

// 解密
function decrypt(encrypted, authTagHex) {
  const decipher = createDecipheriv(algorithm, key, iv);
  decipher.setAuthTag(Buffer.from(authTagHex, 'hex'));
  let decrypted = decipher.update(encrypted, 'hex', 'utf-8');
  decrypted += decipher.final('utf-8');
  return decrypted;
}

const { encrypted, authTag } = encrypt('机密信息');
console.log('密文:', encrypted);
console.log('明文:', decrypt(encrypted, authTag));
```

## 11.4 随机数

```javascript
import { randomBytes, randomUUID, randomInt } from 'node:crypto';

// 随机字节
const bytes = randomBytes(16);
console.log(bytes.toString('hex'));

// UUID v4
const uuid = randomUUID();
console.log(uuid); // 类似 '1b9d6bcd-bbfd-4b2d-9b5d-ab8dfbbd4bed'

// 随机整数
const num = randomInt(1, 100);
console.log(num); // 1 到 99 之间的随机整数
```

## 11.5 密码哈希（scrypt / pbkdf2）

用于安全地存储用户密码：

```javascript
import { scrypt, randomBytes } from 'node:crypto';
import { promisify } from 'node:util';

const scryptAsync = promisify(scrypt);

// 生成密码哈希
async function hashPassword(password) {
  const salt = randomBytes(16).toString('hex');
  const derived = await scryptAsync(password, salt, 64);
  return `${salt}:${derived.toString('hex')}`;
}

// 验证密码
async function verifyPassword(password, stored) {
  const [salt, hash] = stored.split(':');
  const derived = await scryptAsync(password, salt, 64);
  return derived.toString('hex') === hash;
}

const hashed = await hashPassword('my-password');
console.log('哈希:', hashed);
console.log('验证:', await verifyPassword('my-password', hashed));   // true
console.log('验证:', await verifyPassword('wrong-pass', hashed));     // false
```

---

# 第十二章 内置模块 — 其他常用模块

## 12.1 os — 操作系统信息

```javascript
import os from 'node:os';

console.log('主机名:', os.hostname());
console.log('平台:', os.platform());          // 'win32', 'darwin', 'linux'
console.log('架构:', os.arch());              // 'x64', 'arm64'
console.log('CPU:', os.cpus().length, '核');
console.log('总内存:', (os.totalmem() / 1024 / 1024 / 1024).toFixed(1), 'GB');
console.log('空闲内存:', (os.freemem() / 1024 / 1024 / 1024).toFixed(1), 'GB');
console.log('临时目录:', os.tmpdir());
console.log('用户目录:', os.homedir());
console.log('系统运行时间:', (os.uptime() / 3600).toFixed(1), '小时');
```

## 12.2 url — URL 解析

```javascript
// 推荐使用全局的 URL 和 URLSearchParams

const url = new URL('https://example.com:8080/path?name=test&page=1#section');

console.log(url.protocol);   // 'https:'
console.log(url.hostname);   // 'example.com'
console.log(url.port);       // '8080'
console.log(url.pathname);   // '/path'
console.log(url.search);     // '?name=test&page=1'
console.log(url.hash);       // '#section'

// 查询参数
const params = url.searchParams;
console.log(params.get('name'));  // 'test'
console.log(params.get('page')); // '1'

// 构建 URL
const newUrl = new URL('/api/users', 'https://api.example.com');
newUrl.searchParams.set('page', '2');
newUrl.searchParams.set('limit', '20');
console.log(newUrl.toString());
// https://api.example.com/api/users?page=2&limit=20
```

## 12.3 util — 实用工具

```javascript
import { promisify, format, inspect, types } from 'node:util';

// promisify：将回调风格函数转为 Promise
import { exec } from 'node:child_process';
const execAsync = promisify(exec);
const { stdout } = await execAsync('node --version');
console.log(stdout.trim());

// format：格式化字符串
console.log(format('Hello %s, you are %d years old', 'Alice', 25));

// inspect：对象转为可读字符串
const obj = { a: 1, b: { c: [1, 2, 3] } };
console.log(inspect(obj, { depth: null, colors: true }));

// types：类型判断
console.log(types.isDate(new Date()));       // true
console.log(types.isRegExp(/test/));         // true
console.log(types.isPromise(Promise.resolve())); // true
```

## 12.4 events — 事件发射器

Node.js 的事件系统是核心概念，几乎所有 I/O 对象都是 `EventEmitter` 的实例：

```javascript
import { EventEmitter } from 'node:events';

class TaskRunner extends EventEmitter {
  run(taskName) {
    this.emit('start', taskName);

    setTimeout(() => {
      this.emit('done', taskName, { duration: 1500 });
    }, 1500);
  }
}

const runner = new TaskRunner();

runner.on('start', (name) => {
  console.log(`任务 "${name}" 开始`);
});

runner.on('done', (name, result) => {
  console.log(`任务 "${name}" 完成，耗时 ${result.duration}ms`);
});

runner.run('数据处理');
```

## 12.5 child_process — 子进程

```javascript
import { exec, spawn } from 'node:child_process';
import { promisify } from 'node:util';

const execAsync = promisify(exec);

// exec：执行命令并缓冲输出
const { stdout, stderr } = await execAsync('node --version');
console.log('Node 版本:', stdout.trim());

// spawn：流式处理子进程（适合长时间运行或大量输出）
const child = spawn('node', ['--version']);

child.stdout.on('data', (data) => {
  console.log('stdout:', data.toString());
});

child.on('close', (code) => {
  console.log('退出码:', code);
});
```

## 12.6 worker_threads — 多线程

适合 CPU 密集型任务，避免阻塞主线程：

```javascript
// main.mjs
import { Worker, isMainThread, parentPort, workerData } from 'node:worker_threads';

if (isMainThread) {
  const worker = new Worker(new URL(import.meta.url), {
    workerData: { start: 1, end: 1_000_000 }
  });

  worker.on('message', (result) => {
    console.log('计算结果:', result);
  });

  worker.on('error', (err) => {
    console.error('Worker 出错:', err);
  });
} else {
  const { start, end } = workerData;
  let sum = 0;
  for (let i = start; i <= end; i++) {
    sum += i;
  }
  parentPort.postMessage(sum);
}
```

## 12.7 timers — 定时器

```javascript
// 与浏览器中的用法一致
setTimeout(() => console.log('1秒后执行'), 1000);
setInterval(() => console.log('每2秒执行一次'), 2000);
setImmediate(() => console.log('当前事件循环结束后立即执行'));

// Promise 版本（Node.js 16+）
import { setTimeout as sleep } from 'node:timers/promises';

console.log('开始');
await sleep(2000);
console.log('2秒后');
```

---

# 第十三章 Buffer 与编码

## 13.1 什么是 Buffer

`Buffer` 是 Node.js 提供的用于处理二进制数据的类。在处理 TCP 流、文件 I/O、加密等操作时，必须与二进制数据打交道。

```javascript
// 创建 Buffer
const buf1 = Buffer.alloc(10);               // 10 字节，填充 0
const buf2 = Buffer.alloc(10, 0xff);          // 10 字节，填充 0xff
const buf3 = Buffer.from('Hello');            // 从字符串创建
const buf4 = Buffer.from([0x48, 0x65, 0x6c]); // 从字节数组创建
const buf5 = Buffer.from('SGVsbG8=', 'base64'); // 从 Base64 创建
```

## 13.2 Buffer 与字符串转换

```javascript
const buf = Buffer.from('你好，Node.js', 'utf-8');

// Buffer → 字符串
console.log(buf.toString('utf-8'));    // '你好，Node.js'
console.log(buf.toString('hex'));      // 十六进制
console.log(buf.toString('base64'));   // Base64 编码

// 支持的编码
// 'utf-8', 'ascii', 'latin1', 'hex', 'base64', 'base64url'
```

## 13.3 Buffer 操作

```javascript
const buf = Buffer.alloc(256);

// 写入
buf.write('Hello');

// 读取
console.log(buf[0]); // 72 (H 的 ASCII 码)

// 切片
const slice = buf.subarray(0, 5);
console.log(slice.toString()); // 'Hello'

// 拼接
const buf1 = Buffer.from('Hello ');
const buf2 = Buffer.from('World');
const combined = Buffer.concat([buf1, buf2]);
console.log(combined.toString()); // 'Hello World'

// 比较
console.log(buf1.equals(buf2));               // false
console.log(Buffer.compare(buf1, buf2));      // -1, 0, 或 1

// 查找
console.log(buf1.includes('llo'));    // true
console.log(buf1.indexOf('llo'));     // 2
```

## 13.4 Base64 编解码

```javascript
// 编码
const encoded = Buffer.from('Hello, World!').toString('base64');
console.log(encoded); // 'SGVsbG8sIFdvcmxkIQ=='

// 解码
const decoded = Buffer.from(encoded, 'base64').toString('utf-8');
console.log(decoded); // 'Hello, World!'

// Base64 URL 安全编码（不含 +/= 字符）
const urlSafe = Buffer.from('Hello, World!').toString('base64url');
console.log(urlSafe); // 'SGVsbG8sIFdvcmxkIQ'
```

---

# 第十四章 事件循环与异步编程

## 14.1 事件循环机制

Node.js 是单线程的，但它通过事件循环（Event Loop）实现了非阻塞异步 I/O。事件循环的执行顺序：

```
   ┌───────────────────────────┐
┌─>│          timers           │  setTimeout / setInterval 回调
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │  系统操作回调
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │  内部使用
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │          poll              │  I/O 回调（文件、网络等）
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │          check            │  setImmediate 回调
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │      close callbacks      │  socket.on('close') 等
│  └─────────────┬─────────────┘
└─────────────────┘
```

## 14.2 微任务 vs 宏任务

```javascript
console.log('1. 同步代码');

setTimeout(() => console.log('5. setTimeout（宏任务）'), 0);

setImmediate(() => console.log('6. setImmediate（宏任务）'));

Promise.resolve().then(() => console.log('3. Promise.then（微任务）'));

process.nextTick(() => console.log('2. nextTick（微任务，最高优先级）'));

queueMicrotask(() => console.log('4. queueMicrotask（微任务）'));
```

输出顺序：
```
1. 同步代码
2. nextTick（微任务，最高优先级）
3. Promise.then（微任务）
4. queueMicrotask（微任务）
5. setTimeout（宏任务）
6. setImmediate（宏任务）
```

执行优先级：**同步代码 → nextTick → Promise 微任务 → 宏任务**

## 14.3 回调模式

Node.js 最早的异步模式——错误优先回调（Error-First Callback）：

```javascript
import { readFile } from 'node:fs';

readFile('file.txt', 'utf-8', (err, data) => {
  if (err) {
    console.error('读取失败:', err.message);
    return;
  }
  console.log('内容:', data);
});
```

回调的缺点是容易导致"回调地狱"（Callback Hell）。

## 14.4 Promise 模式

```javascript
import { readFile } from 'node:fs/promises';

readFile('file.txt', 'utf-8')
  .then(data => {
    console.log('内容:', data);
    return readFile('file2.txt', 'utf-8');
  })
  .then(data2 => {
    console.log('内容2:', data2);
  })
  .catch(err => {
    console.error('出错:', err.message);
  });
```

## 14.5 async/await 模式（推荐）

```javascript
import { readFile, writeFile } from 'node:fs/promises';

async function processFiles() {
  try {
    const config = JSON.parse(await readFile('config.json', 'utf-8'));
    const template = await readFile('template.html', 'utf-8');

    const result = template.replace('{{title}}', config.title);
    await writeFile('output.html', result);

    console.log('处理完成');
  } catch (err) {
    console.error('处理失败:', err.message);
  }
}

await processFiles();
```

## 14.6 并发控制

```javascript
// 并行执行多个异步操作
const [users, posts, comments] = await Promise.all([
  fetch('/api/users').then(r => r.json()),
  fetch('/api/posts').then(r => r.json()),
  fetch('/api/comments').then(r => r.json()),
]);

// 等待所有操作完成（不管成功还是失败）
const results = await Promise.allSettled([
  fetch('/api/fast'),
  fetch('/api/slow'),
  fetch('/api/might-fail'),
]);
results.forEach((result, i) => {
  if (result.status === 'fulfilled') {
    console.log(`请求 ${i}: 成功`);
  } else {
    console.log(`请求 ${i}: 失败 -`, result.reason);
  }
});

// 竞速：返回最先完成的结果
const fastest = await Promise.race([
  fetch('https://api1.example.com/data'),
  fetch('https://api2.example.com/data'),
]);

// 竞速：返回最先成功的结果（忽略失败）
const firstSuccess = await Promise.any([
  fetch('https://api1.example.com/data'),
  fetch('https://api2.example.com/data'),
]);
```

## 14.7 AbortController 取消操作

```javascript
const controller = new AbortController();
const { signal } = controller;

// 5 秒超时
setTimeout(() => controller.abort(), 5000);

try {
  const response = await fetch('https://api.example.com/slow', { signal });
  const data = await response.json();
  console.log(data);
} catch (err) {
  if (err.name === 'AbortError') {
    console.log('请求超时，已取消');
  } else {
    throw err;
  }
}
```

---

# 第十五章 Web 开发 — Koa 框架

## 15.1 Koa 简介

Koa 是由 Express 团队打造的下一代 Web 框架，设计理念是更小、更富有表现力、更健壮。Koa 使用 `async/await`，彻底告别回调。

```bash
npm install koa
```

## 15.2 Hello World

```javascript
import Koa from 'koa';

const app = new Koa();

app.use(async (ctx) => {
  ctx.body = 'Hello, Koa!';
});

app.listen(3000, () => {
  console.log('服务器运行在 http://localhost:3000/');
});
```

## 15.3 中间件机制（洋葱模型）

Koa 的核心是中间件（Middleware），多个中间件通过 `next()` 串联，形成洋葱模型：

```javascript
import Koa from 'koa';

const app = new Koa();

// 中间件 1：计时
app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  ctx.set('X-Response-Time', `${ms}ms`);
  console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
});

// 中间件 2：错误处理
app.use(async (ctx, next) => {
  try {
    await next();
  } catch (err) {
    ctx.status = err.status || 500;
    ctx.body = { error: err.message };
    ctx.app.emit('error', err, ctx);
  }
});

// 中间件 3：业务逻辑
app.use(async (ctx) => {
  if (ctx.path === '/') {
    ctx.body = { message: 'Hello!' };
  } else if (ctx.path === '/error') {
    throw new Error('故意的错误');
  } else {
    ctx.status = 404;
    ctx.body = { error: 'Not Found' };
  }
});

app.listen(3000);
```

执行顺序如同洋葱：`中间件1 → 中间件2 → 中间件3 → 中间件2 → 中间件1`。

## 15.4 路由处理

使用 `@koa/router` 插件实现路由：

```bash
npm install @koa/router
```

```javascript
import Koa from 'koa';
import Router from '@koa/router';

const app = new Koa();
const router = new Router();

router.get('/', (ctx) => {
  ctx.body = '<h1>首页</h1>';
});

router.get('/users', (ctx) => {
  ctx.body = [
    { id: 1, name: 'Alice' },
    { id: 2, name: 'Bob' },
  ];
});

router.get('/users/:id', (ctx) => {
  ctx.body = { id: ctx.params.id, name: 'Alice' };
});

router.post('/users', (ctx) => {
  ctx.status = 201;
  ctx.body = { id: 3, ...ctx.request.body };
});

app.use(router.routes());
app.use(router.allowedMethods());

app.listen(3000);
```

## 15.5 处理请求体

```bash
npm install koa-bodyparser
```

```javascript
import Koa from 'koa';
import Router from '@koa/router';
import bodyParser from 'koa-bodyparser';

const app = new Koa();
const router = new Router();

app.use(bodyParser());

router.post('/api/login', (ctx) => {
  const { username, password } = ctx.request.body;
  if (username === 'admin' && password === '123456') {
    ctx.body = { token: 'xxx-token-xxx' };
  } else {
    ctx.status = 401;
    ctx.body = { error: '用户名或密码错误' };
  }
});

app.use(router.routes());
app.listen(3000);
```

## 15.6 静态文件服务

```bash
npm install koa-static
```

```javascript
import Koa from 'koa';
import serve from 'koa-static';
import path from 'node:path';

const app = new Koa();

app.use(serve(path.resolve('public')));

app.listen(3000);
```

## 15.7 模板引擎（Nunjucks）

```bash
npm install nunjucks
```

```javascript
import Koa from 'koa';
import Router from '@koa/router';
import nunjucks from 'nunjucks';
import path from 'node:path';

const app = new Koa();
const router = new Router();

nunjucks.configure(path.resolve('views'), { autoescape: true });

router.get('/', (ctx) => {
  ctx.type = 'html';
  ctx.body = nunjucks.render('index.html', {
    title: '首页',
    users: ['Alice', 'Bob', 'Charlie'],
  });
});

app.use(router.routes());
app.listen(3000);
```

`views/index.html`：

```html
<!DOCTYPE html>
<html>
<head><title>{{ title }}</title></head>
<body>
  <h1>{{ title }}</h1>
  <ul>
    {% for user in users %}
    <li>{{ user }}</li>
    {% endfor %}
  </ul>
</body>
</html>
```

---

# 第十六章 Web 开发 — RESTful API

## 16.1 REST 设计原则

RESTful API 是目前最流行的 Web API 设计风格：

| 原则 | 说明 |
|------|------|
| **资源导向** | URL 表示资源，用名词而非动词 |
| **HTTP 方法语义化** | GET 查询、POST 创建、PUT 更新、DELETE 删除 |
| **无状态** | 每个请求包含所有必要信息 |
| **统一接口** | 一致的 URL 结构和响应格式 |

## 16.2 URL 设计

```
GET    /api/users          → 获取用户列表
GET    /api/users/123      → 获取指定用户
POST   /api/users          → 创建用户
PUT    /api/users/123      → 更新用户
PATCH  /api/users/123      → 部分更新用户
DELETE /api/users/123      → 删除用户

GET    /api/users/123/posts    → 获取某用户的文章列表
GET    /api/users?page=2&limit=20  → 分页查询
```

## 16.3 完整的 RESTful API 示例

```javascript
import express from 'express';

const app = express();
app.use(express.json());

let users = [
  { id: 1, name: 'Alice', email: 'alice@example.com' },
  { id: 2, name: 'Bob', email: 'bob@example.com' },
];
let nextId = 3;

// 查询用户列表
app.get('/api/users', (req, res) => {
  const { page = 1, limit = 10 } = req.query;
  const start = (page - 1) * limit;
  const end = start + Number(limit);
  res.json({
    data: users.slice(start, end),
    total: users.length,
    page: Number(page),
    limit: Number(limit),
  });
});

// 查询单个用户
app.get('/api/users/:id', (req, res) => {
  const user = users.find(u => u.id === Number(req.params.id));
  if (!user) {
    return res.status(404).json({ error: '用户不存在' });
  }
  res.json(user);
});

// 创建用户
app.post('/api/users', (req, res) => {
  const { name, email } = req.body;
  if (!name || !email) {
    return res.status(400).json({ error: 'name 和 email 为必填项' });
  }
  const user = { id: nextId++, name, email };
  users.push(user);
  res.status(201).json(user);
});

// 更新用户
app.put('/api/users/:id', (req, res) => {
  const index = users.findIndex(u => u.id === Number(req.params.id));
  if (index === -1) {
    return res.status(404).json({ error: '用户不存在' });
  }
  const { name, email } = req.body;
  users[index] = { ...users[index], name, email };
  res.json(users[index]);
});

// 删除用户
app.delete('/api/users/:id', (req, res) => {
  const index = users.findIndex(u => u.id === Number(req.params.id));
  if (index === -1) {
    return res.status(404).json({ error: '用户不存在' });
  }
  users.splice(index, 1);
  res.status(204).end();
});

app.listen(3000, () => {
  console.log('RESTful API 运行在 http://localhost:3000/');
});
```

## 16.4 响应状态码

| 状态码 | 含义 | 使用场景 |
|--------|------|----------|
| 200 | OK | GET 成功、PUT/PATCH 更新成功 |
| 201 | Created | POST 创建成功 |
| 204 | No Content | DELETE 删除成功 |
| 400 | Bad Request | 请求参数错误 |
| 401 | Unauthorized | 未认证 |
| 403 | Forbidden | 无权限 |
| 404 | Not Found | 资源不存在 |
| 409 | Conflict | 资源冲突（如重复创建） |
| 422 | Unprocessable Entity | 验证失败 |
| 500 | Internal Server Error | 服务器内部错误 |

## 16.5 CORS 跨域处理

```javascript
import express from 'express';
import cors from 'cors';

const app = express();

// 允许所有来源
app.use(cors());

// 自定义配置
app.use(cors({
  origin: ['http://localhost:5173', 'https://example.com'],
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true,
}));
```

```bash
npm install cors
```

---

# 第十七章 数据库操作

## 17.1 数据库选型

| 数据库 | 类型 | Node.js 驱动 | 适用场景 |
|--------|------|-------------|----------|
| **MySQL** | 关系型 | `mysql2` | 传统业务系统 |
| **PostgreSQL** | 关系型 | `pg` | 复杂查询、GIS |
| **SQLite** | 关系型（嵌入式） | `better-sqlite3` | 小型应用、本地工具 |
| **MongoDB** | 文档型 | `mongodb` / `mongoose` | 灵活结构、快速迭代 |
| **Redis** | 键值型 | `ioredis` | 缓存、会话、队列 |

## 17.2 使用 MySQL

```bash
npm install mysql2
```

**基础连接：**

```javascript
import mysql from 'mysql2/promise';

const connection = await mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'password',
  database: 'mydb',
});

// 查询
const [rows] = await connection.execute('SELECT * FROM users WHERE id = ?', [1]);
console.log(rows);

// 插入
const [result] = await connection.execute(
  'INSERT INTO users (name, email) VALUES (?, ?)',
  ['Alice', 'alice@example.com']
);
console.log('插入 ID:', result.insertId);

// 更新
await connection.execute(
  'UPDATE users SET name = ? WHERE id = ?',
  ['Bob', 1]
);

// 删除
await connection.execute('DELETE FROM users WHERE id = ?', [1]);

await connection.end();
```

**连接池（生产推荐）：**

```javascript
import mysql from 'mysql2/promise';

const pool = mysql.createPool({
  host: 'localhost',
  user: 'root',
  password: 'password',
  database: 'mydb',
  waitForConnections: true,
  connectionLimit: 10,
  queueLimit: 0,
});

// 使用连接池查询
const [rows] = await pool.execute('SELECT * FROM users');

// 事务
const connection = await pool.getConnection();
try {
  await connection.beginTransaction();
  await connection.execute('UPDATE accounts SET balance = balance - 100 WHERE id = ?', [1]);
  await connection.execute('UPDATE accounts SET balance = balance + 100 WHERE id = ?', [2]);
  await connection.commit();
} catch (err) {
  await connection.rollback();
  throw err;
} finally {
  connection.release();
}
```

## 17.3 使用 SQLite

```bash
npm install better-sqlite3
```

```javascript
import Database from 'better-sqlite3';

const db = new Database('app.db');

// 创建表
db.exec(`
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
  )
`);

// 插入
const insert = db.prepare('INSERT INTO users (name, email) VALUES (?, ?)');
const result = insert.run('Alice', 'alice@example.com');
console.log('插入 ID:', result.lastInsertRowid);

// 查询
const users = db.prepare('SELECT * FROM users').all();
console.log(users);

const user = db.prepare('SELECT * FROM users WHERE id = ?').get(1);
console.log(user);

// 事务
const insertMany = db.transaction((items) => {
  for (const item of items) {
    insert.run(item.name, item.email);
  }
});

insertMany([
  { name: 'Bob', email: 'bob@example.com' },
  { name: 'Charlie', email: 'charlie@example.com' },
]);

db.close();
```

## 17.4 使用 ORM（Prisma）

Prisma 是现代 Node.js/TypeScript 的 ORM，提供类型安全的数据库操作。

```bash
npm install prisma @prisma/client
npx prisma init
```

定义数据模型（`prisma/schema.prisma`）：

```prisma
datasource db {
  provider = "sqlite"
  url      = "file:./dev.db"
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  posts     Post[]
  createdAt DateTime @default(now())
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  author    User     @relation(fields: [authorId], references: [id])
  authorId  Int
  createdAt DateTime @default(now())
}
```

```bash
npx prisma migrate dev --name init
```

**使用 Prisma Client：**

```javascript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

// 创建用户
const user = await prisma.user.create({
  data: {
    name: 'Alice',
    email: 'alice@example.com',
    posts: {
      create: [
        { title: '第一篇文章', content: '内容...' },
        { title: '第二篇文章', content: '更多内容...' },
      ],
    },
  },
  include: { posts: true },
});

// 查询
const allUsers = await prisma.user.findMany({
  include: { posts: true },
});

const singleUser = await prisma.user.findUnique({
  where: { email: 'alice@example.com' },
});

// 条件查询
const recentPosts = await prisma.post.findMany({
  where: {
    createdAt: { gte: new Date('2024-01-01') },
  },
  orderBy: { createdAt: 'desc' },
  take: 10,
});

// 更新
await prisma.user.update({
  where: { id: 1 },
  data: { name: 'Alice Updated' },
});

// 删除
await prisma.user.delete({ where: { id: 1 } });

await prisma.$disconnect();
```

---

# 第十八章 部署与生产实践

## 18.1 环境变量

使用环境变量管理配置，不要把敏感信息写在代码中：

```javascript
// 读取环境变量
const port = process.env.PORT || 3000;
const dbUrl = process.env.DATABASE_URL;
const nodeEnv = process.env.NODE_ENV || 'development';

console.log(`环境: ${nodeEnv}, 端口: ${port}`);
```

**使用 `.env` 文件（开发环境）：**

Node.js 20.6+ 内置了 `.env` 文件支持：

```bash
# .env
PORT=3000
DATABASE_URL=mysql://root:password@localhost:3306/mydb
NODE_ENV=development
```

```bash
node --env-file=.env app.mjs
```

> 注意：`.env` 文件不应提交到 Git，请在 `.gitignore` 中排除。

## 18.2 进程管理（PM2）

```bash
npm install -g pm2
```

```bash
# 启动应用
pm2 start app.mjs --name my-app

# 集群模式（充分利用多核 CPU）
pm2 start app.mjs -i max

# 常用命令
pm2 list              # 查看所有进程
pm2 logs              # 查看日志
pm2 monit             # 监控面板
pm2 restart my-app    # 重启
pm2 stop my-app       # 停止
pm2 delete my-app     # 删除

# 生态系统配置文件
pm2 ecosystem
```

`ecosystem.config.cjs`：

```javascript
module.exports = {
  apps: [{
    name: 'my-app',
    script: 'app.mjs',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'development',
    },
    env_production: {
      NODE_ENV: 'production',
      PORT: 8080,
    },
  }],
};
```

## 18.3 Docker 容器化

`Dockerfile`：

```dockerfile
FROM node:24-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

USER node

CMD ["node", "app.mjs"]
```

`.dockerignore`：

```
node_modules
npm-debug.log
.env
.git
```

```bash
# 构建镜像
docker build -t my-app .

# 运行容器
docker run -p 3000:3000 -e NODE_ENV=production my-app
```

## 18.4 安全最佳实践

| 实践 | 说明 |
|------|------|
| **使用 Helmet** | 设置安全 HTTP 头 |
| **输入验证** | 验证所有用户输入 |
| **参数化查询** | 防止 SQL 注入 |
| **速率限制** | 防止暴力破解和 DDoS |
| **HTTPS** | 生产环境必须使用 |
| **依赖审计** | 定期运行 `npm audit` |
| **不暴露错误详情** | 生产环境隐藏堆栈信息 |

```bash
npm install helmet express-rate-limit
```

```javascript
import express from 'express';
import helmet from 'helmet';
import rateLimit from 'express-rate-limit';

const app = express();

app.use(helmet());

app.use(rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  message: { error: '请求过于频繁，请稍后再试' },
}));
```

## 18.5 日志管理

```bash
npm install pino
```

```javascript
import pino from 'pino';

const logger = pino({
  level: process.env.LOG_LEVEL || 'info',
  transport: process.env.NODE_ENV !== 'production'
    ? { target: 'pino-pretty' }
    : undefined,
});

logger.info('服务器启动');
logger.warn({ port: 3000 }, '端口警告');
logger.error({ err: new Error('boom') }, '发生错误');
```

## 18.6 性能优化要点

**1\. 使用集群模式**：`pm2 -i max` 或 `node:cluster` 模块
**2\. 启用 gzip 压缩**：使用 `compression` 中间件
**3\. 缓存策略**：Redis 缓存热点数据，设置 HTTP 缓存头
**4\. 数据库连接池**：复用连接，避免频繁创建销毁
**5\. 避免同步操作**：服务器运行时不要使用 `*Sync` 方法
**6\. 流式处理**：大文件使用 Stream 而非一次性加载
**7\. 监控告警**：使用 APM 工具监控性能指标

## 18.7 项目结构建议

```
my-app/
├── src/
│   ├── index.mjs          # 入口文件
│   ├── app.mjs            # Express/Koa 应用配置
│   ├── routes/             # 路由定义
│   │   ├── users.mjs
│   │   └── posts.mjs
│   ├── controllers/        # 控制器（业务逻辑）
│   │   ├── userController.mjs
│   │   └── postController.mjs
│   ├── models/             # 数据模型
│   │   ├── User.mjs
│   │   └── Post.mjs
│   ├── middleware/          # 中间件
│   │   ├── auth.mjs
│   │   └── errorHandler.mjs
│   ├── services/            # 服务层
│   │   └── emailService.mjs
│   ├── utils/               # 工具函数
│   │   └── helpers.mjs
│   └── config/              # 配置
│       └── index.mjs
├── tests/                   # 测试文件
│   ├── users.test.mjs
│   └── posts.test.mjs
├── prisma/                  # 数据库相关
│   └── schema.prisma
├── public/                  # 静态文件
├── .env                     # 环境变量（不提交 Git）
├── .env.example             # 环境变量示例
├── .gitignore
├── package.json
└── README.md
```

---

# 附录 速查表

## Node.js 全局对象

| 对象 | 说明 |
|------|------|
| `global` | 全局命名空间（类似浏览器的 `window`） |
| `globalThis` | 跨环境的全局对象（推荐） |
| `process` | 当前进程信息和控制 |
| `console` | 控制台输出 |
| `Buffer` | 二进制数据处理 |
| `URL` / `URLSearchParams` | URL 解析 |
| `fetch` | HTTP 请求（Node.js 18+） |
| `AbortController` | 取消异步操作 |
| `setTimeout` / `setInterval` | 定时器 |
| `setImmediate` | 下一个事件循环执行 |
| `structuredClone` | 深拷贝（Node.js 17+） |

## 常用内置模块

| 模块 | 说明 |
|------|------|
| `node:fs` / `node:fs/promises` | 文件系统 |
| `node:path` | 路径处理 |
| `node:http` / `node:https` | HTTP 服务器和客户端 |
| `node:crypto` | 加密与哈希 |
| `node:stream` | 流处理 |
| `node:events` | 事件发射器 |
| `node:os` | 操作系统信息 |
| `node:url` | URL 解析 |
| `node:util` | 实用工具 |
| `node:child_process` | 子进程 |
| `node:worker_threads` | 多线程 |
| `node:cluster` | 集群 |
| `node:zlib` | 压缩 / 解压 |
| `node:buffer` | Buffer 类 |
| `node:assert` | 断言 |
| `node:test` | 测试框架 |
| `node:timers/promises` | Promise 版定时器 |
| `node:readline` | 交互式输入 |
| `node:net` | TCP 套接字 |
| `node:dns` | DNS 解析 |

## npm 命令速查

```bash
npm init -y              # 初始化项目
npm install              # 安装所有依赖
npm i <pkg>              # 安装生产依赖
npm i -D <pkg>           # 安装开发依赖
npm i -g <pkg>           # 全局安装
npm uninstall <pkg>      # 卸载
npm update               # 更新所有依赖
npm outdated             # 检查过期依赖
npm audit                # 安全审计
npm run <script>         # 运行脚本
npm ls                   # 查看依赖树
npm cache clean --force  # 清除缓存
npx <command>            # 执行包命令
```

## 常用第三方包推荐

| 包名 | 说明 |
|------|------|
| **Web 框架** | `express` / `koa` / `fastify` / `hono` |
| **数据库 ORM** | `prisma` / `drizzle-orm` / `sequelize` |
| **数据验证** | `zod` / `joi` |
| **认证** | `passport` / `jsonwebtoken` |
| **HTTP 客户端** | `axios`（或使用内置 `fetch`） |
| **日志** | `pino` / `winston` |
| **测试** | 内置 `node:test` / `vitest` / `jest` |
| **进程管理** | `pm2` |
| **环境变量** | 内置 `--env-file`（或 `dotenv`） |
| **安全** | `helmet` / `cors` / `express-rate-limit` |

---

> **参考资料：**
> - [Node.js 官方文档](https://nodejs.org/zh-cn)
> - [Node.js API 文档](https://nodejs.org/docs/latest/api/)
