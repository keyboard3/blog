---
title: react 主要概念
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-09 14:00:27
password:
summary:
tags: [react, 翻译]
categories:
---

## Hello World

最简单的 React 示例如下：

```jsx
ReactDOM.render(<h1>Hello, world!</h1>, document.getElementById("root"));
```

在页面的顶部显示了”Hello,world!“

### 如何读这篇指南

在这个指南中，我们们将介绍构建 React 应用的模块：元素和组件。一旦掌握它们，你可以复用很小的模块创建复杂的应用。

> 提示：
> 这个指南是为了那些喜欢一步步学习概念的人设计的。如果你更喜欢边做边学，看我们的练习教程。你应该发现这篇指南和那个教程相互补充，适合每个人。

这是一步步学习 React 主要概念的第一个章节。你可以在右侧的导航栏找到所有的章节列表。如果你在移动设备上读它，你可以从屏幕上的右下角的按钮访问导航。

这个指南的每一章都建立在章节开头的知识介绍之上。**你可以在侧边栏中顺序的阅读主要概念的指南章节，学习更多的 React 知识**。例如，[介绍 Jsx](https://reactjs.org/docs/introducing-jsx.html)是下一章

### 知识水平假设

React 是一个 JavaScript 库，所以我们假设你对 JavaScript 语言有基础的理解。**如果你感觉不自信的话，我们建议[通过 JavaScript 教程](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)来检查你的知识水平**，使你遵循本指南而不会迷路。这可能会花费你 30 分钟到 1 个小时，但是并不会让你感觉你需要同时学 React 和 JavaScript。

> 注意：
> 这个指南在案例中会偶尔使用最新的 JavaScript 语法。如果你最近几年没有用 JavaScript 工作，这[三点](https://gist.github.com/gaearon/683e676101005de0add59e8bb345340c)会让你收货很大

## 介绍 JSX

考虑下面的变量声明：

```jsx
const element = <h1>Hello, world!</h1>;
```

这是很有趣的标签语法，既不是 string 也不是 HTML。

它叫做 JSX，它是 JavaScript 的语法扩展。我们建议与 React 一起来描述 UI。JSX 可能让你想起模板语言，但是它具有 JavaScript 的全部能力。

JSX 产生 React 元素。我们将在下一章探索将它们渲染到 DOM。在下面，你可以找到必要的 JSX 基础知识。

### 为什么是 JSX？

React 包含一个事实就是渲染逻辑和其他 UI 逻辑强耦合： 事件如何处理，状态如何随时间变化，数据如何准备显示。

取代人为的将标记语言和逻辑放到不同文件来实现分离，React 使用松散耦合的单元”组件“（包含二者）来[关注分离点](https://en.wikipedia.org/wiki/Separation_of_concerns)。我们将在下一节来回到组件上，但是如果你还不熟悉在 JS 中添加标记，[这个话题](https://www.youtube.com/watch?v=x7cQ3mrcKaY)将说服你。

React 不要求使用 JSX，但是它当在 JS 代码中作为可视化 UI 时非常有用。它还允许显示更多有用的错误和警告信息。

不用担心，开始吧

### 嵌套表达式到 JSX

在下面的案例中，我们声明了一个 name 变量，然后使用它，将它包裹在大括号中。

```jsx
const name = "Josh Perez";
const element = <h1>Hello, {name}</h1>;

ReactDOM.render(element, document.getElementById("root"));
```

你可以将任何有效的 JavaScript 表达式放到 JSX 的大括号中。例如，2+2，user.firstName，或者 formatName(user)都是有效的 JavaScript 表达式。

在下面的案例中，我们我们嵌入了 JavaScript 函数调用的结果到 h1 元素中。

```jsx
function formatName(user) {
  return user.firstName + " " + user.lastName;
}

const user = {
  firstName: "Harper",
  lastName: "Perez",
};

//加``故意为之，否则被格式化掉了
const element = `
(
  <h1>
    Hello, {formatName(user)}!
  </h1>
)`;

ReactDOM.render(element, document.getElementById("root"));
```

为了可读性将 JSX 分离成了多行。这并不强制，当你这么做的时候，我们建议你将它们包裹在（）中避免自动插入；符号的陷阱。

### JSX 也是一个表达式

编译之后，JSX 表达式变成了常规的 JavaScript 函数调用并评估为 JavaScript 对象。

这意味着你可以将 JSX 放到 if 语句和 for 循环中，分配它到变量，将它作为一个参数，并从函数中返回它。

```jsx
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

### 用 JSX 指定属性

你可以使用引号来指定字符串字面量作为属性：

```jsx
const element = <div tabIndex="0"></div>;
```

你也可以在大括号中嵌入 JavaScript 表达式作为属性

```jsx
const element = <img src={user.avatarUrl}></img>;
```

当嵌入 JavaScript 表达式作为属性时，不要将大括号周围加上引号。你应该使用为字符串使用”，为表达式使用{}。但是不应该放到相同的属性上。

> 警告：
> 因为 JSX 更加贴近 Javascript 而不是 HTML，React 使用小写驼峰的属性命名约定来代替 HTML 属性名。
> 例如，在 JSX 中 class 变成 className，tabindex 变成 tabIndex。

### 用 JSX 指定 Children

如果标签是空的，你应该立刻使用`/>`标签结束，像 XML：

```jsx
const element = <img src={user.avatarUrl} />;
```

JSX 标签可能包含 children:

```jsx
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```

### JSX 防止注入攻击

将用户输入嵌入到 JSX 是非常安全的：

```jsx
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
```

默认情况下，React 会在渲染之前，转义任何嵌入到 JSX 的值。这样保证你不会注入未显示的写在你应用中的任何东西。任何东西都会被在渲染之前转成字符串。这回帮助防止 XSS（跨站脚本）攻击。

### JSX 代表对象

Babel 编译 JSX 成`React.createElement()`调用。

这两个示例是相同的：

```jsx
const element = <h1 className="greeting">Hello, world!</h1>;
```

```js
const element = React.createElement(
  "h1",
  { className: "greeting" },
  "Hello, world!"
);
```

`React.createElement()`执行一些检查帮助你写无 bug 的代码，但是实际上它会创建类似下面的对象：

```jsx
// Note: 这个结构被简化了
const element = {
  type: "h1",
  props: {
    className: "greeting",
    children: "Hello, world!",
  },
};
```

这些对象被称为“React 元素”。你可以认为它们描述了你想要在屏幕上如何显示。React 读取这些对象，用它们构造 DOM 并实时更新。

我们间能够在下一节讨论 React 元素渲染到 DOM

> 提示：
> 我们建议为你选择的编译器使用[Babel 语言定义](https://babeljs.io/docs/editors)，因为 ES6 和 JSX 代码都会被正确的高亮。

## 渲染元素

元素时 React 应用中最小的构建块。

元素描述了你想要在屏幕上显示的内容：

```jsx
const element = <h1>Hello, world</h1>;
```

不像浏览器 DOM 元素，React 元素时一个纯对象，
创造编译。React DOM 负责更新 DOM 以匹配 React 元素。

> 注意：
> 可能让元素和一个广泛知道的“组件”概念混淆。我们将在下一章介绍组件。元素时组件的组成部分，我们建议你在继续之前读完这一节。

### 渲染元素到 DOM

假设你 HTML 文件中有一处定义了一个 div:

```jsx
<div id="root"></div>
```

我们认为它是 root DOM 节点，因为它下面的任何东西都被 React DOM 管理。

只被 React 构建的应用通常只要一个 root DOM 节点。如果你集成 React 到已存在的应用上，你可能根据你的需求有多个独立的 root DOM 节点。

为了将 React 元素渲染到 root DOM 节点，需要将它们传递到`ReactDOM.render()`中。

```jsx
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById("root"));
```

### 更新渲染过的元素

React 元素时[不可变](https://en.wikipedia.org/wiki/Immutable_object)的。一旦你创建了一个元素，你无法修改它的 children 和属性。一个元素被认为是动画的一帧：它代表了 UI 在那个特定的时间点。

到目前为止，我们的知识中只有一种方式更新 UI，那就是创建新元素，并传递给[ReactDOM.render()](https://reactjs.org/docs/react-dom.html#render)

考虑下面滴答时钟案例：

```jsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById("root"));
}

