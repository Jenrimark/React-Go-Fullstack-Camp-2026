# 异步编程和 Promise 参考教程
> **基于 [现代 JavaScript 教程](https://zh.javascript.info/async) 与 [MDN Web Docs](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 编写**
> 从回调地狱到优雅的 async/await —— 彻底掌握 JavaScript 异步编程的核心机制与实战技巧。

---

# 第一章 JavaScript 为什么需要异步

## 1.1 单线程的宿命

JavaScript 是**单线程**语言 —— 同一时刻只有一段代码在执行。这意味着如果某个操作耗时很长（比如从服务器请求数据），整个页面就会"卡死"，用户无法点击、滚动或输入任何内容。

```javascript
// 假设 fetchDataSync 是一个同步网络请求（实际不存在）
let data = fetchDataSync('/api/users'); // 阻塞 3 秒...
console.log(data);                      // 3 秒后才执行
// 在这 3 秒内，页面完全无响应
```

## 1.2 异步的解决思路

为了解决阻塞问题，JavaScript 采用了**异步非阻塞**模型：

> 发起一个耗时操作 → **不等待**结果，继续执行后面的代码 → 等操作完成后，**通过回调**通知你结果。

```javascript
console.log('1. 发起请求');

setTimeout(() => {
  console.log('3. 请求完成');
}, 2000);

console.log('2. 继续执行');

// 输出顺序：1 → 2 → 3
```

## 1.3 常见的异步场景

| 场景 | 示例 |
|------|------|
| 网络请求 | `fetch()`、`XMLHttpRequest` |
| 定时器 | `setTimeout()`、`setInterval()` |
| 文件操作 | Node.js `fs.readFile()` |
| 用户交互 | 事件监听 `addEventListener()` |
| 数据库操作 | IndexedDB、后端 ORM 查询 |
| 动画 | `requestAnimationFrame()` |

## 1.4 异步编程的演进

JavaScript 的异步编程经历了三个阶段：

```
回调函数（Callback）    →    Promise    →    async/await
    1995+                    2015(ES6)       2017(ES2017)
    ↓                        ↓               ↓
  功能可用，但嵌套地狱     链式调用，可组合    同步写法，最优雅
```

本教程将带你完整走过这三个阶段，让你真正理解每一步"为什么"以及"怎么用"。

---

# 第二章 回调：异步的起源

## 2.1 什么是回调

**回调函数（callback）** 就是"你做完了就调用我"的函数。我们把一个函数作为参数传给异步操作，操作完成后由系统调用它。

```javascript
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);  // 成功
  script.onerror = () => callback(new Error(`加载失败: ${src}`)); // 失败

  document.head.append(script);
}

// 使用
loadScript('/my/script.js', function(error, script) {
  if (error) {
    console.error(error);
  } else {
    console.log(`${script.src} 加载完成`);
  }
});
```

## 2.2 Error 优先回调

Node.js 确立了一个重要约定 —— **Error-first Callback**：

- 回调的**第一个参数**保留给 error（没有错误时传 `null`）
- **后续参数**才是成功的结果

```javascript
// Error-first 约定
callback(error, result1, result2, ...)

// 成功时
callback(null, data);

// 失败时
callback(new Error('出了问题'));
```

这个约定让错误处理变得统一，但在深层嵌套时仍然痛苦。

## 2.3 回调地狱

当多个异步操作需要按顺序执行时，嵌套会越来越深：

```javascript
loadScript('1.js', function(error, script) {
  if (error) {
    handleError(error);
  } else {
    loadScript('2.js', function(error, script) {
      if (error) {
        handleError(error);
      } else {
        loadScript('3.js', function(error, script) {
          if (error) {
            handleError(error);
          } else {
            loadScript('4.js', function(error, script) {
              // ...越来越深的嵌套
            });
          }
        });
      }
    });
  }
});
```

这就是臭名昭著的**「厄运金字塔」**（Pyramid of Doom）或**「回调地狱」**（Callback Hell）。

## 2.4 回调地狱的问题

| 问题 | 说明 |
|------|------|
| **可读性差** | 代码向右不断缩进，难以跟踪逻辑流 |
| **错误处理分散** | 每层都要写 `if (error)`，容易遗漏 |
| **难以组合** | 并行执行多个异步操作非常笨拙 |
| **控制反转** | 你把控制权交给了第三方函数，它可能多次调用回调、不调用、或同步调用 |
| **难以取消** | 没有统一的取消机制 |

## 2.5 尝试用命名函数缓解

```javascript
loadScript('1.js', step1);

function step1(error, script) {
  if (error) return handleError(error);
  loadScript('2.js', step2);
}

function step2(error, script) {
  if (error) return handleError(error);
  loadScript('3.js', step3);
}

function step3(error, script) {
  if (error) return handleError(error);
  console.log('全部加载完毕');
}
```

虽然没有了深层嵌套，但代码被"撕裂"成多个片段，阅读时需要在函数间跳来跳去。

> 我们需要更好的方案 —— 这就是 **Promise** 诞生的动力。

---

# 第三章 Promise 基础

## 3.1 类比理解

想象你在咖啡店点了一杯咖啡：

1. **你下了订单** → 创建了一个 Promise（状态：`pending`）
2. **店员开始制作** → executor 开始执行异步操作
3. 两种结果：
   - **咖啡做好了** → `resolve(coffee)` → 状态变为 `fulfilled`
   - **售罄了** → `reject(error)` → 状态变为 `rejected`
4. **你的取餐号响了** → `.then()` 回调被执行

## 3.2 创建 Promise

```javascript
let promise = new Promise(function(resolve, reject) {
  // 这个函数称为 executor（执行器）
  // 它会在 new Promise 时立即自动执行

  // 异步操作...
  setTimeout(() => {
    let success = true;

    if (success) {
      resolve('操作成功');           // 将 promise 标记为 fulfilled
    } else {
      reject(new Error('操作失败')); // 将 promise 标记为 rejected
    }
  }, 1000);
});
```

**关键规则：**

- executor 中只有**第一次** `resolve` 或 `reject` 调用生效，后续调用全部忽略
- 建议 `reject` 时传入 `Error` 对象（或其子类）
- `resolve/reject` 可以立即调用（不一定要异步）

```javascript
// 状态一旦确定就不可更改
let promise = new Promise((resolve, reject) => {
  resolve('done');

  reject(new Error('...')); // 被忽略
  resolve('again');          // 被忽略
});
```

## 3.3 Promise 的三种状态

```
                ┌─ resolve(value) ──→ fulfilled（已兑现）
                │                      result = value
pending（等待中） ─┤
  result = undefined  │
                └─ reject(error)  ──→ rejected（已拒绝）
                                       result = error
```

- 状态**只能从 pending 转变一次**，转变后不可逆（称为 settled）
- `state` 和 `result` 是内部属性，无法直接访问

## 3.4 消费 Promise：then、catch、finally

```javascript
let promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve('数据加载完毕'), 1000);
});

// .then — 处理成功和/或失败
promise.then(
  result => console.log('成功:', result),   // 第一个参数：成功回调
  error => console.log('失败:', error)     // 第二个参数：失败回调（可选）
);

// .catch — 仅处理失败（等同于 .then(null, errorHandler)）
promise.catch(error => console.error(error));

// .finally — 无论成功失败都执行（无参数，不改变结果）
promise.finally(() => {
  console.log('加载指示器关闭');
});
```

## 3.5 finally 的特殊性

`finally` 不是 `then(f, f)` 的简单别名，它有几个重要区别：

```javascript
new Promise((resolve, reject) => {
  setTimeout(() => resolve('结果'), 1000);
})
  .finally(() => {
    console.log('清理工作');
    // ① 没有参数（不知道 promise 成功还是失败）
    // ② 返回值会被忽略（结果"穿透"到下一个处理程序）
    // ③ 如果这里 throw error，控制权转到最近的 catch
  })
  .then(result => console.log(result));  // "结果"（从 finally 穿透过来）
```

```javascript
// 对比 then(f, f)
new Promise((resolve) => resolve('结果'))
  .then(
    result => { console.log('清理'); return result; },  // 需要手动转发结果
    error => { console.log('清理'); throw error; }
  );
```

## 3.6 用 Promise 改写 loadScript

```javascript
function loadScript(src) {
  return new Promise((resolve, reject) => {
    let script = document.createElement('script');
    script.src = src;
    script.onload = () => resolve(script);
    script.onerror = () => reject(new Error(`加载失败: ${src}`));
    document.head.append(script);
  });
}

// 使用 — 干净、清晰
loadScript('/my/script.js')
  .then(script => console.log(`${script.src} 已加载`))
  .catch(error => console.error(error));
```

**回调 vs Promise 的对比：**

| 对比维度 | 回调 | Promise |
|----------|------|---------|
| 代码流 | 调用前必须准备好回调 | 先发起操作，再用 `.then` 处理 |
| 多处处理 | 只有一个回调 | 可以多次 `.then`（多个消费者） |
| 错误处理 | 每层都要手动检查 | `.catch` 统一捕获 |
| 组合能力 | 几乎无 | `Promise.all`、`Promise.race` 等强大 API |

---

# 第四章 Promise 链

## 4.1 链式调用的原理

`.then()` 的核心秘密：**它总是返回一个新的 Promise**。处理程序中的返回值会成为这个新 Promise 的 result。

```javascript
new Promise(resolve => {
  setTimeout(() => resolve(1), 1000);
})
  .then(result => {
    console.log(result); // 1
    return result * 2;   // 返回值自动包装为 resolve(2) 的 Promise
  })
  .then(result => {
    console.log(result); // 2
    return result * 2;
  })
  .then(result => {
    console.log(result); // 4
  });
```

数据在链中流动的过程：

```
Promise(1) ──.then──→ Promise(2) ──.then──→ Promise(4) ──.then──→ Promise(undefined)
```

## 4.2 常见错误：不是链而是分支

```javascript
let promise = new Promise(resolve => resolve(1));

// ❌ 这不是链！这是在同一个 promise 上注册了三个独立的处理程序
promise.then(result => console.log(result)); // 1
promise.then(result => console.log(result)); // 1
promise.then(result => console.log(result)); // 1
// 它们都收到相同的结果 1，互不影响

// ✅ 这才是链
promise
  .then(result => result * 2)
  .then(result => result * 2)
  .then(result => console.log(result)); // 4
```

## 4.3 在链中返回 Promise

如果处理程序返回一个 Promise，后续的 `.then` 会等待它完成后再执行：

```javascript
new Promise(resolve => {
  setTimeout(() => resolve(1), 1000);
})
  .then(result => {
    console.log(result); // 1（1 秒后）

    return new Promise(resolve => {
      setTimeout(() => resolve(result * 2), 1000);
    });
  })
  .then(result => {
    console.log(result); // 2（又 1 秒后，共 2 秒）

    return new Promise(resolve => {
      setTimeout(() => resolve(result * 2), 1000);
    });
  })
  .then(result => {
    console.log(result); // 4（又 1 秒后，共 3 秒）
  });
```

这使得**多步异步操作**可以按顺序执行，而保持扁平的代码结构。

## 4.4 实战：fetch 链式调用

```javascript
// 加载用户 → 加载头像 → 显示 → 移除
fetch('/api/user/1')
  .then(response => {
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return response.json();   // 返回 Promise
  })
  .then(user => {
    console.log(`用户: ${user.name}`);
    return fetch(`https://api.github.com/users/${user.github}`);
  })
  .then(response => response.json())
  .then(githubUser => {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    document.body.append(img);

    return new Promise(resolve => {
      setTimeout(() => {
        img.remove();
        resolve(githubUser);
      }, 3000);
    });
  })
  .then(githubUser => {
    console.log(`完成展示 ${githubUser.name}`);
  })
  .catch(error => {
    console.error('链中某处出错:', error);
  });
