# Zustand 状态管理参考教程
> Zustand（德语"状态"）是一个小巧、快速且可扩展的 React 状态管理方案。它基于 Hooks 提供了简洁舒适的 API，没有繁琐的模板代码，却拥有足够的约定来保持代码清晰。
>
> 官方文档：https://zustand.docs.pmnd.rs/learn/getting-started/introduction

---

# 目录

1. [为什么选择 Zustand](#1-为什么选择-zustand)
2. [安装](#2-安装)
3. [快速上手](#3-快速上手)
4. [状态更新详解](#4-状态更新详解)
5. [选择器与性能优化](#5-选择器与性能优化)
6. [异步操作](#6-异步操作)
7. [中间件](#7-中间件)
8. [TypeScript 集成](#8-typescript-集成)
9. [Slices 模式（大型应用拆分）](#9-slices-模式大型应用拆分)
10. [订阅状态变化](#10-订阅状态变化)
11. [重置状态](#11-重置状态)
12. [最佳实践](#12-最佳实践)

---

# 1. 为什么选择 Zustand

| 特性 | Zustand | Redux | Context API |
|------|---------|-------|-------------|
| 包体积 | ~1KB | ~7KB | 内置 |
| 模板代码 | 极少 | 大量 | 中等 |
| 学习曲线 | 低 | 高 | 低 |
| Provider 包裹 | 不需要 | 需要 | 需要 |
| 中间件支持 | 有 | 有 | 无 |
| DevTools | 支持 | 支持 | 有限 |
| 异步处理 | 原生支持 | 需要中间件 | 手动实现 |

---

# 2. 安装

```bash
# npm
npm install zustand

# yarn
yarn add zustand

# pnpm
pnpm add zustand
```

---

# 3. 快速上手

## 3.1 创建 Store

Zustand 的 Store 本质上就是一个 Hook。你可以在里面存放任何东西：基础类型、对象、函数。

```typescript
import { create } from 'zustand'

const useBearStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
  updateBears: (newBears) => set({ bears: newBears }),
}))
```

核心概念：
- `create` 函数接收一个回调，该回调接收 `set`、`get` 两个参数
- `set` 用于更新状态（默认**浅合并**）
- `get` 用于在 action 中读取当前状态

## 3.2 在组件中使用

不需要任何 Provider 包裹，直接在组件中当 Hook 用：

```jsx
function BearCounter() {
  const bears = useBearStore((state) => state.bears)
  return <h1>{bears} 只熊在这里...</h1>
}

function Controls() {
  const increasePopulation = useBearStore((state) => state.increasePopulation)
  return <button onClick={increasePopulation}>增加一只</button>
}

function App() {
  return (
    <div>
      <BearCounter />
      <Controls />
    </div>
  )
}
```

传给 `useBearStore` 的函数叫做**选择器（selector）**，它从整个状态中提取你需要的部分。只有选择器返回的值变化时，组件才会重新渲染。

---

# 4. 状态更新详解

## 4.1 扁平更新

`set` 函数默认执行**浅合并**（类似 `Object.assign`），只更新指定的字段：

```javascript
const useStore = create((set) => ({
  firstName: '',
  lastName: '',
  updateFirstName: (firstName) => set({ firstName }),
  updateLastName: (lastName) => set({ lastName }),
}))
```

## 4.2 嵌套对象更新

对于嵌套对象，你需要手动展开来保证不可变性：

```javascript
const useStore = create((set) => ({
  nested: { deep: { count: 0 } },
  increment: () =>
    set((state) => ({
      nested: {
        ...state.nested,
        deep: {
          ...state.nested.deep,
          count: state.nested.deep.count + 1,
        },
      },
    })),
}))
```

> 嵌套层级太深？请参考下文的 [Immer 中间件](#73-immer-中间件--简化嵌套更新)。

## 4.3 替换而非合并

如果你想完全替换状态而不是合并，可以传第二个参数 `true`：

```javascript
set({ count: 0 }, true) // 完全替换整个 state
```

## 4.4 基于上一次状态更新

`set` 支持函数形式，可以拿到上一次的 state：

```javascript
const useCountStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
}))
```

## 4.5 使用 `get` 读取当前状态

```javascript
const useBearStore = create((set, get) => ({
  bears: 0,
  increase: () => {
    const currentBears = get().bears
    set({ bears: currentBears + 1 })
  },
}))
```

---

# 5. 选择器与性能优化

## 5.1 基本选择器

每次调用 `useBearStore` 都传入一个选择器，组件**只在该选择器返回值变化时才重渲染**：

```jsx
// 只在 bears 变化时重渲染
const bears = useBearStore((state) => state.bears)

// 只在 name 变化时重渲染
const name = useBearStore((state) => state.name)
```

## 5.2 选择多个字段 — 使用 `useShallow`

如果需要从 store 中提取多个值，直接返回新对象会导致每次都重渲染（引用不同）。使用 `useShallow` 进行浅比较来避免这个问题：

```jsx
import { useShallow } from 'zustand/react/shallow'

function Component() {
  // useShallow 会对返回的对象做浅比较
  const { bears, fishes } = useBoundStore(
    useShallow((state) => ({ bears: state.bears, fishes: state.fishes }))
  )
  return <div>{bears} 只熊, {fishes} 条鱼</div>
}
```

也可以用来提取数组键名：

```jsx
const keys = useStore(useShallow((state) => Object.keys(state)))
```

## 5.3 不使用选择器（获取整个 state）

```jsx
// 任何状态变化都会导致重渲染 — 适合小型 store 或调试
const state = useBearStore()
```

> 生产环境中应始终使用选择器，避免不必要的重渲染。

---

# 6. 异步操作

Zustand 天然支持异步 action，不需要额外的中间件：

```typescript
const useDataStore = create((set) => ({
  data: null,
  isLoading: false,
  error: null,

  fetchData: async (url) => {
    set({ isLoading: true, error: null })
    try {
      const response = await fetch(url)
      if (!response.ok) throw new Error('请求失败')
      const data = await response.json()
      set({ data, isLoading: false })
    } catch (error) {
      set({ error: error.message, isLoading: false })
    }
  },

  reset: () => set({ data: null, isLoading: false, error: null }),
}))

// 在组件中使用
function DataComponent() {
  const { data, isLoading, error, fetchData } = useDataStore()

  useEffect(() => {
    fetchData('/api/users')
  }, [])

  if (isLoading) return <div>加载中...</div>
  if (error) return <div>错误：{error}</div>
  return <pre>{JSON.stringify(data, null, 2)}</pre>
}
```

---

# 7. 中间件

Zustand 提供了多个开箱即用的中间件，中间件可以**组合嵌套**使用。

## 7.1 `devtools` — Redux DevTools 调试

```javascript
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

const useBearStore = create(
  devtools(
    (set) => ({
      bears: 0,
      increase: () =>
        set(
          (state) => ({ bears: state.bears + 1 }),
          undefined,
          'bears/increase' // action 名称，便于调试识别
        ),
    }),
    { name: 'BearStore' } // store 名称
  )
)
```

在浏览器安装 [Redux DevTools 扩展](https://chromewebstore.google.com/detail/redux-devtools) 后即可使用时间旅行调试。

## 7.2 `persist` — 持久化存储

```javascript
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'

const useFishStore = create(
  persist(
    (set, get) => ({
      fishes: 0,
      addFish: () => set({ fishes: get().fishes + 1 }),
    }),
    {
      name: 'fish-storage',           // localStorage 中的 key（必填，需唯一）
      storage: createJSONStorage(
        () => sessionStorage           // 默认使用 localStorage，这里改为 sessionStorage
      ),
      // partialize: (state) => ({     // 可选：只持久化部分字段
      //   fishes: state.fishes,
      // }),
    }
  )
)
```

`persist` 中间件配置选项：

| 选项 | 说明 |
|------|------|
| `name` | 存储的 key 名称（必填） |
| `storage` | 存储引擎，默认 `localStorage` |
| `partialize` | 函数，筛选需要持久化的字段 |
| `version` | 版本号，配合 `migrate` 做数据迁移 |
| `migrate` | 版本变更时的迁移函数 |
| `onRehydrateStorage` | 恢复数据时的回调 |

## 7.3 `immer` 中间件 — 简化嵌套更新

```bash
npm install immer
```

```typescript
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'

const useUserStore = create(
  immer((set) => ({
    user: {
      name: '张三',
      address: {
        city: '北京',
        district: '海淀区',
      },
    },
    updateCity: (city) =>
      set((state) => {
        // 直接"修改"即可，immer 会处理不可变性
        state.user.address.city = city
      }),
  }))
)
```

## 7.4 组合多个中间件

中间件可以嵌套组合。常见组合：`devtools` + `persist` + `immer`

```typescript
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'
import { immer } from 'zustand/middleware/immer'

const useStore = create(
  devtools(
    persist(
      immer((set) => ({
        count: 0,
        nested: { value: '' },
        increment: () =>
          set((state) => {
            state.count += 1
          }),
        setValue: (value) =>
          set((state) => {
            state.nested.value = value
          }),
      })),
      { name: 'my-store' }
    ),
    { name: 'MyStore' }
  )
)
```

---

# 8. TypeScript 集成

## 8.1 定义类型

推荐分别定义 State 和 Actions 类型：

```typescript
import { create } from 'zustand'

interface BearState {
  bears: number
  honey: number
}

interface BearActions {
  increase: (by: number) => void
  reset: () => void
}

type BearStore = BearState & BearActions

const useBearStore = create<BearStore>()((set) => ({
  bears: 0,
  honey: 0,
  increase: (by) => set((state) => ({ bears: state.bears + by })),
  reset: () => set({ bears: 0, honey: 0 }),
}))
```

> 注意 `create<BearStore>()(...)` 中间有一个空的调用 `()`，这是 TypeScript 的柯里化技巧，用于正确推断中间件类型。

## 8.2 结合中间件的类型

```typescript
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'

interface BearState {
  bears: number
  increase: (by: number) => void
}

const useBearStore = create<BearState>()(
  devtools(
    persist(
      (set) => ({
        bears: 0,
        increase: (by) => set((state) => ({ bears: state.bears + by })),
      }),
      { name: 'bearStore' }
    )
  )
)
```

---

# 9. Slices 模式（大型应用拆分）

当应用变大时，可以把 store 拆分成多个 slice，再组合到一起。

## 9.1 定义各个 Slice

```typescript
// bearSlice.ts
export const createBearSlice = (set) => ({
  bears: 0,
  addBear: () => set((state) => ({ bears: state.bears + 1 })),
  eatFish: () => set((state) => ({ fishes: state.fishes - 1 })), // 跨 slice 访问
})

// fishSlice.ts
export const createFishSlice = (set) => ({
  fishes: 0,
  addFish: () => set((state) => ({ fishes: state.fishes + 1 })),
})
```

## 9.2 组合 Slices

```typescript
// useBoundStore.ts
import { create } from 'zustand'
import { createBearSlice } from './bearSlice'
import { createFishSlice } from './fishSlice'

const useBoundStore = create((...args) => ({
  ...createBearSlice(...args),
  ...createFishSlice(...args),
}))

export default useBoundStore
```

## 9.3 搭配中间件

```javascript
import { create } from 'zustand'
import { persist } from 'zustand/middleware'
import { createBearSlice } from './bearSlice'
import { createFishSlice } from './fishSlice'

const useBoundStore = create(
  persist(
    (...args) => ({
      ...createBearSlice(...args),
      ...createFishSlice(...args),
    }),
    { name: 'bound-store' }
  )
)
```

## 9.4 TypeScript 版 Slices

```typescript
import { create, StateCreator } from 'zustand'
import { devtools } from 'zustand/middleware'

type BearSlice = { bears: number; addBear: () => void }
type FishSlice = { fishes: number; addFish: () => void }
type Store = BearSlice & FishSlice

const createBearSlice: StateCreator<
  Store,
  [['zustand/devtools', never]],
  [],
  BearSlice
> = (set) => ({
  bears: 0,
  addBear: () =>
    set((state) => ({ bears: state.bears + 1 }), undefined, 'bear/add'),
})

const createFishSlice: StateCreator<
  Store,
  [['zustand/devtools', never]],
  [],
  FishSlice
> = (set) => ({
  fishes: 0,
  addFish: () =>
    set((state) => ({ fishes: state.fishes + 1 }), undefined, 'fish/add'),
})

const useStore = create<Store>()(
  devtools((...args) => ({
    ...createBearSlice(...args),
    ...createFishSlice(...args),
  }))
)
```

---

# 10. 订阅状态变化

可以在 React 组件外部订阅 store 的状态变化：

```typescript
// 订阅所有状态变化
const unsubscribe = useBearStore.subscribe((state) => {
  console.log('状态已变化：', state)
})

// 取消订阅
unsubscribe()
```

在 React 组件内订阅（配合 `useEffect`）：

```jsx
import { useEffect } from 'react'

function Logger() {
  useEffect(() => {
    const unsub = useBearStore.subscribe(({ bears }) => {
      console.log('当前熊的数量：', bears)
    })
    return () => unsub()
  }, [])

  return null
}
```

## 使用 `subscribeWithSelector` 精细订阅

```javascript
import { create } from 'zustand'
import { subscribeWithSelector } from 'zustand/middleware'

const useStore = create(
  subscribeWithSelector((set) => ({
    count: 0,
    name: '初始值',
    increment: () => set((state) => ({ count: state.count + 1 })),
  }))
)

// 只在 count 变化时触发
useStore.subscribe(
  (state) => state.count,
  (count, prevCount) => {
    console.log(`count 从 ${prevCount} 变为 ${count}`)
  }
)
```

---

# 11. 重置状态

推荐的重置模式 — 保存初始状态的引用：

```typescript
const initialState = {
  name: '',
  email: '',
  message: '',
}

const useFormStore = create((set) => ({
  ...initialState,
  setField: (field, value) => set({ [field]: value }),
  reset: () => set(initialState),
}))
```

## 全局重置所有 Store

```typescript
const storeResetters = []

const createResettableStore = (initializer) => {
  const store = create(initializer)
  const initialState = store.getState()

  storeResetters.push(() => store.setState(initialState, true))

  return store
}

// 一键重置全部 store
export const resetAllStores = () => {
  storeResetters.forEach((reset) => reset())
}
```

---

# 12. 最佳实践

## 12.1 Store 组织

```
src/
├── stores/
│   ├── useAuthStore.ts      # 认证状态
│   ├── useCartStore.ts      # 购物车状态
│   ├── useUIStore.ts        # UI 状态（弹窗、侧边栏等）
│   └── index.ts             # 统一导出
```

## 12.2 命名约定

- Store Hook 以 `use` 开头 + 功能名 + `Store`：`useAuthStore`、`useCartStore`
- Action 使用动词开头：`addItem`、`removeItem`、`setFilter`

## 12.3 选择器提取

将常用的选择器抽取为独立函数，便于复用和测试：

```typescript
// selectors.ts
export const selectBears = (state) => state.bears
export const selectActions = (state) => ({
  increase: state.increase,
  reset: state.reset,
})

// 使用
const bears = useBearStore(selectBears)
```

## 12.4 避免常见陷阱

```jsx
// ❌ 错误：每次渲染创建新引用，导致无限重渲染
const { bears, name } = useBearStore((state) => ({
  bears: state.bears,
  name: state.name,
}))

// ✅ 正确：使用 useShallow
import { useShallow } from 'zustand/react/shallow'

const { bears, name } = useBearStore(
  useShallow((state) => ({ bears: state.bears, name: state.name }))
)

// ✅ 正确：分别提取
const bears = useBearStore((state) => state.bears)
const name = useBearStore((state) => state.name)
```

## 12.5 在 React 外部使用 Store

Zustand store 也可以在非 React 环境中使用：

```typescript
// 读取状态
const bears = useBearStore.getState().bears

// 更新状态
useBearStore.getState().increase()

// 也可以用 setState
useBearStore.setState({ bears: 10 })
```

这在工具函数、API 拦截器、路由守卫等场景非常有用。

---

# 完整示例：Todo 应用

```tsx
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'
import { immer } from 'zustand/middleware/immer'
import { useShallow } from 'zustand/react/shallow'

// 类型定义
interface Todo {
  id: number
  text: string
  done: boolean
}

interface TodoState {
  todos: Todo[]
  filter: 'all' | 'active' | 'done'
}

interface TodoActions {
  addTodo: (text: string) => void
  toggleTodo: (id: number) => void
  removeTodo: (id: number) => void
  setFilter: (filter: TodoState['filter']) => void
  clearDone: () => void
}

type TodoStore = TodoState & TodoActions

// 创建 Store
const useTodoStore = create<TodoStore>()(
  devtools(
    persist(
      immer((set) => ({
        todos: [],
        filter: 'all',

        addTodo: (text) =>
          set((state) => {
            state.todos.push({
              id: Date.now(),
              text,
              done: false,
            })
          }),

        toggleTodo: (id) =>
          set((state) => {
            const todo = state.todos.find((t) => t.id === id)
            if (todo) todo.done = !todo.done
          }),

        removeTodo: (id) =>
          set((state) => {
            state.todos = state.todos.filter((t) => t.id !== id)
          }),

        setFilter: (filter) => set({ filter }),

        clearDone: () =>
          set((state) => {
            state.todos = state.todos.filter((t) => !t.done)
          }),
      })),
      { name: 'todo-store' }
    )
  )
)

// 派生选择器
const selectFilteredTodos = (state: TodoStore) => {
  switch (state.filter) {
    case 'active':
      return state.todos.filter((t) => !t.done)
    case 'done':
      return state.todos.filter((t) => t.done)
    default:
      return state.todos
  }
}

// 组件
function TodoInput() {
  const addTodo = useTodoStore((s) => s.addTodo)
  const [text, setText] = useState('')

  const handleSubmit = (e) => {
    e.preventDefault()
    if (text.trim()) {
      addTodo(text.trim())
      setText('')
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button type="submit">添加</button>
    </form>
  )
}

function TodoList() {
  const todos = useTodoStore(selectFilteredTodos)
  const { toggleTodo, removeTodo } = useTodoStore(
    useShallow((s) => ({ toggleTodo: s.toggleTodo, removeTodo: s.removeTodo }))
  )

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          <input
            type="checkbox"
            checked={todo.done}
            onChange={() => toggleTodo(todo.id)}
          />
          <span style={{ textDecoration: todo.done ? 'line-through' : 'none' }}>
            {todo.text}
          </span>
          <button onClick={() => removeTodo(todo.id)}>删除</button>
        </li>
      ))}
    </ul>
  )
}

function FilterBar() {
  const { filter, setFilter, clearDone } = useTodoStore(
    useShallow((s) => ({
      filter: s.filter,
      setFilter: s.setFilter,
      clearDone: s.clearDone,
    }))
  )

  return (
    <div>
      {(['all', 'active', 'done'] as const).map((f) => (
        <button
          key={f}
          onClick={() => setFilter(f)}
          style={{ fontWeight: filter === f ? 'bold' : 'normal' }}
        >
          {f === 'all' ? '全部' : f === 'active' ? '进行中' : '已完成'}
        </button>
      ))}
      <button onClick={clearDone}>清除已完成</button>
    </div>
  )
}
```

---

# 延伸阅读

- [官方文档](https://zustand.docs.pmnd.rs/learn/getting-started/introduction)
- [TypeScript 进阶指南](https://zustand.docs.pmnd.rs/learn/guides/advanced-typescript)
- [Next.js 集成](https://zustand.docs.pmnd.rs/learn/guides/nextjs)
- [SSR 与 Hydration](https://zustand.docs.pmnd.rs/learn/guides/ssr-and-hydration)
- [测试指南](https://zustand.docs.pmnd.rs/learn/guides/testing)
- [GitHub 仓库](https://github.com/pmndrs/zustand)