setInterval(tick, 1000);
```

它每秒从[setInterval()](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setInterval)回调中调用[ReactDOM.render()](https://reactjs.org/docs/react-dom.html#render)

> 注意：
> 实际上，大多数 React 应用只调用一次 ReactDOM.render()。下一节我们将学习这样的代码如何封装进有[状态的组件](https://reactjs.org/docs/state-and-lifecycle.html)中。
> 我建议不要跳过主题，因为她们相互关联。

### React 只更新必要的

React DOM 对比之前的哪一个的元素以及它的 children，只应用使得 DOM 达到理想状态需要的 那些 DOM 更新。

你可以使用浏览器工具来诊断[最后的示例](https://reactjs.org/redirect-to-codepen/rendering-elements/update-rendered-element)来验证上述所说的：

虽然我每秒创建了整个 UI 树，但是 React DOM 使得仅仅发生变化的文本节点更新。

根据我们的经验，思考在任何给定时间点如何显示 UI，而不是思考如何随着时间改变它，可以消除一整类 bug。

## 组件和属性

组件让你将 UI 分成独立的，可重用的部分，并独立的思考每个部分。这个页面介绍了组件的概念。你可以[这里有详细的组件 API](https://reactjs.org/docs/react-component.html)

从概念上讲，组件就像 JavaScript 函数。它接受任意的输入（叫"props"）并返回你想要显示到屏幕上的 React 元素。

### 函数和类组件

最简单定义一个组件的方式是写一个 JavaScript 函数：

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

这个函数是一个有效的 React 组件，因为它接受单个带数据的“props”（代表属性）对象并返回 React 元素。我们叫这样的组件为“函数组件”，因为它们实际上是 JavaScript 函数。

你也可以使用[ES6 类](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)定义一个组件：

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上面两个组件从 React 的视角是等价的。

函数和类组件都有一些附加的功能，我们将在下一节讨论它

### 渲染一个组件

以前，我们仅遇到代表 DOM 标签的 React 元素：

```jsx
const element = <div />;
```

然而，元素也可以代表用户定义的组件：

```jsx
const element = <Welcome name="Sara" />;
```

当 React 看到一个元素时用户定义的组件，它会传递 JSX attributes 和 children 作为单独的一个对象传递给这个组件。我们叫这个对象为 props。

例如，这个代码在页面上渲染“Hello, Sara”

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(element, document.getElementById("root"));
```

让我们概括一下这个案例中发生了什么：

1. 我们调用 ReactDOM.render()渲染`<Welcome name="Sara" />`元素
2. React 使用`{name: 'Sara'}`作为 props 来调用 Welcome 组件
3. 我们的 Welcome 组件返回`<h1>Hello, Sara</h1>`
4. React DOM 有效的匹配`<h1>Hello, Sara</h1>.`来更新 DOM。