```

> **最佳实践：** 异步行为应该始终返回 Promise，这样链就可以无限延伸，后续总能追加处理。

## 4.5 Thenable 对象

Promise 链不仅接受真正的 Promise，也接受任何带有 `.then` 方法的对象（称为 thenable）：

```javascript
class Thenable {
  constructor(value) {
    this.value = value;
  }
  then(resolve, reject) {
    setTimeout(() => resolve(this.value * 2), 1000);
  }
}

Promise.resolve(1)
  .then(result => new Thenable(result))
  .then(result => console.log(result)); // 2（1 秒后）
```

这使得第三方库可以实现 Promise 兼容的对象，无需继承 `Promise` 类。

---

# 第五章 Promise 错误处理

## 5.1 错误传播机制

Promise 链有一个强大特性：**错误会沿链"跳跃"到最近的 `.catch`**，中间的 `.then` 会被跳过。

```javascript
fetch('/api/user')
  .then(response => response.json())
  .then(user => fetch(`/api/posts/${user.id}`))
  .then(response => response.json())
  .then(posts => console.log(posts))
  .catch(error => {
    // 捕获上面任何一步中产生的错误
    console.error('出错了:', error);
  });
```

这就像同步代码中的 `try...catch`：

```javascript
// 等价的同步思维模型
try {
  let response = fetch('/api/user');
  let user = response.json();
  let postResponse = fetch(`/api/posts/${user.id}`);
  let posts = postResponse.json();
  console.log(posts);
} catch (error) {
  console.error('出错了:', error);
}
```

## 5.2 隐式 try...catch

Promise 的 executor 和 `.then` 处理程序内部都有一个隐式的 `try...catch`。如果代码抛出异常，Promise 会自动变为 rejected：

```javascript
// 以下两种写法完全等价
new Promise((resolve, reject) => {
  throw new Error('出错了');
});

