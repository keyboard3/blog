---
title: amp教程
top: false
cover: false
toc: true
mathjax: true
date: 2020-06-06 19:51:23
password:
summary:
tags: [amp, 翻译]
categories:
---

[原文 Welcome to the AMP community!](https://amp.dev/zh_cn/documentation/courses/beginning-course/?format=websites&level=beginner)

## 初级课程

### 欢迎来到 AMP 社区

#### 为什么有 AMP？

在许多方面，互联网是世界中心的枢纽。每天，来自全世界的许多人在线访问信息。但是许多人不是高速链接或者高性能设备上访问互联网，许多用户的体验不佳。

为了给网站访问者更好的体验，开发社区找到了提高网站性能的方法。在这个过程中开发者改善了辅助技术的用户的可访问性，变化无常的可靠性和各种设备上的网站设计。

有时候追踪学习所有 web 新技术非常困难。我们相信开发者想要更快速的网站，但是一路走来很容易出错。

AMP 就解决这些问题。AMP 目的是让开发者轻松的关注构建更好的功能而不用担心给用户带来不良的用户体验

#### AMP 有什么好处？

AMP 是一个 web 组件库用于实施最佳的 web 实践。AMP 解决了常见的开发障碍，以允许设计高性能，可访问和响应迅速的网站。

简而言之，AMP 让正确的事变得容易。让开发者腾出时间专注于给用户带来更有价值的功能。

AMP 致力于通过以下方式提高网站性能：

- 通过添加常见网站功能标签来扩展 HTML。HTML 是为了创建基本的内容页面开发的，但是还没发展到跟上现代网站的步伐。一些相关的现代网站功能包括侧滑导航菜单，视频播放器和图片轮播。为这些功能添加的额外标签被叫做“web 组件”

- 减少 JavaScript 数量。AMP 需要运行 JavaScript，但是 AMP 限制了页面上的 JavaScript 何时以及如何使用。这种限制可以极大的提高移动端网站性能。AMP 组件提供了许多开发者第一次使用 JavaScript 的功能。

- 在网站的开发过程中更早的发现问题。正如我们已经说过的，在现代 web 开发中有许多需要跟踪的地方。AMP 通过提供验证程序来帮我们来管理它们，验证程序用来帮我们找到网站中可能影响性能或访问性的问题。它还可以帮助你学习如何解决发现的问题。

AMP 的好处并不止于页面部署。像谷歌和微软都创建了缓存来存储没有验证错误的 AMP 页面。这些缓存对网站内容进行强大的性能优化，而不会影响到用户体验。缓存过的 AMP 页面还与搜索引擎集成在一起，所以你可以在几秒内甚至更少的时间从搜索结果到你的网站。

#### 学习 AMP 开发

学习 AMP 是学习 web 开发的好方法，因为 AMP 站点有：

- 使用标准的 HTML，CSS 和 JavaScript 构建
- 兼容所有现代浏览器
- 不依赖特殊工具或软件来在线部署

你将在构建 AMP 页面时获得技能，这些页面可以被转移到用其他的格式或者框架构建网站中。跟许多流行的框架一样，AMP 是基于组件的方式来设计和构建网站的。你讲学习到公认的构建最佳体验的网站，开始思考组件，并避免损坏用户的不良体验。这些通用的技能可以应用于整个 web。

AMP 解决了性能，可访问性和响应式设计问题，让你可以更加关注功能。但是，如果你磨炼你的技能，学习 AMP 也可以帮助你准确的了解 AMP 如何解决这些问题。你也将作为一名开发者继续学习和走向成熟，甚至你的用户享受 AMP 帮助你构建的体验。

## 课程介绍

### 这个课程适合谁

这个课程是为了有志向的新开发和希望提高网站性能的的开发设计的。在本课程和以下课程你将：

- 向你介绍 AMP 页面和传统“vanilla”网站的区别
- 使用实际的 AMP 页面和最佳体验逐步构建一个示例项目
- 学习构建现代网站的策略

### 先决条件

为了从这些课程中获得收益，你应该对 HTML 和 CSS 有基础的理解。足以识别 HTML 和 CSS 代码，并能够按照练习中的指示进行少量的添加和修改。请注意讲授这些概念超出了本课程的范围。如果需要，你可以在[HTML](https://developer.mozilla.org/en-US/docs/Web/HTML)和[CSS](https://developer.mozilla.org/en-US/docs/Web/CSS)复习这些知识。

### 使用 Glitch 来跟踪代码

为了完成这些课程中的代码示例，我们将使用[Glitch](https://glitch.com/)。Glitch 是一个线上的代码编辑器，它允许你创建和预览网站而不需要在你电脑上安装任何东西。它也允许你创建一个服务，全部都在 Glitch 页面。

这个 Glitch 代码编辑环境看起来像这样：
![The online editor of Glitch](https://amp.dev/static/img/courses/beginner/image13.png?width=1000&hash=81846846441c0719ea8d81a85b15c447cbe5131d)

上面红色的框表明了你可以输入 HTML 和 CSS。绿色的框表示该按钮将带你进入创建页面的实时版本。黄色框的按钮让你可以复制这个项目并修改它。蓝色的框表示你可以使用的文件。在资源文件夹下，你可以找到你的图片。

在这些课程中，你将需要各种图片来完成练习。所有完成这些课程的图片都会被保存在我们的 Glitch 项目中。要查看项目中的图片，请在 Glitch 编辑器的左侧文件列表中点击资产条目。要获得任何单个图片的链接，请从右侧的资产资产列表中选中该图片。在弹出的弹框中，点击 URL 旁边的赋值按钮。然后你可以在任何需要的地方使用这个链接。
![The assets view in Glitch](https://amp.dev/static/img/courses/beginner/image8.png?width=1000&hash=70e74330b4a3cf41bd91d88ecc2e376d991bf509)
![The details pop-up (including URL) for an image in the assets collection](https://amp.dev/static/img/courses/beginner/image12.png?width=1200&hash=7dd67d72f09d12373617986571a37d7b47beeb91)

在这个课程中，我们将从最基本的 HTML 页面开始。我们将在 Glitch 上创建一个空的项目，其包含一些图片，你稍后需要的服务器代码，带标题和一张图片的 index.html 文件。

打开[这个](https://glitch.com/edit/#!/nosy-leech)项目开始。点击右上角的“Remix This”按钮创建一个可以编辑的新项目。你可以在本课程和以后的课程继续使用这个编辑器。以后的每门课程都会给你提供该点开始的解决方案参考版本的机会。

你无需使用 Glitch 来完成这些培训。然而，一些完成练习所需要的代码值包含在 Glitch 示例上。如果你喜欢使用其他编辑器，你仍需要使用 Glitch 示例来找到这些代码。

### 设置 AMP 验证器

为了检测我们 AMP 页面的错误，我们有一个有价值的工具：AMP 验证器。编写有效的 AMP 页面是获得框架全部收益的关键。AMP 验证器可以通过两种方式访问：通过 Chrome 扩展程序，或者是通过在我们的 URL 上添加参数，所以我们需要在代码中内置验证器。就本课程来说，我们建议你使用 Chrome 扩展程序，因为它在构建你网站时更容易使用和访问。

- 安装 Chrome 扩展程序，访问[链接](https://chrome.google.com/webstore/detail/amp-validator/nmoffdblmcmgeicmolmhobpoocbbmknc/related?hl=en)
- 要改为内置的 AMP 验证器，在你 AMP 页面 URL 地址末尾添加#development=1，然后在你浏览器控制台查看结果。如果你使用 Chrome 扩展程序你不需要添加这个参数。

### 我们将构建什么

在我们三个课程中，你将为 Chico 的奶酪自行车商城建立一个网站。Chico 开发一款完全由奶酪制成的革命性自行车。对自行车需求如此之高让 Chico 需要尽可能快的构建一个网站。当我们完成这三门课程，Chico 的网站将看起来这样：
![How the site looks at the end of the Advanced Course](https://amp.dev/static/img/courses/beginner/image14.png?width=330&hash=1b27ffdd511e8550ae8920116b236cdf74c50bd0)
你可以点击[这个](https://nice-consonant.glitch.me/)链接来看到实时的预览。纵观整个网站。我们有视频，注册表单，图片轮播，分享到其他媒体的方式。通过点击左上角的三行图标（也叫做汉堡菜单图标）。一旦菜单展开，点击我们的产品链接导航到产品列表。尝试按照价格对产品列表排序，并按照产品分类过滤产品列表。

我们选择 Chico 的网站作为我们的模型，因为它提供了我们今天在流行网站上常见的功能集合。它完全使用 AMP 来构建。在这些课程中，我们将从头开始构建此站点。

## 我们的第一个 AMP 页面

### 开始我们的旅程

这是我们团队第一天构建 Chico 的奶酪自行车网站。到目前为止，这个网站是基础的 HTML 页面，表头包含网站的标题，一个我们自行车的图片，一些营销文字。
![Our basic HTML website](https://amp.dev/static/img/courses/beginner/image17.png?width=1000&hash=fe919f49d78010e4d32814e1067b0190b46fc608)
在你的 Glitch 项目中，打开 index.html 并查看 HTML：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Chico's Cheese Bicycles</title>
    <link
      rel="shortcut icon"
      href="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fcheese-favicon.png?1540228964214"
    />
  </head>
  <body>
    <header class="headerbar"><h2>Chico's Cheese Bicycles</h2></header>
    <main>
      <div class="main-content">
        <img
          src="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fricotta-racer.jpg?1540228217746"
        />
        <div class="below-hero">
          <h2 class="main-heading">What we're about</h2>
          <p class="main-text">
            We sell the only ten-speed bicycles in the world made entirely of
            cheese. They get you where you need to go, and you never arrive
            hungry. Our bikes are 100% biodegradable. And with our new
            rodent-repelling varnish they last longer than ever!
          </p>
        </div>
      </div>
    </main>
  </body>
</html>
```

我们团队已经确定使用 AMP 将构建所需要的网站功能更加容易，因此我们的工作是将这个 HTML 页面转换成有效的 AMP 页面。

首先我们需要将全世界表明我们正在尝试构建 AMP 网站。为此，我们将为 html 标签加上一些装饰。如果 html 标签包含 ⚡ 符号或者单词`amp`，AMP 验证程序和 AMP 缓存会将我们的网站认为是 AMP 站点。

在你的 Glitch 项目，添加 ⚡ 符号到 html 标签，像这样：

```html
<html ⚡ lang="en"></html>
```

这个符号是必须的。表明我们正在构建 AMP 页面。下一步，我们讨论像 AMP 验证器这样的工具如何帮助我们确定使网站有效需要哪些更改。

### 验证和 AMP 缓存

我们已经多次提到一个概念：编写有效的 AMP。让我们讨论一下它是什么意思，以及为什么它重要。

首先，有效的 AMP 是很重要，因为它对你的用户有好处。AMP 的规则代表了性能，可访问性和安全的最佳实践。但是验证错误是 AMP 告诉你仍有空间为你的用户改善网站。

其次，AMP 重要的原因是有用的 AMP 缓存。这个缓存是 AMP 体系结构中重要的组成部分。它是内容分发网络(CDN)，目的是：

- 只服务有效的 AMP 页面
- 允许 AMP 页面去高效和安全的预加载
- 在缓存中的页面上做一些性能优化

当用户请求你的 AMP 网站，它们可能是从就近的 AMP 缓存服务器发送给它。如果你的网站在 AMP 缓存中，那么当你使用 Google 之类的搜索引擎时，可以高效的预加载在到后台。如果用户在搜索结果中选中你的网站，即使连接不良，它也会在几秒内显示出来，这个 AMP 缓存将你站点上执行自动优化，例如：

- 缓存字体
- 缓存和压缩图片，转换它们成 WebP 这样的新格式
- 对 AMP 文档消毒以阻止跨站脚本攻击或者其他漏洞
- 修复可能导致在各种浏览器下渲染不一致的 HTML 问题;例如，关闭所有标签，小写属性名或者转义文本

当你使用 AMP 构建网站并完成这些训练中的练习时，检查保证你的网站有效。为了跟踪这些验证错误，我将使用在[课程介绍](https://amp.dev/zh_cn/documentation/courses/beginning-course/course-introduction/?format=websites&level=beginner#setting-up-the-amp-validator)中已经安装的 AMP 验证器。

### 练习 1：使用 AMP 验证器

安装 AMP 验证器 chrome 扩展程序之后，这个验证器将自动在你打开的任何带有 html 标签中包含 AMP 符号(⚡)的页面上运行，像我们现在这样。打开你的 Glitch 项目并查看 AMP 验证器扩展程序的图标。它应该看起来类似于红色上有一个徽章，表明了有 7 个验证错误。
![The AMP Validator Chrome extension showing AMP issues.](https://amp.dev/static/img/courses/beginner/image6.png)
点击 AMP 验证器的图标打开弹窗，显示当前页面的验证错误列表，并给出这些错误可能的解决方案。
![The issues displayed in the AMP Validator Chrome Extension.](https://amp.dev/static/img/courses/beginner/image22.png?width=2500&hash=7ee74f950b8b863213940e0dc221a38a4c7c850c)
img 标签这一项：

```
The tag <img> may only appear as a descendant of tag 'noscript'. Did you mean 'amp-img'?
```

点击这一项末尾的 Debug 链接。这个 Debug 链接将带你直接跳转到你页面上包含这个错误的代码行。它帮助你在你文件中找到遇到的错误，并提供所需的上下文来理解如何解决这个错误。不用担心，这个消息现在似乎尚不清楚，但这很容易解决。我们需要使用 AMP 组件 <amp-img> 来替换 HTML 标签 <img> 。在本课程的[组件的思考](https://amp.dev/zh_cn/documentation/courses/beginning-course/thinking-in-components/?format=websites&level=beginner)这一节中，我们将探索为什么会出现这个错误，<amp-img> 是什么，然后如何解决它。
![AMP debugger showing an error inline.](https://amp.dev/static/img/courses/beginner/image16.png?width=2500&hash=73a6f348dbdad8011aa80519378e74a8c4442b14)
对于其他的验证错误项，点击"Learn more"的链接标签。这个链接将你从错误描述中转到响应的 AMP 文档，帮助你修复这个问题。
![AMP documentation reached via the “Learn more” link in the AMP Validator.](https://amp.dev/static/img/courses/beginner/image21.png?width=1750&hash=06f514eceeae89e6bf72c99bfc23ea619f71f6af)
下一步是修复这些验证问题。为此，我们需要更加的了解 AMP 页面必要的元素。除了向我们的 HTML 中添加闪电符号之外我们还需要做更多的事情创建一个有效的 AMP 页面。

### AMP 页面的剖析

每个页面都以相同的基础模板开头。然后我们自定义并从那里构建页面。这个入门模板有时候叫做 AMP 样板。这个样板是设置 AMP 运行时并帮助 AMP 页面运行的更加平滑的标签组合。

在这一节，我们将对 AMP 样板的每个部分都进行一些解释。然而，你无需记住在你用 AMP 创建的每个页面添加这些标签。你可以简单的用这个样板开始构建 AMP 页面。

下面的这些标签是 AMP 页面必须的。有效的 AMP 页面一定包含：

- 从 `<!doctype html>` doctype 开始
- 包含 `<head>` 和 `<body>` 标签
- `<head>` 内的第一个标签是 `<meta charset="utf-8">` 标签
- 在 `<head>` 标签中包含 `<meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1">`。注意：建议 initial-scale=1

下面这些规则专门用于设置 AMP 运行时。验证 AMP 页面一定包含：

- 包含一个顶层的`<html ⚡>`标签。这个闪电象征符号表明它是一个 AMP 站点。注意：`<html amp>`也接受。
- 在它们的`<head>`标签中包含一个`<script async src="https://cdn.ampproject.org/v0.js"></script>`标签。加载 AMP JavaScript 库。注意：作为最佳实践，你应该在`<head>`中尽量早的包含这个脚本。
- 在`<head>`中包含一个`<link rel="canonical" href="$SOME_URL">`标签。如果存在指向常规的 HTML 版本，或者如果不存在非 AMP 的版本就指向自身。注意：你应该在你页面上替换`$SOME_URL`为真实的地址。
- 在`<head>`标签中包含 AMP 样式的样板代码。该 CSS 在页面上隐藏直到 AMP 库加载完成。以下是 AMP 样式的样板代码：

```html
<style amp-boilerplate>
  body {
    -webkit-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
    -moz-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
    -ms-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
    animation: -amp-start 8s steps(1, end) 0s 1 normal both;
  }
  @-webkit-keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }
  @-moz-keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }
  @-ms-keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }
  @-o-keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }
  @keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }</style
><noscript
  ><style amp-boilerplate>
    body {
      -webkit-animation: none;
      -moz-animation: none;
      -ms-animation: none;
      animation: none;
    }
  </style></noscript
>
```

### CSS 和 AMP

CSS 自定义你网站的外观。你将几乎总是添加自定义样式到你的 AMP 页面上。不过，注意 AMP 对 CSS 的使用做了一些限制：

- 样式只能存在于`<style amp-custom>`标签内的文档头，或在需要时作为内联样式使用。这个限制阻止加载外部样式表，但是也保存网络请求，启用缓存并提高性能。
- 一个 AMP 页面只能包含一个`<style amp-custom>`标签(装饰的样式标签)
- 一个页面最多包含 75k 的 css
- `!important`规则受限制
- 更多的关于 CSS 规则的限制，请查看[此处](https://amp.dev/zh_cn/documentation/guides-and-tutorials/develop/style_and_layout/style_pages/?format=websites)的文档

要练习将自定义的样式添加到 AMP，请在`<head>`的页面上添加`<style amp-custom>`标签，看看会发生什么。完成后，你可以从你的页面中删除它。

```html
<style amp-custom>
  body {
    font-family: sans-serif;
    line-height: 1.5rem;
    padding: 20px;
  }
  p,
  h2 {
    border: 1px dotted red;
  }
</style>
```

![Custom CSS affecting our page.](https://amp.dev/static/img/courses/beginner/image10.png?width=560&hash=e71123278226e4245149ac2b1d9f11ea83a1f484)

### 练习 2：转换我们 HTML 页面的剩余部分

现在是时候修正上一节练习在我们网站上发现的验证错误了。为此，我们需要给我们基础 HTML 网站添加缺失的 AMP 样板代码。

对此以及所有以后的练习，我们将应用所学的知识对我们的 Glitch 网站上的真实代码进行更改。我们会给你一些提示。在每次练习结束之后，我们将给出一个完整的解决方案。尝试依靠自己完成练习，但是如果你卡主了或者是需要提示，可以随时从解决方案一节中复制代码。

另外，在每一节课程的开头和末尾，我们提供一个包含我们到此为止完成的所有的代码。如果你丢失了当前的 Glitch 页面或者想从我们的解决方案开始，你可以从 Glitch 示例中复制代码，或者重新混合这些示例并从那里继续前行。

使用 AMP 的[文档](https://amp.dev/zh_cn/documentation/guides-and-tutorials/start/create/basic_markup/?format=websites)和上面的注释，更新你的 Glitch 项目，你会发现只有`<img>`标签验证错误依旧存在。另外，为了帮助我们构建 Chico 的奶酪自行车网站，我们已经提供了一些样式，可以在整个培训中使用。如果你打开[这个](https://pastebin.com/vNws2bA1)页面，`<style amp-custom>`标签包含你所需的样式。你应该复制这些样式到你正在工作的项目中去。

#### 解决方案

可以在[https://glitch.com/~hungry-modem]这个 Glitch 示例中找到解决方案。这个页面包含改动的部分应该如下所示：

```html
<head>
  <meta charset="utf-8" />
  <script async src="https://cdn.ampproject.org/v0.js"></script>
  <title>Chico's Cheese Bicycles</title>
  <link
    rel="shortcut icon"
    href="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fcheese-favicon.png?1540228964214"
  />
  <meta
    name="viewport"
    content="width=device-width,minimum-scale=1,initial-scale=1"
  />
  <link rel="canonical" href="https://hungry-modem.glitch.me/" />
  <style amp-boilerplate>
    body {
      -webkit-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
      -moz-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
      -ms-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
      animation: -amp-start 8s steps(1, end) 0s 1 normal both;
    }
    @-webkit-keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
    @-moz-keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
    @-ms-keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
    @-o-keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
    @keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
  </style>
  <noscript
    ><style amp-boilerplate>
      body {
        -webkit-animation: none;
        -moz-animation: none;
        -ms-animation: none;
        animation: none;
      }
    </style></noscript
  >
  <style amp-custom>
    ... styles elided due to length ...;
  </style>
</head>
```

### 一个几乎有效的 AMP 页面

如果你完成了上述的所有练习，恭喜！你的页面几乎是一个有效的 AMP 页面。如果你打开你的页面并打开 Chrome DevTools(`Command+Option+I 在Mac上 或者 Control+Shift+I 在Windows/Linux`)并选中 Console 标签页，你应该在控制台中看到这个消息，表示这个 AMP 库被成功的加载了：

```
Powered by AMP ⚡ HTML
```

接下来，如果你打开 AMP 验证扩展程序，会显示我们犯下了最后一个错误：

```
The tag 'img' may only appear as a descendant of tag 'noscript'. Did you mean 'amp-img'?
```

这是需要理解的重要错误。一些 HTML 标签不允许出现在 AMP 文档中。在某些情况下，AMP 要求你使用其他来替换。我们叫一些自定义非 HTML 标签的标签为”组件“，并且我们将稍后在本训练的下一节来讨论它们。

## 组件思考

### 添加功能给我们的网站

到目前为止，我们将基础的 HTML 网站转化成了基础的 AMP 网站。目前我们的网站上仍有一个错误，我们将使用`<amp-img>`组件来替换`<img>`标签来修复这个错误。解决最后一个验证问题之后，我们将学习到上面是 AMP 组件，为什么某些 HTML 标签在 AMP 中被替换或禁止，以及如何为我们的网站中添加组件。

之后，是时候向我们的网站上添加额外的功能了。为了完成 Chico 的奶酪自行车首页的初始版本，我们将添加一些额外的营销内容。团队决定添加一个关于如何制作奶酪自行车的 YouTube 视频，一个各种奶酪自行车产品的图片轮播以及一些社交媒体链接，帮助用户分享我们的网站到它们喜欢的社交网络上。

看起来这么快的向我们的网站上添加这么多内容令人害怕。我们应该创建 HTML，CSS 和 JavaScript 来满足我们想要添加功能的要求（例如如何更改轮播图的滑动）。然后，我们来考虑如何提升整个站点的性能。

但是这是 AMP 之美。使用 AMP，我们不需要担心所有这些细节！AMP 库作者为我们提供了这些功能的插入构建块，并关心质量像性能、可访问性和安全。这些块本称之为组件，它们是 AMP 成功的关键。

### 什么是 Web 组件？

组件是构建 Web 的块。它们代表结构（HTML）、样式（CSS）和行为（JavaScript）的组合，并具有很容易在自己网站中使用和与其他人分享的接口。组件有如下特征：

- 一个名字(如`<amp-img>`)，如 tag 名一样来确定一个组件
- 自定义属性，可以改变行为，样式，或者组件内容（如 width,height,src 和 attribution）
- 事件，可以捕获组件的用户输入（属性 on）

可选的，组件也有"children"。在这里,"children"引用了内容(像文本，HTML 标签或者其他组件)被放置在组件的打开和闭合标签之间。这些 children 的显示内容因每个组件不同而不一样。

AMP 组件系统让你用最小的努力帮助你快速的构建高效且响应迅速的功能到页面上。这个组件库提供了一个完整的组件列表给你使用。有用于构建表单和轮播图的组件，用于集成页面分析，用于向服务器发送 XHR 请求的组件，等等。扩展性几乎是无限的。你可以在 AMP 组件参考[这里](https://amp.dev/zh_cn/documentation/components/?format=websites)看到完整的组件列表。

举例，这里是我们网站时使用的 3 个 AMP 组件：
| AMP component | How it renders on our site |  
| ---- | ----- |
| `<amp-img src="IMG-URL" layout="responsive" width="640" height="480"></amp-img>` | ![](https://amp.dev/static/img/courses/beginner/image14.png?width=1000&hash=1b27ffdd511e8550ae8920116b236cdf74c50bd0) |

|`<amp-twitter width="486" height="657" layout="responsive" data-tweetid="ID"></amp-twitter>`|![](https://amp.dev/static/img/courses/beginner/image19.png?width=1000&hash=af27220195f943de1cd2b4815e76acf8ed7dd045) |
|`<amp-youtube data-videoid="ID" layout="responsive" width="480" height="270"></amp-youtube>`|![](https://amp.dev/static/img/courses/beginner/image15.png?width=1000&hash=8b870c5a1cfb74d64993f9841a08814e7eac0fc3) |

建立 AMP 站点的目的是尽可能的使用 AMP 组件。组件使得构建的 AMP 的性能优势最大化，因为你不需要创建已有的组件，从而利用 AMP 库作者的工作。

几乎所有的 AMP 组件都由至少一些 JavaScript 运行。对于某些 AMP 组件(如`<amp-img>`)，这个 JavaScript 直接内置到了 AMP 运行时脚本中，该脚本包含在页面的样式代码的顶部。对于大多数 AMP 组件，你需要包含单独的 script 标签。一个充分的理由是：你只包含你网站上真正需要的脚本。然后，用户只需要下载浏览页面所需要的代码。越少的代码下载意味着你的网站加载更快。

### 练习 3：我们的第一个组件-<amp-img>

大多数 HTML 标签可以被直接在 AMP 中使用，像`<img>`标签，一定要替换成等效的 AMP 组件。这些组件结合了可访问性，响应能力和性能的内置最佳实践。

例如，在`<amp-img>`的情况下，AMP 要求我们去指定图片的尺寸。AMP 需要在资源(如 images)加载之前知道页面的布局。它可以提高在页面正在加载，图片资源还没有下载完成之前的用户体验。当图片下载完，可以将它插入到页面而不会造成页面上已存在的内容四处移动。它给 AMP 运行时空间去决定根据用户设备的能力和网络连接情况何时去加载网络图片资源。

要使用该组件来解决早起的`<amp-img>`验证错误，用 AMP 等效组件来替换页面中已存在的 img 标签。注意：编写`<amp-img ...>`而不是`<img ...>`需要给你的图片指定尺寸。给图片一个宽 640，高 480。

如果需要，[参考](https://amp.dev/zh_cn/documentation/components/amp-img/?format=websites)这里的`<amp-img>`文档。

#### 解决方案

页面中包含的图片部分应该如下所示：

```html
<amp-img
  src="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fricotta-racer.jpg?1540228217746"
  width="”640”"
  height="480"
></amp-img
```

### 排列和调整组件大小

下一个我们需要解决的问题是我们页面的外观。你不会在大的桌面显示器上注意到它，但是当我们在移动设备上查看我们的网站时就很容易看到问题所在。
![自行车的图片超出了屏幕边缘](https://amp.dev/static/img/courses/beginner/image23.png?width=1000&hash=de743706d2eb97cf44b26e9f3945e2657bb88f2f)

我们添加到页面的图片不会缩小以适应更小的屏幕；它会溢出边缘。如果我们没有指定布局图片和调整大小的策略，它将默认按照我们代码中指定的宽高来固定。幸运的是，我们可以使用 AMP 的布局系统来解决这个问题。

我们将给我们的图片指定响应类型的 layout 属性，使其自动根据窗口调整大小来缩放。响应式布局假设图片为父容器的尺寸，同时遵循原始尺寸的长宽比。如果父容器只有 320 像素的宽，图片将维持长宽比并调整成 320*240(而不是 640*480)。

添加 layout 属性到我们的图片。如果正确完成，它看起来像这样：

```html
<amp-img
  src="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fricotta-racer.jpg?1540228217746"
  layout="responsive"
  width="640"
  height="480"
></amp-img>
```

在你完成改动之后，看下你的页面。图片会正确的按照长宽比，响应式的根据屏幕宽度去填充显示。问题解决。
![纵横比正确的自行车图片](https://amp.dev/static/img/courses/beginner/image26.png?width=1000&hash=c8df8c9c35ded613d440e108b6664d8d724fd9f1)

除了`responsive`之外还有其他布局类型(实际上至少总共有 8 种)。例如，`fixed`布局表明组件永远不会调整分配给它的宽和高。`intrinsic`布局和`responsive`布局类似，除了它具有组件不能超过固有宽高的概念。某些布局只能应用于某些组件上。每个组件的文档将会指定那些布局对它有效。你可以在[这里](https://amp.dev/zh_cn/documentation/guides-and-tutorials/learn/amp-html-layout/layouts_demonstrated/?format=websites)查看看剩下的布局类型。

如果你想成为一个成功的 AMP 开发者，学习如何使用布局系统是关键。所有 AMP 提供的布局系统都可以用纯 CSS 去实现，但是通常它们很复杂或者有复杂的边界情况要求很深入的知识来解决。AMP 简化了过程并暴露一些选项，可以在你 AMP 页面上任何元素上使用。查看[官方文档](https://amp.dev/zh_cn/documentation/guides-and-tutorials/learn/amp-html-layout/?format=websites)学习更多的布局系统知识。

### 练习 4：嵌入视频

下一步，来嵌入 YouTube 视频到我们的文档中。我们的营销团队发布了[这个](https://www.youtube.com/watch?v=BlpMQ7fMCzA)视频，来展示我们正在生产一款奶酪自行车。

使用这份`<amp-youtube>`文档嵌入这个 YouTube 视频放到`<amp-imp>`组件之后：

- 设置视频 id 为`BlpMQ7fMCzA`
- 使得视频布局`responsive`
- 注意：不要忘记添加这个脚本到`<head>`中

推荐样式指南：

- 设置元素宽度 480，高度 270

你更改之后，看下页面。你现在应该可以看到 YouTube 视频：
![页面中YouTube视频的图像](https://amp.dev/static/img/courses/beginner/image18.png?width=1000&hash=42af1c8df34f27b866b35d7bc78c6754762e4de4)

#### 解决方案

```html
<amp-youtube
  data-videoid="BlpMQ7fMCzA"
  layout="responsive"
  width="480"
  height="270"
></amp-youtube>
```

记住在`<head>`中包含`<amp-youtube>`脚本：

```html
<script
  async
  custom-element="amp-youtube"
  src="https://cdn.ampproject.org/v0/amp-youtube-0.1.js"
></script>
```

## 找到正确的组件

### 浏览 AMP 组件文档

到目前为止，我们使用的组件非常简单。对于`<amp-img>`和`<amp-youtube>`通过这篇文档的学习足够了，查看示例，然后复制代码到我们的网站。对于某些组件的高级功能，或者对更复杂的组件，我们将从这篇文档中读取和吸收更多的信息。

为了更高效的开发 AMP 网站，学会如何浏览 AMP 组件文档非常重要。在本系列的每个培训中广泛的练习这个技能。

下一步，我将添加奶酪自行车产品的图片集合供用户去滚动。对此，我们将使用一个图片轮播图组件。轮播图组件是一个包含一组子项的元素，它们滑动起来像幻灯片放映。轮播图的 AMP 实现是`<amp-carousel>`。这个组件不是内置的，所以你需要添加它的脚本到页面的`<head>`中。

当我们查看`<amp-carousel>`的[文档](https://amp.dev/zh_cn/documentation/components/amp-carousel/?format=websites)时，我们将寻找下面问题的答案：

- 这个组件做什么？
- 我应该如何使用这个组件？
- 我应该如何使用属性来定制这个组件？
- 我应该如何改变这个组件的样式？
- 我们需要引用外部的脚本来启用这个组件吗？
- 这个组件支持哪些布局类型？

![AMP documentation page fo carousel](https://amp.dev/static/img/courses/beginner/image25.png?width=1750&hash=a70b70e161b20fc05d42dd6afac1f928c28c076b)
查看`<amp-carousel>`文档的下面各项：

- **描述** - 每个组件文档的顶部是一个简单的介绍。总结了这个组件是什么，以及它存在的理由。
- **行为 behavior** - 这一节解析了组件是如何实现的。通常提供了示例代码和它的展示效果
- **列出的属性** - 我们在 web 组件的上一节讨论了自定义属性。它允许我们用某些方式来自定义我们的 AMP 组件。这一节包含了不同属性的列表，它们可能的值，以及这些值控制什么。
- **样式 styling** - 这一节解释了如何使用 CSS 来改变组件的外观。除了通过标签名和 id 来设置样式，许多组件提供了额外的 CSS 类名，在某些状态下使用它来改变组件的外观。例如，`<amp-carousel>`提供了类`.amp-carousel-button`，允许开发者重新调整改变轮播图滑动的按钮的样式。
- **要求的脚本标签** - 置于文档的顶部，这个标签需要添加我们网站的顶部让这个正常工作。许多组件都需要这些脚本才能正常工作。
- **支持的布局类型** - 我们在上一节讨论了[布局](https://amp.dev/zh_cn/documentation/guides-and-tutorials/learn/amp-html-layout/layouts_demonstrated/?format=websites)属性。它控制屏幕上这个元素的显示。这一节解释了那些布局类型对这个组件有效。

几乎所有的 AMP 组件的文档都列出了这些项。让我们来探索其中一个示例的文档：

```html
<amp-carousel
  id="carousel-with-preview"
  width="450"
  height="300"
  layout="responsive"
  type="slides"
>
  <amp-img
    src="images/image1.jpg"
    width="450"
    height="300"
    layout="responsive"
    alt="apples"
  ></amp-img>
  <amp-img
    src="images/image2.jpg"
    width="450"
    height="300"
    layout="responsive"
    alt="lemons"
  ></amp-img>
  <amp-img
    src="images/image3.jpg"
    width="450"
    height="300"
    layout="responsive"
    alt="blueberries"
  ></amp-img>
</amp-carousel>
```

这个轮播图包含了 3 张图片用于提供给用户滑动。这个轮播图组件的实例的属性（id,width,height,layout,和 type）被拆分成 3 组：[所有 HTML 元素共有的属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes)(id),[所有 AMP 组件共有的属性](https://amp.dev/zh_cn/documentation/guides-and-tutorials/learn/common_attributes/?format=websites)(width,height,和 layout)，轮播图组件特有的属性(type)。

在`<amp-carousel>`的文档中，我们看到组件有 type 属性。它显示 type 的有效输入包括 slides 和 carousel。

这意味着如果你不指定 type，它的默认值将是 carousel。

许多其他自定义的属性可以被用到`<amp-carousel>`组件上。当第一次使用 AMP 组件时，查看文档以了解通过哪些方式你可以通过这些属性自定义组件的行为和外观。

### 练习 5：创建一个图片幻灯片

来练习通过这篇文档添加`<amp-carousel>`到我们的项目。添加一个带有下面设置的轮播图到`<p class="main-text">`:

- 给轮播图一个`responsive`的布局
- 给轮播图 type 设置为 slides
- 添加三张图片到轮播图：`assets/cheddar-chaser.jpg`,`assets/cheese.jpg`和`assets/mouse.jpg`。
- 如果用户尝试从最后一张滚动，轮播图图片回到开始

推荐样式指南：

- 给轮播图宽 412 和高 309
- 给每张图片宽 412 和高度 309

在你做这些改动之后，查看实时页面来检查你的成果。你的页面看起来像这样：
![The carousel in our page.](https://amp.dev/static/img/courses/beginner/image9.png?width=1000&hash=ac6a95075bd0b8e33800dd14a2d399b0c6d299be)

#### 解决方案

这里是你添加到项目中的代码：

```html
<amp-carousel layout="responsive" width="412" height="309" type="slides" loop>
  <amp-img
    src="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fcheddar-chaser.jpg?1540228205366"
    width="412"
    height="309"
    layout="responsive"
  ></amp-img>
  <amp-img
    src="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fcheese.jpg?1540228223785"
    width="412"
    height="309"
    layout="responsive"
  ></amp-img>
  <amp-img
    src="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fmouse.jpg?1540228223963"
    width="412"
    height="309"
    layout="responsive"
  ></amp-img>
</amp-carousel>
```

记住在`<head>`中引入`<amp-carousel>`组件：

```html
<script
  async
  custom-element="amp-carousel"
  src="https://cdn.ampproject.org/v0/amp-carousel-0.1.js"
></script>
```

### 发现新组件

当我们继续开发我们的奶酪自行车网站，我们不会一直知道添加我们想要的功能的 AMP 组件的名字。AMP 社区已经生产了大量用于处理不同功能的组件：广告和分析，动态内容，布局，媒体，展示和社会。典型的是当开发一个 AMP 站点给了一些新功能的要求，然后在通过在 AMP 组件的列表中检索出符合要求的组件。

第一种方式发现新 AMP 组件是使用你最喜欢的搜索引擎或者是在 AMP 项目[网站](https://amp.dev/)上使用搜索功能。这种方式适合你已经知道名字直接跳转到组件的文档。另外，你可以搜索你感兴趣的组件描述去找结果。举例，搜索“YouTube 视频”，第一个搜索结果是`<amp-youtube>`。类似，搜索“可折叠内容”，`<amp-accordion>`组件是第一个结果。

另外一种寻找组件的方式是使用[AMP Components Reference](https://amp.dev/zh_cn/documentation/components/?format=websites)页面。它包含了 AMP 支持的组件列表。每个组件的入口包含了组件的名字和这个组件能提供的功能的简单描述。我们可以通过点击它的名字去访问这个组件的文档。正如我们之前学习到的，这个文档将给你对这个组件的行为更深的了解。基于这些信息，我们应该可以决定这个组件是否满足我们的需求或者是否去搜索其他组件。在后面的培训中，我们将讨论如果没有组件符合我们的需求该怎么做。
![The AMP Component Reference page.](https://amp.dev/static/img/courses/beginner/image3.png?width=1750&hash=76dae16d0d5be7f067c1ba8ca1e20ab3de4b88f1)

最后，我们可能仍然对这些组件如何在我们网站上运作有点疑问，或者我们不清楚在更复杂的场景中如何使用组件。在 amp.dev 上[AMP By Example](https://amp.dev/zh_cn/documentation/examples/?format=websites)的页面展示了许多 AMP 组件，显示了这些组件的各种方式来满足现代网站的用户场景。通常，你可以从组件的文档转到响应的 AMP by Example 页面上。
![AMP By Example page for the amp-carousel component.](https://amp.dev/static/img/courses/beginner/image7.png?width=1750&hash=de5b63554527c405e24ebf9b5c7696b31770aba3)

### 练习 6：添加社交分享链接

在现代 web 页面社交媒体链接是很常见的。AMP 提供了一个准备好链接的按钮允许用户通过一次点击来分享你的页面到他的社交媒体上，从而帮你您提供用户的参与度。

使用 AMP 文档，添加按钮到`<amp-youtube>`之后让用户通过一次点击来分享我们的页面。然而，你将需要在[AMP Components Reference](https://amp.dev/zh_cn/documentation/components/?format=websites)上寻找相应的 AMP 组件。（**注意**：这一节的标题应该是帮助你寻找到你想要的组件）。

一旦你定位到正确的的组件，点击组件的名字访问它的文档。使用这个文档添加这样的组件：

- 给用户选项来在下面的平台分享你的页面：Email，LinkedIn，Tumblr，和 Twitter。

推荐样式指南：

- 使用带 social-bar 的样式的 div 包裹 AMP 组件
- 给每个 AMP 组件设置 width 和 height 为 44

在你完成这个任务之后，你的页面应该包含用户可以分享你页面的按钮：
![Social media buttons embedded in the page.](https://amp.dev/static/img/courses/beginner/image19.png?width=680&hash=af27220195f943de1cd2b4815e76acf8ed7dd045)

### 解决方案

```html
<div class="social-bar">
  <amp-social-share type="email" width="44" height="44"></amp-social-share>
  <amp-social-share type="linkedin" width="44" height="44"></amp-social-share>
  <amp-social-share type="tumblr" width="44" height="44"></amp-social-share>
  <amp-social-share type="twitter" width="44" height="44"></amp-social-share>
</div>
```

记住在`<head>`上引用`<amp-social-share>`脚本

```html
<script
  async
  custom-element="amp-social-share"
  src="https://cdn.ampproject.org/v0/amp-social-share-0.1.js"
></script>
```

## 结论和下一步

恭喜！你已经完成了 AMP 初学者的课程，并成功创建你的第一个 AMP 页面。

反思一下到目前为止的构建。你已经创建了一个引人入胜的 AMP 网页，有图片，轮播图和视频。想一想我们不需要做的工作，像编写 JavaScript 允许我们跟踪轮播图当前滑动的哪一张，或者计算如何包装我们的轮播图从最后一张滑到第一张。我们没有沉迷于细节和繁琐的工作，而是专注于关注构建出让我们蓬勃发展的奶酪自行车业务更加高效的网站。

正如你课程中所看到的，大多数现代化网站有的功能都有对应的 AMP 组件。就像我们目前所做的那样，我们通常只需要少量的额外代码就可以使用这些组件来构建出完整功能的网站。在这个课程中，你需要学习如何找到并使用 AMP 组件来构建出简单的用户页面。向你介绍了 AMP 的简单和强大和它提供的工具。最后，你学习了 AMP 验证器以及它在启用 AMP 缓存的作用性。在我们下面的课程中，我们将学习如何处理用户输入和事件，如何在 AMP 组件上调用 action 去改变它的状态和外观，如何组合多个 AMP 组件来只做出更加精良的用户页面。我们将继续在 Chico 的奶酪网站上扩大规模，让 Chico 有机会去打动爱好奶酪自行车的人。

[这里](https://aquamarine-baritone.glitch.me/)是今天我们构建的完成版本。
以下是其他额外话题和链接去探索：

- [AMP By Example](https://amp.dev/zh_cn/documentation/examples/?format=websites)
- [All about Page Discovery](https://amp.dev/zh_cn/documentation/guides-and-tutorials/optimize-and-measure/discovery/?format=websites)
- [Disallowed HTML Tags](https://amp.dev/zh_cn/documentation/guides-and-tutorials/learn/spec/amphtml/?format=websites#html-tags)
- [Restricted CSS Rules & Animations](https://amp.dev/zh_cn/documentation/guides-and-tutorials/develop/style_and_layout/style_pages/?format=websites)
- [The AMP Boilerplate](https://amp.dev/zh_cn/documentation/guides-and-tutorials/start/create/basic_markup/?format=websites)
- [List of available AMP Components](https://amp.dev/zh_cn/documentation/components/?format=websites)

## 附录

### 解释样板 AMP HTML

#### <link rel="canonical">标签

AMP 刚开始时，它仅用于创建用于移动设备的网页。一个网页应该有一个服务于移动设备的 AMP 版本，一个用于桌面设备的常规 HTML 编写的版本（成为“canonical”版本）。你应该使用`<link>`标签来连接这两个版本，让搜索引擎知道这两个文档代表相同的网页。

所以，非 AMP 文档包含 AMP 文档的链接。比如：

```html
<link rel="amphtml" href="https://www.site.com/amp/document.html" />
```

并且，AMP 文档包含非 AMP 文档的链接，比如：

```html
<link rel="canonical" href="https://www.site.com/document.html" />
```

现在 AMP 功能更加强大，除非你在桌面页面上需要其他功能，在移动和桌面两个设备上使用 AMP 都很简单。这样一来，你只需要维护一个页面而非两个！尽管如此，这个`<link>`仍然需要。这种情况下，你只需要简单的连接页面到自己，比如：

```html
<link rel="canonical" href="https://www.site.com/amp/document.html" />
```

在所有设备上都使用一个 AMP 页面被称之为“canonical AMP”。这就是我们为奶酪自行车商店所做的。

#### amp-boilerplate <style> 标签

所有 AMP HTML 页面一定要在`<head>`标签中包含一些默认样式。这个样式会影响到页面外观，直到 AMP 库被完全加载。从本质上讲，它目的是先隐藏内容，直到页面完全准备好，即页面上的所有元素都准备好且 AMP 知道在哪里渲染以及它们将要占多少空间。完成此操作，页面就会淡入。这样，用户可以立刻以最终形式查看页面，使它们感觉页面以及立刻加载完成。

#### 为什么使用视窗`<meta>`标签

AMP 可以在移动和桌面设备上运行。因为用户可能在任何一个设备上运行的你的网页，最好是在开发的时候就在这两个设备上检查你的网页。点击移动手机设备图标就可以在 Chrome DevTools 中模拟移动设备体验：
![Mobile preview in DevTools](https://amp.dev/static/img/courses/beginner/image5.png?width=470&hash=8bd8fac56020d9cadf3fe022193a08532cb0714e)

现在从菜单中选择“Nexus Fx”移动设备：
![Select a mobile device](https://amp.dev/static/img/courses/beginner/image1.png?width=390&hash=4c3da386bfacbc8823e930dd9ded686ed4f5115c)

你应该可以看到你浏览器中选择的设备对应页面显示页面的效果：
![A simulation of how the page looks on mobile](https://amp.dev/static/img/courses/beginner/image11.png?width=470&hash=2980d95ab3572d7d21c381f9bdd4251c036aa04b)

注意内容不应该填充满整个移动设备。这个“viewport”标签可以解决这个问题。这个标签根据给定的屏幕大小缩放页面。由于我们想要 AMP 页面对移动设备优化，并使其变成响应式，不用说 AMP 验证器需要此标签。

所以下面标签一定要放到我们 AMP 页面的`<head>`标签中，将其添加到快捷图标链接下方。

```html
<meta
  name="viewport"
  content="width=device-width,minimum-scale=1,initial-scale=1"
/>
```

如果你刷新页面，现在在小屏幕上看起来会好一些，比如这样：
![Mobile optimized page](https://amp.dev/static/img/courses/beginner/image20.png?width=470&hash=4066d835368fd81e7e1ef796a075bb02b187b1f3)
除了标题之外，你不会注意到太大的区别。随着我们跟进一步了解缩放的原理，你可以尝试一下。

### AMP 中的懒加载

“Lazy-loading”意味着资源（图片，数据，视频，脚本等）不是立刻加载，只有在需要的时候才去加载。AMP 下载资源时，它会优化下载，所以以便更重要的资源被首先下载。图片和广告只有在某些条件满足时才下载，如果它们被用户看到，或者用户可能很快速的滑动它们。这些媒体资产的等效组件(`<amp-img>`代替`<img>`)被称为"托管资源"，因为它们是否以及何时将它们加载显示给用户由 AMP 决定。AMP 可以随时决定卸载当前用户看不到的资源。AMP 性能优化之一就是要求元素如`<amp-img>`之类的元素预先声明其高度。有助于帮助 AMP 计算如何最好的方式布局显示。这非常关键，例如，因为 AMP 会预先加载第一视窗的所有资源，即用户访问网站第一眼看到的所有资源。

### Fixed vs Responsive 布局

页面包含布局系统以保证在浏览器渲染页面之前页面布局尽可能的严格。该系统给我们提供了一个 layout 属性，让我们以各种方式定位和缩放元素 -- 固定尺寸，响应式设计，固定高度等等。布局系统强制某些元素的尺寸声明。

这个布局属性对于大多数元素来说是有效的，它指定了 AMP 组件在页面上的显示方式。两种常见的 layout 属性值是“fixed”和“responsive”。如果元素有固定布局，则宽度和高度一定存在。然后，元素将保持此精确大小（以像素为单位），不用担心屏幕或者视图窗口发生变化。如果元素是响应式布局，同样宽和高也一定存在。这种情况下，虽然，元素将自动调整大小以占用可用空间，维持给定宽高的尺寸比列。可用空间依赖于它的父元素。