> 注意：组件名一直以大写字母开头。
> React 将小写字母开头的组件认为是 DOM 标签。例如，`<div />`代表表示 HTML div 标签，但是`<Welcome />`表示一个组件并在作用域内导入 Welcome。
> 想要了解这个约定背后的信息，阅读[JSX](https://reactjs.org/docs/jsx-in-depth.html#user-defined-components-must-be-capitalized)

### 组合组件

组件可以在它们的输出中引用其他组件。这让我们可以对任何层次的细节都可以用相同的组件抽象。A button, a form, a dialog, a screen: 在 React 应用中，所有都表示为组件。

例如，我们创建一个 App 组件，渲染 Welcome 多次。

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

典型的，新 React 应用的顶部只有一个 App 组件。然而，如果你集成 React 到已存在的应用，你可能会从一个像 Button 这样的很小的组件开始自下而上逐步进入视图结构顶部。

### 提取组件

不要害怕拆分组件到更小的组件。

例如，思考下面的 Comment 组件：

```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img
          className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">{props.author.name}</div>
      </div>
      <div className="Comment-text">{props.text}</div>
      <div className="Comment-date">{formatDate(props.date)}</div>
    </div>
  );
}
```

它接受 author(对象),text(字符串),date(日期)作为 props，并在社交媒体网站上描述为一个评论组件

因为所有都嵌套，所以这个组件改动其他很棘手。并且也很难独立的重用它的各个部分。让我们从它提取出更小的组件。

首先，我们提取 Avatar：

```jsx
function Avatar(props) {
  return (
    <img className="Avatar" src={props.user.avatarUrl} alt={props.user.name} />
  );
}
```

这个 Avatar 不需要知道它将被渲染进 Comment。这是为什么我给他的 prop 一个更广泛的名字：user 而不是 author。

我们建议命名 props 应该从组件自身的角度而不是使用它的上下文。

我们现在可以稍微简化一下 Comment：

```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">{props.author.name}</div>
      </div>
      <div className="Comment-text">{props.text}</div>
      <div className="Comment-date">{formatDate(props.date)}</div>
    </div>
  );
}
```

下面，我们将提取 UserInfo 组件渲染 Avatar 和 user 的 name：

```jsx
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">{props.user.name}</div>
    </div>
  );
}
```

让我们进一步简化 Comment：

```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">{props.text}</div>
      <div className="Comment-date">{formatDate(props.date)}</div>
    </div>
  );
}
```

提取组件可能看起来是一个繁重的工作，但是拥有可重用组件的面板在更大的应用上会带来回报。一个好的经验是如果你 UI 的部分是被多次使用(Button,Panel,Avatar)，或者它自身足够复杂（App,FeedStory,Comment），那么它是重用组件的不错选择。

### Props 是只读的

无论你声明的组件是函数的或者是类的，它自己的 props 一定不能被修改。思考下面的 sum 函数：

```js
function sum(a, b) {
  return a + b;
}
```

这个函数是纯函数，因为它不会修改自己的输入，并且每次针对相同的输入返回相同的结果。

相反，下面的函数是不纯的，因为它修改了自己是输入：

```js
function withdraw(account, amount) {
  account.total -= amount;
}
```

React 是很灵活的但是它有一条严格的规则：

**对于他们的 Props,所有的组件一定要像纯函数一样工作**

当然，应用 UI 是动态并随着时间变化。下一节，我们将介绍新的概念 state。State 允许 React 组件跟着用户的操作随着时间去修改它们的输出，网络响应，或者其他，这些并不违反此规则。

## 状态和生命周期

这个页面介绍了在 React 组件中状态和生命周期的概念。你可以从[组件 API 文档中](https://reactjs.org/docs/react-component.html)找到更详细的说明。

思考上一节的时钟案例。在渲染元素中，我们只有一个方式更新 UI。我们通过调用 ReactDOM.reader()来改变渲染输出：

```jsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById("root"));
}

setInterval(tick, 1000);
```

这一节，我们将学习如何使得时钟组件真正的复用和封装。它将设置自己的 timer 并每秒更新自己。

我们通过如何封装时钟开始学习：

```jsx
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(<Clock date={new Date()} />, document.getElementById("root"));
}

setInterval(tick, 1000);
```

但是，它缺失了一个关键的要求：实际上时钟设置 timer 并每秒更新自己应该是时钟自己的实现细节。

理想情况下，我们想要写一次 Clock，它会更新自己：

```jsx
ReactDOM.render(<Clock />, document.getElementById("root"));
```

为了实现它，我们需要为 Clock 组件添加 state。

State 类似于 props，但是它是私有的，完全由组件自己控制。

### 将函数转成类

你讲函数组件 Clock 转变成类组件，需要 5 步：

1. 创建 ES6 类，使用相同的名字，继承自 React.Component
2. 添加一个 render 空方法
3. 将函数的主体迁移到 render 函数中
4. 在 render 函数中替换 props 为 this.props
5. 删除剩余的空函数声明

```jsx
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

Clock 显示是类定义而不是函数。

这个 render 函数在每次更新时调用，但是只会将<Clock/>渲染到相同的 DOM 节点，只有一个 Clock 类是实例被使用。这让我们可以使用附加的功能比如本地 state 和生命周期函数。

### 给类添加本地 State

我们将 date 从 props 移动到 state 需要 3 补：

1. 在 render 函数中替换 this.props.date 成 this.state.date

```jsx
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

2. 添加[类构造函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor)初始化 this.state

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

注意我们如何传递 props 给基类构造函数：

```js
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
```

类组件应该一直传递 props 给基类构造函数

3. 从`<Clock />`元素中删除 date prop

```jsx
ReactDOM.render(<Clock />, document.getElementById("root"));
```

我们稍后将 timer 代码添加会组件本身。

结果看起来这样：

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(<Clock />, document.getElementById("root"));
```

下面，我们将让 Clock 配置自己的 timer 并每秒更新自己。

### 给类添加生命周期函数

在具有许多应用的组件中，当它们销毁时释放被组件占用的资源非常重要。

我们想要 Clock 第一次渲染到 DOM 之后设置 timer。这种情况被称之为挂载。

我们想在 Clock 的 DOM 被删除的时候清除 timer。这种被称为卸载。

我们声明一些指定方法在组件挂载和卸载的时候运行一些代码。

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }

  componentDidMount() {}

  componentWillUnmount() {}

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

这些方法被称为生命周期函数

componentDidMount 函数运行在组件输出被渲染到 DOM 之后。这是设置 timer 的好地方：

```js
 componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
```

注意我们立刻保留 timer ID 到 this 上(this.timerID)。

虽然 this.props 由 React 自己设置，并且 this.state 有特殊含义。如果你需要存储某些东西并不想让它参与数据流（比如 timer ID），你可以自由的将类中添加额外的字段。

我们将在 componentWillUnmount 生命周期函数中拆除 timer：

```jsx
 componentWillUnmount() {
    clearInterval(this.timerID);
  }
```

最后，我们将实现 tick 函数，用来让 Clock 组件每秒都运行

它将使用 this.setState()来更新调度组件的本地 state。

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

现在时钟每秒都在变更。

让我们快速的概括发生了什么，这些方法调用的顺序：

1. 当`<Clock />`被传递给 ReactDOM.render()，React 调用 Clock 组件的构造函数。由于 Clock 需要显示当前时间，它会使用包含当前时间的对象初始化 this.state。我们稍后更新这个 state。
2. React 然后调用 Clock 组件的 render 方法。这是 React 学习如何在屏幕显示内容的方式。React 更新 DOM 匹配 Clock 渲染输出。
3. 当 Clock 输出被插入到 DOM 上，React 调用 componentDidMount 生命周期方法。在里面，Clock 组件告诉浏览器设置 timer 来每秒调用组件的 tick 方法。
4. 浏览器每秒调用 tick 方法。在里面，Clock 组件通过调用 setState()传递包含当前时间的对象来调度 UI 刷新。感谢 setState 调用，React 知道 state 发生变更，然后再次调用 render 方法告诉屏幕内容刷新。这个时候，render 方法的 this.state.date 将不同，所以 render 输出将包含更新后的时间。React 响应的更新 DOM。
5. 如果 Clock 组件从 DOM 中删除，React 调用 componentWillUnmount 方法，所以 timer 停止。

### 正确的使用状态

这里有三件关于 setState 需要知道的事情。

#### 不要直接修改 State

例如，它将不会触发组件重新渲染：

```
// Wrong
this.state.comment = 'Hello';
```

应该使用 setState():

```
// Correct
this.setState({comment: 'Hello'});
```

只有构造函数允许赋值给 this.state

#### State 更新可能是异步的

React 为了性能可能将多个 setState 调用批处理到单个更新上。

因为 this.props 和 this.state 可能异步更新，你不应该依赖它们的值来计算下一个 state。

举例，下面更新 counter 可能失败：

```js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

为了修复它，使用 setState 接受一个函数而不是对象。这个函数接受上一个 state 作为第一个参数，更新的 props 作为第二个参数。

```js
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment,
}));
```

我们上面使用[箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，它和常规函数一样工作：

```js
// Correct
this.setState(function (state, props) {
  return {
    counter: state.counter + props.increment,
  };
});
```

#### 状态更新已合并

当你调用 setState()，React 合并你提供的对象到当前的 state。

举例，你的 state 可能包含一些独立的变量：

```js
  constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }

```

然后你可以独立的调用 setState 来单独的更新它们：

```js
componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```

这个合并是浅层的，所以 this.setState({comments})完整的保留了 this.state.posts，但是完全的替换了 this.state.comments。

### 向下的数据流

无论父组件还是子组件都不知道某个组件是有状态还是无状态，它们也不知道它是函数还是类定义的。

这是为什么状态经常被称为本地或封装。它无法被其他任何组件访问，只能被自身所有和设置。

一个组件可能选择将自己的状态向下作为 props 传递给子组件：

```jsx
<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```

同样也在用户定义的组件生效：

```jsx
<FormattedDate date={this.state.date} />
```

这个 FormattedDate 组件应该接受 props 来的 date，它不需要知道它来自 Clock 的状态、Clock 的 props 或者手动输入的：

```jsx
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```

这通常被称为“自顶向下”或者“单向”数据流。任何状态始终属于某个特定组件，并且从该状态派生的数据或 UI 都只能影响树中组件和它下方的组件。

如果你将组件树想象成 props 的瀑布流，每个组件的状态就像额外的水源，它在任意点加入瀑布，但也向下流动。

为了证明所有组件是纯粹隔离的，我们创建了一个 App 组件并渲染 3 个`<Clock>`：

```jsx
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```

每个 Clock 设置自己的 timer 并独立的更新。

在 React 应用中，组件是有状态还是无状态的，它的实现细节随着时间变化可能发生许多改动。你可以在有状态的组件中使用无状态的组件，反之亦然。

## 处理事件

给 React 元素处理事件类似于给 DOM 元素处理事件。但是有一些语法有区别：

- react 事件使用驼峰命名，而不是小写
- 你传递给 JSX 的是函数而不是字符串

举例，HTML：

```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

react 有简单的区别：

```js
<button onClick={activateLasers}>Activate Lasers</button>
```

另外不同的是你无法 return false 在 React 中阻止默认行为。你必须显示的调用 preventDefault。举例，在 plain HTML，阻止 link 打开新页面的默认行为，你可以这么写：

```html
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```

在 React 中，应该这么写：

```jsx
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log("The link was clicked.");
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

这里，e 是合成的事件。React 根据[W3C 规范](https://www.w3.org/TR/DOM-Level-3-Events/)来定义这些合成事件，所以你不需要担心夸浏览器兼容问题，看[SyntheticEvent](https://reactjs.org/docs/events.html)文档学习更多相关知识。

使用 React 时，你通常不需要添加 addEventListener 去监听 DOM 元素创建完成。你应该监听元素第一次渲染完成。

当你用[ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)定义一个组件，常见范式是事件处理器是 class 的一个函数。举例，这个 Toggle 组件渲染 button 让用户切换“ON”和“OFF”的状态：

```jsx
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isToggleOn: true };

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState((state) => ({
      isToggleOn: !state.isToggleOn,
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? "ON" : "OFF"}
      </button>
    );
  }
}

ReactDOM.render(<Toggle />, document.getElementById("root"));
```

你必须关心 JSX 回调中的 this 含义。在 JavaScript 中，class 方法不是默认[绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind)的。如果你忘记 bindthis.handleClick 并传递它给 onClick，当方法被实际调用时,this 是 undefined。

这不是 React 指定的行为；它是[函数在 JavaScript 中的工作方式](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/)。通常，如果你更喜欢方法不带()，像 nClick={this.handleClick}，应该要绑定它。

如果 bind 让你烦恼，这里有两个方式可以解决这个问题。如果你使用实验性的[公开的 class 字段语法](https://babeljs.io/docs/plugins/transform-class-properties/)，你应该使用 class 字段正确的绑定回调。

```jsx
class LoggingButton extends React.Component {
  // 这个语法保证this绑定到handleClick
  // Warning: 这是 *实验性* 语法.
  handleClick = () => {
    console.log("this is:", this);
  };

  render() {
    return <button onClick={this.handleClick}>Click me</button>;
  }
}
```

[Create React App](https://github.com/facebookincubator/create-react-app)默认开启了这个语法

如果你不使用 class 字段语法，你可以使用箭头函数：

```jsx
class LoggingButton extends React.Component {
  handleClick() {
    console.log("this is:", this);
  }

  render() {
    // 这个语法保证 `this` 被绑定到handleClick
    return <button onClick={() => this.handleClick()}>Click me</button>;
  }
}
```

这个语法的问题是每次 LoggingButton 渲染时都会创建不同的 callback 函数。大多数情况下，没有关系。但是，如果回调是作为 prop 传递给低级别的组件，这些组件可能会做额外的重复渲染。我们通常建议在构建函数中绑定它或者使用 class 字段语法，来避免这种性能问题。

### 传递参数给事件处理器

循环内，通常希望将额外的参数传递给事件处理器。举例，如果 id 是 row ID，以下任何一种方法都可以：

```jsx
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

上面两个是等效的，分别使用[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)和[Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)。

这两种情况下，e 代表 React 事件作为 id 之后的第二个参数传递。使用箭头函数，我们需要显示的传递，但是使用 bind， 任何参数将自动转发。

## 条件渲染

在 React 中，你可以创建不同的组件封装不同的行为。然后你可以根据应用的状态来渲染其中一些。

在 React 中条件渲染工作和 JavaScript 工作方式一样。使用 JavaScript 操作符 if 或者条件操作符来创建代表当前状态的元素，并让 React 更新 UI 匹配它们。

考虑这两个组件：

```jsx
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}
```

我将创建 Greeting 组件格局 user 是否登录来显示这两个组件之一：

```jsx
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById("root")
);
```

这个案例根据 isLoggedIn prop 的值渲染不同的 greeting。

### 元素变量

你可以使用变量来保存元素。它可以帮助你有条件渲染组件中的一部分，其余的输出不变。

考虑这两个新组件代表登出和登录按钮：

```jsx
function LoginButton(props) {
  return <button onClick={props.onClick}>Login</button>;
}

function LogoutButton(props) {
  return <button onClick={props.onClick}>Logout</button>;
}
```

在下面的案例，我们将创建[无状态组件](https://reactjs.org/docs/state-and-lifecycle.html#adding-local-state-to-a-class)叫 LoginControl。

它将根据它当前状态渲染`<LoginButton />` or `<LogoutButton />`。它也渲染来自上一个案例的`<Greeting />`。

```jsx
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = { isLoggedIn: false };
  }

  handleLoginClick() {
    this.setState({ isLoggedIn: true });
  }

  handleLogoutClick() {
    this.setState({ isLoggedIn: false });
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(<LoginControl />, document.getElementById("root"));
```

声明变量并使用 if 语句能够很好的条件渲染组件，有时候你可能想要用更短的语法。这里有两个在 JSX 中内联的条件表达式，解释如下：

### 内联的 if 逻辑和&&操作符

你可以在 JSX 的大括号中嵌入[任意的表达式](https://reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx)。这包括 JavaScript 逻辑&&操作符。条件的包裹一个元素可以很方便：

```jsx
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 && (
        <h2>You have {unreadMessages.length} unread messages.</h2>
      )}
    </div>
  );
}

const messages = ["React", "Re: React", "Re:Re: React"];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById("root")
);
```

之所以有效，因为在 JavaScript 中 true && expression 总是变成 expression,并且 false && expression 总是 false。

因此，如果条件是 true，&& 后面的元素会立刻输出。如果是 false，React 将忽略并跳过。

### 内联的 if-else 条件操作符

用于内联渲染元素的另一种方法是用 JavaScript 条件符号 condition?true:false。

在下面的案例，我们使用它来条件渲染小文本模块。

```jsx
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}
```

尽管表示的不是很明显，但是它可以使用大的表达式：

```jsx
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn
        ? <LogoutButton onClick={this.handleLogoutClick} />
        : <LoginButton onClick={this.handleLoginClick} />
      }
    </div>
  );
}
```

就像 JavaScript 一样，你可以根据你团队的可读性来选择合适的排版样式。记住只要条件变得太复杂，可能就是提取组件的好时机。

### 阻止组件渲染

在很少的情况下你可能想要隐藏自己，即使被其他组件渲染。返回 null 来代替渲染输出。

在下面的案例中，`<WarningBanner />`依赖 prop 的 warn 值来渲染。如果值是 false，然后这个组件就不会渲染：

```jsx
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return <div className="warning">Warning!</div>;
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = { showWarning: true };
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState((state) => ({
      showWarning: !state.showWarning,
    }));
  }

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? "Hide" : "Show"}
        </button>
      </div>
    );
  }
}