new Promise((resolve, reject) => {
  reject(new Error('出错了'));
});
```

```javascript
// .then 中的错误同样会被自动捕获
new Promise(resolve => resolve('ok'))
  .then(result => {
    noSuchFunction(); // ReferenceError
  })
  .catch(error => {
    console.error(error); // ReferenceError: noSuchFunction is not defined
  });
```

## 5.3 再次抛出（Rethrowing）

在 `.catch` 中，你可以处理已知的错误，并将未知错误继续抛出：

```javascript
class HttpError extends Error {
  constructor(response) {
    super(`HTTP ${response.status}: ${response.url}`);
    this.name = 'HttpError';
    this.response = response;
  }
}

fetch('/api/user')
  .then(response => {
    if (!response.ok) throw new HttpError(response);
    return response.json();
  })
  .catch(error => {
    if (error instanceof HttpError && error.response.status === 404) {
      console.log('用户不存在，显示默认头像');
      return { name: '匿名', avatar: '/default.png' };
      // 返回值会进入下一个 .then，链继续正常执行
    }
    throw error; // 未知错误，继续向下传播
  })
  .then(user => {
    console.log(user.name);
  })
  .catch(error => {
    console.error('无法处理的错误:', error);
  });
```

**执行流的两种可能路径：**

```
成功路径：  .then → .then → ... → 结束
                      ↓
错误路径：          .catch → 处理后恢复 → .then → ...
                      ↓
                    throw → .catch → ...
```

## 5.4 then(f1, f2) 与 then(f1).catch(f2) 的区别

这是一个重要的细节：

```javascript
// 写法 A：如果 f1 本身出错，f2 不会捕获
promise.then(f1, f2);

