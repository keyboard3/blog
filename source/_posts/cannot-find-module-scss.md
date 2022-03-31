---
title: Cannot Find Module 'XXX.scss' or Its Corresponding Type Declarations
top: false
cover: false
toc: true
mathjax: false
date: 2022-03-31 22:23
password:
summary:
tags: [scss, ts, 翻译]
categories:
---

[原文](https://linuxize.com/post/linux-nohup-command/)

## 问题
在 Typescript + Webpack + Sass 项目中使用 CSS Module 时，可以正常使用 CSS 模块，但是 vscode 总是提示找不到 module'XXX.scss' 或其对应的类型声明。

## 解决方案
### 方案1
- 首先确保 webpack 和 sass 已经能够识别 CSS Module，请参考 webpack 官网配置。
[Separating Interoperable CSS-only and CSS Module features](https://webpack.js.org/loaders/css-loader/#separating-interoperable-css-only-and-css-module-features)
- 配置 d.ts
重要的部分来了，要注意两个d.ts文件的配置
  - 主文件 index.d.ts

```ts
declare module'*.scss' {
    const content: {[key: string]: any}
    export = content
}
```
  - 将另一个typings.d.ts文件添加到同级目录

```ts
declare module'*.scss';
```
这样配置后，我的问题就解决了，VSCODE和Node命令行界面都不会报错，可以正常匹配。

### 方案2
网上推荐的解决方案是把d.ts文件写在css文件的同级目录下，一般使用插件自动完成

选择以下插件之一
- webpack 插件 [typings-for-css-modules-loader](typings-for-css-modules-loader)
- webpacl 插件 [css-modules-typescript-loader](https://github.com/seek-oss/css-modules-typescript-loader#readme)
- 可能的插件，Typescript插件[typescript-plugin-css-modules](https://github.com/mrmckeb/typescript-plugin-css-modules#visual-studio-code)（我用过没效果）

通常安装插件自动生成每个 css 声明文件后不会报错，但是有两个缺点，一是项目文件太多，二是路径别名还是不能被 VSCODE 识别（Node 命令行正常），这两个 d.ts 必须如上配置，防止VSCODE提示错误。

# 参考
- 一些可能的 scss 文件声明

```ts
//example one
declare module'*.scss';

//example two
declare module'*.scss' {
    const content: any;
    export default content;
}

//example three
declare module'*.scss' {
    const content: Record<string, string>;
    export default content;
}

//example four
declare module'*.scss' {
    const content: {[key: string]: any}
    export = content
}
```
- 其他答案 [cant-import-css-scss-modules-typescript-says-cannot-find-module](https://stackoverflow.com/questions/40382842/cant-import-css-scss-modules-typescript-says) 
