---
title: ScrollView
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-15 14:06:01
password:
summary:
tags: [react-native, api, 翻译]
categories:
---

# [ScrollView](https://reactnative.dev/docs/scrollview)

## 参考

这篇文章[React-Native 之 ScrollView 使用](https://www.jianshu.com/p/29563c7c548b)结合了使用案例非常棒

## 介绍

一个对原生 ScrollView 的包装组件，同时还集成了触摸锁定“响应者”系统
记住 ScrollViews 需要有确定的高度才能正常工作，因为需要在确定的容器中填充不确定高度的子组件（这样就可以滚动操作）。为了确定 ScrollViews 的高度，可以直接给它设置高度或者让它所有的父组件都为确定高度。忘记{flex:1}会向下 View 栈传递会导致错误，通过元素诊断器来快速定位到那一层高度不确定。
不支持 ScrollView 内部其他响应者 block 掉 ScrollView，自己成为响应者
`ScrollView`vs[FlatList](/blog/flatlist)-应该用哪个？
`ScrollView`一次性渲染所有子组件，但会降低性能
想象一下如果你要显示非常大的列表，可能需要几屏内容。会一次性创建所有的 js 组件以及原生组件，大部分甚至不会显示，会导致渲染很慢以及增加内容占用
因此`FlatList`出现，`FlatList`懒渲染，当要显示的时候才会去渲染，当滚动出屏幕外就会删除 view 实例节省内存和提高处理速度
`FlatList`非常方便。可以渲染分割线，多列，无限加载指示器以及其他一些开箱即用的功能。
**ScrollView**

```ts
import React from "react";
import { StyleSheet, Text, SafeAreaView, ScrollView } from "react-native";
import Constants from "expo-constants";

export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <ScrollView style={styles.scrollView}>
        <Text style={styles.text}>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
          eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad
          minim veniam, quis nostrud exercitation ullamco laboris nisi ut
          aliquip ex ea commodo consequat. Duis aute irure dolor in
          reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla
          pariatur. Excepteur sint occaecat cupidatat non proident, sunt in
          culpa qui officia deserunt mollit anim id est laborum.
        </Text>
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: Constants.statusBarHeight,
  },
  scrollView: {
    backgroundColor: "pink",
    marginHorizontal: 20,
  },
  text: {
    fontSize: 42,
  },
});
```

## Props

继承自[View Props](https://reactnative.dev/docs/view#props)

### alwaysBounceHorizontal

仅 iOS。当 scroll view 水平滚动触摸到底边时产生回弹效果，即使是内容小余 scroll view 大小。当`horizontal={true}`时，默认值 true，否则就是 false

### alwaysBounceVertical

仅 iOS。当 scroll view 竖直滚动触摸到底边时产生回弹效果，即使是内容小余 scroll view 大小。当`horizontal={true}`时，默认值 false,否则就是 true

### automaticallyAdjustContentInsets

仅 iOS。控制如果是 ios，自动适配 scroll views 的 content inset，将 content view 放到导航条和工具栏之间。默认是 true。

### bounces

仅 iOS。如果内容大于 scroll view 两个方向的大小，触摸到边就会产生回弹效果。如果设置 false，即使 alwaysBounce\*\*设置了也没用。

### bouncesZoom

仅 iOS。true 的话手势会驱动缩放的动画效果，否则没有

### canCancelContentTouches

仅 iOS。false 的话触摸拖动元素将不会有反应，默认值是 true

### centerContent

仅 iOS。当内容小余 scroll view，内容会居中显示。当大于 scroll view 则没有效果。默认值是 false

### contentContainerStyle

样式应用到包裹所有子组件的 scroll content container 容器上

### contentInset

仅 iOS。滚动视图的边缘相对于内容的距离。默认是`{top: 0, left: 0, bottom: 0, right: 0}`

### contentInsetAdjustmentBehavior

仅 iOS。属性指定的话会用安全区的距离来调整内容区，默认值是"never"。只有在 ios 11 以上有效

### contentOffset

仅 iOS。用来设置起始滚动的偏移。默认值是`{x: 0, y: 0}`

### decelerationRate

浮点数值，确定用户释放手指之后 scroll view 滚动下降的速度。你可以用简短的字符串"normal"和"fast"来匹配响应 iOS 下的`UIScrollViewDecelerationRateNormal`和`UIScrollViewDecelerationRateFast`

- "normal" (default)，在 iOS 上 0.998，Android 上 0.985
- "fast"，iOS 上 0.99，Android 上 0.9

### directionalLockEnabled

仅 iOS。true 的话，ScrollView 会能够滚动的方向。默认是 false

### disableIntervalMomentum

这里意思含糊，云里雾里的。
如果是 true，无论手势滚动有多快，scroll view 都会停止在下一个 index 处(跟滚动释放的位置有关)。当 page 小余 ScrollView 的宽度时，可以用来水平分页。默认是 false

### disableScrollViewPanResponder

如果是 true，会禁用 Scroll View 的 js 事件响应，并将触摸控制完全交给子组件。如果`snapToInterval`启用将非常有用，因为它不遵循典型的触摸模式。不要在没有`snapToInterval`的正常 Scroll View 去启用它，否则在滚动时会出现无法预知的异常。默认值是 false

### endFillColor

仅 Android。有时候 ScrollView 会比内容填充占用更多的空间。在这种情况下，这个属性的颜色会填充掉剩余空间，避免使用背景颜色从而避免的多余的绘制。这是一种高级的优化，通常情况下不需要。

### horizontal

会将 item 从竖着堆叠变成横着摆放

### indicatorStyle

仅 iOS。滚动指示器的样式

- "default" (默认)，像"black"
- "black"，滚动指示器是黑色，在浅色背景下效果更好
- "white"，滚动指示器是白色，在黑色背景下效果更好

### invertStickyHeaders

如果固定 Header 应该固定在底部而不是在 ScrollView 顶部。通常使用在翻转的 ScrollView

### keyboardDismissMode

用户拖动的时候是否应该隐藏键盘
跨平台

- "none" (默认)，拖动不隐藏键盘
- "on-drag"，当拖动开始时键盘隐藏
  仅 ios 有效
- "interactive"，通过拖动的交互方式关闭键盘，同时同步触摸移动；向上拖动可以取消操作。在 Android 上不支持这种行为并且行为为 none

### keyboardShouldPersistTaps

当敲击后键盘应该保留显示

- "never"
- "always"
- "handled"
- "false"
- "true"

### maintainVisibleContentPosition

(这里有点懵逼，每个字能看懂，连起来就不知道啥意思)
仅 iOS。滚动视图将会调整滚动位置让当前第一个子组件可见，并且`minIndexForVisible`及更高将不会改变位置。这对于双向加载内容的列表有用，比如在聊天线程，否则新消息进来时可能造成滚动位置跳转。0 是常见值。但是 1 被用来跳过加载指示器或者其他不需要保持位置的内容。
如果用户在进行调整之前位于顶部的阈值之内，则`autoscrollToTopThreshold`用于在内容调整之后自动滚动到顶部。当用户想看到新消息滚动到那的应用程序非常有用，但是如果用户已经向上某种方式滚动并且滚动一堆消息
警告 1：启用此功能，滚动视图元素将重新排序，可能会造成跳转混乱。它可以被修复，但是当前没有计划。现在，使用此功能更不要在 ScrollViews or List 进行重新排序
警告 2：这在本机代码中使用 contentOffset 和 frame.origin 来计算可见性。关于内容是否“可见”，将不考虑遮挡，变换和其他复杂性

### maximumZoomScale

仅 iOS。最大允许的缩放比列，默认是 1

### minimumZoomScale

仅 iOS。最小允许的缩放比列，默认是 1

### nestedScrollEnabled

仅 Android。Android api21(5.0)以上才支持嵌套滚动。iOS 默认支持嵌套滚动

### onContentSizeChange

当 ScrollView 的可滚动内容大小放生变化时调用
处理函数被传递了宽和高参数
它被附加到 ScrollView 的内容容器的 onLayout 处理函数实现

### onMomentumScrollBegin

当动量滚动开始时调用（ScrollView 滑动开始）

### onMomentumScrollEnd

当动量滚动停止时调用（ScrollView 滑动停止）

### onScroll

在滚动的过程中，每帧最多调用一次此回调函数。调用的频率可以用`scrollEventThrottle`属性来控制。The event has the following shape (all values are numbers):

```
{
  nativeEvent: {
    contentInset: {bottom, left, right, top},
    contentOffset: {x, y},
    contentSize: {height, width},
    layoutMeasurement: {height, width},
    zoomScale
  }
}
```

### onScrollBeginDrag

当用户开始拖动视图时调用

### onScrollEndDrag

当用户停止拖动并且 view 开始停止或者开始滑动时被动调用

### onScrollToTop

仅 iOS。状态栏被点击时触发滚动到顶部

### overScrollMode

仅 Android。重写 overScrollMode 的默认值
**值可能为**

- "auto"：默认值，当内容足够大超过 ScrollView 时才允许用户滚动
- "always"：允许用户滚动视图
- "never"：不允许用户滚动

### pagingEnabled

如果 true，scroll view 停止在可视视图大小的倍数上。可以用来水平分页。默认是 false
注意：竖直分页不支持 Android

### persistentScrollbar

仅 Android。当它不被使用时不会变成透明。默认是 false

### pinchGestureEnabled

仅 iOS。如果 true，允许通过捏的手势放大缩小。默认是 true

### refreshControl

刷新组件，提供下拉刷新的功能。只有在竖直状态下有效

### removeClippedSubviews

在大列表时可能会提高滚动性能

> 有 bug，在某些场景下会导致内容丢失。谨慎使用

### scrollBarThumbImage

仅 VR。不想写....

### scrollEnabled

如果是 false，无法通过交互滚动。默认是 true。
注意仍然可以通过 scrollTo 来实现滚动

### scrollEventThrottle

仅 iOS。这个属性控制在滚动过程中，scroll 事件被调用的频率（单位是每秒事件数量）。更小的数值能够更及时的跟踪滚动位置，不过可能会带来性能问题，因为更多的信息会通过 bridge 传递。由于 JS 事件循环需要和屏幕刷新率同步，因此设置 1-16 之间的数值不会有实质区别。默认值为 0，意味着每次视图被滚动，scroll 事件只会被调用一次。

### scrollIndicatorInsets

仅 iOS。决定滚动条距离视图边缘的坐标。这个值应该和 contentInset 一样。默认值为{0, 0, 0, 0}。

### scrollPerfTag

仅 Android。用于在此滚动视图上记录滚动性能的标签。将强制打开动量事件（请参见 sendMomentumEvents）,这并没有做任何开箱即用的事情，您需要实现自定义本机 FpsListener 才有用

### scrollToOverflowEnabled

仅 iOS。scroll view 可以通过编程滚动超过内容大小。默认是 false

### scrollsToTop

仅 iOS。如果为 true，当状态被点击，scroll view 滚动到顶部。默认是 true

### DEPRECATED_sendUpdatedChildFrames

仅 iOS。如果为 true，则 ScrollView 将在滚动事件中发出 updateChildFrames 数据，否则将不计算或发出子帧数据。存在是为了支持遗留问题，而应使用 onLayout 来检索帧数据。默认值为 false。

### showsHorizontalScrollIndicator

如果 true，显示水平滚动的指示器。默认是 true

### showsVerticalScrollIndicator

如果 true，显示竖直滚动的指示器。默认是 true

### snapToAlignment

当设置了 snapToInterval，snapToAlignment 会定义 snap 与滚动视图之间的关系。

- start (默认值) 会将 snap 对齐在左侧（水平）或顶部（垂直）
- center 会将 snap 对齐到中间
- end 会将 snap 对齐到右侧（水平）或底部（垂直）

### snapToEnd

和`snapToOffsets`一起使用。默认，列表尾部作为 snap 的偏移量。设置`snapToEnd`为 false 禁用此行为，并允许列表在其结尾和最后一个 snapToOffsets 偏移量之间自由滚动。默认是 true

### snapToInterval

当设置了此属性时，会让滚动视图滚动停止后，停止在 snapToInterval 的倍数的位置。这可以在一些子视图比滚动视图本身小的时候用于实现分页显示 `snapToAlignment`以及`decelerationRate="fast"` 组合使用。覆盖可配置性较低的 pagesEnabled 属性。

### snapToOffsets

设置后，使滚动视图在定义的偏移处停止。这可用于对长度小于滚动视图的各种大小的子项进行分页,通常与`decelerationRate ="fast"`结合使用。重写可配置性较低`pagesEnabled`和 s`snapToInterval`属性。

### snapToStart

与`snapToOffsets`一起使用。默认，列表的开始作为停住点的偏移。设置`snapToStart`false 可以禁止此行为，允许礼包在开始和第一个 snap 偏移量之间滚动。默认是 true

### stickyHeaderIndices

一组子元素索引，确定滚动时哪些子元素停靠在屏幕顶部。例子，传递`stickyHeaderIndices={[0]}`将固定第一个子元素显示在顶部。这个属性不支持与`horizontal={true}`一起使用。

### zoomScale

仅 iOS。滚动内容的当前缩放比列。默认是 1

## Methods

### flashScrollIndicators()

让滚动指示器显示短暂

### scrollTo()

滚动到指定的 x,y 位置。可以带着动画滚动或者直接到该位置
注意：这个混乱的函数签名由于历史原因，这个函数接受分隔的参数而不是对象。由于 x，y 传值模棱两可，应该被废弃

### scrollToEnd()

竖直方向滚动到底部，水平方向滚动到右边。可以带动画滚动。Android 上可能需要制定滚动时常。如果不指定默认是包含动画

### scrollWithoutAnimationTo()

废弃应该用 scrollTo 代替