// 写法 B：如果 f1 出错，.catch 可以捕获
promise.then(f1).catch(f2);
```

```javascript
// 示例
Promise.resolve('ok')
  .then(result => {
    throw new Error('then 里出错了');
  }, error => {
    // 这里捕获不到上面 then 处理程序中的错误！
    // 因为 f2 只处理原始 promise 的 rejection
  });

// 正确写法
Promise.resolve('ok')
  .then(result => {
    throw new Error('then 里出错了');
  })
  .catch(error => {
    console.log(error.message); // "then 里出错了" ✅
  });
```

> **最佳实践：** 总是使用 `.catch()` 而不是 `.then(null, f)` 或 `.then(f1, f2)` 的第二个参数。

## 5.5 setTimeout 中的错误不会被捕获

```javascript
// ⚠️ 这个错误不会被 .catch 捕获！
new Promise((resolve, reject) => {
  setTimeout(() => {
    throw new Error('异步错误');  // 这发生在 executor 已执行完之后
  }, 1000);
}).catch(error => {
  console.log('不会执行到这里');
});
```

因为 `throw` 发生在 `setTimeout` 的回调中，此时 executor 早已执行完毕。正确的做法：

```javascript
new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(new Error('异步错误')); // 用 reject 而不是 throw
  }, 1000);
}).catch(error => {
  console.log('捕获到了:', error.message); // ✅
});
```

## 5.6 全局未处理 rejection

如果一个 Promise 被 reject 但没有 `.catch`，浏览器会触发 `unhandledrejection` 事件：

```javascript
window.addEventListener('unhandledrejection', event => {
  console.error('未处理的 Promise 拒绝:', event.reason);
  console.error('来自:', event.promise);

  // 上报到错误监控服务
  reportError(event.reason);
});

// 这个 Promise 没有 .catch
Promise.reject(new Error('被遗忘的错误'));
```

> **生产环境必须**添加 `unhandledrejection` 处理程序，确保没有错误被静默吞掉。

---

# 第六章 Promise API

## 6.1 Promise.all — 全部成功才成功

并行执行多个 Promise，**全部成功**时返回结果数组；**任一失败**时立即以该错误 reject：

```javascript
let urls = ['/api/users', '/api/posts', '/api/comments'];

let requests = urls.map(url => fetch(url).then(r => r.json()));

let results = await Promise.all(requests);
// results[0] — users 数据
// results[1] — posts 数据
// results[2] — comments 数据
```

**结果顺序与输入顺序一致**，即使不同请求完成的先后不同。

**失败情况：**

```javascript
Promise.all([
  fetch('/api/users'),
  fetch('/bad-url'),           // 失败
  fetch('/api/comments'),
])
  .then(results => console.log(results))
  .catch(error => {
    console.error(error);     // 只收到第一个失败的错误
    // 其他 Promise 仍会执行完毕，但结果被忽略
  });
```

**实用技巧 — 与普通值混合：**

```javascript
Promise.all([
  fetch('/api/user/1').then(r => r.json()),
  42,                          // 非 Promise 值直接包装为 resolve(42)
  Promise.resolve('hello'),
]).then(([user, num, str]) => {
  console.log(user, num, str); // {user对象}, 42, "hello"
});
```

## 6.2 Promise.allSettled — 等待全部完成

不关心成功还是失败，**等所有 Promise 都结束**后返回每个的状态和结果：

```javascript
let results = await Promise.allSettled([
  fetch('/api/users'),
  fetch('/bad-url'),
  fetch('/api/comments'),
]);

results.forEach((result, index) => {
  if (result.status === 'fulfilled') {
    console.log(`#${index}: 成功`, result.value);
  } else {
    console.log(`#${index}: 失败`, result.reason);
  }
});

// 输出示例：
// #0: 成功 Response{...}
// #1: 失败 TypeError: Failed to fetch
// #2: 成功 Response{...}
```

**适用场景：** 批量操作中需要知道每一个的结果，而不是一个失败就全部放弃。

## 6.3 Promise.race — 取最快的

**第一个 settled**（无论成功失败）的结果就是最终结果：

```javascript
let result = await Promise.race([
  fetch('/api/server-a'),
  fetch('/api/server-b'),
  fetch('/api/server-c'),
]);
// 谁先返回就用谁的结果（成功或失败都算）
```

**经典应用：请求超时**

```javascript
function fetchWithTimeout(url, ms) {
  return Promise.race([
    fetch(url),
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error('请求超时')), ms)
    )
  ]);
}

try {
  let response = await fetchWithTimeout('/api/data', 5000);
  let data = await response.json();
} catch (error) {
  console.error(error.message); // "请求超时" 或网络错误
}
```

## 6.4 Promise.any — 取最快成功的

**第一个成功**（fulfilled）的结果就是最终结果；全部失败才 reject（返回 `AggregateError`）：

```javascript
let result = await Promise.any([
  fetch('/api/mirror-1').then(r => r.json()),
  fetch('/api/mirror-2').then(r => r.json()),
  fetch('/api/mirror-3').then(r => r.json()),
]);
// result 是第一个成功的镜像站的数据
```

```javascript
// 全部失败的情况
try {
  await Promise.any([
    Promise.reject(new Error('错误 1')),
    Promise.reject(new Error('错误 2')),
    Promise.reject(new Error('错误 3')),
  ]);
} catch (error) {
  console.log(error instanceof AggregateError); // true
  console.log(error.errors);
  // [Error: 错误 1, Error: 错误 2, Error: 错误 3]
}
```

## 6.5 Promise.resolve 与 Promise.reject

快速创建已完成的 Promise：

```javascript
// 创建一个已成功的 Promise
let resolved = Promise.resolve(42);
resolved.then(v => console.log(v)); // 42

