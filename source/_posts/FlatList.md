---
title: FlatList
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-14 18:05:38
password:
summary:
tags: [react-native, api, 翻译]
categories:
---

# [FlatList](https://reactnative.dev/docs/flatlist)

## 介绍

一个高性能用于渲染基础列表，支持一些便利的特性
感觉[FlatList 中文版](https://reactnative.cn/docs/flatlist/)比英文原版都好。

- 跨平台支持
- 可选的横向展示
- 可配置的已显示的回调函数
- 列表头支持
- 列表尾支持
- 分割线支持
- 下拉刷新
- 滚动加载
- 滚动到指定行支持
- 多列支持

它是对[virtualizedlist](/blog/virtualizedlist)封装，继承自它的所有属性（同样也继承自[ScrollView](https://reactnative.dev/docs/scrollview)),这些继承的属性在这里没有列出来。此外还有以下注意事项：

- 当内容滚动出可视的渲染窗口，滚出部分的状态将丢弃。保证每项的数据被外部存储保存，如 Flux,Redux 以及 Relay.
- 它是`PureComponent`意味着，如果`props`数据内容`浅对比`相等这不会重新渲染。保证`renderItem`函数依赖传入参数`data`以及父组件状态（`extraData`），否则不会刷新
- 为了优化内存的同时保持平滑滚动，内容被异步离屏渲染。这意味着可能滑动速度可能大于内容的填充速度，会看到短暂的白屏。这是优化而不得不做出的妥协，每个应用可以根据自己的需要来调整对应的参数，我们仍然在这方面的优化做努力。
- 默认列表会将 item 数据项的`key`属性作为单项组件的 React key。另外，你可以提供自定义函数`keyExtractor`来获取 key

如果需要 section 支持，请使用[Section List](https://reactnative.dev/docs/sectionlist)
[flatlist-simple](https://snack.expo.io/?session_id=snack-session-fXpTgLMiC&preview=true&platform=web&iframeId=j4bavelnge&supportedPlatforms=ios,android,web&name=flatlist-simple&description=Example%20usage&waitForData=true)

```ts
import React from "react";
import { SafeAreaView, View, FlatList, StyleSheet, Text } from "react-native";
import Constants from "expo-constants";

const DATA = [
  {
    id: "bd7acbea-c1b1-46c2-aed5-3ad53abb28ba",
    title: "First Item",
  },
  {
    id: "3ac68afc-c605-48d3-a4f8-fbd91aa97f63",
    title: "Second Item",
  },
  {
    id: "58694a0f-3da1-471f-bd96-145571e29d72",
    title: "Third Item",
  },
];

function Item({ title }) {
  return (
    <View style={styles.item}>
      <Text style={styles.title}>{title}</Text>
    </View>
  );
}

export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={DATA}
        ItemSeparatorComponent={({ highlighted }) => <Text>--</Text>}
        renderItem={({ item, index, separators }) => (
          <Text>
            {item.title}
            {separators}
          </Text>
        )}
        keyExtractor={(item) => item.id}
      />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: Constants.statusBarHeight,
  },
  item: {
    backgroundColor: "#f9c2ff",
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});
```

为了渲染多列，请使用[numColumns](https://reactnative.dev/docs/flatlist#numcolumns)属性，用这种方式代替 flexWrap 布局解决单行高度的冲突

下面的复杂的多列显示使用案例演示了如何性能优化和避免 bug

- 通过传`extraData={selected}`给`FlatList`,可以保证 state 变更时`FlatList`被重渲染。没有设置这个属性,`FlatList`将不会知道需要重渲染任何 item，因为它是个`PureComponent`对比属性发现没有任何改变。
- `keyExtractor`告诉列表使用 id 来作为每项的 key

[flatlist-selectable
](https://snack.expo.io/?session_id=snack-session-IO5CwSQUH&preview=true&platform=web&iframeId=rua5k99frl&supportedPlatforms=ios,android,web&name=flatlist-selectable&description=Example%20usage&waitForData=true)

```ts
import React from "react";
import {
  SafeAreaView,
  TouchableOpacity,
  FlatList,
  StyleSheet,
  Text,
} from "react-native";
import Constants from "expo-constants";

const DATA = [
  {
    id: "bd7acbea-c1b1-46c2-aed5-3ad53abb28ba",
    title: "First Item",
  },
  {
    id: "3ac68afc-c605-48d3-a4f8-fbd91aa97f63",
    title: "Second Item",
  },
  {
    id: "58694a0f-3da1-471f-bd96-145571e29d72",
    title: "Third Item",
  },
];

function Item({ id, title, selected, onSelect }) {
  return (
    <TouchableOpacity
      onPress={() => onSelect(id)}
      style={[
        styles.item,
        { backgroundColor: selected ? "#6e3b6e" : "#f9c2ff" },
      ]}
    >
      <Text style={styles.title}>{title}</Text>
    </TouchableOpacity>
  );
}

export default function App() {
  const [selected, setSelected] = React.useState(new Map());

  const onSelect = React.useCallback(
    (id) => {
      const newSelected = new Map(selected);
      newSelected.set(id, !selected.get(id));

      setSelected(newSelected);
    },
    [selected]
  );

  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={DATA}
        renderItem={({ item }) => (
          <Item
            id={item.id}
            title={item.title}
            selected={!!selected.get(item.id)}
            onSelect={onSelect}
          />
        )}
        keyExtractor={(item) => item.id}
        extraData={selected}
      />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: Constants.statusBarHeight,
  },
  item: {
    backgroundColor: "#f9c2ff",
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});
```

## Props

继承自[ScrollView Props](/blog/scrollview#props)，当组件被嵌套在同方向的 FlastList 下这些属性将无效。

### renderItem

```
renderItem({item, index, separators});
```

从`data`数据中获取 item 数据，将它渲染到列表上
提供一起其他参数，比如可能需要的`index`参数，还有后面的`separators`。
**separators**

- highlight (Function)
- unhighlight (Function)
- updateProps (Function)
  - select (enum('leading', 'trailing'))
  - newProps (Object)

### data

为了简单，data 是个纯数组。如果你想要使用其他数据结构，比如不可变数组，请直接使用[virtualizedlist](/blog/virtualizedlist)

### ItemSeparatorComponent

在每两项之间渲染一个，默认会给组件提供`highlighted`,`leadingItem`属性。`renderItem`提供属性`separators.highlight`/`unhighlight`来更新它的`highlighted`属性。你也可以通过`separators.updateProps`添加自定义属性给它

### ListEmptyComponent

当列表内容是空的时候渲染它，可以是 component, function, element

### ListFooterComponent

在所有列表元素底部，可以是 component, function, element

### ListFooterComponentStyle

为内部的`ListFooterComponent`提供样式

### ListHeaderComponent

在所有列表元素顶部，可以是 component, function, element

### ListHeaderComponentStyle

为内部的`ListHeaderComponent`提供样式

### columnWrapperStyle

为多列的列项提供包裹样式

### extraData

告诉列表需要重绘的标记样式（因为列表是`PureComponent`）。`renderItem`, Header, Footer 依赖除了`data`以外的任何内容，变更都需要告知`extraData`，要将它作为不可变对象看待。

### getItemLayout

这是一个可选的优化项，如果你提前知道单项的固定宽高可以跳过组件进行的动态布局计算。
利用它在上百项下有很难高的性能。
如果你指定了分割线，高度计算需要包括分隔线进去

### horizontal

会将 item 从竖着堆叠变成横着摆放

### initialNumToRender

指定首屏需要有多少元素被渲染，应该刚好填充满屏幕。注意这些元素在窗口滚动过程中不会卸载，为了在提高执行`scroll-to-top`操作时不需要重新渲染。

### initialScrollIndex

首次渲染不从顶部的第一项开始，取而代之的是`initialScrollIndex`开始。它会禁止掉`initialNumToRender`的优化的项目放在内容中，立刻从 initial index 开始的位置渲染。需要设置`getItemLayout`属性

### inverted

翻转滚动的方向（应该是下滑变成上拉吧），实质是将`scale`设置成-1

### keyExtractor

用来提取 item 下独一无二的 key，key 作为重绘时缓存的依据。默认是取 item.key，获取失败会回滚到 index，机制和 React 一样

### numColumns

多列的数量，表现像 flexWrap 的 z 布局。所有的 item 一样高，`masonry`瀑布流布局不支持

### onEndReached

```
(info: {distanceFromEnd: number}) => void
```

当滚动的位置在渲染内容的触摸区内就会调用

### onEndReachedThreshold

距离列表底部多远时触发`onEndReached`回调，这是一个比值。比如 0.5 距离底部为可见内容一半长度时触发

### onRefresh

如果提供，一个平台标准的组件就会被添加到`下拉刷新`功能上。保证`refreshing`属性被正确设置

### onViewableItemsChanged

```
(info: {
   viewableItems: array,
   changed: array,
 }) => void
```

可见行变化时被调用。可见范围和变化频次可以去设置`viewabilityConfig`

### progressViewOffset

当偏移的位置需要加上加载指示器，可以去设置。(Android)

### legacyImplementation

可能没有实现，仅在调试和性能对比时需要

> May not have full feature parity and is meant for debugging and performance comparison.

### refreshing

等待新数据刷新的状态为 true

### removeClippedSubviews

在大列表时可能会提高滚动性能

> 有 bug，在某些场景下会导致内容丢失。谨慎使用

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
