---
layout: post
title: "react native render html 简单解析"
date: 2020-03-09
Author: keyboard3
categories:
tags: [react-native, html, 翻译]
comments: true
---

# [react-native-render-html](https://github.com/archriss/react-native-render-html)

## 场景

需要将 html 的 UI 渲染标准迁移到 react-native 的跨平台组件上

## 简单推测处理过程

html parse->compute style-> cssom+dom map 到 RNom（主要是组件映射，属性映射，`这个阶段可以干预`）->渲染成 RNElements
流程是阶段性流程

## 源码流程梳理

- registerDOM：记录将要渲染的 html 内容
- parseDOM：解析 html 出 dom
- htmlparser2.Parser DomHandler:dom 解析完毕，拿到 DOMNodes（类型为 DomElement 的 dom 节点数组）
- mapDOMNodesTORNElements 将 DOMNodes 转换成 RNElements
  - 可以选择 ignoreNodesFunction，ignoredTags 忽略某些 tag 标签以及指定 Node
  - 可以操作替换节点，可以操作替换节点数据，可以操作替换孩子节点
  - 如果是叶子节点（只是纯文本内容），映射成一个 Text 组件，样式就是 dom node 节点的样式属性
  - 如果是标签节点（ps:标签就认为是一个容器）
    - 递归计算所有 children node 出 RNElements
    - 当前标签不是只包含文本就需要被映射为 View 容器
    - tag 是那种纯文本的标签,被映射成 Text 容器
    - 如果 tag 是自定义的，可以调用自定义的 renders 标签函数处理
    - 最后条件都不满足默认就认为是 View 容器
    - 将以上拿到组装 RNElements 的参数定义，尝试进行递归层次合并。（发现子节点和自己是同一个容器类型，且属性相同）
    - 将以上参数转换成实际的 RNElements 组件
    - 最后尝试合并那些不需要独立成行的 tag 文本的组件
- RNNodes 被包裹一个容器渲染到页面

## 官方文档翻译

一个纯 js 实现用来将 html 内容 100%绘制到原生上的 rn 组件，支持 Android/iOS。它能够 easy 自定义和使用。

### 安装

从 4.2.0 版本开始，react-native-webview 是一个独立的库。你需要自行安装它

### Props

| 属性                    | 描述                                                                  | 类型           | 值要求                                |
| ----------------------- | --------------------------------------------------------------------- | -------------- | ------------------------------------- |
| renderers               | 你自定义标签渲染函数                                                  | custom renders | object                                | 可选，提供一些默认值(a,img) |
| renderersProps          | custom renders 函数里的第四个参数 props                               | object         | 可选                                  |
| html                    | 需要渲染的 html 文本                                                  | string         | 必须                                  |
| uri                     | 网址内容的解析和渲染（实验中）                                        | string         | 可选                                  |
| decodeEntities          | 这个是 html parse2 的参数，(大意是文档中的实体也会被解析?)            | bool           | 默认是 true                           |
| imagesMaxWidth          | 调整图片到最大宽度                                                    | number         | 可选                                  |
| staticContentMaxWidth   | 设置非响应式内容的最大宽度(iframe 实例)（ps：得验证）                 | number         | 可选                                  |
| imagesInitialDimensions | 图片的默认显示的宽高{width:100,height:100}                            | number         | 可选                                  |
| onLinkPress             | 随着点击事件触发，url 和标签属性对象将作为回调函数的参数              | function       | 可选                                  |
| onParsed                | 当 html 内容被解析完成时触发，对调整渲染后续过程有帮助                | function       | 可选                                  |
| tagsStyles              | 可以指定 html 标签的显示的 rn style                                   | object         | 可选                                  |
| classesStyles           | 可以指定 html 中类的 rn style                                         | object         | 可选                                  |
| listsPrefixesRenderers  | 包含自定义 ul,ol 的前缀渲染函数对象                                   | object         | 可选                                  |
| containerStyle          | html 容器的样式                                                       | object         | 可选                                  |
| customWrapper           | 替换掉默认的 warpper 的函数，第一个参数是渲染的内容。                 | function       | 可选                                  |
| remoteLoadingView       | 替换默认加载网络内容的加载框                                          | function       | 可选                                  |
| emSize                  | 1em 对应的像素值                                                      | number         | 14                                    |
| ptSize                  | 1pt 对应的像素值                                                      | number         | 1.3                                   |
| baseFontStyleText       | 组件的默认样式                                                        | object         | {fontSize:14}                         |
| allowFontScaling        | 允许字体大小被缩放的开关                                              | boolean        | true                                  |
| textSelectable          | 允许所有文本被选中                                                    | boolean        | false                                 |
| alterData               | 指定文本改变目标节点的内容                                            | function       | 可选                                  |
| alterChildren           | 修改目标的节点的 children                                             | function       | 可选                                  |
| alterNode               | 修改目标节点                                                          | function       | 可选                                  |
| ignoreTags              | 指定不想渲染的 html 标签                                              | array          | 可选                                  |
| allowStyles             | 它只渲染给定的 style，如果这个 style 也在 ignoreStyles 下，则还是忽略 | array          | 可选 (应该是 background 这类样式属性) |
| ignoredStyles           | 不想渲染的 style                                                      | array          | 可选                                  |
| ignoreNodesFunction     | 忽略给定节点                                                          | function       | 可选                                  |
| debug                   | 打印 htmlparser2 的解析 result,渲染的结果                             | boolean        | false                                 |