ReactDOM.render(<Page />, document.getElementById("root"));
```

组件 render 方法返回 null 不会印象组件生命周期方法的触发。componentDidUpdate 仍然会被被调用。

## 列表和 keys

首先让我们回顾一下，如何在 JavaScript 中转换列表。

下面给我们的代码，我们使用 map()函数将 numbers 数组的值转换成 2 倍。我们通过 map()返回一个新数组给 doubled 变量，然后打印它：

```js
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```

这个代码输出`[2, 4, 6, 8, 10]`到控制台。

在 React 中，将数组转换成元素列表几乎相同。

### 渲染多个组件

你可以构建元素的集合，然后在 JSX 中用大括号{}包裹它们。

下面，我们使用 JavaScript map()函数循环 numbers 数组。每个 item 返回`<li>`元素。最后我们赋值元素数组结果给 listItems:

```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) => <li>{number}</li>);
```

我们将整个 listItems 数组包含在`<ul>`元素内，然后渲染它到 DOM 中：

```jsx
ReactDOM.render(<ul>{listItems}</ul>, document.getElementById("root"));
```

这段代码显示数字到 1-5 的符号列表。

### 基础的 List 组件

通常你应该在组件中渲染列表。

我们可以重构之前的案例为一个接受 numbers 数组的组件并输出 list 元素。

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) => <li>{number}</li>);
  return <ul>{listItems}</ul>;
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById("root")
);
```

当你运行这段代码，系统会警告应该为 list item 提供 key。当创建列表元素时，key 是你需要包括的特殊字符串属性。我们将在下一节讨论为什么它如此重要。

