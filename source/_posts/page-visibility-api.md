---
title: Page 可见性 API
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-11 16:49:15
password:
summary:
tags: [mdn, 翻译]
categories:
---

[原文](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API)

使用选项卡浏览，任何 web 页面都有可能在后台，因此对用户不可见。这个页面可见性 API 提供了当 document 显示和隐藏的事件给你监听，以及查看当前页面可见性的状态。

> 注意：这个 API 对于保存资源和提高性能非常有用，它使得页面不显示的时候可以避免执行不必要的任务。

当用户缩小窗口或者切换其他 tab 页的时候，这个 API 会发送[visibilitychange](https://developer.mozilla.org/en-US/docs/Web/Events/visibilitychange)事件告诉监听者页面状态发生变更。你可以检测这个事件执行不同的行为。例如，如果你的 web 应用正在播放视频，当用户切换 tab 进入后台时暂停视频，在用户回到页面时重新播放视频。用户不会丢失它的播放位置，这个视频的音轨不会干扰到新 tab 页面的声音，并且用户在这个期间不会错误任何视频。

iframe 的可见性状态和福文档一样。使用 css 属性(像 display:none;)不会触发可见性事件或者改变 document 包含的 frame 的状态。

### 使用场景

让我们考虑下页面可见性 API 的几个用户场景：

- 站点有图片轮播控件，它不应该前进下一张除非用户看到这个页面了。
- 当页面不再显示，显示信息仪表盘的应用程序不应该继续轮询服务器了
- 页面想要检测什么时候被渲染，以便可以精确的计算出页面浏览量。
- 网站像在当设备处于待机状态时关闭声音（用户按住电源按钮关闭屏幕显示）

开发者历史上采用过不完善的代理来检测这一点。例如，在 window 上监听 blur 和 focus 事件帮助你知道什么时候你的页面不再是激活页面，但是它没有告诉你你的页面是不是真的在用户面前隐藏了。这个页面可见性 API 可以做到。

> 注意：onblur 和 onfocus 会告诉你用户切换了窗口焦点，但并不意味着它隐藏了。只有当用户切换了选项卡或者缩小了包含 tab 的浏览器窗口才算是页面隐藏了。

### 策略的目的是为了后台页面性能

区别于 Page 可见性 API，用户代理通常有很多策略来减轻后台或隐藏 tab 带来的性能影响。这些包括:

- 许多浏览器会停止向后台 tab 和隐藏的 iframe 发送[requestAnimationFrame()](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame)回调，为了提高性能以及电池寿命。
- 计时器像[setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)在后台/未激活 tab 中是节流的，帮助提高性能。详情见[延迟超过指定值的原因](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout#Reasons_for_delays_longer_than_specified)。
- 在现代浏览器（firefox58+，Chrome 57+）中基于预算的后台超时限制是有效的，对后台计时器 CPU 使用率做了额外的限制。在现代浏览器的这类操作都相似，详细看下面：
  - 在 Firefox 中，后台选项卡的窗口每个都有自己的时间预算（时间单位是毫秒） -- 预算最大最小值分贝是 +50 毫秒和-150 毫秒。chrome 类似，预算单位是秒。
  - 窗口控制 30 秒之后进行节流，它的节流延时器规则和窗口计时器指定的规则一样（同样，[延迟时间比指定长的原因](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout#Reasons_for_delays_longer_than_specified)）。在 Chrome 中，它的值是 10 秒。
  - 计时器任务仅当预算为负的时候才允许执行。
  - 一旦计时器代码运行结束，它执行花费的时间是减去了窗口的超时预算的时间。
  - 在 Firefox 和 Chrome 中，预算以每秒 10 毫秒的速度增加。

某些进程不收此节流行为的限制。在这些场景中，你可以使用页面可见性 API 来减少用户隐藏页面是的性能影响。

- 播放音频的 tab 页面可以被视为前台并且不被节流
- 运行实时网络连接的代码 tab 页面（[WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)和[WebRTC](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API)）不被节流，为了避免关闭这些连接造成的意外关闭。
- [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)进程也不应该被节流，为了避免超时。

### 案例

查看[现场案例](http://daniemon.com/tech/webapps/page-visibility/)（带声音的视频）
这个案例当你选择其他 tab 时视频暂停，当你回到这个 tab 时视频播放，它被下面的代码创建：

```js
// Set the name of the hidden property and the change event for visibility
var hidden, visibilityChange;
if (typeof document.hidden !== "undefined") {
  // Opera 12.10 and Firefox 18 and later support
  hidden = "hidden";
  visibilityChange = "visibilitychange";
} else if (typeof document.msHidden !== "undefined") {
  hidden = "msHidden";
  visibilityChange = "msvisibilitychange";
} else if (typeof document.webkitHidden !== "undefined") {
  hidden = "webkitHidden";
  visibilityChange = "webkitvisibilitychange";
}

var videoElement = document.getElementById("videoElement");

// If the page is hidden, pause the video;
// if the page is shown, play the video
function handleVisibilityChange() {
  if (document[hidden]) {
    videoElement.pause();
  } else {
    videoElement.play();
  }
}

// Warn if the browser doesn't support addEventListener or the Page Visibility API
if (typeof document.addEventListener === "undefined" || hidden === undefined) {
  console.log(
    "This demo requires a browser, such as Google Chrome or Firefox, that supports the Page Visibility API."
  );
} else {
  // Handle page visibility change
  document.addEventListener(visibilityChange, handleVisibilityChange, false);

  // When the video pauses, set the title.
  // This shows the paused
  videoElement.addEventListener(
    "pause",
    function () {
      document.title = "Paused";
    },
    false
  );

  // When the video plays, set the title.
  videoElemnt.addEventListener(
    "play",
    function () {
      document.title = "Playing";
    },
    false
  );
}
```

### 将属性添加到 Document 接口

页面可见性 API 添加下面的属性给 Document 接口：

- Document.hidden **只读**
  如果页面对用户不显示这个值就是 true，否则就是 false
- Document.visibilityState | **只读**
  [DOMString](https://developer.mozilla.org/en-US/docs/Web/API/DOMString)表明了 document 的当前显示状态，值可能有：
  - visible
    页面内容至少部分被显示。实践中意味着页面是非缩小窗口中的前台 tab
  - hidden
    页面内容没有显示给用户看，由于 document 的 tab 在后台或者是 window 被缩小了，又或者是设备屏幕息屏了。
  - prerender
    这个页面内容被预渲染并且没有显示给用户看。document 可以以预渲染状态开始，但无法从其他状态变化过来，因为一个 document 只有一次预渲染机会。
  - unloaded
    这个页面正在被从内存中卸载
- Document.onvisibilitychange
  [EventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventListener)提供当[visibilitychange](https://developer.mozilla.org/en-US/docs/Web/Events/visibilitychange)事件被触发时的代码调用。
  ```js
  //startSimulation and pauseSimulation defined elsewhere
  function handleVisibilityChange() {
    if (document.hidden) {
      pauseSimulation();
    } else {
      startSimulation();
    }
  }
  document.addEventListener("visibilitychange", handleVisibilityChange, false);
  ```