// 创建一个已失败的 Promise
let rejected = Promise.reject(new Error('失败'));
rejected.catch(e => console.log(e.message)); // "失败"
```

**实际用途：统一接口**

```javascript
function loadData(useCache) {
  if (useCache && cache.has('data')) {
    return Promise.resolve(cache.get('data')); // 同步值包装为 Promise
  }
  return fetch('/api/data').then(r => r.json()); // 异步操作
}

// 调用者不需要关心数据来源
let data = await loadData(true);
```

## 6.6 四个组合方法的对比

| 方法 | 等待 | 成功条件 | 失败条件 | 返回值 |
|------|------|----------|----------|--------|
| `Promise.all` | 全部 settled | 全部 fulfilled | 任一 rejected | 结果数组 |
| `Promise.allSettled` | 全部 settled | 总是成功 | — | 状态+结果数组 |
| `Promise.race` | 第一个 settled | 第一个 fulfilled | 第一个 rejected | 第一个结果 |
| `Promise.any` | 至少一个 fulfilled | 任一 fulfilled | 全部 rejected | 第一个成功值 |

---

# 第七章 Promisification：回调转 Promise

## 7.1 什么是 Promisification

将基于回调的函数转换为返回 Promise 的函数，称为 Promisification。

```javascript
// 原始的回调风格函数
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;
  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`加载失败: ${src}`));
  document.head.append(script);
}

// Promisify 后
function loadScriptPromise(src) {
  return new Promise((resolve, reject) => {
    loadScript(src, (error, script) => {
      if (error) reject(error);
      else resolve(script);
    });
  });
}
```

## 7.2 通用的 Promisify 函数

```javascript
function promisify(fn) {
  return function(...args) {
    return new Promise((resolve, reject) => {
      args.push((error, result) => {
        if (error) {
          reject(error);
        } else {
          resolve(result);
        }
      });
      fn.apply(this, args);
    });
  };
}

// 使用
let loadScriptPromise = promisify(loadScript);
let script = await loadScriptPromise('/my/script.js');
```

## 7.3 处理多返回值的 Promisify

有些回调会传递多个成功参数 `callback(null, result1, result2, ...)`：

```javascript
function promisifyMulti(fn) {
  return function(...args) {
    return new Promise((resolve, reject) => {
      args.push((error, ...results) => {
        if (error) {
          reject(error);
        } else {
          resolve(results); // 将多个结果包装为数组
        }
      });
      fn.apply(this, args);
    });
  };
}
```

## 7.4 Node.js 内置的 Promisify

Node.js 提供了 `util.promisify`：

```javascript
const { promisify } = require('util');
const fs = require('fs');

const readFile = promisify(fs.readFile);
let content = await readFile('./data.txt', 'utf-8');
```

Node.js 还为许多核心模块提供了 Promise 版本：

```javascript
const fs = require('fs/promises');  // Node.js 10+

let content = await fs.readFile('./data.txt', 'utf-8');
let files = await fs.readdir('./');
```

---

# 第八章 微任务与事件循环

## 8.1 为什么执行顺序"反直觉"

```javascript
let promise = Promise.resolve(); // 立即成功

promise.then(() => console.log('Promise'));

console.log('同步代码');

// 输出：
// 同步代码
// Promise
```

Promise 已经立即成功了，但 `.then` 回调却在 `console.log('同步代码')` 之后才执行。为什么？

## 8.2 微任务队列

`.then`/`.catch`/`.finally` 的处理程序不会立即执行，而是被放入一个特殊的**微任务队列**（Microtask Queue）。

**执行规则：**

> 只有当前同步代码**全部执行完毕**后，引擎才会从微任务队列中取出任务执行。

## 8.3 宏任务与微任务

JavaScript 有两种异步任务队列：

| 类别 | 来源 | 优先级 | 执行时机 |
|------|------|--------|----------|
| **微任务** | `.then`/`.catch`/`.finally`、`queueMicrotask()`、`MutationObserver` | 高 | 当前宏任务结束后立即执行 |
| **宏任务** | `setTimeout`、`setInterval`、`setImmediate`、I/O、UI 渲染 | 低 | 下一轮事件循环 |

## 8.4 事件循环的执行模型

```
┌───────────────────────────────────────┐
│           一轮事件循环                  │
│                                       │
│  ① 执行一个宏任务（如 <script>）       │
│        ↓                              │
│  ② 执行所有微任务（直到队列清空）       │
│        ↓                              │
│  ③ 浏览器渲染（如果需要）              │
│        ↓                              │
│  ④ 取下一个宏任务 → 回到 ①             │
│                                       │
└───────────────────────────────────────┘
```

## 8.5 经典面试题：输出顺序

```javascript
console.log('1');                           // 同步