为 numbers.map()中的列表项赋值 key，修复这个缺失 key 的问题。

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) => (
    <li key={number.toString()}>{number}</li>
  ));
  return <ul>{listItems}</ul>;
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById("root")
);
```

### Keys

keys 帮助 React 识别哪些 item 发生更改，添加或者删除。应该为数组元素分配 keys，让元素能够稳定的被识别：

```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) => (
  <li key={number.toString()}>{number}</li>
));
```

选 key 的最好方式是使用独一无二的字符串识别 list 项和它相邻的项：

```jsx
const todoItems = todos.map((todo) => <li key={todo.id}>{todo.text}</li>);
```

你没有使用稳定 id 来渲染 item 时，最后一招，你可以使用 item 的 index 作为 keys：

```jsx
const todoItems = todos.map((todo, index) => (
  // 仅当item没有稳定的id时
  <li key={index}>{todo.text}</li>
));
```

如果 items 的顺序可能发生变化，我们不建议使用 index 作为 keys。这可能导致性能的负面影响并且会对组件状态造成问题。查询 Robin Pokorny 的文章[深度解析为什么使用 index 作为 key 会带来负面影响](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)

如果你有兴趣了解更多，这里有一篇[深度解析为什么 keys 是必须的](https://reactjs.org/docs/reconciliation.html#recursing-on-children)

### 用 key 提取组件

keys 仅在数组周围的上下文中才有效。

举例，如果提取 ListItem 组件，你应该保留 key 在`<ListItem />`数组中而不是 ListItem 组件里的`<li>`元素上。

例子：错误使用 key

```jsx
function ListItem(props) {
  const value = props.value;
  return (
    // Wrong! 不应该在这里指定key:
    <li key={value.toString()}>{value}</li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) => (
    // Wrong! key应该在这里指定:
    <ListItem value={number} />
  ));
  return <ul>{listItems}</ul>;
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById("root")
);
```

例子：正确使用 key

```jsx
function ListItem(props) {
  // Correct! 不需要在这里指定key:
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) => (
    // Correct! key应该在数组中指定.
    <ListItem key={number.toString()} value={number} />
  ));
  return <ul>{listItems}</ul>;
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById("root")
);
```

一个好的经验是 map()中的元素都需要 keys。

### key 一定在相邻 key 中唯一

在数组中使用 keys 应该让它们相邻唯一。但是不需要它们在全局唯一。我们可以在不同的数组中使用相同的 keys。

```jsx
function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
  const content = props.posts.map((post) => (
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  ));
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  { id: 1, title: "Hello World", content: "Welcome to learning React!" },
  { id: 2, title: "Installation", content: "You can install React from npm." },
];
ReactDOM.render(<Blog posts={posts} />, document.getElementById("root"));
```

keys 是给 React 的提示，它们不会传递给你的组件。如果你的组件需要相同的值，使用不同的名字显示的作为 prop 传递它：

```jsx
const content = posts.map((post) => (
  <Post key={post.id} id={post.id} title={post.title} />
));
```

上面的例子，Post 组件应该读 props.id，而不是 props.key。

### 在 JSX 中嵌入 map()

在上面的例子中我们独立声明了 listItems 变量，然后把它包含到 JSX 中：

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) => (
    <ListItem key={number.toString()} value={number} />
  ));
  return <ul>{listItems}</ul>;
}
```

JSX 允许[嵌入任何表达式](https://reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx)到大括号中，所以我们应该内联 map()结果：

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) => (
        <ListItem key={number.toString()} value={number} />
      ))}
    </ul>
  );
}
```

有时候这么做会使得代码更清晰，但是这种风格也会被滥用。像在 JavaScript 中，由你决定值得提取变量以提高可读性。记住如果 map()的主体过于嵌套，这应该是提取组件的好时机。

## 表单

> HTML 表单元素和 React 的其他 DOM 元素工作方式有点不一样，因为表单元素原生保持一些内部状态。举例，这个表单在纯 HTML 中接受单个 name：

```jsx
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

当用户提交表单，这个表单有默认 HTML 表单的行为，即浏览到新页面。如果你想要在 React 使用这个行为，这就可以了。但是大多数情况下，更方便的是用 JavaScript 函数来处理表单提交，可以访问用户输入这个表单的数据。标准实现这个的技术叫做“受控组件”。

### 受控组件

在 HTML 中，表单元素像`<input>`, `<textarea>`, and `<select>`通常维护自己的状态并且根据用户输入来更新它。在 react 中，易变的状态通常保存在组件的 state 中，只被 setState()更新。

我们可以通过将 React 状态变成“单一事实真像”来合并他们俩。然后 React 渲染的表单控制后续用户输入发生的事情。输入表单元素的值受 由 React 这样方式的受控组件控制。

举例，如果我们想让上一个列子在提交时打印 name，我们可以将表单写成受控组件：

```jsx
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: "" };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({ value: event.target.value });
  }

  handleSubmit(event) {
    alert("A name was submitted: " + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input
            type="text"
            value={this.state.value}
            onChange={this.handleChange}
          />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

因为 value 属性是在我们表单元素上设置的，显示的 value 一直是 this.state.value，使得 React 状态称为了真像的来源。因为每次按键运行 handleChange 来更新 React 状态，显示的 value 由用户输入来更新。

使用受控组件，输入的 value 一直由 React 状态驱动。虽然这意味着你必须输入更多的代码，但是现在你也可以传递值给其他 UI 元素，或者从其他事件处理器中重置它。

### textarea 标签

在 HTML 中，`<textarea>`元素由它的 children 来定义 text：

```jsx
<textarea>Hello there, this is some text in a text area</textarea>
```

在 React 中，`<textarea>`使用 value 属性来替换它。这种方式，使用`<textarea>`的表单可以与使用单行 input 的表单一样。

```jsx
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: "Please write an essay about your favorite DOM element.",
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({ value: event.target.value });
  }

  handleSubmit(event) {
    alert("An essay was submitted: " + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

注意 this.state.value 在构造函数中初始化，所以文本区域开始带一些文本在其中。

### select 标签

在 HTML 中，`<select>`创建一个下拉的列表。举例，这个 HTML 创建了一个下拉列表的 flavors：

```jsx
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">
    Coconut
  </option>
  <option value="mango">Mango</option>
</select>
```

注意 Coconut 选项是初始被选中的，因为这个 selected 属性。React 中，用根的 Select 标签的 value 属性来替换选项的 selected 属性。在受控组件中非常方便，因为你只需要在一个地方更新它。举例：

```jsx
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: "coconut" };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({ value: event.target.value });
  }

  handleSubmit(event) {
    alert("Your favorite flavor is: " + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

总的来说，这使得`<input type="text">`, `<textarea>`, and `<select>`所有工作都非常相似 - 它们都接受一个 value 属性，你可以它来实现一个受控组件。

> 注意：
> 你可以传递一个数组给 value 属性，允许你在 select 标签中选择多个选项：

```jsx
<select multiple={true} value={['B', 'C']}>
```

### 文件输入标签

在 HTML 中，一个`<input type="file">`让用户从它们的设备存储中选择一个或多个文件上传到服务器或者由 JavaScript 通过[File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications)操作

```html
<input type="file" />
```

因为它的值是只读的，所以它在 React 中是不受控的组件。稍后在文档中和其他不受控组件一起讨论。

### 处理多个输入

当你需要处理多个受控的 input 元素，你可以给每个元素添加 name 属性，并让处理器函数根据 event.target.name 的值来选择要执行的操作。

```jsx
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2,
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.name === "isGoing" ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value,
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange}
          />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange}
          />
        </label>
      </form>
    );
  }
}
```

现在我们使用 ES6 的[计算属性 name](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)语法来更新 state key 为给定的输入 name。

```js
this.setState({
  [name]: value,
});
```

它和 ES5 这段代码等效：

```js
var partialState = {};
partialState[name] = value;
this.setState(partialState);
```

另外，因为 setState()自动[合并部分 state 到当前 state](https://reactjs.org/docs/state-and-lifecycle.html#state-updates-are-merged)，我们只需要用修改过的部分来调用它。

### 控制输入 Null 值

在受控组件上指定 value 属性值会阻止用户更改这个 input，除非你希望这么做。如果你指定了 value，但是 input 仍然可以被编辑，你可能偶然的设置 value 为 undefined 或者 null。

下面代码演示它。（输入首先被锁定，但经过短暂的延迟后变成可编辑）

```jsx
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function () {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);
```

### 受控组件的替代品

使用受控组件有时可能很乏味，因为你可能需要为你每种数据更改方式写一个事件处理器，还需要通过 React 组件来传递所有输入状态。当你将已存在的代码转成 React，或者使用非 React 库集成到 React 应用时它会变得特别烦人。在这些场景下，你可能想看下[非受控组件](https://reactjs.org/docs/uncontrolled-components.html)，这是一种实现 input 表单的另外一种技术。

### 全面解决方案

如果你在查找一个完美的解决方案包括验证，跟踪访问的字段以及处理表单提交，[Formik](https://jaredpalmer.com/formik)是一个不错的选择。然而，它基于受控组件和管理状态相同的原理创建- 所以不要忽略学习它们。

## 状态提升

> 通常，一些组件需要反映相同的变化数据。我们建议提升共享的状态到它们最近的父组件中。让我们看看它是如何运作的。

在这节中，我们创建了一个温度计来计算在给定气压下水是否会沸腾。

我们从 BoilingVerdict 的组件开始。它接受 celsius 温度作为 prop，然后打印它是否足以沸腾水。

```jsx
function BoilingVerdict(props) {
  if (props.celsius >= 100) {
    return <p>The water would boil.</p>;
  }
  return <p>The water would not boil.</p>;
}
```

下面，我们创建了一个名为 Calculator 组件。它渲染`<input>`组件让你输入水温，保存它的值到 this.state.temperature。

另外，它根据当前输入值渲染 BoilingVerdict。

```jsx
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = { temperature: "" };
  }

  handleChange(e) {
    this.setState({ temperature: e.target.value });
  }

  render() {
    const temperature = this.state.temperature;
    return (
      <fieldset>
        <legend>Enter temperature in Celsius:</legend>
        <input value={temperature} onChange={this.handleChange} />
        <BoilingVerdict celsius={parseFloat(temperature)} />
      </fieldset>
    );
  }
}
```

### 添加第二个输入

我们新的要求是，除了已有的摄氏度输入，我们还提供华氏度输入，并保持它们的同步。

我们以从 Calculator 中提取 TemperatureInput 组件开始。我们将添加新的 scale prop 到其中，它可以是"c"或者"f"：

```jsx
const scaleNames = {
  c: "Celsius",
  f: "Fahrenheit",
};

