---
title: 无痛升级的 Node 全栈方案
top: false
cover: false
toc: true
mathjax: true
date: 2022-09-09 11:10:23
password:
summary:
tags: [node, next, egg, midway, nest, daruk]
categories:
---

# 需求
产品发展几个阶段，初期->中期，后期  
- 初期: 规模小，需求迭代快
  - 常见方案: 这个时期不会投入太多人力，所以这个时候方案选型，前后端不分离
    - web框架+渲染模板:  spring+模板, c#+模板, nest+模板
    - ssr框架+弱api: next+api, nuxt+api
- 中后期: 规模大: 人力多/难管理，前后端分离
  - 常见方案:
    - ssr 框架: next,nuxt
    - 后端: midway,nest,java,c#,python...

# 痛点
明显前期和中后期的方案选型是有差异的，分离迁移的时候需要重构乃至重写原来的逻辑

# 理想
前期如果能够在一个项目中将ssr框架和强web框架松耦合，然后中后期分离只要稍作处理就能分离继续独立开发。(选型自由，不影响升级)
- 理论支持
  - nodejs 是 io 多路复用，http 托管网络请求
  - express, koa 提供了比较基础的 web 框架的能力，具有中间件等特点
  - next,nuxt 则是在 express,koa基础之上封装了ssr+ssg+csr等能力
  - egg,midway,nest 则是在 express,koa 基础之上封装了web api等能力
  - 如果在request进来的时候通过中间件将响应分离，/api请求走web框架，其他走 ssr 框架。然后 ssr 框架的聚合结合也复用这个请求链路，获取 web框架的响应结果就行

# 实施案例
[egg-midway-next](https://github.com/keyboard3/egg-midway-next)
[next-nest](https://github.com/keyboard3/next-nest)
[next-daruk](https://github.com/keyboard3/next-daruk)
