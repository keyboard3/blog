---
title: 转盘抽奖库 lottery-wheel
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-11 23:15:04
password:
summary:
tags: [js, animate, library, 翻译]
categories:
---

[原文](https://github.com/fralonra/lottery-wheel)
一个帮助你实现转盘抽奖游戏的库。使用了[Snap.svg](https://github.com/adobe-webplatform/Snap.svg)和[anime.js](https://github.com/juliangarnier/anime/)。
[demo](https://fralonra.github.io/lottery-wheel/demo/)

## 使用

```shell
npm install lottery-wheel
```

或者下载最新版[release](https://github.com/fralonra/lottery-wheel/releases)。
然后在你的 HTML 中引用 lottery-wheel.min.js 或者 lottery-wheel.js

```html
<script src="/path/to/lottery-wheel.min.js"></script>
```

假设在你的 html 文件中有一个 id 为"wheel"的元素。

```html
<svg id="wheel"></svg>
```

然后你可以使用下面的代码来创建抽奖轮盘:

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: [
    {
      text: "apple",
      chance: 20,
    },
    {
      text: "banana",
    },
    {
      text: "orange",
    },
    {
      text: "peach",
    },
  ],
  onSuccess(data) {
    console.log(data.text);
  },
});
```

## API

### Methods

- constructor(option)
  option 详情看[下面](https://github.com/fralonra/lottery-wheel#options)
- draw()
  当 draw 属性被设置为 false 时，手动渲染轮子。
  ```js
  const wheel = new Wheel({
    el: document.getElementById("wheel"),
    data: ["Beijing", "London", "New York", "Tokyo"],
    draw: false,
  });
  setTimeout(() => {
    wheel.draw();
  }, 2000);
  ```

### Options

| 属性           | 描述                                                                                                                      | 类型     | 默认值     |
| -------------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ---------- |
| el             | 当轮子被挂载的元素.[详情](https://github.com/fralonra/lottery-wheel#el)                                                   | Object   | -          |
| data           | 奖品数组。[详情](https://github.com/fralonra/lottery-wheel#data)                                                          | Array    | -          |
| pos            | 转轮的左上角与父元素关联 (el 元素)                                                                                        | Array    | [0,0]      |
| radius         | 转轮的半径, px                                                                                                            | Number   | 100        |
| buttonText     | 按钮上的文字                                                                                                              | String   | 'Draw'     |
| fontSize       | 奖品上的文字大小                                                                                                          | Number   | (自动生成) |
| buttonWidth    | button 的宽度,px                                                                                                          | Number   | 50         |
| buttonFontSize | button 上文本的大小                                                                                                       | Number   | (自动生成) |
| limit          | 转轮可以运行的最大次数                                                                                                    | Number   | 0 (不限制) |
| duration       | 动画要执行多久 单位是毫秒                                                                                                 | Number   | 5000       |
| turn           | 在动画期间，转轮旋转的最小圈数                                                                                            | Number   | 4          |
| draw           | true 的话，转轮将在实例被创建时立刻渲染。否则你可以调用[draw](https://github.com/fralonra/lottery-wheel#draw)手动渲染它。 | Boolean  | true       |
| clockwise      | true 的话，旋转运动将是顺时针。否则，将是逆时针方向                                                                       | Boolean  | true       |
| theme          | 预设置使用的颜色 [详情](https://github.com/fralonra/lottery-wheel#themes)                                                 | String   | default    |
| image          | 允许你使用图片资源渲染转轮。看[image](https://github.com/fralonra/lottery-wheel#image)                                    | Object   | -          |
| color          | 用来重写当前主题颜色的对象。看[themes](https://github.com/fralonra/lottery-wheel#themes)                                  | Object   | -          |
| onSuccess      | 当奖品被领取成功会调用这个回调函数。[详情](https://github.com/fralonra/lottery-wheel#onsuccess)                           | Function | -          |
| onFail         | 超过领取限制次数时，尝试领取会调用这个回调函数                                                                            | Function | -          |
| onButtonHover  | 当鼠标划过按钮上时触发回调 [详情](https://github.com/fralonra/lottery-wheel#onbuttonhover)                                | Function | -          |

#### el

el 属性定义了在哪个元素上渲染转轮。你应该传递一个 DOM 元素：

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: [],
});
```

#### data

这个 data 属性使用数组来定义转轮游戏关联的事务。数组的长度要在 3 和 12 之间。

最简单的方式是将每个奖品的名字放到数组中：

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: ["Beijing", "London", "New York", "Tokyo"],
});
```

它将会生成下面带默认 options 的转轮。每个奖品奖项都有相同的机会被抽奖，程序创建 4 个奖品对象，text 属性被 data 数组中的字符串设置，chance 属性自动为 1。
![](https://github.com/fralonra/lottery-wheel/blob/master/doc/images/data.png)
你也可以自定义每个奖品的对象。奖品对象的属性在[这里](https://github.com/fralonra/lottery-wheel#prize-object)被列出来

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: [
    {
      text: "Beijing",
      chance: 5,
    },
    {
      text: "London",
      chance: 4,
    },
    "New York",
    "Tokyo",
  ],
});
```