class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = { temperature: "" };
  }

  handleChange(e) {
    this.setState({ temperature: e.target.value });
  }

  render() {
    const temperature = this.state.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature} onChange={this.handleChange} />
      </fieldset>
    );
  }
}
```

我们现在可以改变 Calculator 渲染独立的温度输入框：

```jsx
class Calculator extends React.Component {
  render() {
    return (
      <div>
        <TemperatureInput scale="c" />
        <TemperatureInput scale="f" />
      </div>
    );
  }
}
```

现在我有两个输入框，但是当你向其中一个输入温度时，另外一个不会更新。它与我们的要求相矛盾：我们想要保持两个同步。

我们无法在 Calculator 中显示 BoilingVerdict。因为 Calculator 无法知道当前的温度，它们被隐藏在 TemperatureInput 内部。

### 编写转换功能

首先，我们将写两个函数分别是将摄氏度转成华氏度，以及相反过程：

```jsx
function toCelsius(fahrenheit) {
  return ((fahrenheit - 32) * 5) / 9;
}

function toFahrenheit(celsius) {
  return (celsius * 9) / 5 + 32;
}
```

这两个函数转换数字。我们将写另外一个函数接受温度字符串和转换函数作为参数，并返回字符串。我们将使用它来计算根据另外一个值来计算它的值。

```js
function tryConvert(temperature, convert) {
  const input = parseFloat(temperature);
  if (Number.isNaN(input)) {
    return "";
  }
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  return rounded.toString();
}
```

例如，`tryConvert('abc', toCelsius)`返回空字符串，而`tryConvert('10.22', toFahrenheit)`返回 50.396。

### 提升状态

当前，两个 TemperatureInput 组件独立的保存它们的值到本地 state 中：

```js
class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {temperature: ''};
  }

  handleChange(e) {
    this.setState({temperature: e.target.value});
  }

  render() {
    const temperature = this.state.temperature;
    // ...
```

然而，我们想让输入组件互相同步。当我们更新摄氏度输入，华氏度输入组件反映转化后的温度，反之亦然。

在 React 中，共享状态是通过根据需要将它们移到最近的公共父组件中。这叫做”状态提升“。我们将从 TemperatureInput 中删除本地状态并将它们移到 Calculator 中。

如果 Calculator 拥有共享状态，它会变成两个输入组件的当前温度的”真相源头“。它可以指导两个拥有一致的值。因为两个 TemperatureInput 组件的 props 都来自原相同的父组件 Calculator，这两个组件将一直是同步的。

让我们来看下它是如何一步步的工作。

首先，我们将在 TemperatureInput 组件中使用 this.props.temperature 来替换 this.state.temperature。至此，让我们假装 this.props.temperature 已经存在，虽然未来我们将从 Calculator 传递它：

```jsx
render() {
    // Before: const temperature = this.state.temperature;
    const temperature = this.props.temperature;
    // ...
```

我们知道[props 是只读的](https://reactjs.org/docs/components-and-props.html#props-are-read-only)。当 temperature 在本地状态中，TemperatureInput 可以调用`this.setState()`来更改它。然而，现在 temperature 来自于父组件传递的 prop，TemperatureInput 组件无法控制它。

在 React 中，它通常让组件受控来解决。就像 DOM`<input>`接受 value 和 onChange prop，所以可以自定义 TemperatureInput 组件接受 temperature 和 onTemperatureChange 来自父组件 Calculator 的 props。

现在，当 TemperatureInput 组件想要更新它的 temperature 时，它调用`this.props.onTemperatureChange`：

```js
  handleChange(e) {
    // Before: this.setState({temperature: e.target.value});
    this.props.onTemperatureChange(e.target.value);
    // ...

```

> 注意：
> 在自定义组件中 temperature 和 onTemperatureChange 属性名没有特殊的意义。我们可以用任意的名字称呼它们，像 value 和 onChange 的名字是一种常见的约束。

Calculator 组件将提供 onTemperatureChange 和 temperature 属性。它将通过修改自己的本地状态来处理更改，用新值来渲染两个输入组件。我们将看到新 Calculator 实现的非常快。

深入了解 Calculator 更改之前，让我们来概括 TemperatureInput 的更改。我们删除了本地状态，改用 this.state.temperature 而不是 this.state.temperature。当我们想要更改时我们调用`this.props.onTemperatureChange()`来代替`this.setState()`，它由 Calculator 提供：

```jsx
class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(e) {
    this.props.onTemperatureChange(e.target.value);
  }

  render() {
    const temperature = this.props.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature} onChange={this.handleChange} />
      </fieldset>
    );
  }
}
```

现在我们转到 Calculator 组件。

我们将存储当前输入组件的 temperature 和 scale 到本地状态。它是我们从输入组件中提升的状态，它将作为两个组件的"真相源头"。它是我们需要知道渲染两个输入组件的所有数据的最小表示形式。

举例，如果我们输入 37 到摄氏度输入组件，Calculator 组件状态将是：

```json
{
  "temperature": "37",
  "scale": "c"
}
```

如果稍后修改华摄度字段为 212，Calculator 状态值变成：

```json
{
  "temperature": "212",
  "scale": "f"
}
```

我们可以存储两个输入的值，但是实际证明不需要。存储最近修改输入的值和 scale 足够了。我们可以根据当前的 temperature 和 scale 来推断另外一个输入组件的值。

这两个输入是同步的，因为它们的值是被相同的 state 计算出来的：

```jsx
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
    this.state = { temperature: "", scale: "c" };
  }

  handleCelsiusChange(temperature) {
    this.setState({ scale: "c", temperature });
  }

  handleFahrenheitChange(temperature) {
    this.setState({ scale: "f", temperature });
  }

  render() {
    const scale = this.state.scale;
    const temperature = this.state.temperature;
    const celsius =
      scale === "f" ? tryConvert(temperature, toCelsius) : temperature;
    const fahrenheit =
      scale === "c" ? tryConvert(temperature, toFahrenheit) : temperature;

    return (
      <div>
        <TemperatureInput
          scale="c"
          temperature={celsius}
          onTemperatureChange={this.handleCelsiusChange}
        />
        <TemperatureInput
          scale="f"
          temperature={fahrenheit}
          onTemperatureChange={this.handleFahrenheitChange}
        />
        <BoilingVerdict celsius={parseFloat(celsius)} />
      </div>
    );
  }
}
```

现在，无论你编辑哪个输入框，Calculator 的 this.state.temperature 和 this.state.scale 都会被更新。其中一个输入框保留用户输入，另外一个输入值始终根据这个值来重新计算。

让我们概括当你修改一个输入框时发生了什么：

- React 调用 DOM`<input>`的 onChange 函数。在我们的例子中，在 TemperatureInput 组件中它是 handleChange 方法
- 在 TemperatureInput 组件中的 handleChange 方法使用新的值调用`this.props.onTemperatureChange()`。它的 props 包含 onTemperatureChange，由父组件 Calculator 提供。
- 起初渲染时，Calculator 要指定摄氏度 TemperatureInput 组件的 onTemperatureChange 是 Calculator 的 handleCelsiusChange 方法，华摄度 TemperatureInput 组件的 onTemperatureChange 是 Calculator 的 handleFahrenheitChange 方法。所以我们修改的输入组件调用 Calculator 其中任何一个。
- 在这些方法中，Calculator 通过调用`this.setState()`设置当前修改过的 scale 和 input 来告诉 React 重新渲染它自己。
- React 调用 Calculator 组件的 render 方法去 UI 呈现。两个输入组件的值基于当前温度和 scale 重新计算。在此执行温度转换。
- React 根据 Calculator 提供的新值来分别调用 TemperatureInput 组件的 render 方法去 UI 呈现。
- React 调用 BoilingVerdict 组件的 render 方法，传递华摄度的温度给它。
- React DOM 根据输入的值是否匹配水沸腾，并将结果更新回 DOM。我刚编辑输入框接收的当前值，另一个输入框更新了转化之后的温度值。

每次更新都会走这些步骤，所以输入框一直同步。

### 学习总结

在 React 应用中任何可变数据应该只有唯一数据源。通常，state 是第一个被添加到需要渲染数据的组件中。然后，如果其他组件也需要它，你可以提升它到最近的公共父组件中。你应该依赖[自顶向下的数据流](https://reactjs.org/docs/state-and-lifecycle.html#the-data-flows-down)，而不是在不同组件之间同步 state。

提升状态会比双向绑定方式写更多的模板代码，但是好处是，花费很少的工作来找到和隔离 bug。因为由于状态只存在于组件内，并且只有组件可以更改它，bug 出现的范围很少。另外，你可以实现任何自定义逻辑来转换用户输入。

如果有些东西可以被 props 和 state 同时驱动，它就不应该存在 state 中。举例，没有存储 celsiusValue 和 fahrenheitValue，我们只存储了最近修改的 temperature 和它的 scale。另外的输入框的值可以被 render()函数计算出来。这使得我们可以清楚输入框内容，在不丢失用户输入精度的情况下应用四舍五入计算。

当你在 UI 上看到某些错误的时候，你可以使用[React Developer Tools](https://github.com/facebook/react/tree/master/packages/react-devtools)来诊断 props,逐级搜索结构树知道找到相应修改状态的组件。它让你能够跟踪 bug 的源头：
![图](https://reactjs.org/ef94afc3447d75cdc245c77efb0d63be/react-devtools-state.gif)

## 组合 vs 继承

> React 有非常强大的组合模型，我们建议使用组合而不是继承来在组件间复用代码

这一节，我们将考虑初学者使用继承时遇到些问题，并展示我们如何使用组合来解决它们的。

### 包含关系

一些组件无法提前知道它们的 children。这在 Sidebar（侧边栏）或者 Dialog 等代表盒子的组件中非常常见。

我们建议像这样的组件使用使用指定的 children 属性来传递 children 元素给它们的输出。

```jsx
function FancyBorder(props) {
  return (
    <div className={"FancyBorder FancyBorder-" + props.color}>
      {props.children}
    </div>
  );
}
```

让其他组件通过嵌套到 JSX ，可以传任意的 children 给它们。

```jsx
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">Welcome</h1>
      <p className="Dialog-message">Thank you for visiting our spacecraft!</p>
    </FancyBorder>
  );
}
```

任何在`<FancyBorder>`JSX 标签都被认为是 FancyBorder 的 children prop。因为 FancyBorder 在`<div>`中渲染`{props.children}`, 传递的元素最终出现在输出中。

很少有，有时候你需要在组件中要多个洞。这种情况你可以自己想出约定，而不是使用 children。

```jsx
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">{props.left}</div>
      <div className="SplitPane-right">{props.right}</div>
    </div>
  );
}