setTimeout(() => console.log('2'), 0);      // 宏任务

Promise.resolve()
  .then(() => console.log('3'))             // 微任务
  .then(() => console.log('4'));            // 微任务

console.log('5');                           // 同步

// 输出顺序：1 → 5 → 3 → 4 → 2
```

**详细分析：**

1. 执行同步代码：输出 `1`
2. `setTimeout` 回调被放入**宏任务**队列
3. `.then` 回调被放入**微任务**队列
4. 执行同步代码：输出 `5`
5. 同步代码执行完毕，开始清空微任务队列：输出 `3`
6. 第二个 `.then` 被加入微任务队列并执行：输出 `4`
7. 微任务队列清空，执行下一个宏任务：输出 `2`

## 8.6 进阶题目

```javascript
console.log('start');

setTimeout(() => console.log('timeout'), 0);

Promise.resolve()
  .then(() => {
    console.log('promise 1');
    return Promise.resolve('inner');
  })
  .then(value => {
    console.log('promise 2:', value);
  });

queueMicrotask(() => console.log('microtask'));

console.log('end');

// 输出顺序：
// start
// end
// promise 1
// microtask
// promise 2: inner
// timeout
```

## 8.7 queueMicrotask

显式地将函数加入微任务队列：

```javascript
queueMicrotask(() => {
  console.log('这是一个微任务');
});
```

**使用场景：** 需要在当前同步代码之后、下一次渲染之前执行某个操作（如批量 DOM 更新）。

## 8.8 未处理 rejection 的时机

`unhandledrejection` 事件在**微任务队列清空后**触发。如果 `.catch` 是通过 `setTimeout` 延迟添加的，它不会阻止 `unhandledrejection`：

```javascript
let promise = Promise.reject(new Error('失败'));

// 这个 catch 注册得太晚了
setTimeout(() => {
  promise.catch(err => console.log('延迟捕获'));
}, 0);

window.addEventListener('unhandledrejection', event => {
  console.log('未处理:', event.reason.message);
});

// 输出顺序：
// 未处理: 失败        （微任务队列清空后触发）
// 延迟捕获            （下一轮宏任务）
```

> **结论：** `.catch` 应该同步注册（在当前微任务中），不要延迟添加。

---

# 第九章 async/await

## 9.1 async 函数

在函数前加 `async` 关键字，函数就会**总是返回 Promise**：

```javascript
async function greet() {
  return '你好';
}

// 等价于
function greet() {
  return Promise.resolve('你好');
}

greet().then(msg => console.log(msg)); // "你好"
```

- 返回一个非 Promise 值 → 自动包装为 `Promise.resolve(值)`
- 抛出异常 → 自动包装为 `Promise.reject(error)`
- 返回一个 Promise → 保持不变

## 9.2 await 表达式

`await` 让引擎**等待 Promise 完成**，然后返回结果：

```javascript
async function loadUser() {
  let response = await fetch('/api/user/1');   // 等待网络请求完成
  let user = await response.json();            // 等待 JSON 解析完成
  return user;
}

let user = await loadUser();
console.log(user.name);
```

**执行过程：**

1. 遇到 `await fetch(...)` → 暂停 `loadUser` 函数
2. 引擎去执行其他微任务/宏任务（不阻塞线程）
3. `fetch` 返回的 Promise fulfilled → 恢复执行，`response` 获得结果
4. 遇到 `await response.json()` → 再次暂停
5. JSON 解析完成 → 恢复执行，`user` 获得结果

## 9.3 await 只能在 async 函数内使用

```javascript
// ❌ 普通函数中不能用 await
function loadData() {
  let data = await fetch('/api/data'); // SyntaxError
}

// ✅ async 函数中可以
async function loadData() {
  let data = await fetch('/api/data');
}

// ✅ 顶层 await（ES2022，需要在模块中）
// 在 <script type="module"> 或 .mjs 文件中
let response = await fetch('/api/data');
```

## 9.4 错误处理：try...catch

`await` 的 reject 会变成 `throw`，所以可以用熟悉的 `try...catch` 处理：

```javascript
async function loadUser(id) {
  try {
    let response = await fetch(`/api/user/${id}`);

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }

    let user = await response.json();
    return user;

  } catch (error) {
    // 捕获网络错误、HTTP 错误、JSON 解析错误
    console.error('加载用户失败:', error);
    return null; // 返回默认值
  }
}
```

**也可以不用 try...catch，而在外部用 .catch：**

```javascript
async function loadUser(id) {
  let response = await fetch(`/api/user/${id}`);
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return await response.json();
}

// 外部处理错误
loadUser(999)
  .then(user => console.log(user))
  .catch(error => console.error(error));
```

## 9.5 并行执行

`await` 是串行等待的。如果多个操作之间没有依赖关系，应该**并行执行**：

```javascript
// ❌ 串行（慢）—— 总耗时 = t1 + t2 + t3
async function loadAllSerial() {
  let users = await fetch('/api/users').then(r => r.json());
  let posts = await fetch('/api/posts').then(r => r.json());
  let comments = await fetch('/api/comments').then(r => r.json());
  return { users, posts, comments };
}

