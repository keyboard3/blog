---
title: PayPal 网站支付专业版托管解决方案集成指南 (7)
top: false
cover: false
toc: true
mathjax: true
date: 2022-04-01 21:25:36
password:
summary:
tags: [PayPal, Guide, 翻译]
categories:
---
[原文](https://www.paypalobjects.com/webstatic/en_AU/developer/docs/pdf/hostedsolution_au.pdf#page=61&zoom=100,84,126)
# 订单处理
本章将带领您体验终端订单处理的经验。它包括在执行订单之前验证订单状态和真
实性的信息。

## 验证交易状态和真实性
当买方成功完成交易时，他们将被重定向到 PayPal 确认页面或者您在返回变量中指定的网站，或者在 Profile 部分的 Settings 页面(如第 15 页“ Simple Hosted Solution Integration”中概述的)。当浏览器被重定向到你指定的网站时，一个交易 ID 被附加到它上面。

注意: 为了确保交易 ID 被追加到返回的 URL，登录到您的 PayPal 商户帐户并选择 Profile。在 Profile 页面的 Website Payments Standard and Express Checkout 部分，选择 Preferences 并验证 Auto Return 是否设置为 On。
在同一个设置页面上，也确认 Payment Data Transfer 设置为 On。

当您收到重定向(带有交易 ID 的 URL)时，您必须在将订单发送给买家之前验证订单是否在 PayPal 上成功完成。你可以通过检查 PayPal 发送给你的确认邮件或者验证交易历史来做到这一点。你也可以使用以下方法之一:

### 验证即时付款通知 (IPN)
IPN 允许你通过异步、服务器到服务器的通信从 PayPal 接收有关交易支付和活动的消息。这允许你整合你的在线支付和你的订单执行过程。

通过 IPN，您会收到以下消息：
- 付款及其状态(待定、完成或拒绝)
- 欺诈管理过滤行为
- 定期付款活动
- 授权，退款，纠纷，撤销和退款。

处理完交易后，PayPal 将使用参数 notify_url 或在您的 PayPal 个人资料中向您在交易中指定的通知 URL 发送 IPN。您必须验证在 IPN 中发送的交易 ID、交易金额和其他订单特定参数（例如发票 ID）与您在订单处理系统中的信息匹配。有关更多详细信息，请参阅即时付款通知 (IPN) 集成指南。

### 执行 GetTransactionDetails API 调用
使用 GetTransactionDetails API，您可以获得有关特定交易的信息。

如果您集成了 PayPal api，您可以使用 web 重定向中返回的交易 ID 调用
GetTransactionDetails 来验证订单的真实性。

有关详细信息，请参阅第 ”GetTransactionDetails API“

## 履行订单
在您验证了付款金额和状态的真实性之后，您可以通过将货物运送给买方来履行订单。