function App() {
  return <SplitPane left={<Contacts />} right={<Chat />} />;
}
```

React 元素像 `<Contacts />`和`<Chat />`只是对象，所以你可以跟其他数据一样作为 props 传递。这种方法可能让你想到了其他库中的槽，但是 React 没有这样的限制，你可以将任何东西作为 props 属性传递。

### 特列关系

有时候我们认为相关的组件是其他组件的特列。我们可能认为 WelcomeDialog 是 Dialog 的特列。

在 React 中，它可能被组合实现，特殊组件渲染通用组件并使用 props 配置它。

```jsx
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">{props.title}</h1>
      <p className="Dialog-message">{props.message}</p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog title="Welcome" message="Thank you for visiting our spacecraft!" />
  );
}
```

组合也适用于 classes 定义的组件：

```jsx
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">{props.title}</h1>
      <p className="Dialog-message">{props.message}</p>
      {props.children}
    </FancyBorder>
  );
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSignUp = this.handleSignUp.bind(this);
    this.state = { login: "" };
  }

  render() {
    return (
      <Dialog
        title="Mars Exploration Program"
        message="How should we refer to you?"
      >
        <input value={this.state.login} onChange={this.handleChange} />
        <button onClick={this.handleSignUp}>Sign Me Up!</button>
      </Dialog>
    );
  }

  handleChange(e) {
    this.setState({ login: e.target.value });
  }

  handleSignUp() {
    alert(`Welcome aboard, ${this.state.login}!`);
  }
}
```

### 那么继承呢？

在 Facebook,我们使用上千个组件，我们没有找到任何案例使用继承来创建层级结构。

Props 和组合让你清晰和安全方式灵活的自定义组件 UI 和行为。记住组件可能接受任意的 props，包括基础类型，React 元素或者函数。

如果你想要在组件之间复用非 UI 功能，我们建议提取它到独立的 JavaScript 模块中。组件不用继承，可以导入它使用函数，对象或者 class。

## React 哲学

> 我们认为 React 是使用 JavaScript 构建大型快速应用的首选方式。它在 Facebook 和 Instagram 上表现的很好。

React 重要方面之一是让你在构建应用时候思考应用程序。在这篇文档中，我们将引导你完成在 React 中构建一个可搜索的产品数据表格的思考过程。

### 从设计稿开始

想象我们已经有了 JSON API 和设计师的设计稿。设计稿如下：
![设计稿](https://reactjs.org/static/1071fbcc9eed01fddc115b41e193ec11/d4770/thinking-in-react-mock.png)
我们 JSON API 返回的数据格式如下：

```json
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

### 步骤 1：将 UI 拆分为组件层次结构

首先在设计稿中你将需要给每个组件（和子组件）画一个方框，并给它们名字。如果你和设计师一起工作，他们可能已经做过这样的工作了，所以跟他们进行交流！他们的 Photoshop 图层名字最终可以作为你 React 组件的名字。