// ✅ 并行（快）—— 总耗时 = max(t1, t2, t3)
async function loadAllParallel() {
  let [users, posts, comments] = await Promise.all([
    fetch('/api/users').then(r => r.json()),
    fetch('/api/posts').then(r => r.json()),
    fetch('/api/comments').then(r => r.json()),
  ]);
  return { users, posts, comments };
}
```

## 9.6 在类方法中使用

```javascript
class UserService {
  async getById(id) {
    let response = await fetch(`/api/users/${id}`);
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return await response.json();
  }

  async getAll() {
    let response = await fetch('/api/users');
    return await response.json();
  }
}

let service = new UserService();
let user = await service.getById(1);
```

## 9.7 async/await 与 Promise 的对照

将同一段逻辑用两种风格编写对比：

**Promise 链写法：**

```javascript
function loadAndShowUser(id) {
  return fetch(`/api/users/${id}`)
    .then(response => {
      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      return response.json();
    })
    .then(user => {
      return fetch(`/api/avatar/${user.avatarId}`)
        .then(r => r.blob());
    })
    .then(blob => {
      let url = URL.createObjectURL(blob);
      document.querySelector('#avatar').src = url;
    })
    .catch(error => {
      console.error('失败:', error);
      document.querySelector('#avatar').src = '/default.png';
    });
}
```

**async/await 写法：**

```javascript
async function loadAndShowUser(id) {
  try {
    let response = await fetch(`/api/users/${id}`);
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    let user = await response.json();

    let avatarResponse = await fetch(`/api/avatar/${user.avatarId}`);
    let blob = await avatarResponse.blob();

    let url = URL.createObjectURL(blob);
    document.querySelector('#avatar').src = url;

  } catch (error) {
    console.error('失败:', error);
    document.querySelector('#avatar').src = '/default.png';
  }
}
```

> async/await 让代码读起来像同步代码，逻辑流一目了然。

## 9.8 await 可以接受 thenable

和 `.then` 一样，`await` 也能处理 thenable 对象：

```javascript
class Timer {
  constructor(ms) {
    this.ms = ms;
  }
  then(resolve) {
    setTimeout(resolve, this.ms);
  }
}

async function demo() {
  console.log('开始');
  await new Timer(2000); // 等待 2 秒
  console.log('2 秒后');
}
```

---

# 第十章 实战模式与最佳实践

## 10.1 请求超时与取消

**使用 AbortController 取消 fetch：**

```javascript
async function fetchWithTimeout(url, timeoutMs = 5000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeoutMs);

  try {
    const response = await fetch(url, { signal: controller.signal });
    clearTimeout(timeoutId);
    return response;
  } catch (error) {
    clearTimeout(timeoutId);
    if (error.name === 'AbortError') {
      throw new Error(`请求超时 (${timeoutMs}ms): ${url}`);
    }
    throw error;
  }
}
```

**用 AbortController 取消多个关联操作：**

```javascript
const controller = new AbortController();
const { signal } = controller;

// 同一个 signal 可以传给多个操作
await Promise.all([
  fetch('/api/data1', { signal }),
  fetch('/api/data2', { signal }),
  fetch('/api/data3', { signal }),
]);

// 一次性取消全部
controller.abort();
```

## 10.2 重试模式

```javascript
async function fetchWithRetry(url, maxRetries = 3, delay = 1000) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      const response = await fetch(url);
      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      return await response.json();
    } catch (error) {
      console.warn(`第 ${attempt} 次尝试失败:`, error.message);

      if (attempt === maxRetries) {
        throw new Error(`${maxRetries} 次重试后仍然失败: ${error.message}`);
      }

      // 指数退避：1s → 2s → 4s
      await new Promise(r => setTimeout(r, delay * Math.pow(2, attempt - 1)));
    }
  }
}
```

## 10.3 并发控制：限制并行数量

当需要发起大量请求但不想同时发出太多：

```javascript
async function asyncPool(concurrency, items, iteratorFn) {
  const results = [];
  const executing = new Set();

  for (const [index, item] of items.entries()) {
    const promise = iteratorFn(item, index).then(result => {
      executing.delete(promise);
      return result;
    });

    results.push(promise);
    executing.add(promise);

    if (executing.size >= concurrency) {
      await Promise.race(executing);
    }
  }

  return Promise.all(results);
}

// 使用：最多同时 3 个请求
let urls = Array.from({ length: 20 }, (_, i) => `/api/item/${i}`);

let results = await asyncPool(3, urls, async (url) => {
  let response = await fetch(url);
  return response.json();
});
```

## 10.4 防抖与节流的异步版本

```javascript
function debounceAsync(fn, ms) {
  let timeoutId;
  let pendingReject;

  return function(...args) {
    return new Promise((resolve, reject) => {
      if (pendingReject) pendingReject(new Error('debounced'));
      clearTimeout(timeoutId);

      pendingReject = reject;
      timeoutId = setTimeout(async () => {
        try {
          resolve(await fn.apply(this, args));
        } catch (error) {
          reject(error);
        }
      }, ms);
    });
  };
}

