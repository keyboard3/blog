---
title: next.js docs
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-30 10:47:26
password:
summary:
tags: [next.js, docs, 翻译]
categories:
---

[nextjs-docs](https://nextjs.org/docs/getting-started)

# 文档

## 介绍

如果你是新人，建议你学习该[课程](https://nextjs.org/learn/basics/getting-started)
带有测试的交互课程会指导你完成使用 Next.js 所需要的知识
如果你有任何有关 Next.js 的问题，你可以去[社区](https://github.com/zeit/next.js/discussions)中提问
**系统要求**

- Node.js 10 以上
- MacOS，windows(包括 WSL),以及 Linux

### 设置

我们建议使用 create-next-app 来创建 Next.js 应用，它将自动为你设置一切。运行以下命令创建项目：

```
npm init next-app
# or
yarn create next-app
```

安装完成之后，跟着说明启动开发服务。尝试修改 pages/index.js 并在你浏览器上查看结果。

### 手动设置

在你的项目中安装 next,react 和 react-dom：

```
npm install next react react-dom
```

打开 package.json 并添加下面的 scripts

```js
"scripts": {
  "dev": "next",
  "build": "next build",
  "start": "next start"
}
```

下面脚本指向开发应用的不同阶段：

- dev - 运行 next 在开发模式下启动 Next.js
- build - 运行 next build 为生产模式构建应用
- start - 运行 next start 启动 Next.js 生产服务

Next.js 是围绕页面概念构建的。 页面是从在 pages 目录下的.js,.jsx,.ts 或者.tsx 文件中导出的 react 组件

页面是基于它们的文件名来关联路由的。例如 pages/about.js 被映射成 /about. 你甚至可以在文件名上添加动态路由参数。

在你的项目中创建 pages 目录

使用下面的内容填充到 ./pages/index.js

```jsx
function HomePage() {
  return <div>Welcome to Next.js!</div>;
}

export default HomePage;
```

运行 npm run dev 来开始开发应用。它启动一个开发服务在 http://localhost:3000

通过 http://localhost:3000 来查看你的应用

到此为止，我们获得：

- 自动编译和打包 （通过 webpack 和 babel）
- 热加载
- 静态生成和服务端渲染 ./pages/
- 静态文件服务 ./public/ 映射成 /

## 基础功能

### Pages

> 这个文档基于 Next.js 9.3 及以上，如果你使用的是旧版，参考 [之前的文档](https://nextjs.org/docs/tag/v9.2.2/basic-features/pages)

在 Next.js 中，一个页面就是一个组件，它们从 pages 目录文件下的 .js, jsx, .ts 或者 .tsx 文件中导出。每个页面基于它们的文件名与路由关联。

例如：如果你创建 pages/about.js，并导出下面的 React 组件，它将可以通过 /about 访问。

```jsx
function About() {
  return <div>About</div>;
}

export default About;
```

#### Pages 使用动态路由

Next.js 支持页面使用动态路由。比如，如果你创建文件名叫做 pages/posts/[id].js, 然后它就可以通过 posts/1, posts/2,等等。

> 学学习更多动态路由知识，查看[动态路由文档](https://nextjs.org/docs/routing/dynamic-routes)

#### 预渲染

默认，Next.js 对每个页面预渲染。这意味着 Next.js 会提前为每个页面生成 HTML，而不是由客户端 JavaScript 来生成。预渲染会带来更好的性能和 SEO。

每个生成的 HTML 只关联那个页面需要的最少 js 代码。当页面被浏览器加载完毕，它的 js 代码运行会使得页面完全可交互。（这个过程被叫做水合/hydration）

##### 两种预渲染的方式

Next.js 有两种预渲染方式：静态生成和服务端渲染。这区别在于为页面生成 HTML 的时机。

- **静态生成（建议）**：HTML 在构建时被生成，并可以被每个请求复用。
- **服务端渲染**：HTML 在每次请求时都会生成

重要的是，Next.js 让你可以选择你每个页面的预渲染方式。你可以针对大部分页面使用静态生成方式以及针对特殊请求使用服务端渲染来，创建一个“混合” Next.js 应用。

我们建议使用静态生成来代替服务端渲染来提高性能。静态生成的页面可以被 CDN 缓存来提高启动性能。然而，在某些情况下，服务端渲染可能是唯一的选择。

最后，你可以始终将客户端渲染和静态生成或者服务端渲染一起使用。这意味着页面的某些部分可以完全的由客户端代码渲染。为了进一步学习，可以查看[数据获取](https://nextjs.org/docs/basic-features/data-fetching#fetching-data-on-the-client-side)文档

#### 静态生成（建议）

如果页面使用静态生成，页面的 HTML 将在构建时被生成。这意味着，在生产环境下，页面 HTML 在你运行 next build 时生成。这个 HTML 将会被每个请求复用，它也可以被 CDN 缓存。

在 Next.js 中，你可以带着数据或者不带数据生成页面，让我们来看看这两个情况。

##### 不带数据静态生成

默认情况下，Next.js 不带数据静态生成预渲染页面。下面是案例：

```jsx
function About() {
  return <div>About</div>;
}

export default About;
```

注意这个页面不需要获取任何额外数据去预渲染。这个场景比如：Next.js 在构建时对每个 page 生成单个 HTML。

##### 带数据静态生成

有些页面在预渲染时获取额外数据。这里有两个情况，一种或者两种都适用。每种情况，那你可以适用 Next.js 提供的特殊功能:

1. 页面内容依赖外部数据：适用 getStaticProps
2. 页面路径依赖外部数据：适用 getStaticPaths (通常也用到 getStaticProps)

###### 场景 1：页面内容依赖额外数据

例如：你博客页面可能需要从 CMS（内容管理系统）中获取博客列表。

```jsx
// TODO: 需要在页面预渲染之前通过调用 api 获取 posts
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  );
}

export default Blog;
```

为了在预渲染时获取这个数据，Next.js 允许在这个文件下导出 getStaticProps 的异步函数。这个函数在构建时调用，然后在预渲染时将获取的数据传递给页面的 props。

```jsx
// 你可以使用任何数据获取库
import fetch from "node-fetch";

function Blog({ posts }) {
  // 渲染 posts...
}

// 这个函数在构建时被调用
export async function getStaticProps() {
  // 调用外部api获得posts
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // 返回 { props: posts }, 这个 Blog component
  // 在构建时将接收 `posts` 作为属性
  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

为了学习 getStaticProps 如何工作，查看[数据获取文档](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation)

###### 场景 2：页面路径依赖额外数据

Next.js 允许你通过动态路由来创建页面。比如，你可以创建 pages/posts/[id].js 来基于 id 来显示单个博客。当你访问 posts/1 是博客将获得 id:1 的参数来显示博客内容。

> 为了进一步学习动态路由，查看[动态路由文档](https://nextjs.org/docs/routing/dynamic-routes)

然而，你可能需要额外的数据提供 id 来预渲染。
例如：假设你只在数据库中添加了 id:1 的文章。这种情况下，你值需要在构建时渲染 posts/1。

随后，你可能添加了 id:2 的文章，然后你想要同样的预渲染 posts/2。

所以你预渲染的页面路径需要依赖外部数据。为了处理它，Next.js 让你在动态页面( pages/posts/[id].js )中导出异步函数 getStaticPaths。这个函数在构建时调用，并让你可以指定那些路径你想要预渲染。

```js
import fetch from "node-fetch";

// 在构建时被调用
export async function getStaticPaths() {
  // 调用外部api获取文章列表
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // 获取我们想要渲染的文章路径
  const paths = posts.map((post) => `/posts/${post.id}`);

  // 我们只在构建时预渲染这些路径
  // { fallback: false } 因为着其他路径将是404
  return { paths, fallback: false };
}
```

同样在 pages/posts/[id].js 中，你需要导出 getStaticProps 来根据 id 获取数据预渲染页面。

```jsx
import fetch from "node-fetch";

function Post({ post }) {
  // Render post...
}

export async function getStaticPaths() {
  // ...
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  // Pass post data to the page via props
  return { props: { post } };
}

export default Post;
```

##### 什么时候使用静态生成？

我们建议尽可能的使用静态生成，因为那你的页面只被构建一次且可以被 CDN 缓存，可以使得比每次请求都服务端渲染快很多。

许多页面类型都可以使用静态生成，包括：

- 营销页面
- 博客文章
- 电子商务产品列表
- 帮助和文档

你应该问你自己：“我们可以在用户请求之前预渲染页面吗？” 如果答案是 yes，然后你应该选择静态生成。

另一方面，如果在用户请求之前无法预渲染，静态生成则不是一个好建议。可能你的页面频繁根据数据刷新，页面内容在每次请求时改变。

这种情况，你可以渲染以下任何操作。

- 使用带客户端渲染的静态生成：你可以跳过页面的某些部分预渲染，然后使用客户端渲染来填充它们。
- 使用服务端渲染：Next.js 在每次请求预渲染。它会很慢，因为页面无法被 CDN 缓存，但是预渲染页面是实时刷新的。

#### 服务端渲染

> 也叫做 SSR 或者动态渲染

如果页面使用服务端渲染，页面 HTML 每次请求时生成。

为了让页面使用服务端渲染，你需要导出 getServerSideProps 异步函数。这个函数将在每次请求时在服务端被调用。

例如，假设你的页面需要用最新的数据预渲染（通过外部的 api 获取数据）。你应该写下 getServerSideProps 来获取数据传递给 Page。

```js
import fetch from "node-fetch";

function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`);
  const data = await res.json();

  // Pass data to the page via props
  return { props: { data } };
}

export default Page;
```

如你所见，getServerSideProps 和 getStaticProps 很像，但是区别的是，getServerSideProps 是每个请求都会调用而不是在构建时。

#### 总结

我们讨论了 Next.js 中的两种预渲染方式。

- 静态生成（推荐）：页面在构建时生成并每次请求都可以复用。为了让页面使用静态生成，导出纯页面组件，或者导出 getStaticProps（如果需要导出 getStaticPaths）。会在用户请求之前就预渲染好。你也可以使用客户端渲染额外的数据。

- 服务端渲染：每次请求都会生成 HTML。为了让页面使用服务端渲染，导出 getServerSideProps。因为服务端渲染的结果性能比静态生成差，只有当绝对必须的时候才用它。

### 数据获取

在 Pages 文档中，我们解释了 Next.js 有两种预渲染方式：静态生成和服务端渲染。在这个页面，我们将深度讨论每种情况的数据获取策略。我们建议你首先读一下 Pages 文档。

我们将讨论 3 种你可以在预渲染时使用的 Next.js 函数：

- getStaticProps(静态生成)：在构建时获取数据
- getStaticPaths(静态生成)：指定预渲染时的根据数据的动态路由
- getServerSideProps(服务端渲染)：在每次请求时获取数据。

另外，我们将简要讨论如何在客户端获取数据。

#### getStaticProps(静态生成)

如果你在页面中导出 getStaticProps 异步函数，Next.js 将在构建时使用 getStaticProps 返回的数据作为 Props 渲染页面。

```js
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  };
}
```

这个 Context 参数是一个包含下面属性的对象：

- params 包含了当页面使用动态路由的路由参数。例如，如果页面名是 [id].js ，然后 params 将是 {id:...}。会和 getStaticPaths 一起使用
- preview 是 true 表示页面是在预览模式下。[预览模式文档](https://nextjs.org/docs/advanced-features/preview-mode)
- previewData 包含通过 setPreviewData 设置的预览数据。[预览模式文档](https://nextjs.org/docs/advanced-features/preview-mode)

##### 简单实例

下面的案例使用 getStaticProps 从 CMS 中获取博客章节列表。

```jsx
// You can use any data fetching library
import fetch from "node-fetch";

// posts will be populated at build time by getStaticProps()
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  );
}

// This function gets called at build time on server-side.
// It won't be called on client-side, so you can even do
// direct database queries. See the "Technical details" section.
export async function getStaticProps() {
  // Call an external API endpoint to get posts.
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // By returning { props: posts }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

##### 什么时机应该使用 getStaticProps?

当遇到以下条件时应该使用 getStaticProps:

- 在用户请求之前的构建阶段渲染有数据的页面是有效的
- 数据来自无头的 CMS
- 数据可以被公开缓存（不带用户信息）
- 页面需要预渲染（为 SEO）并且非常快 - getStaticProps 生成 HTML 和 JSON 文件，它们可以被 CDN 缓存来提高性能

##### TypeScript: 使用 GetStaticProps

对于 TypeScript,你可以从 next 中使用 GetStaticProps 类型：

```js
import { GetStaticProps } from "next";

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
};
```

##### 读文件：使用 process.cwd()

在 getStaticProps 中可以从文件系统中被直接读取文件。

为了做到，你需要获取文件的全路径。

因为 Next.js 编译你的代码到独立的目录，你无法使用 `__dirname` 作为路径，因为它返回的不是 pages 目录。

你可以使用 process.cwd()，它会给你 Next.js 被执行的目录。

```jsx
import fs from "fs";
import path from "path";

// 文章列表将在构建时被getStaticProps()填充
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>
          <h3>{post.filename}</h3>
          <p>{post.content}</p>
        </li>
      ))}
    </ul>
  );
}

// 这个函数将在服务端构建时被调用
// 它不会被在客户端调用，所以你甚至可以直接访问数据库
export async function getStaticProps() {
  const postsDirectory = path.join(process.cwd(), "posts");
  const filenames = fs.readdirSync(postsDirectory);

  const posts = filenames.map((filename) => {
    const filePath = path.join(postsDirectory, filename);
    const fileContents = fs.readFileSync(filePath, "utf8");

    // 通常你应该解析和转换内容
    // 比如你可以在这里转换markdown成HTML

    return {
      filename,
      content: fileContents,
    };
  });
  // 返回 { props: posts }, the Blog component 将在构建时接收posts作为属性
  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

##### 技术细节

**只在构建时运行**
因为 getStaticProps 在构建时生成静态 HTML，所以它不会接收那些仅在请求时有效的数据，比如查询参数或者 HTTP 请求头。

**直接写服务端代码**
注意 getStaticProps 只运行在服务端。它永远不会运行到客户端。它不会包含为浏览器生成的 JS 打包文件。这因为着你可以写直接访问数据库查询的代码，不会发送到浏览器上。你可以不在 getStaticProps 调用 API，你可以直接在 getStaticProps 中写下服务端代码。

**静态生成 HTML 和 JSON**
当页面在构建时使用 getStaticProps 进行预渲染，除了页面内的 HTML 文件，Next.js 生成 JSON 文件来存储 getStaticProps 的运行结果。

这个 JSON 文件将使用在客户端通过`next/link`((文档)[https://nextjs.org/docs/api-reference/next/link])或者`next/router`((文档)[https://nextjs.org/docs/api-reference/next/router])导航时请求。当你导航到这个页面，它使用 getStaticProps，Next.js 获取这个 JSON 文件（构建时提前计算的），然后使用它作为页面组件的属性参数。这意味着客户端跳转时不会调用 getStaticProps，只使用导出的 JSON 文件。

**仅在页面中允许**
只能在 page 中导出 getStaticProps。不能再非 Page 文件下到导出。

其中一个限制原因是 React 需要在渲染页面之前加载所有的数据

同样，你必须使用`export async function getStaticProps() {}` - 如果你添加 getStaticProps 作为页面组件的属性，则无法工作。

**在开发模式下每个请求运行**
在开发模式下(`next dev`), getStaticProps 将在每次请求时调用。

**预览模式**
在某些情况下，你可能想要临时绕过静态生成，并在使用请求时渲染页面而不是使用构建时的结果。比如，你可能使用无头的 CMS 并想要在发布之前预览草稿。

这种场景 Next.j 使用预览模式的功能提供支持。进步了解[预览模式文档](https://nextjs.org/docs/advanced-features/preview-mode)

#### getStaticPaths (静态生成)

如果页面有动态路由（(文档)[https://nextjs.org/docs/routing/dynamic-routes]）并使用了 getStaticProps，需要定义 paths 列表，才会在构建时生成 HTML。

如果你在动态路由的 Page 中导出 getStaticPaths 异步函数，Next.js 将静态预渲染所有被 getStaticPaths 指定的路径。

```js
export async function getStaticPaths() {
  return {
    paths: [
      { params: { ... } } // See the "paths" section below
    ],
    fallback: true or false // See the "fallback" section below
  };
}
```

**paths 键必须**
这个 paths 键决定哪些路径会被预渲染。例如，假设你有一个命名为 pages/posts/[id].js 的动态路由页面。如果你在这个页面中导出 getStaticProps 并返回以下的路径：

```js
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: ...
}
```

然后 Next.js 将在构建时使用`pages/posts/[id].js`的页面组件静态生成`posts/1`和`posts/2`

注意每个 params 的值都必须与页面名称中使用的参数匹配：

- 如果页面名称是`pages/posts/[postId]/[commentId]`,params 应该包含 postId 和 commentId。
- 如果页面名使用捕获所有的路由，比如`pages/[...slug]`，然后 params 应该包含 slug 是个数组。比如，如果数组是`['foo','bar']`，然后 Next.js 将静态生成页面在`/foo/bar`

**fallback 键必须**
getStaticPaths 返回的对象一定要包含 bool 类型的 fallback 键。

`fallback:false`

如果 fallback 是 false，任何不在 getStaticPaths 的路径的结果将是 404 页面。如果你预渲染的路径数量很小的话可以之前在构建时全部生成。当新页面不经常添加时非常有用。如果你要向数据中添加很多数据项并需要渲染它们，你需要再次构建。

这里有个实例，每个预渲染的博客文章的页面都是`pages/posts/[id].js`。博客文章的列表在 getStaticPaths 中通过 CMS 获得。然后，每个页面都适用 getStaticProps 从 CMS 中获取文章数据。

```js
// pages/posts/[id].js
import fetch from "node-fetch";

function Post({ post }) {
  // Render post...
}

// This function gets called at build time
export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }));

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false };
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  // Pass post data to the page via props
  return { props: { post } };
}

export default Post;
```

`fallback:true`
如果 fallback 是 true，getStaticProps 的行为将会发生变化：

- getStaticPaths 返回的路径将在构建时被渲染成 HTML
- 没有在构建时生成的路径将不会返回 404 页面。相反，Next.js 将在页面被第一次请求时提供后备版本。在下面看[后备页面](https://nextjs.org/docs/basic-features/data-fetching#fallback-pages)。
- 在后台，Next.js 将静态生成请求路径的 HTML 和 JSON。它们会运行 getStaticProps。
- 当这些完成，浏览器会接收到已生成的路径的 JSON 文件。然后将使用它作为参数自动渲染页面。从用户的视角看，页面从备份页面切换到了完整页面。
- 同时，Next.js 会将这个路径添加到预渲染页面列表。后续请求到相同路径将返回已生成的页面，就像其他在构建时预渲染页面一样。

**备份页面**
页面的备份版本：

- 页面的属性将是空的
- 使用 router,你可以检测如果 fallback 已经被渲染，router.isFallback 将是 true

下面是使用 isFallback 的案例：

```jsx
// pages/posts/[id].js
import { useRouter } from "next/router";
import fetch from "node-fetch";

function Post({ post }) {
  const router = useRouter();

  // 如果页面还没有被生成，它将显示
  // 初始化 until getStaticProps() 结束运行
  if (router.isFallback) {
    return <div>Loading...</div>;
  }

  // Render post...
}

// 这个函数将在构建时运行
export async function getStaticPaths() {
  return {
    // 只有 `/posts/1` and `/posts/2` 在构建时被运行
    paths: [{ params: { id: "1" } }, { params: { id: "2" } }],
    // 启用静态生成其他页面
    // 例如: `/posts/3`
    fallback: true,
  };
}

// 在构建时被执行
export async function getStaticProps({ params }) {
  // 参数包含文章 `id`.
  // 如果路由是 /posts/1, 这个 params.id 将是 1
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  // 传递文章数据作为页面属性
  return { props: { post } };
}

export default Post;
```

**什么时候`fallback:true`有用？**
`fallback:true`当你的应用有非常大数量的静态页面时有用（思考：大型电商网站）。你想要预渲染所有产品页面，但是你的构建将花费很长。

相反，你应该静态生成少量集合的页面，剩下的页面使用`fallback:true`。当有人请求没有生成过的页面时，用户将会看到加载进度条。很快，getStaticProps 完成页面将使用请求的数据渲染。那么从现在开始，所有请求相同路径的人将获得静态预渲染页面。

它保证在保障构建速度和静态生成的优势同时，用户也一直有很快的体验。

**什么时候应该使用 getStaticPaths?**
如果你使用动态路由来静态预渲染页面，那你应该使用 getStaticPaths

**TypeScript: 使用 GetStaticPaths**
在 TypeScript 中，你可以从 next 中使用 GetStaticPaths:

```js
import { GetStaticPaths } from "next";

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
};
```

##### 技术细节

**和 getStaticProps 一起使用**
当你在动态路由参数的页面上使用 getStaticProps 时，你肯定要使用 getStaticProps

你无法让 getStaticPaths 和 getServerSideProps 一起使用。

**只在服务端构建时运行**
getStaticPaths 只在服务端构建时运行

**只运行在 Page 中**
getStaticPaths 只能被 page 导出。你不能在非 Page 的文件中导出它

同样，你必须使用`export async function getStaticPaths() {}` -如果你添加 getStaticPaths 到页面组件上不生效

**在开发模式下每次请求运行**
在开发模式下（`next dev`）, getStaticPaths 将在每次请求时调用。

#### getServerSideProps(服务端渲染)

如果你在页面导出 getServerSideProps 异步函数，Next.js 将在每次请求时通过调用 getServerSideProps 来预渲染页面。

```js
export async function getServerSideProps(context) {
  return {
    props: {}, // 将作为页面组件的属性
  };
}
```

context 参数是包含下列 key 的对象：

- params: 如果页面使用动态路由，params 包含路由的参数。如果页面名字是`[id].js`，那么 params 将是这样的`{id:...}`
- req: Http IncomingMessage 对象.
- res: Http 响应对象
- query: 查询参数
- preview: true 如果页面是预览模式。否则就是 false
- previewData: 通过 setPreviewData 来设置预览数据

##### 简单案例

这里是使用 getSeverSideProps 在每次请求时获取数据预渲染的案例。

```jsx
function Page({ data }) {
  // Render data...
}

// 每次请求获取
export async function getServerSideProps() {
  // 通过api获取数据
  const res = await fetch(`https://.../data`);
  const data = await res.json();

  // 传递data作为页面属性
  return { props: { data } };
}

export default Page;
```

##### 什么时候应该使用 getServerSideProps?

只有当你预渲染的页面需要实时获取数据的时候才应该使用 getServerSideProps。相较于 getStaticProps 第一个字节到达(TTFB)的时间比较慢，因为服务端一定每次请求都要计算结果，并且这个结果没有额外的配置无法被 CDN 缓存。

如果你不需要预渲染数据，你可以考虑在客户端获取数据

##### TypeScript:使用 GetServerSideProps

对于 TypeScript， 你从 next 中使用 GetServerSideProps 类型

```js
import { GetServerSideProps } from "next";

export const getServerSideProps: GetServerSideProps = async (context) => {
  // ...
};
```

##### 技术细节

**只在服务端运行**
getServerSideProps 只能运行服务端并且不会运行在浏览器上。如果页面使用 getServerSideProps，需要注意：

- 当你直接请求这个页面，getServerSideProps 会在每次请求时运行，并且页面每次预渲染返回 props。
- 当你在客户端通过`next/link`或者`next/router`来跳转时，Next.js 执行 getServerSideProps 发送 Api 请求给服务器。它将会返回 getServerSideProps 运行的结果，并且这个 JSON 将会被用来渲染页面。所有这些工作将被 Next.js 自动处理，所以除了 getServerSideProps 你不需要其他额外的定义。

**在能在 page 中导出**
getServerSideProps 只能被 page 导出。你无法在非 page 文件中导出。

同样，你必须使用`export async function getServerSideProps() {}` - 如果你将它作为页面组件的属性这无法工作

#### 在客户端请求数据

如果你页面需要频繁请求数据，你不需要预渲染数据，你可以只在客户端请求数据。在客户端请求数据的案例，如何工作：

- 首先，不带数据立刻显示页面。页面部分内容可以使用静态生成预渲染。你可以在缺失数据的请求下显示加载状态。
- 然后，在客户端请求数据，并在准备好时显示。

这种方式对于用户管理页面非常好，比如。因为管理面板是私有的，用户特有页面，不需要 SEO 则页面不需要预渲染。数据是频繁更新的，要求每次数据都实时请求。

#### SWR

Next.js 背后的团队创建[SWR](https://swr.now.sh/)来获取数据的 React Hook。我们强烈建议如果在客户端请求使用它。它会处理缓存，重新验证，焦点跟踪，间隔重新获取等等。你可以这么使用：

```js
import useSWR from "swr";

function Profile() {
  const { data, error } = useSWR("/api/user", fetch);

  if (error) return <div>failed to load</div>;
  if (!data) return <div>loading...</div>;
  return <div>hello {data.name}!</div>;
}
```

[进一步查看 SWR 文档](https://swr.now.sh/)

### 内置 CSS 支持

Next.js 允许你在 JavaScript 文件中导入 CSS 文件。这是可能的，因为 Next.js 扩展 import 概念到 JS 之外。

#### 添加全局的样式表

为了给你的应用添加样式表，导入 CSS 文件到`pages/_app.js`

例如，考虑将下面的样式命名为 styles.css

```css
body {
  font-family: "SF Pro Text", "SF Pro Icons", "Helvetica Neue", "Helvetica",
    "Arial", sans-serif;
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

创建一个`pages/_app.js`文件，然后导入 styles.css 文件

```js
import "../styles.css";

//  在这个 `pages/_app.js` 文件中，这个默认的导出是必须的.
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}
```

这些样式将会应用到你应用的所有页面和组件上。由于这个全局特性，需要避免冲突，你应该只将它们导入到`pages/_app.js`中

在开发中，用这种方式表示样式表允许你边修改变应用样式-意味着你可以保持应用状态。

在生产环境中，所有 CSS 文件都会被自动级联到一个压缩后的.css 文件中。

#### 添加组件级 CSS

Next.js 支持[CSS 模块](https://github.com/css-modules/css-modules)，使用`[name].module.css`文件命名约定。

CSS 模块通过自动创建独一无二的 class 名字来实现本地域的 CSS。它允许你在不同的文件中使用相同的 CSS 类，而不用担心碰撞。

这个行为使得 CSS 模块拥有了包含组件级 CSS 的理想方法。CSS 模块文件可以被应用中任何地方导入。

比如，思考在`components/`文件下创建一个可重用的 Button 组件。

首先，创建`components/Button.module.css`文件填充以下内容：

```css
/*
你不需要担心 .error {} 碰撞其他类 `.css` 或者
`.module.css` 文件中的error
*/
.error {
  color: white;
  background-color: red;
}
```

然后，创建`components/Button.js`，导入和使用以上 CSS 文件：

```js
import styles from "./Button.module.css";

export function Button() {
  return (
    <button
      type="button"
      // 注意error类可以被导入的styles对象访问
      className={styles.error}
    >
      Destroy
    </button>
  );
}
```

CSS 模块是一个可选的特性，并且只在文件扩展名为 .module.css 有效。常规的`<Link>`样式表和全局的 CSS 文件仍然被支持。

在生产环境下，所有 CSS 模块文件都将被自动级联到许多压缩、代码分割的 .css 文件中。这些 .css 文件表示应用热执行路径，保证加载应用绘制所需的 CSS 最少。

#### CSS-in-JS

**例子**

- [Styled JSx](https://github.com/zeit/next.js/tree/canary/examples/basic-css)
- [Styled Components](https://github.com/zeit/next.js/tree/canary/examples/with-styled-components)
- [Styletron](https://github.com/zeit/next.js/tree/canary/examples/with-styletron)
- [Glamor](https://github.com/zeit/next.js/tree/canary/examples/with-glamor)
- [Cxs](https://github.com/zeit/next.js/tree/canary/examples/with-cxs)
- [Aphrodite](https://github.com/zeit/next.js/tree/canary/examples/with-aphrodite)
- [Fela](https://github.com/zeit/next.js/tree/canary/examples/with-fela)

可以使用现有的任何一种 JS 中使用 CSS 的方案。最简单的使用内联。

```js
function HiThere() {
  return <p style={{ color: "red" }}>hi there</p>;
}

export default HiThere;
```

我们打包[styled-jsx](https://github.com/zeit/styled-jsx)来支持独立作用域的 CSS。目的是用来支持类似于 Web Components 的影子 CSS，不幸的是它[不支持服务端渲染并且仅支持 JS](https://github.com/w3c/webcomponents/issues/71)

看上面的案例中其他流行的 CSS-in-JS 方法（比如 Styled Components）。

使用 styled-jsx 的组件案例：

```jsx
function HelloWorld() {
  return (
    <div>
      Hello world
      <p>scoped!</p>
      <style jsx>{`
        p {
          color: blue;
        }
        div {
          background: red;
        }
        @media (max-width: 600px) {
          div {
            background: blue;
          }
        }
      `}</style>
      <style global jsx>{`
        body {
          background: black;
        }
      `}</style>
    </div>
  );
}

export default HelloWorld;
```

请参考 [styled-jsx 文档](https://github.com/zeit/styled-jsx)看更多的案例

#### Sass 支持

Next.js 允许使用 .scss 和 sass 扩展来导入 Sass。你可以通过 CSS 模块 .module.scss 或者 .module.sass 扩展导入组件级的 Sass。

在你使用 Next.js 内置的 Sass 支持之前，保证安装了 sass

```
npm install sass
```

Sass 的支持和上述内置的 CSS 支持有相同的优势和限制

#### Less 和 Stylus 支持

为了支持导入 .less 或者 .styl 文件，你需要使用下面的插件

- [@zeit/next-less](https://github.com/zeit/next-plugins/tree/master/packages/next-less)
- [@zeit/next-stylus](https://github.com/zeit/next-plugins/tree/master/packages/next-stylus)

#### 关联

- [自定义 PostCSS 配置](https://nextjs.org/docs/advanced-features/customizing-postcss-config)
  通过自定义 Next.js 来扩展 PostCSS 配置和插件

### 静态文件服务

Next.js 可以服务静态文件，比如图片，在根目录下的 public 文件夹下。在 public 下的文件可以被你代码直接通过 '/'引用。

```jsx
function MyImage() {
  return <img src="/my-image.png" alt="my image" />;
}

export default MyImage;
```

这个文件夹同样对于 robots.txt，Google 网站验证，以及其他静态文件（包括.html）都有用。

> 不要重命名 public 文件夹，这个文件名不能被修改并且只能被用来放静态资源

> 确保没有静态文件与 pages/ 文件夹下同名的文件，它会导致错误
> 读 [http://err.sh/next.js/conflicting-public-file-page](http://err.sh/next.js/conflicting-public-file-page)

### TypeScript

Next.js 提供了一个集成的 TypeScript 开箱即用的体验，类似于 IDE

开始，在你的项目根目录下创建一个空的 tsconfig.json 文件

```
touch tsconfig.json
```

Next.js 将使用默认值自动配置这个文件，同样支持使用自定义[编译参数](https://www.typescriptlang.org/docs/handbook/compiler-options.html)配置你的 tsconfig.json

> Next.js 使用 Babel 来处理 TypeScript，有一些[注意事项](https://babeljs.io/docs/en/babel-plugin-transform-typescript#caveats), 以及一些[编译参数有区别](https://babeljs.io/docs/en/babel-plugin-transform-typescript#typescript-compiler-options)

然后，运行 next（通常 npm run dev ），然后 Next.js 将指导你安装必须包完成配置。

```
npm run dev
# 你讲看到下面指令:
#
# 请安装 typescript, @types/react, and @types/node 通过运行:
#
#         yarn add --dev typescript @types/react @types/node
#
# ...
```

你先可以准备开始把.js 文件转化成.tsx，并利用 TypeScript 的优势

> 命名为 next-env.d.ts 文件将被项目的根目录创建。这个文件保证 Next.js 类型被 TypeScript 编译器选中。你不能删除它，然而你可以修改它（但你不需要这么做）

> Next.js 严格模式默认关闭。当你感觉熟悉 TypeScript，建议你在 tsconfig.json 中打开它

默认，Next.js 会上报你开发期间积极处理页面的 TypeScript 错误。不积极页面的 TypeScript 错误不会阻止开发进程。

如果你想要静默这些错误上报，参考[忽略 TypeScript 错误](https://nextjs.org/docs/api-reference/next.config.js/ignoring-typescript-errors)的文档

#### 静态生成和服务端渲染

对于 getStaticProps, getStaticPaths, and getServerSideProps。你可以分别使用 GetStaticProps, GetStaticPaths, and GetServerSideProps 类型

```js
import { GetStaticProps, GetStaticPaths, GetServerSideProps } from "next";

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
};

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
};

export const getServerSideProps: GetServerSideProps = async (context) => {
  // ...
};
```

> 如果你使用 getInitialProps，你可以[参考这个页面](https://nextjs.org/docs/api-reference/data-fetching/getInitialProps#typescript)

#### API 路由

下面是如何为 API 路由使用内置的类型的案例

```js
import { NextApiRequest, NextApiResponse } from "next";

export default (req: NextApiRequest, res: NextApiResponse) => {
  res.status(200).json({ name: "John Doe" });
};
```

你也可以指定响应数据

```js
import { NextApiRequest, NextApiResponse } from "next";

type Data = {
  name: string,
};

export default (req: NextApiRequest, res: NextApiResponse<Data>) => {
  res.status(200).json({ name: "John Doe" });
};
```

#### 自定义 App

如果你有自定义的 App，你可以使用内置的 AppProps 类型，并将文件名改为 ./pages/\_app.tsx：

```
import { AppProps } from 'next/app'

function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}

export default MyApp
```

## 路由

Next.js 具有页面概念的文件系统路由。

当文件被添加到 pages 目录下，它自动成为了有效的路由

pages 目录下的文件被用来定义成常见的模式

**索引路由**
router 将会自动的路由到目录下的 index 文件

- `pages/index.js` → /
- `pages/blog/index.js` → /blog

**嵌套路由**
路由支持嵌套文件。如果你创建一个嵌套目录结构的文件夹，将自动以相同的方式路由

- `pages/blog/first-post.js` → `/blog/first-post`
- `pages/dashboard/settings/username.js` → /`dashboard/settings/username`

**动态路由段**
为了匹配动态分段你可以使用括号语法。它允许你匹配命名参数

- `pages/blog/[slug].js` → `/blog/:slug` (`/blog/hello-world`)
- `pages/[username]/settings.js` → `/:username/settings` (`/foo/settings`)
- `pages/post/[...all].js` → `/post/\*` (`/post/2020/id/title`)

#### 不同页面之间的链接

Next.js 的 router 允许你在页面间进行客户端路由转换，类似于单页应用。

Link React 组件提供了客户端路由转换

```jsx
import Link from "next/link";

function Home() {
  return (
    <ul>
      <li>
        <Link href="/">
          <a>Home</a>
        </Link>
      </li>
      <li>
        <Link href="/about">
          <a>About Us</a>
        </Link>
      </li>
    </ul>
  );
}

export default Home;
```

当链接到动态路由分段，你必须要提供 href 和 as 属性保证 router 知道那个 js 文件可以加载

- href - pages 目录下页面的名字。例如 `/blog/[slug]`
- as - 浏览器显示的 URL。例如 `/blog/hello-world`

```jsx
import Link from "next/link";

function Home() {
  return (
    <ul>
      <li>
        <Link href="/blog/[slug]" as="/blog/hello-world">
          <a>To Hello World Blog post</a>
        </Link>
      </li>
    </ul>
  );
}

export default Home;
```

这个 as 属性也可以是动态生成的。例如，显示章节列表，需要传递给页面作为属性

```jsx
function Home({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link href="/blog/[slug]" as={`/blog/${post.slug}`}>
            <a>{post.title}</a>
          </Link>
        </li>
      ))}
    </ul>
  );
}
```

#### 注入 router

为了在 React 组件中访问 router 对象，你可以使用 useRouter 或者 withRouter

通常我们建议使用 useRouter

#### 继续学习

router 被分为两个部分

- [next/link](https://nextjs.org/docs/api-reference/next/link)
  处理客户端导航
- [next/router](https://nextjs.org/docs/api-reference/next/router)
  在页面中利用 router 的 API

### 动态路由

通过预定义路径来定义路由对于复杂应用不够。在 Next.js 中，你可以添加中括号给页面([param])来创建动态路由（a.k.a url slug,纯 urls，或者其他）

考虑下面的页面`pages/post/[pid].js`：

```
import { useRouter } from 'next/router'

const Post = () => {
  const router = useRouter()
  const { pid } = router.query

  return <p>Post: {pid}</p>
}

export default Post
```

任何类似于`/post/1`, `/post/abc`,等。都将被`pages/post/[pid].js`匹配。匹配路径的参数将作为 query 的参数发送给页面，并且会和其他查询参数一起合并。

例如，路由`/post/abc`将有下面的 query 对象

```json
{ "pid": "abc" }
```

同样，路由`/post/abc?foo=bar`将有下面的 query 对象：

```json
{ "foo": "bar", "pid": "abc" }
```

然而，路由参数将会重写同名的查询参数。例如，路由`/post/abc?pid=123`将有下面的 query 对象：

```json
{ "pid": "abc" }
```

多个动态路由分段工作类似。页面`pages/post/[pid]/[comment].js`将匹配`/post/abc/a-comment`路由，将有下面的 query 对象：

```json
{ "pid": "abc", "comment": "a-comment" }
```

客户端导航到一个动态路由，可以通过`next/link`处理

#### 捕获所有路由

动态路由可以通过添加三个点(...)在[]中来扩展捕获所有路径。例如

- pages/post/[...slug].js 匹配 /post/a, 也包括 /post/a/b, /post/a/b/c 等等。

> 注意：你可以使用非 slug 名字，比如[...param]

匹配的参数将作为查询参数发送给页面(案例中是 slug)，并且它一直是一个数组，所以，路径`/post/a`将有下面的 query 对象：

```json
{ "slug": ["a"] }
```

`/post/a/b`和其他匹配的路径，参数是这样的数组：

```json
{ "slug": ["a", "b"] }
```

> 一个捕获所有路由的好案例是 Next.js docs，页面`pages/docs/[...slug].js`捕获所有你看到的 docs

#### 注意事项

- 预定义路由优先于动态路由，动态路由优选于捕获所有路由。案例：
  - `pages/post/create.js` - 将匹配 `/post/create`
  - `pages/post/[pid].js` - 将匹配 `/post/1`, `/post/abc`, etc. 不包括 `/post/create`
  - `pages/post/[...slug].js` - 将匹配 `/post/1/2`, `/post/a/b/c`, etc. 不包括 `/post/create`, `/post/abc`
- 通过自动静态优化的页面将水合，且不带路由参数。query 将是一个空对象

水合之后，Next.js 将触发应用更新，并在 query 对象中提供路由参数。

### 势在必行

`next/link`应该尽可能的覆盖你路由的需求，但是你可能不需要在客户端跳转时使用它，查看[Router API 文档](https://nextjs.org/docs/api-reference/next/router#router-api)

下面的案例显示了 Router API 的基础用法：

```jsx
import Router from "next/router";

function ReadMore() {
  return (
    <div>
      Click <span onClick={() => Router.push("/about")}>here</span> to read more
    </div>
  );
}

export default ReadMore;
```

### 浅层路由

浅层路由允许你改变 URL 而不用再次运行数据获取方法，这些方法包括 getServerSideProps, getStaticProps, and getInitialProps.

你将通过 router 对象来接收到更新后的 pathname 和 query 对象（[userRouter](https://nextjs.org/docs/api-reference/next/router#userouter)或者[withRouter](https://nextjs.org/docs/api-reference/next/router#withrouter)）,不丢失状态

为了启用浅层路由，设置 shallow 参数 true。下面案例：

```js
import { useEffect } from "react";
import { useRouter } from "next/router";

// Current URL is '/'
function Page() {
  const router = useRouter();

  useEffect(() => {
    // 总是在第一次渲染之后执行跳转
    router.push("/?counter=10", undefined, { shallow: true });
  }, []);

  useEffect(() => {
    // 计数器发生变化
  }, [router.query.counter]);
}

export default Page;
```

如果你不需要添加 router 对象到页面，那你也可以直接使用[Router API](https://nextjs.org/docs/api-reference/next/router#router-api), 例如：

```js
import Router from "next/router";
// Inside your page
Router.push("/?counter=10", undefined, { shallow: true });
```

URL 将更新为`/?counter=10`。并且页面不会替换，只是 route 的状态发生变化

你也可以通过 componentDidUpdate 来观察 URL 变化：

```
componentDidUpdate(prevProps) {
  const { pathname, query } = this.props.router
  // 验证属性，避免无限循环
  if (query.counter !== prevProps.router.query.counter) {
    // 基于新的查询对象获取数据
  }
}
```

#### 注意事项

浅层路由只在相同页面 URL 变更时有效。例如，让我们假设另外一个页面叫做`pages/about.js`，你可以运行

```js
Router.push("/?counter=10", "/about?counter=10", { shallow: true });
```

即使我们要求是浅层路由，因为它是个新页面，会卸载当前页，等数据加载完创建新的

## API 路由

Next.js 的 API routes 中提供了直接解决方案来构建你自己的 API
任何放到`pages/api`文件夹的文件会被映射成`/api/*`，会被认为是 API 还不是页面
比如，下面 API 路由`pages/api/user.js`处理一个简单的 json 响应：

```
export default (req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'application/json')
  res.end(JSON.stringify({ name: 'John Doe' }))
}
```

为了 API 路由正常工作，你需要导出一个 default 函数(请求处理函数),它会接收下面的参数

- `req`: 它是[http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)实例，加一些预先构建的中间件，你可以看[这里](https://nextjs.org/docs/api-routes/api-middlewares)
- `res`: 它是[http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)实例，添加一些帮助函数可以看[这里](https://nextjs.org/docs/api-routes/response-helpers)
  为了在一个 API 路由中处理不同的 http 方法，你可以使用`req.method`,比如

```
export default (req, res) => {
  if (req.method === 'POST') {
    // Process a POST request
  } else {
    // Handle any other HTTP method
  }
}
```

获取 API 节点，看下下面章节的案例

> API 路由不要求指定跨域头，意味着默认只支持同源请求。你可以用[跨域中间件](https://nextjs.org/docs/api-routes/api-middlewares#connectexpress-middleware-support)包装请求处理函数来自定义这个行为
> API 路由不会增加你客户端的文件大小，只会增加服务端的大小

### 动态 API 路由

API 路由支持动态路由，命名规则还是使用`pages`。例子，`pages/api/post/[pid].js`下面代码：

```
export default (req, res) => {
  const {
    query: { pid },
  } = req

  res.end(`Post: ${pid}`)
}
```

现在，请求`/api/post/abc`将会返回`Post: abc`

#### 捕获所有的路由

API 路由通过在括号中添加...捕获它下面所有路径，比如：

- `pages/api/post/[...slug].js`匹配`/api/post/a`,同样`/api/post/a/b`以及`/api/post/a/b/c`也会匹配

> 注意：你可以不用`slug`用其他的比如`[..param]`
> 匹配参数将会作为查询参数发送给页面（这个例子是`slug`）,它经常是一个数组，路径`/api/post/a`的`query`对象为：

```
{ "slug": ["a"] }
```

`/api/post/a/b`路径的参数这样

```
{ "slug": ["a", "b"] }
```

`pages/api/post/[...slug].js`的 API 处理可以这样：

```
export default (req, res) => {
  const {
    query: { slug },
  } = req

  res.end(`Post: ${slug.join(', ')}`)
}
```

现在，请求`/api/post/a/b/c`将返回`Post: a, b, c`

#### 注意事项

- 预先定义的 API 路由优先级高于动态路由，动态路由会捕获剩下的所有 API。看下下面的例子：
- `pages/api/post/create.js` - 将匹配 `/api/post/create`
- `pages/api/post/[pid].js` - 将匹配 `/api/post/1`, `/api/post/abc`, 等等. `/api/post/create`将不会被匹配
- `pages/api/post/[...slug].js` - 将匹配 `/api/post/1/2`, `/api/post/a/b/c`, 等等. `/api/post/create`, `/api/post/abc`将不会被匹配

### API 中间件

API 路由提供内置中间件解析进来的请求`req`。有这些中间件

- `req.cookies` - 对象包含请求发送过来的 cookie，默认是`{}`
- `req.query` - 对象包含[query string](https://en.wikipedia.org/wiki/Query_string)，默认是`{}`
- `req.body` - 对象包含根据`content-type`解析过的 body，如果没有`body`，它是 null

#### 自定义配置

每个 API 路由都可以到处一个`config`对象来修改默认配置，例如

```
export const config = {
  api: {
    bodyParser: {
      sizeLimit: '1mb',
    },
  },
}
```

这个`api`对象包含所有 API 路由的配置
`bodyParser`启用 body 解析，你如果想要自己消费`Stream`可以禁用掉
`bodyParser.sizeLimit`是最大允许解析的 Size，支持[bytes](https://github.com/visionmedia/bytes.js)，比如：

```
export const config = {
  api: {
    bodyParser: {
      sizeLimit: '500kb',
    },
  },
}
```

#### 支持 Connect/Express 中间件

你也可以使用[Connect](https://github.com/senchalabs/connect)兼容中间件。
比如，利用[cors 包]为 API[配置 CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)。
首先，安装`cors`:

```
npm i cors
# or
yarn add cors
```

现在，让我们添加`cors`到 API 路由

```
import Cors from 'cors'

// Initializing the cors middleware
const cors = Cors({
  methods: ['GET', 'HEAD'],
})

// Helper method to wait for a middleware to execute before continuing
// And to throw an error when an error happens in a middleware
function runMiddleware(req, res, fn) {
  return new Promise((resolve, reject) => {
    fn(req, res, result => {
      if (result instanceof Error) {
        return reject(result)
      }

      return resolve(result)
    })
  })
}

async function handler(req, res) {
  // Run the middleware
  await runMiddleware(req, res, cors)

  // Rest of the API logic
  res.json({ message: 'Hello Everyone!' })
}

export default handler
```

#### 响应帮助

响应`res`包含一些类似于 express.js 的函数，提高开发者的体验并加快创建一个 API 路由的效率。看下下面的案例。

```
export default (req, res) => {
  res.status(200).json({ name: 'Next.js' })
}
```

包含下面的帮助：

- `res.status(code)` - 设置状态码，`code`一定是一个有效的 HTTP 状态码
- `res.json(json)` - 发送 json 响应。`json`一定是一个有效的 JSON 对象
- `res.send(body)` - 发送 HTTP 响应。`body`可以是`string`、`object`或者是`Buffer`

## 部署

### Vercel (推荐)

部署 Next.js 到生产环境最简单的方式是使用 Next.js 创建提供的[Vercel 平台](https://vercel.com/)。Vercel 是一个多合一平台，支持全局静态 CDN，JAMstack 部署和无服务功能。

### 开始

如果你尚未这么做，你可以将 Next.js 应用推到你自己的 GitHub,GitLab,或者 BitBucket 这些 git provider 上。你的仓库可以是私有的或者公共的。

然后，跟随着下面的这些步骤

- 注册 Vercel (无需信用卡)
- 注册之后，你讲到达[导入项目](https://vercel.com/import)页面，在"From Git Repository"下，选择你使用的 Git provider 去配置和集成。(使用说明:[GitHub](https://vercel.com/docs/v2/git-integrations/zeit-now-for-github) / [GitLab](https://vercel.com/docs/v2/git-integrations/zeit-now-for-gitlab) / [BitBucket](https://vercel.com/docs/v2/git-integrations/zeit-now-for-bitbucket)).
- 一旦配置好，点击“Import Project From …”并导入你的 Next.js 应用。它会为你检测你的应用使用 Next.js 并设置构建配置。不需要改变任何东西 - 任何事情都会正常工作
- 导入完成之后，它将部署你的 Next.js 应用并提供给你部署 URL。点击"Visit"去在生产环境下看你的应用

恭喜！你已经成功部署 Next.js 应用！如果你有任何疑问，看一下[Vercel documentation](https://vercel.com/docs)

> 如果你使用自定义服务，我们强烈建议你从其迁移走（例如，使用[动态路由](https://nextjs.org/docs/routing/dynamic-routes)）。如果无法迁移，请考虑其他[托管选项](https://nextjs.org/docs/deployment#other-hosting-options)。

### DPS: Develop, Preview, Ship

让我们谈谈我们建议使用的工作流程。Vercel 支持 DPS 工作流：开发，预览，发货：

- 开发：在 Next.js 中写下代码。保证开发服务器运行并利用热代码重新加载功能。
- 预览：每次你将代码变更推到 GitHub / GitLab / BitBucket 的分支，Vercel 自动创建新的部署以及唯一的 URL。当你在 Github 打开一个 pull request 时，可以看到它。或者在 Vercel 你项目目录下的“预览部署”下面。[进一步学习](https://vercel.com/features/deployment-previews)
- 发货：当你准备发货，合并请求到你默认分支（master）。Vercel 将自动创建一个生产部署。

通过 DPS 工作流，除了代码审查之外，你可以进行部署预览。每次部署都会创建一个唯一 URL，可以被共享和用于集成测试。

### Next.js 优化

Vercel 由 Next.js 创建者创建，对 Next.js 一流支持。
例如，[混合页面](https://nextjs.org/docs/basic-features/pages)开箱即用的都支持

- 每个页面都可以使用静态生成和服务端渲染
- 使用静态生成和资源（JS，CSS，图片，字体等等）的页面将自动从[Vercel Smart CDN](https://vercel.com/smart-cdn)提供服务，速度非常快。
- 使用服务端渲染和 API 路由的页面都会自动成为无服务功能。允许页面渲染和 API 请求无限扩展。

### 自定义域名，环境变量，自动 HTTPS，等等

- **自定义域名：** 在 Vercel 部署过，你可以给你的 Next.js 应用分配一个自定义域名。看下[我们的文档](https://vercel.com/docs/v2/custom-domains)
- **环境变量：** 你可以在 Vercel 设置环境变量。看下[我们的文档]。然后你可以在 Next.js 应用中[使用这些环境变量](https://nextjs.org/docs/api-reference/next.config.js/environment-variables)
- **自动 HTTPS：** HTTPS 默认启动（包括自定义域名）并且不要求其他配置。我们自动刷新 SSL 证书
- **更多：** [读我们文档](https://vercel.com/docs)学习更多 Vercel 平台

### 其他托管选项

#### Node.js 服务

Next.js 可以被部署到任何支持 Node.js 的托管服务器上。如果你使用自定义服务应该采用这种方法。
保证你 package.json 有 build 和 start 脚本：

```js
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

next build 构建的生产应用丢到.next 文件夹下。构建之后，next start 启动 Node 服务支持[混合页面](https://nextjs.org/docs/basic-features/pages)，服务静态生成和服务端渲染页面。

#### 静态 HTML 导出

如果你喜欢为你的 Next.js 应用导出静态 HTML，跟随着[我们文档](https://nextjs.org/docs/advanced-features/static-html-export)的指示。默认，next export 将生成 out 文件夹，可以被放到任何静态托管服务或者 CDN。

> 我们强力建议使用 Vercel，即使如果你的 Next.js 应用是全静态的。Vercel 优化过的使得静态 Next.js 应用更快。next export 在 Vercel 部署 0 配置。

## 高级特性

### 预览模式

在页面文档和数据获取文档中，我们讨论了在构建时（静态生成）使用 getStaticProps 和 getStaticPaths 来预渲染页面。

静态生成在你从无头的 CMS 中获取数据非常有用。然而，当你在无头的 CMS 上写下草稿并且想要马上预览它无法实现。你想要 Next.js 在请求时预渲染这些页面而不是在构建时，获取的是草稿箱内容而不是发布内容。你想在这种情况下 Next.js 可以跳过静态生成。

Next.js 有预览功能可以解决这个问题。这里有如何使用的指令。

#### 步骤 1.创建和访问预览 API 路由

> 如果你不熟悉 Next.js API 路由，你需要看下[API 路由 文档](https://nextjs.org/docs/api-routes/introduction)

首先创建预览 API 路由。它可以有任何名字。比如`pages/api/preview.js`(或者如果使用 TypeScript 则是.ts)。

在 API 路由上，你需要在响应对象上调用 setPreviewData。setPreviewData 的参数应该是一个对象，并且它可以被 getStaticProps 使用。现在我们使用的是一个`{}`

```js
export default (req, res) => {
  // ...
  res.setPreviewData({});
  // ...
};
```

`res.setPreviewData`在浏览器上设置一些 cookie 来启动预览模式。Next.js 任何请求包含这个 cookie 都会可能变成预览模式，并且静态生成页面的行为将会发生变化（更详细的在后面）

你可以像下面这样手动创建 API 路由，并通过浏览器访问它：

```js
// 一个在浏览器上手动测试的简单案例
//如果是在 pages/api/preview.js上,
//然后在你的浏览器上打开 `/api/preview`
export default (req, res) => {
  res.setPreviewData({});
  res.end("Preview mode enabled");
};
```

如果你使用的是浏览器开发工具，你可能注意到每次请求，cookie 都设置`__prerender_bypass`和`__next_preview_data`

**安全访问无头 CMS**

实践中，你可能想要安全的调用这个 API 访问无头 CMS。具体步骤可能会因为使用的不同的 CMS 而不一样，但是有一些公共的步骤你需要关心。

这些步骤假设你用的无头 CMS 支持设置自定义预览 URLs。否则，你仍然可以使用这个方法来保护你的预览 URLs，但是你需要手动构造和访问这个预览 URL。

首先，你应该使用你选择的 token 生成器来生成一个私密的 token 字符串。这个 token 只有你 Next.js 应用和你的无头 CMS 知道。这个 token 阻止那些没有权限的人访问预览 URL。

然后，如果你的无头 CMS 支持设置自定义 URLs，指定下面的作为预览 URL。（它假设你的预览 API 路由是这样的`pages/api/preview.js`）

```
https://<your-site>/api/preview?secret=<token>&slug=<path>
```

- `<your-site>` 应该是你部署的域名
- `<token>` 应该被替换成你生成的私密 token
- `<path>` 应该是你想要预览的页面的路径。如果你想要预览`/posts/foo`, 你应该使用`&slug=/posts/foo`

你无头 CMS 应该允许你在预览 URL 中使用变量，所以`<path>`可以被动态根据 CMS 数据设置，比如：`&slug=/posts/{entry.fields.slug}`

最后，在这个预览 API 路由：

- 检查私密 token 匹配并且 slug 参数存在（如果没有，这个请求应该失败）
- 调用`res.setPreviewData`
- 然后重定向浏览器到 slug 指定的路径。（下面的案例使用 [307 重定向](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/307)）

```js
export default async (req, res) => {
  // 检查secret和next参数
  // 这个secret应该只有这个API路由和CMS知道
  if (req.query.secret !== "MY_SECRET_TOKEN" || !req.query.slug) {
    return res.status(401).json({ message: "Invalid token" });
  }

  // 如果提供了slug，获取无头 CMS 数据
  // getPostBySlug 应该事先获取无头 CMS 逻辑
  const post = await getPostBySlug(req.query.slug);

  // 如果slug不存在，阻止预览模式启用
  if (!post) {
    return res.status(401).json({ message: "Invalid slug" });
  }

  // 设置cookie启用预览模式
  res.setPreviewData({});

  // 重定向路径到获取到的post的slug
  // 我们不能重定向查询参数的slug,因为可能会造成潜在的漏洞
  res.writeHead(307, { Location: post.slug });
  res.end();
};
```

如果成功，浏览器都会重定向你想要预览的路径。

#### 步骤 2.更新 getStaticProps

下一个步骤就是更新 getStaticProps 来支持预览模式。

如果你请求的页面的 getStaticProps 支持预览模式设置(通过 `res.setPreviewData`)，然后 getStaticProps 将在请求时被调用（而不是在构建时）

进一步，它会带着 context 对象调用：

- context.preview 将是 true
- context.previewData 将与 setPreviewData 的参数相同

```js
export async function getStaticProps(context) {
  // 如果设置了预览模式请求页面
  //
  // - context.preview 是 true
  // - context.previewData 将与 setPreviewData 的参数相同
}
```

我们在预览 API 路由中使用`res.setPreviewData({})`，所以 context.previewData 将被设置成 `{}`。你如果需要你可以在预览 API 路由中像 getStaticProps 传递会话信息。

如果你也使用了 getStaticPaths，context.params 也会生效。

**获取预览数据**

你可以更新 getStaticProps 根据 context.preview 和 context.previewData 获取不同的数据。

举例，你无头 CMS 可能访问草稿文章用不同的 API。如果这样，你可以使用 context.preview 来修改 API,比如下面这样。

```js
export async function getStaticProps(context) {
  // 如果 context.preview is true, 则向API路径添加 "/preview"
  // 去请求草稿内容而不是发布内容. 这非常依赖于你使用的CMS.
  const res = await fetch(`https://.../${context.preview ? "preview" : ""}`);
  // ...
}
```

到此！如果你带着 secret 和 slug 访问你的预览 API 路由，你应该看到预览内容。并且如果你更新草稿内容，你应该可以预览这个草稿。

```
# Set this as the preview URL on your headless CMS or access manually,
# and you should be able to see the preview.
https://<your-site>/api/preview?secret=<token>&slug=<path>
```

#### 更多案例

...略

#### 更多详情

**清除预览模式 cookies**

默认情况下，没有给预览模式 cookie 设置有效期，所以当浏览器关闭预览模式结束。

为手动清除预览模式 cookies,你可以创建 API 路由来调用 clearPreviewData，然后访问这个 API 路由。

```js
export default (req, res) => {
  // Clears the preview mode cookies.
  // This function accepts no arguments.
  res.clearPreviewData();
  // ...
};
```

**指定预览模式有效期**

setPreviewData 的第二个可选参数，它是一个 options 对象。它接受下面的 keys：

- maxAge：指定预览回话要持续的时间（但为是秒）

```js
setPreviewData(data, {
  maxAge: 60 * 60, // The preview mode cookies expire in 1 hour
});
```

**previewData 大小限制**

你可以传递对象给 setPreviewData，它在 getStaticProps 中有效。然而，因为数据是存在 cookie 中，它们有大小限制。当前预览数据限制为 2KB。

**getServerSideProps 一起工作**

预览模式统一可以在 getServerSideProps 工作很好。它的 context 对象也包含 preview 和 previewData

**每次`next build`唯一**

当 next build 运行，旁路的 cookie 和加密的 previewData 的私钥会更改，它会保证旁路的 cookie 不会被猜到。

### 动态导入

Next.js 支持 JavaScript 的 ES2020 动态导入。用它你可以动态导入 JavaScript 模块（包括 React 组件）并使用它们。它们也可以在 SSR 中使用。

你可以考虑用到动态导入作为另外一种分割代码成可管理的 chunk。

#### 基础使用

下面的示例，模块 `../components/hello`将被页面动态导入：

```jsx
import dynamic from "next/dynamic";

const DynamicComponent = dynamic(() => import("../components/hello"));

function Home() {
  return (
    <div>
      <Header />
      <DynamicComponent />
      <p>HOME PAGE is here!</p>
    </div>
  );
}

export default Home;
```

DynamicComponent 将作为 default 组件被`../components/hello`返回。它就像常规的 React 组件一样工作，你可以正常传递属性给它。

#### 命名导出

如果动态组件不是默认导出，你也可以使用命名导出。考虑模块`../components/hello.js`它有命名导出 Hello：

```jsx
export function Hello() {
  return <p>Hello!</p>;
}
```

为了动态导入 Hello 组件，你可以从`import()`返回的 Promise 中返回它，比如：

```jsx
import dynamic from "next/dynamic";

const DynamicComponent = dynamic(() =>
  import("../components/hello").then((mod) => mod.Hello)
);

function Home() {
  return (
    <div>
      <Header />
      <DynamicComponent />
      <p>HOME PAGE is here!</p>
    </div>
  );
}

export default Home;
```

#### 自定义加载组件

loading 组件是可选的，它渲染表示当动态组件正在加载中的 loading 状态。例如：

```jsx
import dynamic from "next/dynamic";

const DynamicComponentWithCustomLoading = dynamic(
  () => import("../components/hello"),
  { loading: () => <p>...</p> }
);

function Home() {
  return (
    <div>
      <Header />
      <DynamicComponentWithCustomLoading />
      <p>HOME PAGE is here!</p>
    </div>
  );
}

export default Home;
```

#### 服务端渲染不用

你可能不想在服务端一直包含这个模块，例如，当模块包含的库只在浏览器端生效。

看下下面的示例：

```jsx
import dynamic from "next/dynamic";

const DynamicComponentWithNoSSR = dynamic(
  () => import("../components/hello3"),
  { ssr: false }
);

function Home() {
  return (
    <div>
      <Header />
      <DynamicComponentWithNoSSR />
      <p>HOME PAGE is here!</p>
    </div>
  );
}

export default Home;
```

### 自动静态优化

如果没有要求阻塞的数据，Next.js 自动决定页面是否是静态的（可以被预渲染）。这个的判断是依据页面中是否存在`getServerSideProps`和`getInitialProps`。
这个特性允许 Next.js 打包出包含服务端渲染和静态生成页面的混合应用

> 静态生成的页面仍然是具有响应式的：Next.js 将混合你客户端的应用使其具有完整的交互性。

这个特性最大的好处是页面无服务端计算，可以立刻从多个 CDN 的位置流式传递给终端用户。给你的用户带来极致的加载体验。

#### 怎么做

如果`getInitialProps`存在，Next.js 将使用它默认的行为并且在后台渲染，提前请求（在服务端渲染的时候）
如果`getInitialProps`没有，Next.js 将自动静态化（预渲染页面成静态 HTML）。在预先渲染的时候，因为在这个阶段我们没有`query`信息，所以`query`对象是空的。混合之后，任何`query`值将填充到客户端。
`next build`将为静态优化页面构建出.html 文件。举例，`pages/about.js`页面将会生成这的结果

```

.next/server/static/\${BUILD_ID}/about.html

```

如果你添加`getInitialProps`到页面，它的产物结果将是

```

.next/server/static/\${BUILD_ID}/about.js

```

在开发环境下，如果`pages/about.js`被优化，会包含静态优化指示符。

#### 注意事项

- 如果你在`getInitialProps`中有自定义`App`,这个优化将对所有页面关闭
- 如果你在`getInitialProps`中有自定义`Document`，确保在服务端渲染之前就已经检查`ctx.req`已经被定义了。`ctx.req`在预渲染的时候是未定义的。

### 静态页面导出

next export 允许你讲你的应用导出成静态 HTML，它可以独立允许，无需 Node.js 服务。

这个导出的应用支持几乎 Next.js 的所有特性，包括动态路由，预获取，预渲染和动态导入。

next export 的工作方式是将所有页面预渲染成 HTML；它基于 exportPathMap 的映射，exportPathMap 提供需要渲染成 HTML 的预定义路径。

> 如果你的所有页面都没有 getInitialProps 你可能根本不需要 next export；next build 借助自动静态化已经足够。

#### 如何使用

使用 Next.js 开发应用，然后运行：

```
next build && next export
```

至此，你可能想要更新 package.json 的脚本：

```js
"scripts": {
  "build": "next build && next export"
}
```

然后只需要运行一次：

```
npm run build
```

然后你要的应用静态版本在 out 目录下

默认 next export 不需要任何配置。它会生成一个默认的 exportPathMap, exportPathMap 包含 pages 目录下的所有页面路由。

> 为了学习更多关于 exportPathMap ，关注[exportPathMap API 文档](https://nextjs.org/docs/api-reference/next.config.js/exportPathMap)

#### 注意事项

- 使用 next export,我们可以构建应用的 HTML 版本。在导出阶段我们将运行页面的 getInitialProps。context 对象的 req 和 res 字段在导出阶段将是空对象，因为没有服务器在运行。
- 在静态导出的时候你无法动态渲染 HTML，因为我们预先构建了 HTML 文件。当你不使用 next export 时，你的应用可以是静态生成和服务端渲染的混合体。你可以学习一下[pages 一节](https://nextjs.org/docs/basic-features/pages)
- API 路由不支持这个方法，因为它们不渲染 HTML。

#### 介绍

使用 Next.js 你可以将任何 React 页面变成 AMP 页面，只需最少的配置，而不需要离开 React。

你可以去[amp.dev 官网](https://amp.dev/)了解更多关于 AMP 的知识

##### 启用 AMP

为了启用对页面 AMP 的支持，要了解更多关于 AMP 的配置，阅读[next/amp 的 API 文档](https://nextjs.org/docs/api-reference/next/amp)

##### 注意

- 只支持 CSS-in-JS，AMP 页面目前不支持 CSS 模块。你可以[贡献 CSS 模块给 Next.js](https://github.com/zeit/next.js/issues/10549)

##### 相关

- [AMP 组件](https://nextjs.org/docs/advanced-features/amp-support/adding-amp-components)
  使用 AMP 组件让你的页面更具交互性
- [AMP 验证](https://nextjs.org/docs/advanced-features/amp-support/amp-validation)
  学习更多关于 Next.js 如何验证 AMP 页面

#### 添加 AMP 组件

AMP 社区提供了[许多组件](https://amp.dev/documentation/components/)使得 AMP 页面更交互性。Next.js 将自动导入页面上使用的 AMP 组件，并且不需要收到导入代码。

```jsx
export const config = { amp: true };

function MyAmpPage() {
  const date = new Date();

  return (
    <div>
      <p>Some time: {date.toJSON()}</p>
      <amp-timeago
        width="0"
        height="15"
        datetime={date.toJSON()}
        layout="responsive"
      >
        .
      </amp-timeago>
    </div>
  );
}

export default MyAmpPage;
```

上面的案例使用了 amp-timeago 组件

默认，总是导入组件最近的版本。如果你想要自定义版本，你可以使用 next/head，如下案例：

```jsx
import Head from "next/head";

export const config = { amp: true };

function MyAmpPage() {
  const date = new Date();

  return (
    <div>
      <Head>
        <script
          async
          key="amp-timeago"
          custom-element="amp-timeago"
          src="https://cdn.ampproject.org/v0/amp-timeago-0.1.js"
        />
      </Head>

      <p>Some time: {date.toJSON()}</p>
      <amp-timeago
        width="0"
        height="15"
        datetime={date.toJSON()}
        layout="responsive"
      >
        .
      </amp-timeago>
    </div>
  );
}

export default MyAmpPage;
```

#### AMP 验证

AMP 页面在开发期间使用[amphtml-validator](https://www.npmjs.com/package/amphtml-validator)自动验证。当你启动 Next.js 错误和警告都会出现在终端。

在静态 HTML 生成期间页面也会被验证，并且错误和警告也被打印在终端。任何 AMP 错误将导致 1 状态码退出，因为导出的不是一个有效的 AMP。

#### 静态导出 HTML 中的 AMP

当使用 next export 导出静态 HTML 预渲染页面，Next.js 检测如果页面支持 AMP，将根据下面改变导出行为。

例如，混合 AMP 页面 `pages/about.js`将导出：

- `out/about.html` -带 React 客户端运行时渲染的 HTML 页面
- `out/about.amp.html` -AMP 页面

如果`pages/about.js`只是 AMP 页面，将输出：

- `out/about.html` - 优化过的 AMP 页面
  Next.js 将自动插入 HTML 版本到你 AMP 版本页面，所以你不必这么做，比如：

```html
<link rel="amphtml" href="/about.amp.html" />
```

- 你页面的 AMP 版本将自动包括 HTML 页面的链接

```html
<link rel="canonical" href="/about" />
```

当为`pages/about.js`启动[exportTrailingSlash](https://nextjs.org/docs/api-reference/next.config.js/exportPathMap#0cf7d6666b394c5d8d08a16a933e86ea)导出页面：

- `out/about/index.html` - HTML page
- `out/about.amp/index.html` - AMP page

#### TypeScript

AMP 当前不支持内置的 TypeScript 类型，但是这里有它们的路线图[#13791](https://github.com/ampproject/amphtml/issues/13791)

解决方案你可能需要手动创建`amp.d.ts`放到你项目中，并添加自定义类型[描述](https://stackoverflow.com/a/50601125)

### 自定义 Babel 配置

Next.js 为你的应用包含 next/babel 预设，它包含编译 React 应用和服务端代码需要的一切。但是如果你想要扩展默认的 Babel 配置，是可能的。

开始，你只需要在你应用的顶层创建一个 .babelrc 文件，如果这个文件找到，我们将认为这是真理的源头，需要它定义 Next.js 的需求，是 next/babel 的预设。

这里是 .babelrc 文件的案例：

```js
{
  "presets": ["next/babel"],
  "plugins": []
}
```

这个 next/babel 预设包括：

- preset-env
- preset-react
- preset-typescript
- plugin-proposal-class-properties
- plugin-proposal-object-reset-spread
- plugin-transform-runtime
- styled-jsx

为了配置这些预设插件，请不要添加它们到 presets 或者 plugins。而应该配置它们到 next/babel 预设中。

```js
{
  "presets": [
    [
      "next/babel",
      {
        "preset-env": {},
        "transform-runtime": {},
        "styled-jsx": {},
        "class-properties": {}
      }
    ]
  ],
  "plugins": []
}
```

为了学习每个配置更多有效的参数配置，访问这些文档站点。

> Next.js 使用当前的 Node.js 版本进行服务端编译。

> preset-env 的 modules 参数配置应该一直是 false, 否则 将关闭 webpack 的代码拆分

### 自定义 PostCSS 配置

Next.js 使用 PostCSS 为内置的 CSS 支持编译 CSS。

开箱即用，不需要任何配置，Next.js 使用下面的转换编译 CSS：

1. [Autoprefixer](https://github.com/postcss/autoprefixer)自动添加供应商前缀给 CSS 规则（退回到 IE11）
2. [扩浏览器的 Flexbox bugs](https://github.com/philipwalton/flexbugs)被修复使得它的行为和规范一致
3. 新 CSS 特性将自动编译支持 IE11 的兼容

- [all Property](所有属性)
- [Break Properties](断裂属性)
- [font-variant Property](字体变化属性)
- [Gap Properties](间隙属性)
- [Media Query Ranges](媒体查询范围)

默认，[自定义属性](CSS 变量)是不兼容 IE11

CSS 变量未编译，因为它还[无法被安全的编译](https://github.com/MadLittleMods/postcss-css-variables#caveats)。如果你一定要使用变量，考虑使用像 Sass 这样的变量，它被 Sass 给编译掉了。

> 注意：为了支持[Grid Layout](https://developer.mozilla.org/en-US/docs/Web/CSS/grid), 你需要启用`grid: "autoplace"`自动前缀，看下面的自定义插件

#### 自定义目标浏览器列表

Next.js 通过[Browserslist](https://github.com/browserslist/browserslist)允许你配置目标浏览器列表(补充前缀和编译 css)

为了自定义 browserslist，在 package.json 中创建 browserslist 的 key：

```json
{
  "browserslist": [">0.3%", "not ie 11", "not dead", "not op_mini all"]
}
```

你可以使用[browserl.ist](https://browserl.ist/?q=%3E0.3%25%2C+not+ie+11%2C+not+dead%2C+not+op_mini+all)工具可视化你想要的浏览器

#### CSS 模块

支持 CSS 模块不需要任何配置。为文件启用 CSS 模块，请重命名文件为 .module.css 的扩展。

你可以学习关于[Next.js CSS 模块的支持]https://nextjs.org/docs/basic-features/built-in-css-support()

#### 自定义插件

> 警告：当你定义一个自定义 PostCSS 配置文件，Next.js 会完全禁止它的默认行为。确保你编译的所有功能都需要你手动配置，包括[Autoprefixer](https://github.com/postcss/autoprefixer)。你需要手动安装你自定义配置中的所有插件，比如:`npm install postcss-flexbugs-fixes`

为了自定义 PostCSS 配置，在项目的根目录创建 postcss.config.json

Next.js 使用的默认配置：

```js
{
  "plugins": [
    "postcss-flexbugs-fixes",
    [
      "postcss-preset-env",
      {
        "autoprefixer": {
          "flexbox": "no-2009"
        },
        "stage": 3,
        "features": {
          "custom-properties": false
        }
      }
    ]
  ]
}
```

> 注意：Next.js 也允许文件重命名成 .postcssrc.json，或者从 package.json 的 postcss key 中读取。

也能通过 postcss.config.js 文件配置 PostCSS，当你想要根据环境来区别使用配置的时候很有用。

```js
module.exports = {
  plugins:
    process.env.NODE_ENV === "production"
      ? [
          "postcss-flexbugs-fixes",
          [
            "postcss-preset-env",
            {
              autoprefixer: {
                flexbox: "no-2009",
              },
              stage: 3,
              features: {
                "custom-properties": false,
              },
            },
          ],
        ]
      : [
          // No transformations in development
        ],
};
```

> 注意： Next.js 也允许文件名重命名为 .postcssrc.js

不要使用 `require()`来导入 PostCSS 插件，插件一定用字符串来提供。

> 如果你的 postcss.config.js 需要在这个项目下支持其他非 Next.js 工具，你一定要使用客户操作的基于对象的格式

### 自定义 Server

通常，你使用哪个 next start 来启动 next 服务。为了使用自定义路由模式，也可以百分百编程的方式启动服务。

> 在决定使用自定义服务之前请思考下只有当 Next.js 的路由无法满足你 APP 要求时才能使用。自定义服务将会删除重要的性能优化，比如无服务器功能以及[自动静态优化](https://nextjs.org/docs/advanced-features/automatic-static-optimization)

看下下面的自定义服务案例：

```js
// server.js
const { createServer } = require("http");
const { parse } = require("url");
const next = require("next");

const dev = process.env.NODE_ENV !== "production";
const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  createServer((req, res) => {
    // Be sure to pass `true` as the second argument to `url.parse`.
    // This tells it to parse the query portion of the URL.
    const parsedUrl = parse(req.url, true);
    const { pathname, query } = parsedUrl;

    if (pathname === "/a") {
      app.render(req, res, "/b", query);
    } else if (pathname === "/b") {
      app.render(req, res, "/a", query);
    } else {
      handle(req, res, parsedUrl);
    }
  }).listen(3000, (err) => {
    if (err) throw err;
    console.log("> Ready on http://localhost:3000");
  });
});
```

> server.js 不会通过 babel 或者 webpack。确保文件的语法和源与你当前运行的 node 版本兼容。

然后，为了运行自定义 server，你需要更新 scripts 和 package.json。比如

```json
"scripts": {
  "dev": "node server.js",
  "build": "next build",
  "start": "NODE_ENV=production node server.js"
}
```

自定义服务使用以下导入将 Next.js 应用和 server 连接起来。
上面的 next 导入函数接收的是下面的参数对象：

- dev: Boolean - 是否在开发模式下启动 Next.js。 默认是 false
- dir: String - Next.js 项目的路径，默认是.
- quiet: Boolean - 隐藏包含服务信息的错误消息。默认是 false
- conf: object - 这个对象和在 Next.config.js 使用的一样的对象，默认是{}

返回的 app 可以让 Next.js 根据需求处理请求了

#### 关闭文件路由

默认，Next 用路径名匹配文件名来为在 pages 文件夹下的每个文件提供服务。如果你项目使用了自定义 server，这个行为可能会导致多个路径都可以为相同的内容提供服务，可能为 SEO 和用户体验造成问题。

```js
module.exports = {
  useFileSystemPublicRoutes: false,
};
```

> 注意 useFileSystemPublicRoutes 是简单的让服务端渲染禁用了文件路由；客户端路由可能仍可以访问路由。当使用这个选项时，你应该避免使用手动编程的方式连接到页面

> 你可能还需要配置客户端路由，防止客户端跳转到这个文件路由。参考[Router.beforePopState](https://nextjs.org/docs/api-reference/next/router#routerbeforepopstate)

### 自定义`App`

Next.js 使用 App 组件来初始化所有页面。你可以重写它并控制页面初始化。那些事情允许你做的：

- 在不同页面之前持久化相同布局
- 当导航页面时保持状态
- 使用 componentDidCatch 来自定义错误处理
- 向页面注入额外的数据
- [添加全局 CSS](https://nextjs.org/docs/basic-features/built-in-css-support#adding-a-global-stylesheet)

为了重写默认的 App，创建如下的 `./pages/_app.js` 文件：

```jsx
// import App from 'next/app'

function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />;
}

// 只有在你每个页面都有阻塞数据请求的需求是才取消这些代码注释
// 它会阻止自动静态优化，造成你应用的每个页面都被服务端渲染
//
// MyApp.getInitialProps = async (appContext) => {
//   // calls page's `getInitialProps` and fills `appProps.pageProps`
//   const appProps = await App.getInitialProps(appContext);
//
//   return { ...appProps }
// }

export default MyApp;
```

这个 Component 原型是当前激活的页面，所以你在路由之间跳转，Component 都会变成这个新页面。然而，你发送给 Component 的任何属性都会被这个页面接收。

pageProps 是页面预加载初始属性的对象。如果你页面没有使用 getInitialProps 则是一个空对象。

> 在你的 App 中添加 getInitialProps 将禁止自动静态优化

#### TypeScript

如果你使用 TypeScript，看下[TypeScript 文档](https://nextjs.org/docs/basic-features/typescript#custom-app)

### 自定义 `Document`

自定义 Document 通常被用来增加你应用的 html 和 body 标签。这非常有必要，因为 Next.js 页面需要跳过包括 document 的标签定义。

一个自定义 Document 可以包含 getInitialProps 来表达同步的服务端数据渲染要求。

为了重写默认的 Document，创建`./pages/_document.js`文件下面那样继承 Document 类：

```jsx
import Document, { Html, Head, Main, NextScript } from "next/document";

class MyDocument extends Document {
  static async getInitialProps(ctx) {
    const initialProps = await Document.getInitialProps(ctx);
    return { ...initialProps };
  }

  render() {
    return (
      <Html>
        <Head />
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}

export default MyDocument;
```

<Html>, <Head />, <Main /> and <NextScript /> 页面正确渲染必须的。

自定义的 attributes 允许作为 props，比如 lang

```html
<html lang="en"></html>
```

这个 ctx 对象等价于 getInitialProps 接收的，但是多了一项：

- renderPage: Function - 运行实际 React 渲染逻辑（同步）的回调。包装这个函数用来支持服务端渲染包装器比如 Aphrodite 的[renderStatic](https://github.com/Khan/aphrodite#server-side-rendering)非常有用。

#### 注意事项

- Document 只在服务端渲染，事件处理器不会工作(onClick)
- `<Main />`外部的 React 组件不会被浏览器初始化。不要在这里添加应用逻辑。如果你需要在页面之间共享组件（比如菜单栏目和工具栏目），尝试使用 App 组件来替代
- Document 的 getInitialProps 函数不会再客户端转化期间调用，也不会在静态优化页面时执行。
- 确认是否在 getInitialProps 中定义了 ctx.req 和 ctx.res。当页面被自动静态化或者 next export 导出时，这些变量都是未定义的。
- 常见的错误包括添加 title 和 Head 标签或者使用 styled-jsx。因为在 `pages/_document.js`中避免它们，否则会造成意外行为。

#### 自定义 renderPage

> 需要注意的是你使用自定义 renderPage 的唯一原因应该是使用 css-in-js 库时，这些库包裹应用程序以正确的显示服务端渲染。

它采用一个 options 对象作为进一步自定义的参数：

```jsx
import Document from "next/document";

class MyDocument extends Document {
  static async getInitialProps(ctx) {
    const originalRenderPage = ctx.renderPage;

    ctx.renderPage = () =>
      originalRenderPage({
        // 包裹整个react树有用
        enhanceApp: (App) => App,
        // 对每一页的基础包裹有用
        enhanceComponent: (Component) => Component,
      });

    // 运行父类的getInitialProps，它现在包括自定义的renderPage
    const initialProps = await Document.getInitialProps(ctx);

    return initialProps;
  }
}

export default MyDocument;
```

### 自定义错误页面

#### 404 页面

404 页面可能访问非常频繁。每次访问都服务端渲染会增加 Next.js 服务的负载。这样会导致成本增加和很低的用户体验。

为了避免上诉的陷阱，Next.js 添加了静态的 404 页面，默认不需要任何额外的文件。

##### 自定义 404 页面

为了自定义 404 页面，你可以创建一个`pages/404.js`文件。这个文件在构建时静态生成。

```js
// pages/404.js
export default function Custom404() {
  return <h1>404 - Page Not Found</h1>;
}
```

#### 500 页面

默认 Next.js 提供 500 错误页，样式和默认的 404 页面一样。这个页面没有自动静态化，允许报告服务端错误。这是为什么 404 和 500（其他错误）被分隔的原因。

##### 自定义错误页面

500 错误被客户端和服务端的 Error 组件处理掉。如果你希望重写它，定义错误文件`pages/_error.js`并添加以下代码：

```jsx
function Error({ statusCode }) {
  return (
    <p>
      {statusCode
        ? `An error ${statusCode} occurred on server`
        : "An error occurred on client"}
    </p>
  );
}

Error.getInitialProps = ({ res, err }) => {
  const statusCode = res ? res.statusCode : err ? err.statusCode : 404;
  return { statusCode };
};

export default Error;
```

> `pages/_error.js`只在生产环境中被使用。在开发环境下你会获得完整的错误堆栈来知道原始错误出现在哪里。

##### 复用内置的错误页面

如果要渲染内置的错误页，你可以导入 Error 组件：

```jsx
import Error from "next/error";
import fetch from "node-fetch";

export async function getServerSideProps() {
  const res = await fetch("https://api.github.com/repos/zeit/next.js");
  const errorCode = res.ok ? false : res.statusCode;
  const json = await res.json();

  return {
    props: { errorCode, stars: json.stargazers_count },
  };
}

export default function Page({ errorCode, stars }) {
  if (errorCode) {
    return <Error statusCode={errorCode} />;
  }

  return <div>Next stars: {stars}</div>;
}
```

这个错误组件同样也接收 title 作为属性，如果你想传递除了 statusCode 的文本消息。

### `src`目录

也可以添加页面到`src/pages`下来替代根目录的 pages 目录。

src 目录在许多应用中都非常常用，Next.js 默认也支持

#### 注意事项

- `src/pages`如果 pages 在根目录下存在，它将被忽略
- 像 next.config.js 和 tsconfig.json 文件都应该放到根目录下，迁移它们到 src 目录不会工作。public 目录也是如此。

### 多空间(zone)

一个空间是一个 Next.js 应用的部署。你应该有多个空间并合并它们成单个应用。

例如，假如你有以下应用程序：

- 用于`/blog/**`的应用
- 另一个用于提供所有其他页面的应用

使用多空间的支持，你可以合并两个应用成单个，允许你的用户在同一个 URL 上浏览它们，但是你可以独立开发和部署它们。

#### 如何定义一个空间(zone)

没有特殊的 zone api，你只需要遵守下面的条件：

- 确保仅将你应用需要的页面保留，意味着你不需要其他应用的页面，例如 应用 A 有`/blog`，应用 B 不会有
- 确保添加 assetPrefix 来避免静态资源的冲突

#### 如何合并多个 zone

你可以使用 HTTP 代理合并多个 zones

对于 Vercel，你可以使用一个 now.json 来部署多个应用。它允许你定义多个应用的路由：

```js
{
  "version": 2,
  "builds": [
    { "src": "blog/package.json", "use": "@now/next" },
    { "src": "home/package.json", "use": "@now/next" }
  ],
  "routes": [
    { "src": "/blog/_next(.*)", "dest": "blog/_next$1" },
    { "src": "/blog(.*)", "dest": "blog/blog$1" },
    { "src": "(.*)", "dest": "home$1" }
  ]
}
```

你可以使用上面的路由集合来配置代理服务的路由，比如 部署博客应用到 `https://blog.example.com`，然后首页应用部署到`https://home.example.com`。最后将两个应用添加到代理服务`https://example.com`

## 升级指南

### 升级从 8 到 9

#### 前言

**在 Vercel 生产部署**

如果你之前为了动态路由在你的 now.json 文件中配置了 routes，当升级 Next.js9 的新动态路由功能时，这些规则可以被删除了。

Next.js 9 的动态路由是自动在 Now 上配置的，并不要求任何 now.json 的自定义

你可以阅读[动态路由更多信息](https://nextjs.org/docs/routing/dynamic-routes)

**检查自定义的(`pages/_app.js`)**

如果你之前拷贝了自定义`<app>`的案例，你可以要删除你的 getInitialProps。

若有可能从`pages/_app.js`删除 getInitialProps，这是迁移 Next.js 新功能的关键。

下面的 getInitialProps 不做任何事情，可以删除掉

```js
class MyApp extends App {
  // Remove me, I do nothing!
  static async getInitialProps({ Component, ctx }) {
    let pageProps = {};

    if (Component.getInitialProps) {
      pageProps = await Component.getInitialProps(ctx);
    }

    return { pageProps };
  }

  render() {
    // ... etc
  }
}
```

#### 重大变化

**`@zeit/next-typescript`不再需要**

Next.js 将忽略用法`@zeit/next-typescript`并警告你删除它。请从 next.config.js 中删除这个插件。

如果存在，从你自定义的.babelrc 中删除`@zeit/next-typescript/babel`的引用。

你的 next.config.js 的`fork-ts-checker-webpack-plugin`应该被删除

next 包发布了 TypeScript 定义，所以你需要卸载`@types/next`，否则会冲突。

它们的类型也有些不同：

> 社区创建的列表帮助你升级，如果你其他不同的，请给这个 list 提 pull 请求，帮助其他人。

From：

```js
import { NextContext } from "next";
import { NextAppContext, DefaultAppIProps } from "next/app";
import { NextDocumentContext, DefaultDocumentIProps } from "next/document";
```

to

```js
import { NextPageContext } from "next";
import { AppContext, AppInitialProps } from "next/app";
import { DocumentContext, DocumentInitialProps } from "next/document";
```

**现在 config key 是页面的特殊导出**

你可能不再从页面导出自定义的 config 变量(比如: `export { config }`/ `export const config ...`)。现在这个导出的变量被用哪个来指定页面级的 Next.js 配置，比如启用 AMP 和 API 路由功能。

你必须将非 Next.js 用途的 config 的导出重命名为其他

**`next/dynamic`不再加载中渲染 loading...**

动态组件加载时默认不在渲染任何东西。你仍可以设置 loading 属性来自定义这个行为。

```jsx
import dynamic from "next/dynamic";

const DynamicComponentWithCustomLoading = dynamic(
  () => import("../components/hello2"),
  {
    loading: () => <p>Loading</p>,
  }
);
```

**withAmp 已经被删除，为了导出配置对象**

Next.js 现在有页面级配置的概念，所以 withAmp 高级函数组件为了一致性可以删除。

这个改变可以通过在 Next.js 项目的根目录下运行这些命令自动完成迁移：

```
curl -L https://github.com/zeit/next-codemod/archive/master.tar.gz | tar -xz --strip=2 next-codemod-master/transforms/withamp-to-config.js npx jscodeshift -t ./withamp-to-config.js pages/**/*.js
```

手动执行这个迁移，或者想看下程序是如何做的，看下面：
**之前**

```js
import { withAmp } from "next/amp";

function Home() {
  return <h1>My AMP Page</h1>;
}

export default withAmp(Home);
// or
export default withAmp(Home, { hybrid: true });
```

**之后**

```js
export default function Home() {
  return <h1>My AMP Page</h1>;
}

export const config = {
  amp: true,
  // or
  amp: "hybrid",
};
```

**next export 不再导出页面成 index.html**

之前导出`pages/about.js`的结果是`out/about/index.html`。这个行为已经改成结果为`out/about.html`。

你可以通过创建带下面内容的 next.config.js 文件来还原之前的行为：

```js
// next.config.js
module.exports = {
  exportTrailingSlash: true,
};
```

**`./pages/api/` 处理方式有些不一样**

页面中的`./pages/api/`被认为是 API 路由。这个目录的页面将不再被打包到客户端

#### 过时功能

**`next/dynamic` 已经废弃一次加载多个模块**
`next/dynamic`一次加载多个模块的能力已经被废弃，为了更贴近 React 的实现(`React.lazy`和`Suspense`)。

依赖这个行为的代码更新相对简单！我们提供了 before/after 的案例来帮助你迁移应用。

**Before**

```jsx
import dynamic from "next/dynamic";

const HelloBundle = dynamic({
  modules: () => {
    const components = {
      Hello1: () => import("../components/hello1").then((m) => m.default),
      Hello2: () => import("../components/hello2").then((m) => m.default),
    };

    return components;
  },
  render: (props, { Hello1, Hello2 }) => (
    <div>
      <h1>{props.title}</h1>
      <Hello1 />
      <Hello2 />
    </div>
  ),
});

function DynamicBundle() {
  return <HelloBundle title="Dynamic Bundle" />;
}

export default DynamicBundle;
```

**after**

```jsx
import dynamic from "next/dynamic";

const Hello1 = dynamic(() => import("../components/hello1"));
const Hello2 = dynamic(() => import("../components/hello2"));

function HelloBundle({ title }) {
  return (
    <div>
      <h1>{title}</h1>
      <Hello1 />
      <Hello2 />
    </div>
  );
}

function DynamicBundle() {
  return <HelloBundle title="Dynamic Bundle" />;
}

export default DynamicBundle;
```

## 常见问题

### 频繁问的问题

- 那些浏览器支持
  Next.js 使用`@babel/preset-env`开箱即用的支持 IE11 和其他现代浏览器。为了支持 IE11 添加全局的 Promise polyfill。

  如果你自己的代码或者你使用的外部 npm 依赖用来你目标浏览器不支持的特性，你需要实现 polyfills。如果你需要实现 polyfills，这个[polyfills](https://github.com/zeit/next.js/tree/canary/examples/with-polyfills)案例演示了最佳实现方式

- 这个产品准备好了吗？
  自 Next.js 成立依赖，一直为[https://vercel.com ](https://vercel.com)提供支持。

  我们对开发者的体验和用户端性能都非常满意，所以我们决定分享给社区。

- 有多大
  客户端包带下应该每个应用衡量。最小的 Next 珠宝大约压缩后 65kb.

- 我如何改变内部 webpack 配置
  Next.js 尽力消除 webpack 配置的开销，但是对于需要更多控制的高级情况，参考[自定义 webpack 配置文档](https://nextjs.org/docs/api-reference/next.config.js/custom-webpack-config)

- 编译了那些语法功能？我如何修改它们？
  我们跟随 v8。由于 v8 对 ES6 的 async 和 await 支持，所以我们编译它们。由于 v8 不支持 class 装饰器，我们也不会编译它们。

  查看[自定义 babel 配置](https://nextjs.org/docs/advanced-features/customizing-babel-config)了解更多

- 为什么要用新 Router？
  Next.js 特殊之处在于：

  - 路由不需要提前知道，我们不运送路由清单
  - 路由一直是懒加载

- 我如何获取数据？
  由你决定，那你可以在你 React 组件中通过 fetch api 或者 SWR 获取远程数据；或者使用我们的数据获取方法来进行初始互数据的填充

- 我可以和 GraphQL 一起使用吗？
  可以，这里是[Apollo 案例](https://github.com/zeit/next.js/tree/canary/examples/with-apollo)

- 我可以和 Redux 一起使用吗？
  可以，这里是[案例](https://github.com/zeit/next.js/tree/canary/examples/with-redux)。并且这里有其他的[带 thunk 的案例](https://github.com/zeit/next.js/tree/canary/examples/with-redux-thunk)

- 静态资源我可以使用 CDN 吗？
  可以，你可以在[这里](https://nextjs.org/docs/api-reference/next.config.js/cdn-support-with-asset-prefix)了解更多

- 我可以在 Next 中使用最喜爱的 js 库或者工具集吗？
  自从我们第一个版本，我们有许多案例贡献。你可以在[examples](https://github.com/zeit/next.js/tree/canary/examples)目录下查看

- 这是受什么启发
  我们设置的许多实现的目标都是 Guillermo Rauch 提出的[7 个富 web 应用原则](https://rauchg.com/2014/7-principles-of-rich-web-applications)之上

  PHP 的易用性是一个很大的启发。我们认为 Next.js 应该是在很多场景下的替代品，否则你应该使用 PHP 输出 HTML。

  与 PHP 不同，我们受益于 ES6 模块系统，每个页面导出的组件和函数都可以轻松的导入以进行懒惰评估或者测试。

  在研究不涉及大量步骤的 React 服务端渲染配置项时，我们遇到了 React-page（已抛弃），这是 React Jordan Walke 的创建者与 Next.js 类似的方法

# API 文档

## CLI

Next.js CLI 允许你启动，构建和导出那你的应用程序。

为了获得有效的 CLI 命令列表，在你的项目目录下运行这些命令：

```
npx next -h
```

(npx 出现在 npm5.2 及以上)
这个的输出应该是这样：

```
Usage
  $ next <command>

Available commands
  build, start, export, dev, telemetry

Options
  --version, -v   Version number
  --help, -h      Displays this message

For more information run a command with the --help flag
  $ next build --help
```

你可以像 next 命令传递任何 [node 参数](https://nodejs.org/api/cli.html#cli_node_options_options)

```
NODE_OPTIONS='--throw-deprecation' next
NODE_OPTIONS='-r esm' next
NODE_OPTIONS='--inspect' next
```

### 构建

next build 为你的应用穿件一个优化过的生产构建。每个路由输出的信息是这样的：

- Size -在客户端跳转到页面需要下载的资源大小。每个路由大小只包含它自己的依赖
- First Load JS - 访问服务端这个页面需要下载的资源大小。所有共享的 JS 大小作为单独的指标

第一次加载的颜色会是绿色，黄色，或者红色。为高性能一能用提供的是绿色。

### 遥测

Next.js 收集有关常用使用的完全异步的遥测数据。
参与此匿名程序是可选的，如果你不喜欢共享这些数据你可以退出。

[读这个文档](https://nextjs.org/telemetry/)进一步学习遥测

## next/router

路由管理器

### useRouter

如果你想要在你应用的任何函数组件中访问[路由管理器对象](https://nextjs.org/docs/api-reference/next/router#router-object)，你应该使用 useRouter 钩子，看下下面的示例：

```jsx
import { useRouter } from "next/router";

function ActiveLink({ children, href }) {
  const router = useRouter();
  const style = {
    marginRight: 10,
    color: router.pathname === href ? "red" : "black",
  };

  const handleClick = (e) => {
    e.preventDefault();
    router.push(href);
  };

  return (
    <a href={href} onClick={handleClick} style={style}>
      {children}
    </a>
  );
}

export default ActiveLink;
```

> useRouter 是一个 React 钩子，意味着它无法被使用在类中。你可以选择使用 [withRouter](https://nextjs.org/docs/api-reference/next/router#withrouter) 或者将 class 包装在函数组件中

#### router object

下面是 router 对象的定义，useRouter 和 withRouter 都会返回改对象：

- pathname: String - 当前路由，它是`/pages`下页面的路径
- query: Object - 查询参数被解析成对象，默认是 {}
- asPath: String - 限制在浏览器中实际路径（包括查询）

另外，Router API 也被包含在这个对象中。

> 如果页面被静态优化，那么在预渲染期间这个 query 对象是空的

### withRouter

如果 useRouter 不适合你，withRouter 同样可以添加相同的 router 对象给任何组件，这里是如何使用它：

```jsx
import { withRouter } from "next/router";

function Page({ router }) {
  return <p>{router.pathname}</p>;
}

export default withRouter(Page);
```

### Router API

Router 的 API，由`next/router`导出，api 定义如下。

#### Router.push

处理客户端过度，当[next/link](https://nextjs.org/docs/api-reference/next/link)不够用的时候，这个方法非常有用。

```js
import Router from "next/router";

Router.push(url, as, options);
```

- url - 导航的 URL。它通常是页面的名字
- as - 显示到浏览器上 URL 的可选装饰器，默认是 url
- options - 具有以下配置选项的可选对象
  - shallow: 只更新当前页面的路径，而不重新运行 getStaticProps，getServerSideProps 或者 getInitialProps。默认是 false

> 对于外部 URL，你不需要使用 Router，window.location 是更适合这些情况。

#### 使用

导航到`pages/about.js`这个预定义路由：

```jsx
import Router from "next/router";

function Page() {
  return <span onClick={() => Router.push("/about")}>Click me</span>;
}
```

导航到`pages/post/[pid].js`这样的动态路由：

```jsx
import Router from "next/router";

function Page() {
  return (
    <span onClick={() => Router.push("/post/[pid]", "/post/abc")}>
      Click me
    </span>
  );
}
```

#### 带 URL 对象

你可以像`next/link`那样的方式带 URL 对象。适用 url 和 as 参数：

```jsx
import Router from "next/router";

const handler = () => {
  Router.push({
    pathname: "/about",
    query: { name: "Zeit" },
  });
};

function ReadMore() {
  return (
    <div>
      Click <span onClick={handler}>here</span> to read more
    </div>
  );
}

export default ReadMore;
```

#### Router.replace

类似于`next/link`的 replace 属性，Router.replace 将阻止添加新的 URL 到 history 堆栈，看下下面的实例：

```js
import Router from "next/router";

Router.replace("/home");
```

Router.replace 的 API 和 Router.push 的用法完全相同

#### Router.beforePopState

在某些情况下（举例，如果使用的是自定义 Server），你可能希望监听[弹出状态](https://developer.mozilla.org/en-US/docs/Web/Events/popstate)并想在路由管理执行动作之前做些事情。

你可以使用它来操作请求，或者强制 SSR 刷新，如下案例：

```js
import Router from "next/router";

Router.beforePopState(({ url, as, options }) => {
  // 我只想允许这两个路由
  if (as !== "/" && as !== "/other") {
    // 让SSR呈现错误路由为404
    window.location.href = as;
    return false;
  }

  return true;
});
```

`Router.beforePopState(cb: () => boolean)`

- cb -在传入的 popstate 事件上运行这个函数。这个函数接收事件状态，该事件是具有以下属性的对象：
  - url: String - 新状态的路由。通常是页面的名字
  - as: String - 显示在浏览器的 url
  - options: Object - Router.push 发送的额外选项

如果你传递给 beforePopState 的函数返回了 false,Router 将不处理 popstate 并且 你将响应处理它。看[禁止文件路由](https://nextjs.org/docs/advanced-features/custom-server#disabling-file-system-routing)

#### Router.events

你可以监听发生在 Router 内部的不同事件。这里是支持的事件列表：

- routeChangeStart(url) - 当路由开始变化
- routeChangeComplete(url) - 当路由变化完成
- routeChangeError(err, url) - 当改变路由时发生错误或者路由加载被取消
  - err.cancelled - 表明导航是否被取消
- beforeHistoryChange(url) - 只在浏览器 history 变化之前触发
- hashChangeStart(url) - hash 值将发生变化但页面不变化
- hashChangeComplete(url) - 当 has 变化完成页面不变化

> 这里的 URL 是显示在浏览器的 url。如果你调用`Router.push(url, as)`，然后这个 url 值将是 as 的值

例如，监听路由事件 routeChangeStart：

```js
import Router from "next/router";

const handleRouteChange = (url) => {
  console.log("App is changing to: ", url);
};

Router.events.on("routeChangeStart", handleRouteChange);
```

如果你不在想监听这个事件，使用 off 函数取消订阅：

```js
import Router from "next/router";

Router.events.off("routeChangeStart", handleRouteChange);
```

如果路由加载被取消（举例，通过快速的连续点击两次链接），routeChangeError 将触发。并且传入的 err 将包含 cancelled 为 true，如下：

```js
import Router from "next/router";

Router.events.on("routeChangeError", (err, url) => {
  if (err.cancelled) {
    console.log(`Route to ${url} was cancelled!`);
  }
});
```

路由事件应该在页面被挂载的时候注册（useEffect 或者 componentDidMount/ComponentWillUnmount)或者事件必定发生，如下：

```js
import Router from "next/router";

useEffect(() => {
  const handleRouteChange = (url) => {
    console.log("App is changing to: ", url);
  };

  Router.events.on("routeChangeStart", handleRouteChange);
  return () => {
    Router.events.off("routeChangeStart", handleRouteChange);
  };
}, []);
```

## next/head

我们暴露内置组件用于将元素添加到页面的顶部

```
import Head from 'next/head'

function IndexPage() {
  return (
    <div>
      <Head>
        <title>My page title</title>
        <meta name="viewport" content="initial-scale=1.0, width=device-width" />
      </Head>
      <p>Hello world!</p>
    </div>
  )
}

export default IndexPage
```

为了避免在你的`head`中使用重复标签，你可以用`key`属性，这样会保证一个标签只会渲染一次。例子如下

```
import Head from 'next/head'

function IndexPage() {
  return (
    <div>
      <Head>
        <title>My page title</title>
        <meta
          name="viewport"
          content="initial-scale=1.0, width=device-width"
          key="viewport"
        />
      </Head>
      <Head>
        <meta
          name="viewport"
          content="initial-scale=1.2, width=device-width"
          key="viewport"
        />
      </Head>
      <p>Hello world!</p>
    </div>
  )
}

export default IndexPage
```

在这个例子中只有第二个会被渲染

> 卸载组件时会清除在`head`上的内容，保证每个页面需要的`head`都是干净完整定义，不用关心会有其他页面的 Head 干扰它

> `title`和`meta`元素是`Head`的直接子组件，或者包裹在`<React.Fragment>`下，否则 meta 标签不会正确的被服务端渲染找到

## next/amp

> AMP 支持是我们的一个高级特性，你可以进一步[阅读](https://nextjs.org/docs/advanced-features/amp-support/introduction)

为了启用 AMP，添加下面的配置给你的页面：

```js
export const config = { amp: true };
```

这个 amp 配置接收下面的值：

- true -页面只是 AMP
- 'hybird' - 页面只有两个版本，AMP 和 HTML

### AMP 第一页

看下面的案例：

```jsx
export const config = { amp: true };

function About(props) {
  return <h3>My AMP About Page!</h3>;
}

export default About;
```

上面页面只是 AMP,意味着：

- 该页面没有 Next.js 或者 React 客户端运行时
- 该页面使用[AMP 优化器](https://github.com/ampproject/amp-toolbox/tree/master/packages/optimizer)自动优化，这个优化器用 AMP 缓存（将性能多提高 42%）
- 这个页面有优化的用户可访问版本，和未优化过的搜索引擎索引的版本

### 混合 AMP 页面

看下面的案例：

```jsx
import { useAmp } from "next/amp";

export const config = { amp: "hybrid" };

function About(props) {
  const isAmp = useAmp();

  return (
    <div>
      <h3>My AMP About Page!</h3>
      {isAmp ? (
        <amp-img
          width="300"
          height="300"
          src="/my-img.jpg"
          alt="a cool image"
          layout="responsive"
        />
      ) : (
        <img width="300" height="300" src="/my-img.jpg" alt="a cool image" />
      )}
    </div>
  );
}

export default About;
```

上面的页面是混合 AMP 页面，意味着：

- 这个页面默认渲染成传统的 HTML，通过加?amp=1 渲染成 AMP HTML
- 页面的 AMP 版本只对 AMP 优化器应用了有效优化，所以它可以被搜索引擎索引

这个页面使用了 useAmp 来区分两个模式，如果页面使用了 AMP，它的 React 钩子返回 true，否则 false。

## Data Fetching

> 建议: 你正在阅读新文档。[旧文档](https://nextjs.org/docs/old)仍然有效

### getInitialProps

推荐：`getStaticProps`或者`getServerSideProps`
如果你使用 Next.js 9.3 或者更新，我们建议你使用它们来代替`getInitialProps`
这两个新获取数据的方法允许在静态生成和服务端渲染时选择粒度更细。更多信息看[Pages](https://nextjs.org/docs/basic-features/pages)和[Data fetching](https://nextjs.org/docs/basic-features/data-fetching)

**不贴 Examples**
`getInitialProps`启用页面中的服务端渲染，并允许你初始化数据填充。这意味着从服务端过来的页面携带了已经填充好的数据了。它对[SEO](https://en.wikipedia.org/wiki/Search_engine_optimization)非常友好。

> `getInitialProps` 将禁用自动静态优化

它是一个添加到任何页面的静态异步函数。看下面这些示例

```
import fetch from 'isomorphic-unfetch'

function Page({ stars }) {
  return <div>Next stars: {stars}</div>
}

Page.getInitialProps = async ctx => {
  const res = await fetch('https://api.github.com/repos/zeit/next.js')
  const json = await res.json()
  return { stars: json.stargazers_count }
}

export default Page
```

或者使用 class 组件

```
import React from 'react'
import fetch from 'isomorphic-unfetch'

class Page extends React.Component {
  static async getInitialProps(ctx) {
    const res = await fetch('https://api.github.com/repos/zeit/next.js')
    const json = await res.json()
    return { stars: json.stargazers_count }
  }

  render() {
    return <div>Next stars: {this.props.stars}</div>
  }
}

export default Page
```

`getInitialProps`用来异步获取数据，然后填充到`props`上
当服务端渲染时`getInitialProps`返回的数据是被序列化的。类似于`JSON.stringify`。保证返回的对象是一个 Plain 对象，而不是`Date`, `Map` or `Set`
对于初始化页面的加载，`getInitialProps`将只在服务端执行。只有当页面间跳转`next/link`或者使用`next/router`会在客户端执行。

### Context Object

getInitialProps 接收单个 context,带有下面属性：

- pathname - 当前路由。是`/pages`页面的路径
- query - URL 的 query 部分字符串被解析成对象
- asPath - 展示在浏览器中的真实地址
- req - HTTP 请求对象（只在服务端）
- res - HTTP 响应对象（只在服务端）
- err - 在渲染期间的错误对象

### Caveats

- getInitialProps 无法被使用到子组件中，只能被页面导出
- 如果你某些模块只在 getInitialProps 服务端使用，保证[正确导入它们](https://arunoda.me/blog/ssr-and-server-only-modules)，否则就会降低 app 下载速度。

### TypeScript

如果你使用 TypeScript,你可以在函数组件中使用 NextPage 类型

```jsx
import { NextPage } from "next";

interface Props {
  userAgent?: string;
}

const Page: NextPage<Props> = ({ userAgent }) => (
  <main>Your user agent: {userAgent}</main>
);

Page.getInitialProps = async ({ req }) => {
  const userAgent = req ? req.headers["user-agent"] : navigator.userAgent;
  return { userAgent };
};

export default Page;
```

对于 React.Component，你可以使用 NextPageContext：

```jsx
import React from "react";
import { NextPageContext } from "next";

interface Props {
  userAgent?: string;
}

export default class Page extends React.Component<Props> {
  static async getInitialProps({ req }: NextPageContext) {
    const userAgent = req ? req.headers["user-agent"] : navigator.userAgent;
    return { userAgent };
  }

  render() {
    const { userAgent } = this.props;
    return <main>Your user agent: {userAgent}</main>;
  }
}
```

## next.config.js

### 介绍

想要自定义 next.js 的高级行为，你可以在项目的根目录下创建 next.config.js（在 package.json 旁边）。

next.config.js 是 Node.js 的常规模块，而不是 json 文件。它被 Next.js 的服务和它的构建阶段使用，并不会再浏览器构建时使用。

在下面的实例中：

```js
module.exports = {
  /* 在这里配置选项 */
};
```

你也可以使用函数：

```js
module.exports = (phase, { defaultConfig }) => {
  return {
    /* 在这里配置选项 */
  };
};
```

phase 是已经加载配置的当前上下文。你可以在[这里](https://github.com/zeit/next.js/blob/canary/packages/next/next-server/lib/constants.ts#L1-L4)看到有哪些 Phases。Phases 可以从 next/constants 导入：

```js
const { PHASE_DEVELOPMENT_SERVER } = require("next/constants");

module.exports = (phase, { defaultConfig }) => {
  if (phase === PHASE_DEVELOPMENT_SERVER) {
    return {
      /* 只在开发时用的配置 */
    };
  }

  return {
    /* 除了开发环境下的其他所有阶段的配置*/
  };
};
```

带注释的行是让你可以放配置的地方。

但是所有的配置都不强制要求，不需要知道每个配置都是干什么的。相反，你搜索你需要的属性去启用和修改，它将会显示你想要的结果。

### 环境变量

为了添加环境变量给 JS bundle，打开 next.config.js 文件并添加 env 配置：

```js
module.exports = {
  env: {
    customKey: "my-value",
  },
};
```

现在你可以在你的代码中访问 process.env.customKey。例如：

```js
function Page() {
  return <h1>The value of customKey is: {process.env.customKey}</h1>;
}

export default Page;
```

Next.js 将在构建时使用 my-value 替换 process.env.customKey。尝试对 process.env 进行解构不会工作，因为 webpack [DefinePlugin](https://webpack.js.org/plugins/define-plugin/) 的性质。

例如，下面一行

```jsx
return <h1>The value of customKey is: {process.env.customKey}</h1>;
```

将会被替换成：

```jsx
return <h1>The value of customKey is: {"my-value"}</h1>;
```

### 自定义页面扩展

和[@next/mdx](https://github.com/zeit/next.js/tree/canary/packages/next-mdx)的作用一样，mdx 支持页面以文件.mdx 结尾。你可以配置 pages 目录下那些扩展可以认为是页面。

打开 next.config.js 并添加 pageExtensions 配置：

```js
module.exports = {
  pageExtensions: ["mdx", "jsx", "js", "ts", "tsx"],
};
```

### 用资源前缀来支持 CDN

为了设置 CDN，你可以设置一个资源前缀并配置你 CDN 源去解析为 Next.js 项目运行所在的域名。

打开 next.config.js 并添加 assetPrefix 配置：

```js
const isProd = process.env.NODE_ENV === "production";

module.exports = {
  // Use the CDN in production and localhost for development.
  assetPrefix: isProd ? "https://cdn.mydomain.com" : "",
};
```

Next.js 将在加载的脚本自动使用你的前缀，但是对 public 目录下文件夹下的资源没有作用；如果你想要这写资源也用 CDN，你需要自己处理这个前缀。一种可以在你组件中因环境变量引入的前缀可以参考这个[案例](https://github.com/zeit/next.js/tree/canary/examples/with-universal-configuration-build-time)

### 构建目标

Next.js 支持各种构建目标，每个都会让你的应用改变构建和运行方式。我们将在下面解释每个目标。

#### server 目标

> 这是默认目标，然而，我们强烈建议使用无服务目标。无服务目标强制附加约束保证你走上正确的道路上。

这个目标和 next start 以及自定义服务设置兼容（自定义服务强制支持）

你的应用将被构建并部署成一个整体。这是默认目标，你不需要任何动作就可以加入。

#### serverless 目标

> 部署到[ZEIT Now](https://zeit.co/)将自动启用这个目标。你不需要自己加入。

这个目标输出独立的页面，不需要作为一个整体服务。

它只兼容 next start 或者无服务部署平台（like ZEIT Now）--你无法使用自定义的服务 API。

为了加入这个目标。在你的 next.config.js 中用以下配置：

```js
module.exports = {
  target: "serverless",
};
```

### 自定义 webpack 配置

一些常见的功能可以作为插件使用：

- [@zeit/next-sass](https://github.com/zeit/next-plugins/tree/master/packages/next-sass)
- [@zeit/next-less](https://github.com/zeit/next-plugins/tree/master/packages/next-less)
- [@zeit/next-stylus](https://github.com/zeit/next-plugins/tree/master/packages/next-stylus)
- [@zeit/next-preact](https://github.com/zeit/next-plugins/tree/master/packages/next-preact)
- [@next/mdx](https://github.com/zeit/next.js/tree/canary/packages/next-mdx)
- [@next/bundle-analyzer](https://github.com/zeit/next.js/tree/canary/packages/next-bundle-analyzer)

为了扩展 webpack 的使用，你可以在 next.config.js 定义一个函数扩展它的 config：

```js
module.exports = {
  webpack: (config, { buildId, dev, isServer, defaultLoaders, webpack }) => {
    // 注意: 我们在上面提供了 webpack，你不用在 require 它
    // 对 webpack 配置执行自定义化
    // 重点: 返回修改过的config
    config.plugins.push(new webpack.IgnorePlugin(/\/__tests__\//));
    return config;
  },
  webpackDevMiddleware: (config) => {
    // 对webpack开发中间件配置执行自定义华
    // 重点: 返回修改过的config
    return config;
  },
};
```

> 这个 webpack 函数被执行两次，一次在服务端一次在客户端。它云心你通过 isServer 属性区分客户端和服务端的配置

给 webpack 第二个参数是带下面属性的对象：

- buildId:String - 构建内 id，作为内部版本之间的标识
- dev:Boolean - 指示编译是否将在开发环境中完成
- isServer:Boolean - 如果是服务端编译是 true，否则就是客户端编译
- defaultLoaders:Object - 默认加载器由 Next.js 内部提供：
  - babel:Object - 默认 babel-loader 配置

defaultLoaders.babel 的使用案例：

```js
// 添加依赖于 babel-loader 的加载器的配置示例
// 来源于 @next/mdx 插件:
// https://github.com/zeit/next.js/tree/canary/packages/next-mdx
module.exports = {
  webpack: (config, options) => {
    config.module.rules.push({
      test: /\.mdx/,
      use: [
        options.defaultLoaders.babel,
        {
          loader: "@mdx-js/loader",
          options: pluginOptions.options,
        },
      ],
    });

    return config;
  },
};
```

### 压缩

Next.js 提供 gzip 压缩来压缩渲染内容和静态文件。压缩只在服务端工作。通常需要在 HTTP 代理上启用压缩(如 nginx)，减轻 Node.js 进程的负载。

为了禁止压缩，打开 next.config.js 来禁止 compress 配置：

```js
module.exports = {
  compress: false,
};
```

### 静态优化指示器

当页面符合自动静态化优化条件时，我们会显示一个指示器让你知道。

它非常有用，如果页面符合条件，它会在开发环境下立刻执行自动静态优化并让你知道。

在某些情况下，指示器没用，比如在 electron 应用上工作时。为了在 next.config.js 删除并在 devIndicators 下禁用 autoPrerender 配置

```js
module.exports = {
  devIndicators: {
    autoPrerender: false,
  },
};
```

### 运行时配置

> 通常你想要为你的配置使用构建时环境变量。原因是因为运行时配置会增加渲染和初始化的开销，并且与自动静态化不兼容。
> 当使用 [serverless 目标](https://nextjs.org/docs/api-reference/next.config.js/build-target#serverless-target) 时运行时配置无效

为了添加运行时配置到你的应用，打开 next.config.js 并添加 publicRuntimeConfig 和 serverRuntimeConfig 配置。

```js
module.exports = {
  serverRuntimeConfig: {
    // Will only be available on the server side
    mySecret: "secret",
    secondSecret: process.env.SECOND_SECRET, // Pass through env variables
  },
  publicRuntimeConfig: {
    // Will be available on both server and client
    staticFolder: "/static",
  },
};
```

将只在服务端使用的运行时配置放到 serverRuntimeConfig 下

需要在客户端和服务端都要访问的变量应该放在 publicRuntimeConfig

> 依赖 publicRuntimeConfig 的页面必须使用 getInitialProps 来退出自动静态化。没有 getInitialProps，运行时配置将不再任何页面或者页面中的组件上有效。

使用 next/config 来访问运行时配置，比如

```jsx
import getConfig from "next/config";

// Only holds serverRuntimeConfig and publicRuntimeConfig
const { serverRuntimeConfig, publicRuntimeConfig } = getConfig();
// Will only be available on the server-side
console.log(serverRuntimeConfig.mySecret);
// Will be available on both server-side and client-side
console.log(publicRuntimeConfig.staticFolder);

function MyImage() {
  return (
    <div>
      <img src={`${publicRuntimeConfig.staticFolder}/logo.png`} alt="logo" />
    </div>
  );
}

export default MyImage;
```

### 禁止 x-powered-by

默认 Next.js 将添加 x-powered-by 到请求头。为了跳过它，可以在 next.config.js 中禁止 poweredByHeader 配置

```js
module.exports = {
  poweredByHeader: false,
};
```

### 禁止 ETag 生成

Next.js 为每个页面默认生成[etas](https://en.wikipedia.org/wiki/HTTP_ETag)。你想要禁止给 HTML 页面生产 etag，具体取决于你的缓存策略。

打开 next.config.js 并禁止 generateEtags 选项：

```js
module.exports = {
  generateEtags: false,
};
```

### 设置自定义构建目录

你可以对自定义构建目录制定名字来替换.next

打开 next.config.js 并添加 distDir 配置：

```js
module.exports = {
  distDir: "build",
};
```

现在如果你运行 next build 将使用 build 来替换默认的.next 目录。

> distDir 不应该是你项目之外的目录。比如 ../build 是一个无效目录。

### 配置构建 ID

Next.js 使用构建时的生成的约定的 id 来识别你部署的应用是哪个版本。当在多服务上会造成麻烦（每个服务上都会执行 next build）。为了保证在各个构建之间保持同一个静态 id，你可以自己定义 id。

打开 next.config.js 并添加 generateBuildId 函数

```js
module.exports = {
  generateBuildId: async () => {
    // You can, for example, get the latest git commit hash here
    return "my-build-id";
  },
};
```

### 配置 onDemandEntries

Next.js 暴露了一些选项让你可以在开发中控制服务如何处理或者保持构建的页面内在内存中。

为了修改默认配置，打开 next.config.js 并添加 onDemandEntries 配置。

```js
module.exports = {
  onDemandEntries: {
    // 服务器保存页面在缓存中的周期(ms)
    maxInactiveAge: 25 * 1000,
    // 同时保存，不丢弃的页面数量
    pagesBufferLength: 2,
  },
};
```

### 忽略 TypeScript 错误

Next.js 默认报告 TypeScript 错误。如果你不想利用这个行为，可以选择其他方式，就像你编辑器集成一样，你可能想要禁止它。

打开 next.config.js 在 typescript 配置下启用 ignoreDevErrors 选项：

```js
module.exports = {
  typescript: {
    ignoreDevErrors: true,
  },
};
```

当项目中仍存在 TypeScript 错误时，Next.js 生成构建仍旧会失败(next build)。

---

如果你希望在应用有错误的情况下，Next.js 仍危险的运行生产代码，你可以禁止构建错误报告。

> 确保在你构建和部署过程运行了类型检查，否则会非常危险。

打开 next.config.js 并在 typescript 配置中启用 ignoreBuildErrors:

```js
module.exports = {
  typescript: {
    // !! WARN !!
    // 如果你项目有类型错误，让产品构建成功非常危险
    // 很少需要用到这个选项，暴露此选项用于高级配置。
    // 你可能要寻找`ignoreDevErrors` 代替
    // !! WARN !!
    ignoreBuildErrors: true,
  },
};
```

### exportPathMap

> 这个功能只为 next export 服务。如果想了解更多看[静态 HTML 导出](https://nextjs.org/docs/advanced-features/static-html-export)

开始例子，为带有下面页面的应用创建自定义的 exportPathMap：

- `pages/index.js`
- `pages/about.js`
- `pages/post.js`

打开 next.config.js 并添加下面的 exportPathMap 配置：

```
module.exports = {
  exportPathMap: async function(
    defaultPathMap,
    { dev, dir, outDir, distDir, buildId }
  ) {
    return {
      '/': { page: '/' },
      '/about': { page: '/about' },
      '/p/hello-nextjs': { page: '/post', query: { title: 'hello-nextjs' } },
      '/p/learn-nextjs': { page: '/post', query: { title: 'learn-nextjs' } },
      '/p/deploy-nextjs': { page: '/post', query: { title: 'deploy-nextjs' } },
    }
  },
}

```

这些页面将会导出成 HTML 文件，例如，`/about`将变成`/about.html`。

exportPathMap 是一个接收两个参数的异步函数： 第一个是 defaultPathMap，Next.js 的默认 map。第二个参数对象带有下面属性：

- dev - 当 exportPathMap 被开发时调用为 true.当运行 next export 是 false。开发时，exportPathMap 被用来定义路由。
- dir -项目目录的绝对路径
- outDir - out 目录(由-o 配置)的绝对路径。dang dev 是 true，outDir 将是 null
- distDir - `.next/`目录的绝对路径（由[distDir](https://nextjs.org/docs/api-reference/next.config.js/setting-a-custom-build-directory)配置）
- buildId - 生成的内部版本号

返回的对象是页面 map，key 是 pathname 并且 value 是接收下面字段的对象

- page: String - 在 pages 目录下的页面
- query: Object - query 对象是当预渲染时传递给 getInitialProps，默认是 {}

> 导出的 pathname 也可以是文件名(比如, `/readme.md`)，但是如果它的内容与.html 不同，你需要响应内容时将头 Content-Type 设置成 `text/html`.

#### 添加结尾斜杠

可以配置 Next.js 导出页面成 index.html 文件，并要求结尾加/。比如 `/about` 变成 `/about/index.html`，并且可以通过`/about/`路由。这是 Next.js 9 之前的默认行为

回退添加结尾/，打开 next.config.js 并启用 exportTrailingSlash 配置：

```js
module.exports = {
  exportTrailingSlash: true,
};
```

### 错误

- 在 getInitialProps 返回空对象
  **为什么出现这个错误**
  在页面组件中的`getInitialProps`返回空对象。它会取消自动静态化优化。如果你知道自己的操作并且知道后果。你可以在开发环境下忽略这个消息。
  **修复的方法** (我反正没看懂)
  查看`getInitialProps`返回空对象的页面。如果它们存在传递的组件，你可能需要给更新高阶组件添加`getInitialProps`
  **有用链接**
- [自动静态优化文档](Automatic Static Optimization Documentation)