但是你知道哪些应该在一个组件？使用决定创建新方法和对象相同的技术。其中一种技术是[单一职责](https://en.wikipedia.org/wiki/Single_responsibility_principle)，它就是一个组件应该只做一件事。随着组件增长，它应该被分解到更小的组件中。

因为你需要经常显示 JSON 数据模型给用户，你会发现如果你的模型构建的恰当，你的 UI （组件的的层次结构）将一一对应。因为 UI 和数据模型都倾向于相同的信息结构。分离你的 UI 到组件，每个组件都会对应数据模型的一部分。
![modal-ui map](https://reactjs.org/static/eb8bda25806a89ebdc838813bdfa3601/6b2ea/thinking-in-react-components.png)
你看到这，在我们的应用中有 5 个组件。已经将代表每个组件的数据斜体表示了。

1. **FilterableProductTable(orange):** 包含整个案例
2. **archBar (blue):** 接收所有用户输入
3. **ProductTable (green):** 根据用户的输入显示和过滤数据集合
4. **ProductCategoryRow (turquoise):** 显示么个目录的头
5. **ProductRow (red):** 每行显示一个产品

如果你查看 ProductTable，你讲会看到 table 头（包含 Name 和 Prize 的标签）不是我们自己的组件。这是偏好的问题，选择哪种方式一直有一个争论。在这个示例中，保密保留它作为 ProductTable 的一部分，因为它是数据渲染集合的一部分，这是 ProductTable 的责任。然而，如果头变得很复杂（比如，如果我们向添加用于分类的排序），创建我们自己的 ProductTableHeader 组件会更有意义。

现在我们确定了在设计稿中的组件，让我们拿牌它们到层次结构中。在设计稿中组件在另外的组件中，在层次结构中应该作为 child 显示。

- FilterableProductTable
  - SearchBar
  - ProductTable
    - ProductCategoryRow
    - ProductRow

### 步骤 2：在 React 中构建一个静态的版本

现在你有了组件的层次结构，是时候实现自己的应用。最简单的方式是构建一个版本将你的数据模型渲染成 UI，但没有交互。最好应该将这些处理过程分开，因为构建静态版本要求大量的打字而无需思考，而添加交互性要求大量思考而不是大量的打字。我们将了解原因。

要构建呈现数据模型的应用的静态版本，你需要构建可以复用其他组件的组件并使用 props 作为数据传递。props 是从父对象给子对象传递数据的一种方式。如果你熟悉 state 概念，根本不需要使用 state 来创建静态版本。State 只在交互的时候用，它是随着时间发生变化的数据。因为这是应用的静态版本，所以你不需要使用它。

你可以自顶向下或者自底向上的构建。也就是说，你可以从层次机构中较高的组件开始构建（比如，从 FilterableProductTable 开始），也可以从层次结构较低的组件开始构建（ProductRow）。在简单的示例中，通常自顶向下更简单。对于大型项目，自底向上构建写单元测试容易。

在这一步的最后，你讲拥有一个用于呈现数据模型的可复用的组件库。因为这是你应用的静态版本所以这些组件只有 render 方法。层次结构顶层的组件（FilterableProductTable）将用的数据模型作为 prop。如果更改了基础数据模型，可以再调用一次`ReactDOM.render()`，UI 将会更新。你可以看到 UI 的更新方式以及在哪里可以修改它们。React 单向数据流（也叫做单向绑定）让一切都模块化和快速。

如果你需要执行这个步骤的帮助，可以参考[React 文档](https://reactjs.org/docs/)

一个简单的插曲：Props 对比 State
React 中有两种类型的数据"model"：props 和 state。理解它们两者的区别很重要；如果不熟悉它们之间的不同可以去浏览官方 React 文档。或者看常见问题汇总（FAQ）：state 和 props 之间有什么区别？

### 步骤 3：确定 UI 状态的最小表现（但完整）

为了使你的 UI 具有可交互性，你需要能够触发对基础数据模型的更改。React 使用 state 来实现。

为了正确的构建应用，你需要思考你应用需要的最小可变状态集。这里的关键是 DRY：不要重复自己。找出你应用所需的状态的绝对最小表现，根据需要计算任何事情。举例，如果你构建 TODO 列表，保存 TODO items 数组；不要保存一个独立的 count 状态变量。而应该当想要渲染 TODO 的 count 时，获取 TODO items 数组的长度。

考虑我们示例应用中的所有数据。我们有：

- 产品的原始列表
- 用户输入的搜索文字
- 复选框的值
- 筛选的产品列表

让我们研究每一个，看是否是状态。对所有数据询问 3 个问题：

- 它是父对象通过 props 传递进来的嘛？如果是，它可能不是 state。
- 它随着时间推移不变嘛？如果是，它应该不是 strate。
- 你可以根据你组件中的其他任何的 state 或者 props 计算得到它吗？如果是，它不是 state

产品的原始列表作为 props 传递，所以它不是 state。搜索文本和复选框应该是 state，因为它们随着时间推移更改并且无法通过其他任何东西计算得到。最后，过滤的产品列表也不是 state，因为它可以被产品的原始列表和搜索文本以及复选框的值组合在一起计算得到。Think of all of the pieces of data in our example application. We have:

The original list of products
The search text the user has entered
The value of the checkbox
The filtered list of products
Let’s go through each one and figure out which one is state. Ask three questions about each piece of data:

Is it passed in from a parent via props? If so, it probably isn’t state.
Does it remain unchanged over time? If so, it probably isn’t state.
Can you compute it based on any other state or props in your component? If so, it isn’t state.
The original list of products is passed in as props, so that’s not state. The search text and the checkbox seem to be state since they change over time and can’t be computed from anything. And finally, the filtered list of products isn’t state because it can be computed by combining the original list of products with the search text and value of the checkbox.

So finally, our state is:

The search text the user has entered
The value of the checkbox

所以，最后，我们的 state 是：
用户已经输入的搜索文本
复选框的值

### 步骤 4：确定你的状态应该放在那里

好的，我们可以确定应用最小 state 集。下面，我们需要确定那些组件改变，或者拥有改状态

记住：React 是单向数据流的组件层次结构。它可能无法立刻清除那个组件应该拥有什么状态。**它通常是新人最难理解的部分**。所以请按照下面的步骤来弄清楚。

对于你应用的每个状态：

- 根据每个组件的状态来确定显示的内容
- 找到公共所有者组件（在层次结构中所有需要状态的组件之上的单个组件）
- 公共所有者或者其他在层次结构中更高级别的组件应该拥有它的状态
- 如果你无法找到拥有状态意义的组件，创建一个仅用于保留状态的新组件，然后放在公共的所有者组件之上。

让我们在应用中运行这个策略：

- ProductTable 需要根据 state 和搜索文本以及复选框状态来过滤产品列表。
- 公共的所有者组件是 FilterableProductTable。
- 从概念上将过滤文本和复选框值放到 FilterableProductTable 有意义。

所以，我们决定将我们的状态放到 FilterableProductTable 中，首先，添加实例属性`this.state = {filterText: '', inStockOnly: false}`到 FilterableProductTable’s 的构造函数中反映应用的初始状态。然后传递属性 filterText 和 inStockOnly 到 ProductTable 以及 SearchBar 中。最后使用这些属性在 ProductTable 中过滤这些行，然后在 SearchBar 中设置表单字段的值。

你可以看到你应用将有下面的行为：设置 filterText 到”ball“然后刷新你的应用。你讲看到这个数据 table 被正确的更新。

### 步骤 5：添加逆向数据流

至此，我们已经创建一个根据 props 和 state 顺着层次结构正确的呈现的应用。现在是时候支持另外一种数据流：在深层次结构中的表单组件需要更新 FilterableProductTable 的状态。

React 使得数据流清晰帮助你理解你的程序是如何工作的，但是要求比传统的数据流双向绑定要输入更多的代码。

如果你尝试输入或者选中的例子当前版本的复选框，你讲看到 React 会忽略你的输入。这是故意的，因为我们将 input 的输入 prop 设置成 FilterableProductTable 的 state。

让我们考虑下我们要发生的事情。我们想要保证当用户更改表单时，我们更新 state 来反映用户输入。因为组件应该只更新它们自己的 state，FilterableProductTable 将传递回调给 SearchBar，当它触发时状态就会更新。我们可以在输入组件中使用 onChange 事件来通知它。回调函数由 FilterableProductTable 传入，将调用 setState(),并且应用将会被更新。

### 结尾

希望让你了解如何使用 React 来构建组件和应用，尽管让你比之前使用过的输入更多代码，记住代码的读取远要比写多，并且阅读这个模块清晰代码也不困难。当你开始构建大型的组件库时，你将感谢这种显示性和模块性，并且随着代码复用，你代码行数将开始减少。