#### onSuccess

当奖品被领取成功之后，回调函数被调用。

| 参数 | 描述         | 类型   |
| ---- | ------------ | ------ |
| data | 领取奖品对象 | Object |

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: ["prize A", "prize B", "prize C", "prize D"],
  onSuccess(data) {
    alert(`Congratulations! You picked up ${data.text}`);
  },
});
```

#### onFail

尝试已经达到领取限制时调用这个回调。(限制是 limit 属性)。注意默认可以领取无限次

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: ["prize A", "prize B", "prize C", "prize D"],
  limit: 1,
  onFail() {
    alert("You have no more chance to draw");
  },
});
```

在这种情况下，如果你已经领取奖品，下次点击这个按钮将会弹出警告对话框。

#### onButtonHover

当鼠标滑过按钮时会调用

| 参数   | 描述                                                                   | 类型   |
| ------ | ---------------------------------------------------------------------- | ------ |
| anime  | 引用 animejs。看这个使用[文档](https://github.com/juliangarnier/anime) | -      |
| button | 参考按钮所在的捕获元素                                                 | Object |

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: ["prize A", "prize B", "prize C", "prize D"],
  onButtonHover(anime, button) {
    anime({
      targets: button.node,
      scale: 1.2,
      duration: 500,
    });
  },
});
```

奖品对象

| 属性      | 描述                                                                                                               | 类型   | 默认 |
| --------- | ------------------------------------------------------------------------------------------------------------------ | ------ | ---- |
| text      | 奖品名字                                                                                                           | String | ''   |
| chance    | 奖品可以被领取的可能性。值越高，奖品被领取的概率越大。概率计算的公式 `probability = 1 * chance / (每个chance的和)` | Number | 1    |
| color     | 奖品的背景颜色 (重写转轮的 color.prize)                                                                            | String | -    |
| fontColor | 文本的颜色 (重写转轮的 color.fontColor)                                                                            | String | -    |
| fontSize  | 文本的大小（将重写转轮的 fontSize）                                                                                | Number | -    |

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: [
    {
      text: "Beijing",
      color: "silver",
      fontSize: 24,
    },
    {
      text: "London",
      fontColor: "#008000",
    },
    "New York",
    "Tokyo",
  ],
});
```

上面的代码的转轮结果是
![](https://github.com/fralonra/lottery-wheel/blob/master/doc/images/prize.png)

#### 主题

主题是个存储转轮颜色的对象。它有下面的属性：

- border: 转轮边框的背景颜色
- prize: 奖品部分的背景颜色
- button: 按钮部分的背景颜色
- line: 奖品部分之间的线条颜色
- prizeFont: 奖品文本的颜色
- buttonFont: 按钮文本的颜色

下面是 3 个预设置的主题：

- default

```js
default: {
    border: 'red',
    prize: 'gold',
    button: 'darkorange',
    line: 'red',
    prizeFont: 'red',
    buttonFont: 'white'
}
```

- light

```
light: {
    border: 'orange',
    prize: 'lightyellow',
    button: 'tomato',
    line: 'orange',
    prizeFont: 'orange',
    buttonFont: 'white'
}
```

![](https://github.com/fralonra/lottery-wheel/raw/master/doc/images/theme-light.png)

- dark

```js
dark: {
    border: 'silver',
    prize: 'dimgray',
    button: 'darkslategray',
    line: 'silver',
    prizeFont: 'silver',
    buttonFont: 'lightyellow'
}
```

![](https://github.com/fralonra/lottery-wheel/raw/master/doc/images/theme-dark.png)
你可以通过设置 color 属性来改变颜色

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: ["Beijing", "London", "New York", "Tokyo"],
  theme: "dark",
  color: {
    button: "#fef5e7",
    buttonFont: "#34495e",
  },
});
```

![](https://github.com/fralonra/lottery-wheel/raw/master/doc/images/color.png)

#### Image

使用 image 属性让你可以通过对象的设置，使用一个已存在的资源来渲染转轮。它生成图像 svg 元素，并支持 jpeg,png 和 svg 格式。
属性

| 参数      | 描述                                                                      | 类型   |
| --------- | ------------------------------------------------------------------------- | ------ |
| turntable | 转盘图片                                                                  | String |
| button    | 按钮的图片。宽度由 buttonWidth 控制，剩下的按照比列缩放。默认在转盘的中心 | String |
| offset    | 按钮的 y 轴的偏移。如果是负，按钮向上移动                                 | Number |

这里的案例显示当时用仓库下的/doc/images 的图片的效果：

```js
const wheel = new Wheel({
  el: document.getElementById("wheel"),
  data: ["Prize A", "Prize B", "Prize C", "Prize D", "Prize E", "Prize F"],
  image: {
    turntable: "turntable.png",
    button: "button.png",
    offset: -10,
  },
});
```

![](https://github.com/fralonra/lottery-wheel/blob/master/doc/images/image.png)

## 最后

我开了分支，为它支持了 remoteDrawn 属性，支持了奖品有远程接口支持的功能
[keyboard3/lottery-wheel](https://github.com/keyboard3/lottery-wheel)
