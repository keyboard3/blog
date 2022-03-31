---
title: VirtualizedList
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-15 11:01:06
password:
summary:
tags: [react-native, api, 翻译]
categories:
---

# [VirtualizedList](https://reactnative.dev/docs/virtualizedlist)

## 介绍

是[FlatList](/blog/flatlist)和[SectionList](https://reactnative.dev/docs/sectionlist)的父类实现。有更好的记录。通常情况下应该使用`FlatList`，除非需要更灵活采用它。比如你需要使用不可变数据
[中文文档](https://reactnative.cn/docs/virtualizedlist/)
虚拟化列表通过维护一个可见的元素在有限的窗口，不可见的元素维护一个空白空间来替代所有离开屏幕的所有元素。这种方式可以适配所有滚动行为，如果元素滚动离可视区域越远它就获得一个低优先级，否则就获得一个高优先级。通过这种方式来尽可能的减少见到空白空间
**一些警告**

- 当内容滚动出可视的渲染窗口，滚出部分的状态将丢弃。保证每项的数据被外部存储保存，如 Flux,Redux 以及 Relay.
- 它是`PureComponent`意味着，如果`props`数据内容`浅对比`相等这不会重新渲染。保证`renderItem`函数依赖传入参数`data`以及父组件状态（`extraData`），否则不会刷新
- 为了优化内存的同时保持平滑滚动，内容被异步离屏渲染。这意味着可能滑动速度可能大于内容的填充速度，会看到短暂的白屏。这是优化而不得不做出的妥协，每个应用可以根据自己的需要来调整对应的参数，我们仍然在这方面的优化做努力。
- 默认列表会将 item 数据项的`key`属性作为单项组件的 React key。另外，你可以提供自定义函数`keyExtractor`来获取 key

## Props

继承自[ScrollView Props](/blog/scrollview#props)

### renderItem

```
(info: any) => ?React.Element<any>
```

从`data`数据中获取 item 数据，将它渲染到列表上

### data

它是一个默认的访问器函数，默认认为数组中带有{key:string}属性。但是你可以重写`getItem`, `getItemCount`, 和 `keyExtractor`，来覆盖基于 data 的默认数据

### getItem

```
(data: any, index: number) => object;
```

用于从任何数据中提取 item,它是一个通用的数据提取器

### getItemCount

```
(data: any) => number;
```

决定有多少 item 的数量

### debug

`debug`将会打开额外的日志以及悬浮层来帮助 debug 和实现。但是对性能有影响

### extraData

告诉列表需要重绘的标记样式（因为列表是`PureComponent`）。`renderItem`, Header, Footer 依赖除了`data`以外的任何内容，变更都需要告知`extraData`，要将它作为不可变对象看待。

### getItemLayout

这是一个可选的优化项，如果你提前知道单项的固定宽高可以跳过组件进行的动态布局计算。
利用它在上百项下有很难高的性能。
如果你指定了分割线，高度计算需要包括分隔线进去

### initialScrollIndex

首次渲染不从顶部的第一项开始，取而代之的是`initialScrollIndex`开始。它会禁止掉`initialNumToRender`的优化的项目放在内容中，立刻从 initial index 开始的位置渲染。需要设置`getItemLayout`属性

### inverted

翻转滚动的方向（应该是下滑变成上拉吧），实质是将`scale`设置成-1

### CellRendererComponent

单元格的渲染，可以是 component, function

### listKey

对于列表来说唯一的 key。如果有多个列表同级且嵌套在 VirtualizedList,它的 key 对于虚拟化列表的工作时必须的

### ListEmptyComponent

当列表内容是空的时候渲染它，可以是 component, function, element

### ListItemComponent

每项的渲染，可以是 component, function

### ListFooterComponent

在所有列表元素底部，可以是 component, function, element

### ListFooterComponentStyle

为内部的`ListFooterComponent`提供样式

### ListHeaderComponent

在所有列表元素顶部，可以是 component, function, element

### ListHeaderComponentStyle

为内部的`ListHeaderComponent`提供样式

### onLayout

在 List 计算布局是会调用

### onRefresh

如果提供，一个平台标准的组件就会被添加到`下拉刷新`功能上。保证`refreshing`属性被正确设置

### onScrollToIndexFailed

```
(info: {
    index: number,
    highestMeasuredFrameIndex: number,
    averageItemLength: number,
  }) => void
```

当滚动到尚未测量的索引时，用于处理这个错误。推荐的操作是计算你自己的偏移量然后滚动到它，或者尽可能的滚，然后在呈现更多内容之后再试一次。

### onViewableItemsChanged

```
(info: {
   viewableItems: array,
   changed: array,
 }) => void
```

可见行变化时被调用。可见范围和变化频次可以去设置`viewabilityConfig`

### refreshing

等待新数据刷新的状态为 true

### refreshControl

可以自定义刷新组件。当设置成功之后，它会覆盖掉内置的组件。

### removeClippedSubviews

在大列表时可能会提高滚动性能

> 有 bug，在某些场景下会导致内容丢失。谨慎使用

### renderScrollComponent

```
(props: object) => element;
```

渲染一个自定义的滚动组件，跟`RefreshControl`有一点不一样

### viewabilityConfig

看更全的文档可以去看[ViewabilityHelper.js](https://github.com/facebook/react-native/blob/master/Libraries/Lists/ViewabilityHelper.js)库
viewabilityConfig 是一个对象具有下面属性

| PROPERTY                         | REQUIRED | TYPE    |
| -------------------------------- | -------- | ------- |
| minimumViewTime                  | No       | number  |
| viewAreaCoveragePercentThreshold | No       | number  |
| itemVisiblePercentThreshold      | No       | number  |
| waitForInteraction               | No       | boolean |

要求至少有其中一个在`viewAreaCoveragePercentThreshold`，`itemVisiblePercentThreshold`之间。它需要在构造函数中赋值，避免报错
`Error: Changing viewabilityConfig on the fly is not supported`

```ts
constructor (props) {
  super(props)

  this.viewabilityConfig = {
      waitForInteraction: true,
      viewAreaCoveragePercentThreshold: 95
  }
}
```

```ts
<FlatList
    viewabilityConfig={this.viewabilityConfig}
  ...
```

- minimumViewTime
  单个 Item 达到最小的物理显示时间（单位是毫秒）就会触发 viewability 回调。一个较大的值表明滚动不停的话，将没有内容会被标记已显示。
- viewAreaCoveragePercentThreshold
  部分被遮挡的 Item 必须大于可视窗口的百分之多少时才会被记为已显示（0-100）。如果全部显示则也会被认为已显示。0 表示离可视窗口为 0 像素是才被认为显示。100 表示被遮挡的内容填充满屏幕时才会被认为已显示
- itemVisiblePercentThreshold
  跟`viewAreaCoveragePercentThreshold`一样，但是会认为 Item 自身被显示了百分之多少才算已显示，而不是它覆盖可见区域的比列
- waitForInteraction
  只有在用户交互或者 recordInteraction 被调用才去计算 item 是否已显示

### viewabilityConfigCallbackPairs

`ViewabilityConfig`/`onViewableItemsChanged`值对的列表，`ViewabilityConfig`'s 条件会触发响应的`onViewableItemsChanged`调用

### horizontal

会将 item 从竖着堆叠变成横着摆放

### initialNumToRender

指定首屏需要有多少元素被渲染，应该刚好填充满屏幕。注意这些元素在窗口滚动过程中不会卸载，为了在提高执行`scroll-to-top`操作时不需要重新渲染。

### keyExtractor

用来提取 item 下独一无二的 key，key 作为重绘时缓存的依据。默认是取 item.key，获取失败会回滚到 index，机制和 React 一样

### maxToRenderPerBatch

每个增量渲染批次最大的渲染项目数。一次渲染的越多，填充率越好。但是渲染内容会影响到按钮点击和其他交互行为的响应，因此可能会造成卡住

### onEndReached

```
(info: {distanceFromEnd: number}) => void
```

当滚动的位置在渲染内容的触摸区内就会调用

### onEndReachedThreshold

距离列表底部多远时触发`onEndReached`回调，这是一个比值。比如 0.5 距离底部为可见内容一半长度时触发

### updateCellsBatchingPeriod

渲染低优先级批次的间隔时间，例如渲染屏幕外的项目。类似于填充率和响应度的妥协`maxToRenderPerBatch`

### windowSize

决定渲染的总数量，单位是项目数。默认是 21 个，如果单项占满整个屏幕，那么在上方会有 10 个屏幕，下放也会有 10 个屏幕。减少这个的数量会提高内存占用和性能，但是可能会带来快速滚动时带来的白屏

### disableVirtualization

> 废弃。虚拟化可以提供高效的性能和内存占用，但是会将离开屏幕的元素卸载掉。你可能只在需要 debug 的时候需要它

### persistentScrollbar

是否一直显示滚动条

### progressViewOffset

当偏移的位置需要加上加载指示器，可以去设置。(Android)

## Methods

### scrollToEnd()

滚动到底部，如果没有`getItemLayout`行为会比较混乱
**params**

- animated (boolean)：滚动时是否需要动画，默认是 true

### scrollToIndex()

滚动到指定行，且可以指定可显示的位置，`viewPosition`为 0 时会在视口的顶部，1 时在视口底部，0.5 在视口中间。

> 注意：如果没有指定 getItemLayout 属性则没有办法滚动到渲染窗口之外的位置

**params**

- animated (boolean)：滚动时是否需要动画，默认是 true
- index (number)：滚动位置的索引，必须
- viewOffset (number)：一个固定值标识指定行后的偏移距离
- viewPosition (number)：表示指定行在可视窗口的位置，上面已经表述过了

### scrollToItem()

滚动到指定数据的行，它会线性扫描 data。建议使用`scrollToIndex`
**params**

- animated (boolean)：滚动时是否需要动画，默认是 true
- item (object)：滚动位置的对象，必须
- viewPosition (number)：表示指定行在可视窗口的位置，上面已经表述过了

### scrollToOffset()

滚动至列表内容下的偏移像素
**params**

- offset (number)：滚动的偏移距离。如果是在水平模式下，这个就是 x 轴距离。否则就是 y 轴距离。必须
- animated (boolean)：滚动时是否需要动画，默认是 true

### recordInteraction()

告诉列表有交互了，当`waitForInteractions`是 true 会触发 view 是否显示的计算（即使用户没有滚动）。在点击 item 或者导航跳转的时候该方法也会被调用

### flashScrollIndicators()

让滚动指示器显示短暂

### getScrollResponder()

提供滚动响应者的引用

### getScrollableNode()

提供滚动节点的引用