// 使用：搜索输入防抖
const debouncedSearch = debounceAsync(async (query) => {
  let response = await fetch(`/api/search?q=${query}`);
  return response.json();
}, 300);
```

## 10.5 缓存 Promise

避免对同一资源重复请求：

```javascript
function createCachedFetcher() {
  const cache = new Map();

  return async function cachedFetch(url, ttl = 60000) {
    let cached = cache.get(url);
    if (cached && Date.now() - cached.timestamp < ttl) {
      return cached.data;
    }

    let response = await fetch(url);
    let data = await response.json();

    cache.set(url, { data, timestamp: Date.now() });
    return data;
  };
}

const cachedFetch = createCachedFetcher();
let user1 = await cachedFetch('/api/user/1');       // 网络请求
let user1Again = await cachedFetch('/api/user/1');  // 缓存命中
```

**进阶：缓存正在进行的 Promise（避免并发重复请求）**

```javascript
function createSmartCachedFetcher() {
  const cache = new Map();
  const inflight = new Map();

  return async function(url) {
    if (cache.has(url)) return cache.get(url);

    if (inflight.has(url)) return inflight.get(url);

    const promise = fetch(url)
      .then(r => r.json())
      .then(data => {
        cache.set(url, data);
        inflight.delete(url);
        return data;
      })
      .catch(error => {
        inflight.delete(url);
        throw error;
      });

    inflight.set(url, promise);
    return promise;
  };
}
```

## 10.6 用 AbortController 管理事件监听器

```javascript
function setupHandlers() {
  const controller = new AbortController();
  const { signal } = controller;

  document.querySelector('#btn').addEventListener('click', onClick, { signal });
  document.querySelector('#input').addEventListener('input', onInput, { signal });
  window.addEventListener('resize', onResize, { signal });
  window.addEventListener('scroll', onScroll, { signal });

  // 一次性移除所有监听器
  return () => controller.abort();
}

let cleanup = setupHandlers();
// 需要清理时
cleanup();
```

## 10.7 串行执行异步队列

按顺序执行一组异步任务（不是并行）：

```javascript
// 方式一：for...of + await
async function serial(tasks) {
  let results = [];
  for (let task of tasks) {
    results.push(await task());
  }
  return results;
}

// 方式二：reduce
function serialReduce(tasks) {
  return tasks.reduce(
    (chain, task) => chain.then(results =>
      task().then(result => [...results, result])
    ),
    Promise.resolve([])
  );
}
```

## 10.8 异步初始化模式

确保某个初始化操作只执行一次：

```javascript
class Database {
  #initPromise = null;

  async #init() {
    console.log('初始化数据库连接...');
    await new Promise(r => setTimeout(r, 1000));
    this.connection = { connected: true };
    console.log('数据库已连接');
  }

  async ensureInitialized() {
    if (!this.#initPromise) {
      this.#initPromise = this.#init();
    }
    return this.#initPromise;
  }

  async query(sql) {
    await this.ensureInitialized();
    console.log('执行查询:', sql);
    return [];
  }
}

const db = new Database();
// 即使并发调用，初始化也只执行一次
await Promise.all([
  db.query('SELECT * FROM users'),
  db.query('SELECT * FROM posts'),
  db.query('SELECT * FROM comments'),
]);
```

## 10.9 常见陷阱总结

| 陷阱 | 说明 | 解决方案 |
|------|------|----------|
| 忘记 `await` | `fetch()` 返回的是 Promise，不是结果 | 确保在每个异步调用前加 `await` |
| `forEach` 中用 `await` | `forEach` 不会等待异步操作 | 用 `for...of` 或 `Promise.all` + `map` |
| 串行代替并行 | 无依赖的操作一个个 `await` | 用 `Promise.all` 并行执行 |
| `.catch` 后不 return | `.catch` 返回 `undefined` 会导致后续 `.then` 继续执行 | 在 catch 中 return 值或 throw |
| async 函数的错误静默丢失 | 没有 `await` 也没有 `.catch` | 始终添加错误处理 |
| `setTimeout` 中 throw | Promise 无法捕获 setTimeout 回调中的 throw | 在 setTimeout 中用 `reject` |

**`forEach` 与 `for...of` 的关键区别：**

```javascript
let urls = ['/api/a', '/api/b', '/api/c'];

// ❌ forEach 不等待 await（三个请求几乎同时发出）
urls.forEach(async (url) => {
  let data = await fetch(url);
  console.log(data);
});
console.log('完成'); // 在请求完成前就输出了！

// ✅ for...of 正确按顺序执行
for (let url of urls) {
  let response = await fetch(url);
  let data = await response.json();
  console.log(data);
}
console.log('完成'); // 所有请求完成后才输出

// ✅ 并行执行所有请求
let results = await Promise.all(
  urls.map(async (url) => {
    let response = await fetch(url);
    return response.json();
  })
);
console.log('完成', results);
```

---

> **参考资料：**
> - [现代 JavaScript 教程 — Promise，async/await](https://zh.javascript.info/async)
> - [MDN — Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
> - [MDN — async function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)
> - [MDN — await](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/await)
> - [MDN — 使用 Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises)
