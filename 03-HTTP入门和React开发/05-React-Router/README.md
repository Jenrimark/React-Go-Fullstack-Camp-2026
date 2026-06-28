# React Router v7 参考教程
> 参考官方文档：https://reactrouter.com/home
>
> React Router 是一个面向 React 的多策略路由器，架起 React 18 到 React 19 的桥梁。你可以把它当作完整的 React 框架来用，也可以只用最简单的功能。

---

# 目录

1. [概述与模式选择](#1-概述与模式选择)
2. [安装与项目搭建](#2-安装与项目搭建)
3. [创建路由——createBrowserRouter](#3-创建路由createbrowserrouter)
4. [嵌套路由与 Outlet](#4-嵌套路由与-outlet)
5. [动态参数、可选参数与通配符](#5-动态参数可选参数与通配符)
6. [导航](#6-导航)
7. [数据加载（Loader）](#7-数据加载loader)
8. [数据变更（Action）](#8-数据变更action)
9. [Pending UI 与乐观更新](#9-pending-ui-与乐观更新)
10. [错误处理](#10-错误处理)
11. [常用 Hooks 参考](#11-常用-hooks-参考)
12. [其他路由器类型](#12-其他路由器类型)
13. [声明式模式简介](#13-声明式模式简介)
14. [实战：构建一个博客应用](#14-实战构建一个博客应用)

---

# 1. 概述与模式选择

React Router v7 提供了三种使用模式：

| 模式 | 适用场景 | 特点 |
|------|----------|------|
| **Framework 模式** | 全新项目、全栈应用 | 基于 Vite 脚手架，支持 SSR/SSG、文件约定 |
| **Data 模式** ★ | 已有项目、纯客户端 SPA | 使用 `createBrowserRouter`，支持 `loader`/`action`，无需框架 |
| **Declarative 模式** | 最简单的用法 | 传统 `<BrowserRouter>` + `<Routes>` + `<Route>`，无数据 API |

**本教程聚焦 Data 模式**——它是在已有 React 项目中获得路由数据能力的最佳选择。你只需 `npm install react-router`，即可获得 `loader`、`action`、自动数据重验证、错误边界等强大功能。

---

# 2. 安装与项目搭建

## 2.1 安装

```bash
npm install react-router
```

仅需这一个包，Data 模式的所有功能都已包含在内。

## 2.2 最小启动示例

假设你已经有一个 React 项目（如 Vite + React、Create React App 等），在入口文件中：

```tsx
// src/main.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { createBrowserRouter, RouterProvider } from "react-router";
import Root from "./routes/Root";
import Home from "./routes/Home";
import About from "./routes/About";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    children: [
      { index: true, element: <Home /> },
      { path: "about", element: <About /> },
    ],
  },
]);

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

这就是 Data 模式的核心：用 `createBrowserRouter` 创建路由对象，用 `<RouterProvider>` 将其注入应用。

---

# 3. 创建路由——createBrowserRouter

## 3.1 路由对象结构

每条路由是一个普通 JS 对象，常用字段如下：

```tsx
{
  path: "users",            // URL 匹配模式
  element: <UsersPage />,   // 匹配时渲染的组件
  loader: async () => {},   // 数据加载函数
  action: async () => {},   // 数据变更函数
  errorElement: <Error />,  // 出错时渲染的组件
  children: [],             // 子路由数组
}
```

## 3.2 完整路由配置示例

```tsx
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router";

const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    errorElement: <GlobalError />,
    children: [
      {
        index: true,
        element: <HomePage />,
        loader: homeLoader,
      },
      {
        path: "about",
        element: <AboutPage />,
      },
      {
        path: "users",
        element: <UsersLayout />,
        children: [
          {
            index: true,
            element: <UsersList />,
            loader: usersLoader,
          },
          {
            path: ":userId",
            element: <UserProfile />,
            loader: userLoader,
            action: userAction,
            errorElement: <UserError />,
          },
        ],
      },
      {
        path: "login",
        element: <LoginPage />,
        action: loginAction,
      },
      {
        path: "*",
        element: <NotFound />,
      },
    ],
  },
]);

function App() {
  return <RouterProvider router={router} />;
}
```

## 3.3 路由配置要点

| 字段 | 说明 |
|------|------|
| `path` | URL 模式，支持动态参数 `:id`、通配符 `*`、可选参数 `:id?` |
| `index` | 设为 `true` 表示索引路由（父路由 URL 的默认子路由） |
| `element` | 匹配时渲染的 React 元素 |
| `loader` | 路由渲染前的异步数据加载函数 |
| `action` | 处理 `<Form>` 提交的异步函数 |
| `errorElement` | loader/action/渲染 出错时的回退 UI |
| `children` | 子路由数组（用 `<Outlet />` 渲染） |
| `lazy` | 懒加载路由模块（代码拆分，见 3.4） |

## 3.4 路由懒加载（代码拆分）

使用 `lazy` 字段可以实现按需加载，减小首屏 bundle 体积：

```tsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    children: [
      {
        path: "dashboard",
        lazy: () => import("./routes/Dashboard"),
      },
      {
        path: "settings",
        lazy: () => import("./routes/Settings"),
      },
    ],
  },
]);
```

懒加载的模块需要导出路由所需的属性：

```tsx
// src/routes/Dashboard.tsx
import { useLoaderData } from "react-router";

export async function loader() {
  const data = await fetchDashboardData();
  return data;
}

export function Component() {
  const data = useLoaderData();
  return <h1>Dashboard: {data.title}</h1>;
}
```

> 当使用 `lazy` 时，用 `Component`（大写）代替 `element`；用命名导出 `loader`/`action` 代替路由对象上的同名字段。

---

# 4. 嵌套路由与 Outlet

## 4.1 嵌套路由的工作方式

将路由放入父路由的 `children` 数组中即可嵌套。子路由的 `path` 会自动拼接到父路由之后：

```tsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    children: [
      { index: true, element: <Home /> },           // 匹配 /
      {
        path: "dashboard",
        element: <DashboardLayout />,
        children: [
          { index: true, element: <DashboardHome /> }, // 匹配 /dashboard
          { path: "settings", element: <Settings /> }, // 匹配 /dashboard/settings
          { path: "profile", element: <Profile /> },   // 匹配 /dashboard/profile
        ],
      },
    ],
  },
]);
```

## 4.2 Outlet——渲染子路由

父路由组件通过 `<Outlet />` 决定子路由内容在哪里渲染：

```tsx
import { Outlet, Link } from "react-router";

function RootLayout() {
  return (
    <div>
      <header>
        <nav>
          <Link to="/">首页</Link>
          <Link to="/dashboard">控制面板</Link>
        </nav>
      </header>
      <main>
        <Outlet />
      </main>
      <footer>© 2026 My App</footer>
    </div>
  );
}
```

```tsx
import { Outlet, NavLink } from "react-router";

function DashboardLayout() {
  return (
    <div style={{ display: "flex" }}>
      <aside>
        <NavLink to="/dashboard">概览</NavLink>
        <NavLink to="/dashboard/settings">设置</NavLink>
        <NavLink to="/dashboard/profile">个人资料</NavLink>
      </aside>
      <section style={{ flex: 1 }}>
        <Outlet />
      </section>
    </div>
  );
}
```

## 4.3 布局路由（无 path 的路由）

省略 `path` 的路由不会增加 URL 段，仅作为布局容器：

```tsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    children: [
      {
        // 无 path —— 这是一个布局路由
        element: <MarketingLayout />,
        children: [
          { index: true, element: <HomePage /> },     // /
          { path: "about", element: <AboutPage /> },   // /about
          { path: "contact", element: <ContactPage /> }, // /contact
        ],
      },
      {
        path: "app",
        element: <AppLayout />,
        children: [
          { index: true, element: <AppHome /> },
          { path: "settings", element: <AppSettings /> },
        ],
      },
    ],
  },
]);
```

## 4.4 Index 路由

Index 路由是父路由 URL 下的默认子路由（类似 `index.html`）。它没有自己的 `path`，通过 `index: true` 标识：

```tsx
{
  path: "dashboard",
  element: <DashboardLayout />,
  children: [
    { index: true, element: <DashboardHome /> }, // 在 /dashboard 下渲染
    { path: "stats", element: <Stats /> },       // 在 /dashboard/stats 下渲染
  ],
}
```

> Index 路由不能有 `children`。

## 4.5 Outlet Context

父路由可以通过 `<Outlet context={...} />` 向子路由传递数据：

```tsx
// 父路由
function Dashboard() {
  const user = { name: "张三" };
  return <Outlet context={{ user }} />;
}

// 子路由
import { useOutletContext } from "react-router";

function DashboardHome() {
  const { user } = useOutletContext();
  return <h1>欢迎，{user.name}</h1>;
}
```

---

# 5. 动态参数、可选参数与通配符

## 5.1 动态参数

以 `:` 开头的路径段是动态参数：

```tsx
{ path: "users/:userId", element: <UserProfile /> }
```

在组件中通过 `useParams` 获取：

```tsx
import { useParams } from "react-router";

function UserProfile() {
  const { userId } = useParams();
  return <h1>用户 ID: {userId}</h1>;
}
```

在 `loader`/`action` 中通过 `params` 参数获取：

```tsx
async function userLoader({ params }) {
  const user = await fetchUser(params.userId);
  return { user };
}
```

支持多个动态参数：

```tsx
{ path: "c/:categoryId/p/:productId", element: <Product /> }
// params = { categoryId: "...", productId: "..." }
```

## 5.2 可选参数

在参数末尾加 `?` 使其变为可选：

```tsx
{ path: ":lang?/categories", element: <Categories /> }
// 匹配 /categories 和 /en/categories

{ path: "users/:userId/edit?", element: <UserEdit /> }
// 匹配 /users/42 和 /users/42/edit
```

## 5.3 通配符（Splat / Catchall）

以 `/*` 结尾可以匹配任意后续路径：

```tsx
{ path: "files/*", element: <FileBrowser /> }
// /files/docs/readme.md → params["*"] = "docs/readme.md"
```

在组件中使用：

```tsx
function FileBrowser() {
  const params = useParams();
  const filePath = params["*"];
  return <p>当前文件: {filePath}</p>;
}
```

捕获所有未匹配的路由（404 页面）：

```tsx
{
  path: "*",
  element: <NotFound />,
}
```

---

# 6. 导航

## 6.1 Link 组件

最基本的声明式导航：

```tsx
import { Link } from "react-router";

function Header() {
  return (
    <nav>
      <Link to="/">首页</Link>
      <Link to="/about">关于</Link>
      <Link to="/users/42">用户详情</Link>
    </nav>
  );
}
```

## 6.2 NavLink 组件

`NavLink` 自带激活状态检测，适合做导航菜单：

```tsx
import { NavLink } from "react-router";

function Navbar() {
  return (
    <nav>
      <NavLink
        to="/"
        className={({ isActive, isPending }) =>
          isActive ? "active" : isPending ? "pending" : ""
        }
      >
        首页
      </NavLink>

      <NavLink
        to="/about"
        style={({ isActive }) => ({
          fontWeight: isActive ? "bold" : "normal",
          color: isActive ? "#1890ff" : "#333",
        })}
      >
        关于
      </NavLink>
    </nav>
  );
}
```

`NavLink` 的 `className`、`style`、`children` 都支持接收函数，参数包含：

| 属性 | 说明 |
|------|------|
| `isActive` | 当前路由是否匹配 |
| `isPending` | 是否正在导航到此路由（数据加载中） |
| `isTransitioning` | 是否正在 View Transition |

## 6.3 编程式导航——useNavigate

在非链接交互中（如表单回调、定时器、条件判断）使用：

```tsx
import { useNavigate } from "react-router";

function LoginPage() {
  const navigate = useNavigate();

  async function handleLogin(formData) {
    const result = await login(formData);
    if (result.success) {
      navigate("/dashboard");
    }
  }

  return <LoginForm onSubmit={handleLogin} />;
}
```

常用调用方式：

```tsx
navigate("/about");                           // 跳转到 /about
navigate(-1);                                 // 后退一步
navigate(1);                                  // 前进一步
navigate("/users", { replace: true });        // 替换当前历史记录（不可后退）
navigate("/detail", { state: { from: "/" } }); // 传递 state 数据
```

## 6.4 Form 导航

`<Form>` 组件提交会触发目标路由的 `action`（POST）或 `loader`（GET）：

```tsx
import { Form } from "react-router";

// GET 表单 —— 触发 loader，URL 变为 /search?q=xxx
function SearchForm() {
  return (
    <Form action="/search" method="get">
      <input type="text" name="q" placeholder="搜索..." />
      <button type="submit">搜索</button>
    </Form>
  );
}

// POST 表单 —— 触发 action
function NewPostForm() {
  return (
    <Form method="post">
      <input type="text" name="title" />
      <button type="submit">创建</button>
    </Form>
  );
}
```

## 6.5 redirect

在 `loader` 或 `action` 中进行重定向：

```tsx
import { redirect } from "react-router";

async function protectedLoader() {
  const user = await getUser();
  if (!user) {
    return redirect("/login");
  }
  return { user };
}

async function createPostAction({ request }) {
  const formData = await request.formData();
  const post = await createPost(Object.fromEntries(formData));
  return redirect(`/posts/${post.id}`);
}
```

---

# 7. 数据加载（Loader）

Data 模式下，`loader` 是路由对象上的异步函数，在路由渲染**之前**执行。

## 7.1 基本用法

在路由对象上定义 `loader`，在组件中用 `useLoaderData()` 获取数据：

```tsx
// 定义路由
{
  path: "products/:pid",
  element: <ProductPage />,
  loader: async ({ params }) => {
    const res = await fetch(`/api/products/${params.pid}`);
    if (!res.ok) throw new Response("Not Found", { status: 404 });
    return res.json();
  },
}
```

```tsx
// 组件
import { useLoaderData } from "react-router";

function ProductPage() {
  const product = useLoaderData();
  return (
    <div>
      <h1>{product.name}</h1>
      <p>价格: ¥{product.price}</p>
      <p>{product.description}</p>
    </div>
  );
}
```

## 7.2 Loader 参数

`loader` 函数接收一个对象参数，包含：

| 属性 | 说明 |
|------|------|
| `params` | 动态路由参数，如 `{ userId: "42" }` |
| `request` | 标准 Fetch API 的 `Request` 对象，可获取 URL、搜索参数等 |

```tsx
async function loader({ params, request }) {
  // 获取动态参数
  const userId = params.userId;

  // 获取搜索参数
  const url = new URL(request.url);
  const query = url.searchParams.get("q");
  const page = url.searchParams.get("page") || "1";

  const data = await fetchData(userId, query, page);
  return data;
}
```

## 7.3 返回值

`loader` 可以返回任何可序列化的数据：

```tsx
// 返回对象
loader: async () => {
  return { users: [...], total: 100 };
}

// 返回 Response 对象
loader: async () => {
  const res = await fetch("/api/data");
  return res; // React Router 会自动解析 JSON
}

// 抛出重定向
loader: async () => {
  return redirect("/login");
}

// 抛出错误响应（会被 errorElement 捕获）
loader: async ({ params }) => {
  const user = await fetchUser(params.id);
  if (!user) {
    throw new Response("用户不存在", { status: 404 });
  }
  return { user };
}
```

## 7.4 多路由并行加载

嵌套路由的 `loader` 会**并行执行**。例如父路由和子路由的 loader 同时开始加载，而不是串行等待：

```tsx
{
  path: "/",
  element: <Root />,
  loader: rootLoader,      // ← 同时开始
  children: [
    {
      path: "dashboard",
      element: <Dashboard />,
      loader: dashboardLoader, // ← 同时开始（不等 rootLoader 完成）
    },
  ],
}
```

## 7.5 useRouteLoaderData——访问其他路由的数据

子路由可以通过 `useRouteLoaderData` 访问父路由或其他路由的 loader 数据：

```tsx
// 路由配置中给路由设置 id
{
  id: "root",
  path: "/",
  element: <Root />,
  loader: async () => {
    const user = await getCurrentUser();
    return { user };
  },
  children: [...]
}
```

```tsx
// 任意子路由组件中
import { useRouteLoaderData } from "react-router";

function ChildPage() {
  const rootData = useRouteLoaderData("root");
  return <p>当前用户: {rootData.user.name}</p>;
}
```

---

# 8. 数据变更（Action）

`action` 处理数据写入（表单提交、POST/PUT/DELETE 请求等）。**Action 完成后，页面上所有 loader 数据会自动重新验证**，确保 UI 与数据同步。

## 8.1 基本用法

在路由对象上定义 `action`，用 `<Form>` 或 `useFetcher` 触发：

```tsx
// 路由配置
{
  path: "projects/:projectId",
  element: <ProjectPage />,
  loader: projectLoader,
  action: async ({ request, params }) => {
    const formData = await request.formData();
    const title = formData.get("title");
    const project = await updateProject(params.projectId, { title });
    return { project };
  },
}
```

```tsx
// 组件
import { Form, useLoaderData, useActionData } from "react-router";

function ProjectPage() {
  const { project } = useLoaderData();
  const actionData = useActionData();

  return (
    <div>
      <h1>{project.title}</h1>
      <Form method="post">
        <input type="text" name="title" defaultValue={project.title} />
        <button type="submit">更新</button>
      </Form>
      {actionData?.project && <p>已更新为: {actionData.project.title}</p>}
    </div>
  );
}
```

## 8.2 Action 参数

`action` 函数的参数与 `loader` 类似：

| 属性 | 说明 |
|------|------|
| `request` | 包含表单数据的 `Request` 对象 |
| `params` | 动态路由参数 |

```tsx
async function action({ request, params }) {
  const formData = await request.formData();
  const intent = formData.get("intent");

  switch (intent) {
    case "update":
      await updateItem(params.id, Object.fromEntries(formData));
      return { success: true };
    case "delete":
      await deleteItem(params.id);
      return redirect("/items");
    default:
      throw new Response("Unknown intent", { status: 400 });
  }
}
```

## 8.3 调用 Action 的三种方式

**方式一：`<Form>` 组件**（触发导航）

```tsx
import { Form } from "react-router";

function EditForm() {
  return (
    <Form method="post" action="/projects/123">
      <input type="text" name="title" />
      <button type="submit">保存</button>
    </Form>
  );
}
```

> 如果省略 `action` 属性，默认提交到当前路由的 action。

**方式二：`useSubmit`**（编程式提交，触发导航）

```tsx
import { useSubmit } from "react-router";

function AutoSave({ data }) {
  const submit = useSubmit();

  function handleBlur(e) {
    submit(
      { title: e.target.value },
      { method: "post", action: `/items/${data.id}` }
    );
  }

  return <input defaultValue={data.title} onBlur={handleBlur} />;
}
```

**方式三：`useFetcher`**（不触发导航，适合局部更新）

```tsx
import { useFetcher } from "react-router";

function TaskItem({ task }) {
  const fetcher = useFetcher();
  const busy = fetcher.state !== "idle";

  return (
    <fetcher.Form method="post" action={`/tasks/${task.id}`}>
      <input type="text" name="title" defaultValue={task.title} />
      <button type="submit" disabled={busy}>
        {busy ? "保存中..." : "保存"}
      </button>
    </fetcher.Form>
  );
}
```

`useFetcher` 也支持编程式提交和数据加载：

```tsx
const fetcher = useFetcher();

// 提交
fetcher.submit({ title: "新标题" }, { action: "/api/update", method: "post" });

// 加载
fetcher.load("/api/data");

// 获取结果
fetcher.data;  // loader/action 返回的数据
fetcher.state; // "idle" | "loading" | "submitting"
```

## 8.4 自动重验证

Action 完成后，React Router 会自动重新调用页面上所有活跃路由的 `loader`，使 UI 保持最新状态。你无需手动刷新或管理缓存。

如果需要手动触发重验证：

```tsx
import { useRevalidator } from "react-router";

function RefreshButton() {
  const revalidator = useRevalidator();
  return (
    <button
      onClick={() => revalidator.revalidate()}
      disabled={revalidator.state === "loading"}
    >
      刷新数据
    </button>
  );
}
```

---

# 9. Pending UI 与乐观更新

## 9.1 全局加载指示器

使用 `useNavigation` 检测全局导航状态：

```tsx
import { useNavigation, Outlet } from "react-router";

function RootLayout() {
  const navigation = useNavigation();
  const isNavigating = Boolean(navigation.location);

  return (
    <div>
      {isNavigating && <div className="global-spinner">页面加载中...</div>}
      <Outlet />
    </div>
  );
}
```

`useNavigation` 返回的常用属性：

```tsx
const navigation = useNavigation();

navigation.state;      // "idle" | "loading" | "submitting"
navigation.location;   // 正在导航的目标 location（空闲时为 undefined）
navigation.formData;   // 正在提交的表单数据
navigation.formAction; // 提交目标的 URL
navigation.formMethod; // 提交方法（post/put/delete 等）
```

## 9.2 局部加载指示器

`NavLink` 支持 `isPending` 状态：

```tsx
import { NavLink } from "react-router";

function Navbar() {
  return (
    <nav>
      <NavLink to="/dashboard">
        {({ isPending, isActive }) => (
          <span style={{ opacity: isPending ? 0.5 : 1 }}>
            控制面板 {isPending && " (加载中)"}
          </span>
        )}
      </NavLink>
    </nav>
  );
}
```

## 9.3 表单提交 Pending 状态

**使用 fetcher（推荐，适合不需要导航的场景）：**

```tsx
import { useFetcher } from "react-router";

function NewProjectForm() {
  const fetcher = useFetcher();

  return (
    <fetcher.Form method="post" action="/projects">
      <input type="text" name="title" />
      <button type="submit">
        {fetcher.state !== "idle" ? "创建中..." : "创建项目"}
      </button>
    </fetcher.Form>
  );
}
```

**使用 Form + useNavigation（会触发页面导航）：**

```tsx
import { Form, useNavigation } from "react-router";

function NewProjectForm() {
  const navigation = useNavigation();
  const isSubmitting = navigation.formAction === "/projects/new";

  return (
    <Form method="post" action="/projects/new">
      <input type="text" name="title" />
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? "创建中..." : "创建项目"}
      </button>
    </Form>
  );
}
```

## 9.4 乐观更新（Optimistic UI）

当表单数据可以预知 UI 结果时，跳过等待直接更新 UI：

```tsx
import { useFetcher } from "react-router";

function TodoItem({ todo }) {
  const fetcher = useFetcher();

  // 用提交中的数据覆盖当前状态，实现瞬间反馈
  let isComplete = todo.completed;
  if (fetcher.formData) {
    isComplete = fetcher.formData.get("completed") === "true";
  }

  return (
    <li style={{ textDecoration: isComplete ? "line-through" : "none" }}>
      <span>{todo.title}</span>
      <fetcher.Form method="post" action={`/todos/${todo.id}`}>
        <button name="completed" value={String(!isComplete)}>
          {isComplete ? "撤销完成" : "完成"}
        </button>
      </fetcher.Form>
    </li>
  );
}
```

---

# 10. 错误处理

## 10.1 errorElement

每个路由都可以定义 `errorElement`，当该路由的 `loader`、`action` 或渲染过程中抛出错误时，显示此元素：

```tsx
{
  path: "users/:userId",
  element: <UserProfile />,
  loader: userLoader,
  errorElement: <UserError />,
}
```

## 10.2 useRouteError

在 `errorElement` 对应的组件中，使用 `useRouteError` 获取抛出的错误：

```tsx
import { useRouteError, isRouteErrorResponse, Link } from "react-router";

function UserError() {
  const error = useRouteError();

  if (isRouteErrorResponse(error)) {
    return (
      <div>
        <h1>{error.status} {error.statusText}</h1>
        <p>{error.data}</p>
        <Link to="/">返回首页</Link>
      </div>
    );
  }

  return (
    <div>
      <h1>出错了</h1>
      <p>{error instanceof Error ? error.message : "未知错误"}</p>
      <Link to="/">返回首页</Link>
    </div>
  );
}
```

## 10.3 在 Loader/Action 中抛出错误

```tsx
// 抛出 Response 对象（推荐，可设置状态码）
async function userLoader({ params }) {
  const user = await fetchUser(params.userId);
  if (!user) {
    throw new Response("用户不存在", { status: 404 });
  }
  return { user };
}

// 抛出普通 Error
async function dataLoader() {
  try {
    const data = await fetchData();
    return data;
  } catch (err) {
    throw new Error("数据加载失败，请稍后重试");
  }
}
```

## 10.4 错误冒泡

如果一个路由没有 `errorElement`，错误会向上冒泡到最近定义了 `errorElement` 的父路由。

**最佳实践**：至少在根路由上定义一个全局 `errorElement` 作为兜底。

```tsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    errorElement: <GlobalError />,   // 全局兜底
    children: [
      {
        path: "users/:id",
        element: <UserProfile />,
        errorElement: <UserError />, // 针对性错误处理
        loader: userLoader,
      },
      // 其他没有 errorElement 的路由出错时，会冒泡到 GlobalError
    ],
  },
]);
```

---

# 11. 常用 Hooks 参考

## 11.1 useParams

获取 URL 中的动态参数：

```tsx
import { useParams } from "react-router";

function UserPage() {
  const { userId } = useParams();
  return <h1>用户 ID: {userId}</h1>;
}
```

## 11.2 useLoaderData

获取当前路由 `loader` 返回的数据：

```tsx
import { useLoaderData } from "react-router";

function ProductPage() {
  const { name, price } = useLoaderData();
  return <h1>{name} - ¥{price}</h1>;
}
```

## 11.3 useActionData

获取当前路由 `action` 返回的数据（常用于表单验证错误）：

```tsx
import { useActionData, Form } from "react-router";

function LoginPage() {
  const actionData = useActionData();

  return (
    <Form method="post">
      <input name="email" type="email" />
      <input name="password" type="password" />
      {actionData?.error && <p className="error">{actionData.error}</p>}
      <button type="submit">登录</button>
    </Form>
  );
}
```

## 11.4 useNavigate

编程式导航：

```tsx
import { useNavigate } from "react-router";

const navigate = useNavigate();
navigate("/home");                  // 前往 /home
navigate(-1);                       // 后退
navigate("/users", { replace: true }); // 替换当前记录
```

## 11.5 useNavigation

获取全局导航状态：

```tsx
import { useNavigation } from "react-router";

const navigation = useNavigation();
// navigation.state: "idle" | "loading" | "submitting"
// navigation.location: 目标 location（导航中）
// navigation.formData: 正在提交的表单数据
```

## 11.6 useLocation

获取当前 URL 的 location 对象：

```tsx
import { useLocation } from "react-router";

function CurrentPath() {
  const location = useLocation();
  return <p>当前路径: {location.pathname}{location.search}</p>;
}
// location.pathname — 路径
// location.search   — 查询字符串（如 ?q=hello）
// location.hash     — 哈希（如 #section）
// location.state    — 通过 navigate/Link 传递的 state
```

## 11.7 useSearchParams

读取和修改 URL 查询参数：

```tsx
import { useSearchParams } from "react-router";

function SearchPage() {
  const [searchParams, setSearchParams] = useSearchParams();
  const query = searchParams.get("q") || "";

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setSearchParams({ q: e.target.value })}
        placeholder="搜索..."
      />
      <p>当前搜索: {query}</p>
    </div>
  );
}
```

## 11.8 useMatches

获取当前匹配的所有路由（含整条嵌套链）：

```tsx
import { useMatches } from "react-router";

function Breadcrumbs() {
  const matches = useMatches();
  return (
    <nav>
      {matches
        .filter((m) => m.handle?.breadcrumb)
        .map((m, i) => (
          <span key={m.id}>
            {i > 0 && " > "}
            {m.handle.breadcrumb}
          </span>
        ))}
    </nav>
  );
}
```

配合路由配置中的 `handle` 字段使用：

```tsx
{
  path: "dashboard",
  element: <Dashboard />,
  handle: { breadcrumb: "控制面板" },
}
```

## 11.9 useRouteError

在 `errorElement` 组件中获取抛出的错误（见 [第 10 章](#10-错误处理)）。

## 11.10 useFetcher

不触发导航的数据交互，适合局部提交/加载（见 [第 8.3 节](#83-调用-action-的三种方式)）。

## 11.11 useRouteLoaderData

访问其他路由的 loader 数据（见 [第 7.5 节](#75-userouteloaderdata访问其他路由的数据)）。

## 11.12 useBlocker

阻止用户在表单未保存时离开页面：

```tsx
import { useBlocker } from "react-router";

function EditForm() {
  const [isDirty, setIsDirty] = useState(false);

  const blocker = useBlocker(
    ({ currentLocation, nextLocation }) =>
      isDirty && currentLocation.pathname !== nextLocation.pathname
  );

  return (
    <div>
      <input onChange={() => setIsDirty(true)} />
      {blocker.state === "blocked" && (
        <div className="modal">
          <p>你有未保存的更改，确定离开吗？</p>
          <button onClick={() => blocker.proceed()}>确定离开</button>
          <button onClick={() => blocker.reset()}>留在页面</button>
        </div>
      )}
    </div>
  );
}
```

## 11.13 useRevalidator

手动触发所有 loader 重新执行：

```tsx
import { useRevalidator } from "react-router";

function RefreshButton() {
  const { revalidate, state } = useRevalidator();
  return (
    <button onClick={() => revalidate()} disabled={state === "loading"}>
      {state === "loading" ? "刷新中..." : "刷新数据"}
    </button>
  );
}
```

---

# 12. 其他路由器类型

除了 `createBrowserRouter`，Data 模式还提供了其他路由器工厂函数：

| 路由器 | 说明 |
|--------|------|
| `createBrowserRouter` | 使用 HTML5 History API，URL 如 `/about`（**最常用**） |
| `createHashRouter` | 使用 URL hash，URL 如 `/#/about`，适合无法控制服务器配置的场景 |
| `createMemoryRouter` | 路由状态存在内存中，适合测试和非浏览器环境（如 React Native） |

## createHashRouter 示例

```tsx
import { createHashRouter, RouterProvider } from "react-router";

const router = createHashRouter([
  {
    path: "/",
    element: <Root />,
    children: [
      { index: true, element: <Home /> },
      { path: "about", element: <About /> },
    ],
  },
]);

function App() {
  return <RouterProvider router={router} />;
}
```

## createMemoryRouter 示例（单元测试）

```tsx
import { createMemoryRouter, RouterProvider } from "react-router";
import { render, screen } from "@testing-library/react";

test("renders home page", () => {
  const router = createMemoryRouter(
    [
      { path: "/", element: <Home /> },
      { path: "/about", element: <About /> },
    ],
    { initialEntries: ["/"] }
  );

  render(<RouterProvider router={router} />);
  expect(screen.getByText("首页")).toBeInTheDocument();
});
```

---

# 13. 声明式模式简介

如果你不需要 `loader`/`action` 等数据功能，可以使用更简单的声明式模式：

```tsx
import { BrowserRouter, Routes, Route, Link, useParams } from "react-router";

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">首页</Link>
        <Link to="/about">关于</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users/:userId" element={<UserProfile />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

> 声明式模式不支持 `loader`、`action`、`errorElement`、自动代码拆分等功能。
> 如果有数据加载需求，建议升级到 Data 模式。

---

# 14. 实战：构建一个博客应用

下面用 **Data 模式** 构建一个带有文章列表、详情和创建功能的小博客。

## 14.1 模拟数据

```tsx
// src/data/posts.ts
export interface Post {
  id: string;
  title: string;
  content: string;
  createdAt: string;
}

let posts: Post[] = [
  { id: "1", title: "React Router v7 入门", content: "React Router v7 带来了全新的架构，支持三种使用模式...", createdAt: "2026-02-20" },
  { id: "2", title: "理解嵌套路由", content: "嵌套路由是 React Router 最强大的功能之一，它让布局组合变得自然...", createdAt: "2026-02-22" },
  { id: "3", title: "Loader 与 Action 的妙用", content: "Loader 和 Action 让数据流变得简单、可预测...", createdAt: "2026-02-24" },
];

export async function getPosts(): Promise<Post[]> {
  await delay(300);
  return [...posts];
}

export async function getPost(id: string): Promise<Post | undefined> {
  await delay(200);
  return posts.find((p) => p.id === id);
}

export async function createPost(data: { title: string; content: string }): Promise<Post> {
  await delay(300);
  const newPost: Post = {
    id: String(posts.length + 1),
    title: data.title,
    content: data.content,
    createdAt: new Date().toISOString().slice(0, 10),
  };
  posts.push(newPost);
  return newPost;
}

export async function deletePost(id: string): Promise<void> {
  await delay(200);
  posts = posts.filter((p) => p.id !== id);
}

function delay(ms: number) {
  return new Promise((r) => setTimeout(r, ms));
}
```

## 14.2 路由配置与入口

```tsx
// src/main.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import {
  createBrowserRouter,
  RouterProvider,
  redirect,
} from "react-router";
import RootLayout from "./routes/RootLayout";
import Home from "./routes/Home";
import PostsList from "./routes/PostsList";
import PostDetail from "./routes/PostDetail";
import NewPost from "./routes/NewPost";
import ErrorPage from "./routes/ErrorPage";
import { getPosts, getPost, createPost, deletePost } from "./data/posts";

const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    errorElement: <ErrorPage />,
    children: [
      {
        index: true,
        element: <Home />,
      },
      {
        path: "posts",
        element: <PostsList />,
        loader: async () => {
          const posts = await getPosts();
          return { posts };
        },
      },
      {
        path: "posts/:postId",
        element: <PostDetail />,
        loader: async ({ params }) => {
          const post = await getPost(params.postId!);
          if (!post) {
            throw new Response("文章不存在", { status: 404 });
          }
          return { post };
        },
        action: async ({ params }) => {
          await deletePost(params.postId!);
          return redirect("/posts");
        },
      },
      {
        path: "posts/new",
        element: <NewPost />,
        action: async ({ request }) => {
          const formData = await request.formData();
          const title = formData.get("title") as string;
          const content = formData.get("content") as string;
          await createPost({ title, content });
          return redirect("/posts");
        },
      },
    ],
  },
]);

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

## 14.3 根布局

```tsx
// src/routes/RootLayout.tsx
import { Outlet, NavLink, useNavigation } from "react-router";

export default function RootLayout() {
  const navigation = useNavigation();
  const isLoading = navigation.state === "loading";

  return (
    <div style={{ maxWidth: 800, margin: "0 auto", padding: 20 }}>
      <header>
        <h1>我的博客</h1>
        <nav style={{ display: "flex", gap: 16, marginBottom: 8 }}>
          <NavLink to="/" end style={navStyle}>首页</NavLink>
          <NavLink to="/posts" end style={navStyle}>文章列表</NavLink>
          <NavLink to="/posts/new" style={navStyle}>写文章</NavLink>
        </nav>
        {isLoading && <div style={{ color: "#1890ff" }}>加载中...</div>}
      </header>
      <main style={{ marginTop: 20 }}>
        <Outlet />
      </main>
      <footer style={{ marginTop: 40, color: "#999", borderTop: "1px solid #eee", paddingTop: 12 }}>
        © 2026 My Blog — Powered by React Router v7
      </footer>
    </div>
  );
}

function navStyle({ isActive }: { isActive: boolean }) {
  return {
    fontWeight: isActive ? "bold" as const : "normal" as const,
    color: isActive ? "#1890ff" : "#333",
    textDecoration: "none",
  };
}
```

## 14.4 首页

```tsx
// src/routes/Home.tsx
import { Link } from "react-router";

export default function Home() {
  return (
    <div>
      <h2>欢迎来到我的博客</h2>
      <p>这是一个使用 React Router v7 Data 模式构建的示例应用。</p>
      <p>
        <Link to="/posts">浏览文章 →</Link>
      </p>
    </div>
  );
}
```

## 14.5 文章列表

```tsx
// src/routes/PostsList.tsx
import { useLoaderData, Link } from "react-router";
import type { Post } from "../data/posts";

export default function PostsList() {
  const { posts } = useLoaderData() as { posts: Post[] };

  return (
    <div>
      <h2>文章列表</h2>
      {posts.length === 0 ? (
        <p>暂无文章。<Link to="/posts/new">去写一篇？</Link></p>
      ) : (
        <ul style={{ listStyle: "none", padding: 0 }}>
          {posts.map((post) => (
            <li key={post.id} style={{ marginBottom: 12, padding: 12, border: "1px solid #eee", borderRadius: 8 }}>
              <Link to={`/posts/${post.id}`} style={{ fontSize: 18, textDecoration: "none" }}>
                {post.title}
              </Link>
              <div style={{ color: "#999", fontSize: 14, marginTop: 4 }}>{post.createdAt}</div>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

## 14.6 文章详情（含删除 Action）

```tsx
// src/routes/PostDetail.tsx
import { useLoaderData, Form, Link } from "react-router";
import type { Post } from "../data/posts";

export default function PostDetail() {
  const { post } = useLoaderData() as { post: Post };

  return (
    <article>
      <Link to="/posts" style={{ color: "#1890ff" }}>← 返回列表</Link>
      <h2>{post.title}</h2>
      <time style={{ color: "#999" }}>{post.createdAt}</time>
      <p style={{ marginTop: 16, lineHeight: 1.8 }}>{post.content}</p>
      <Form method="post" onSubmit={(e) => {
        if (!confirm("确定要删除这篇文章吗？")) e.preventDefault();
      }}>
        <button type="submit" style={{ color: "red", marginTop: 20 }}>
          删除文章
        </button>
      </Form>
    </article>
  );
}
```

## 14.7 创建文章

```tsx
// src/routes/NewPost.tsx
import { Form, useNavigation, Link } from "react-router";

export default function NewPost() {
  const navigation = useNavigation();
  const isSubmitting = navigation.state === "submitting";

  return (
    <div>
      <h2>写新文章</h2>
      <Form method="post">
        <div style={{ marginBottom: 12 }}>
          <label>
            标题：
            <input
              type="text" name="title" required
              style={{ marginLeft: 8, width: 300, padding: "4px 8px" }}
            />
          </label>
        </div>
        <div style={{ marginBottom: 12 }}>
          <label>
            内容：
            <textarea
              name="content" required rows={8}
              style={{ display: "block", width: "100%", marginTop: 4, padding: 8 }}
            />
          </label>
        </div>
        <button type="submit" disabled={isSubmitting}>
          {isSubmitting ? "发布中..." : "发布"}
        </button>
        <Link to="/posts" style={{ marginLeft: 12, color: "#999" }}>取消</Link>
      </Form>
    </div>
  );
}
```

## 14.8 全局错误页面

```tsx
// src/routes/ErrorPage.tsx
import { useRouteError, isRouteErrorResponse, Link } from "react-router";

export default function ErrorPage() {
  const error = useRouteError();

  if (isRouteErrorResponse(error)) {
    return (
      <div style={{ textAlign: "center", marginTop: 80 }}>
        <h1>{error.status}</h1>
        <p>{error.data || error.statusText}</p>
        <Link to="/">返回首页</Link>
      </div>
    );
  }

  return (
    <div style={{ textAlign: "center", marginTop: 80 }}>
      <h1>出错了</h1>
      <p>{error instanceof Error ? error.message : "发生了未知错误"}</p>
      <Link to="/">返回首页</Link>
    </div>
  );
}
```

## 14.9 应用数据流总结

```
用户访问 /posts
       ↓
  匹配路由 → 执行 loader → 返回 { posts }
       ↓
  渲染 PostsList 组件 → useLoaderData() 获取数据
       ↓
  用户点击 "删除" 按钮 → <Form method="post"> 提交
       ↓
  执行 action → deletePost() → redirect("/posts")
       ↓
  自动重新执行 /posts 的 loader → 获取最新数据 → UI 更新
```

---

# 附录：常用 API 速查表

| API | 类型 | 用途 |
|-----|------|------|
| `createBrowserRouter` | 工厂函数 | 创建基于 History API 的数据路由器 |
| `createHashRouter` | 工厂函数 | 创建基于 Hash 的数据路由器 |
| `createMemoryRouter` | 工厂函数 | 创建内存路由器（用于测试） |
| `<RouterProvider>` | 组件 | 将路由器注入应用 |
| `<Link>` | 组件 | 声明式导航 |
| `<NavLink>` | 组件 | 带激活状态的导航链接 |
| `<Form>` | 组件 | 声明式表单提交（触发 action） |
| `<Outlet>` | 组件 | 渲染子路由 |
| `<Navigate>` | 组件 | 声明式重定向 |
| `<ScrollRestoration>` | 组件 | 导航时恢复滚动位置 |
| `useParams` | Hook | 获取动态路由参数 |
| `useLoaderData` | Hook | 获取当前路由 loader 数据 |
| `useActionData` | Hook | 获取当前路由 action 返回数据 |
| `useRouteLoaderData` | Hook | 获取指定路由的 loader 数据 |
| `useNavigate` | Hook | 编程式导航 |
| `useNavigation` | Hook | 全局导航/提交状态 |
| `useLocation` | Hook | 当前 location 信息 |
| `useSearchParams` | Hook | 读写 URL 查询参数 |
| `useMatches` | Hook | 当前匹配的路由列表 |
| `useRouteError` | Hook | 在 errorElement 中获取错误 |
| `useFetcher` | Hook | 不触发导航的数据请求 |
| `useSubmit` | Hook | 编程式表单提交 |
| `useBlocker` | Hook | 阻止导航（离开确认） |
| `useRevalidator` | Hook | 手动触发 loader 重新执行 |
| `useOutletContext` | Hook | 获取父路由通过 Outlet 传递的 context |
| `redirect` | 工具函数 | 在 loader/action 中重定向 |
| `isRouteErrorResponse` | 工具函数 | 判断错误是否为 Response |
