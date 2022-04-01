---
title: PayPal 网站支付专业版托管解决方案集成指南 (2)
top: false
cover: false
toc: true
mathjax: true
date: 2022-04-01 14:21:36
password:
summary:
tags: [PayPal, Guide, 翻译]
categories:
---

[原文](https://www.paypalobjects.com/webstatic/en_AU/developer/docs/pdf/hostedsolution_au.pdf#page=22&zoom=100,84,126)

# Chapter 2 使用 HTML 集成到你的站点

本章提供了有关集成的说明，使您能够开始使用托管方案处理交易。

> 注意: PayPal 建议你实现最简单的集成，以便在实现更加定制化的集成之前熟悉托管方案

作为简单集成的一部分，您可以在支付页面上获得默认设置。为了定制页面的外观和感觉，使其与你的网站相匹配，你可以执行下列操作之一：

- 在 PayPal.com 的个人资料部分更改你的设置
- 在付款页面添加适当的 HTML 变量

> 重要: HTML 变量将覆盖你保存在个人资料页面上的设置

## 简单托管方案集成

要将托管方案集成到你的网站，在您的网站结账流程中确定一点，您希望放置一个按钮，买方点击启动付款。这个按钮应该被标记为”继续支付“，”支付“或者类似的按钮，当点击这个按钮时，应该执行一个发送到 PayPal 的表单。点击这个按钮可以将购买者的浏览器重定向到 PayPal 支付页面，在那里他们可以用信用卡或者他们的 PayPal 账号支付。

Form POST 包含一组描述交易的 HTML 变量。在 Form POST 中，你必须指定以下内容：

- subtotal: 交易金额
- business: 安全商户 ID(在个人资料页面找到)或电子邮件地址与您的 PayPal 账号
- paymentaction: 表明交易是为了最终销售的支付，还是最终销售的授权（稍后会被捕获）

默认货币是美元。此外，你可以在[付款页面设置的 HTML 变量]中指定适当的 HTML 变量，以指定在付款页面收集的资料。或者在[付款页面外观和感觉的 HTML 变量]，以指定该页面的外观和感受。如果支付成功，那么买家要么看到 PayPal 确认页面，要么被重定向到你在配置中指定的 URL。

返回的 URL 在支付页面返回到您的网站的过程中，会在查询字符串上附加一个交易 ID。这个交易 ID 可以用来检索交易的状态和验证交易的真实性。有关在执行订单之前验证交易真实性的详细信息，请参阅第 7 章”订单处理“

## 示例集成

下面是一个简单的托管解决方案集成的例子：

1. 示例托管解决方案表单 POST

```html
<form
  action="https://securepayments.paypal.com/webapps/HostedSoleSolutionApp/
webflow/sparta/hostedSoleSolutionProcess"
  method="post"
>
  <input type="hidden" name="cmd" value="_hosted-payment" />
  <input type="hidden" name="subtotal" value="[50]" />
  <input type="hidden" name="business" value="[HNZ3QZMCPBAAA]" />
  <input type="hidden" name="paymentaction" value="[sale]" />
  <input
    type="hidden"
    name="return"
    value="[https://yourwebsite.com/receipt_page.html]"
  />
  <input type="submit" name="METHOD" value="[Pay]" />
</form>
```

[xx]是相应的变量的值。建议你用引号括起这些值。有关这些值的详细信息，请参考"支付页面设置的 HTML 变量"。

2. 输出 HTML 文本到您的网站上，买家将继续与他们结账
3. 打开你的结账页面，测试按钮，以确保它打开 PayPal 页面
   您还可以使用 PayPal 沙箱环境来测试您的集成。关于在 PayPal Sandbox 环境中测试集成的完整信息，参阅第六章"在 Sandbox 中测试你的集成"

## 支付页面设置的 HTML 变量

下面的表格列出了托管解决方案的 HTML 变量，你可以使用它们来发送额外的交易信息以及你的 web 请求。有关可用于自定义支付页面外观和感觉的 HTML 变量列表，请参阅”支付页面外观和感觉的 HTML 变量“

> 注意: 传递的值不能包含任何这些特殊字符(){}<>";

> 注意: 一些商家需要在每笔交易中传递账单信息。建议您首先测试您的集成，特别是如果您计划使用 iFrame,以确定是否需要计费信息字段

表 2.1 付款页面设置的 HTML 变量


| 变量               | 描述                                                                          | 必要条件            |
| ----- | ----- | ---------- |
| address1           | 送货地址的接到名称(2 个字段中的 1)                                            | No                  |
| address2           | 送货地址的接到名称(2 个字段中的 1)                                            | No                  |
| address_override   | 付款人显示传递地址，但无法编辑。如果地址中出现错误时这个变量会覆盖。允许 true/false，默认是 false | No  |
| billing_address1   | 账单地址的街道名称。(2 个字段中的 1)                                          | No                  |
| billing_address1   | 账单地址的街道名称。(2 个字段中的 2)                                          | No                  |
| billing_city       | 账单地址的城市名称                                                            | 可选                |
| billing_country    | 账单地址的国家代码                                                            | 可选                |
| billing_first_name | 收款人第一个名                                                                | 可选                |
| billing_last_name  | 收款人的姓                                                                    | 可选                |
| billing_state      | 账单地址的状态名称                                                            | 可选                |
| billing_zip        | 账单地址的邮政编码                                                            | 可选                |
| bn                 | 识别构建按钮代码的源代码。格式-<Company>_<Service>_<Product>\_<Country>       | No                  |
| business| 您 PayPal 账户电子邮件地址或您的 PayPal ID(安全商户ID)与您的 PayPal 关联，建议使用 PayPal 账号，可以在个人资料页面的顶部找到在 PayPal.com上 | Yes|
|buyer_email | 买方的电子邮件地址 | No |
| cancel_return |浏览器将被重定向到这个 URL， 如果卖家点击返回商户链接，确保填写完整的 URL，包括协议头 |No|
| cbt| 在 PayPal 确认页面设置"Return to Merchant"链接的文本。对于企业账户，文本显示您企业的名称，默认显示"Merchant"| No |
| city|运输地址城市名称| No |
| country | 运输地址国家名称| No |
| currency_code | 支付的货币。默认是美元| No|
| custom | 传递从未呈现给付款人的变量。| No|
| first_name | 货物被运送到的人的名字 | No|
| handling | 已收手续费。数额加在总金额上 | No|
| invoice | 商家订货/发票系统中的订单编号 | No|
| last_name | 货物被运送到的人的姓 | No|
| lc | 登录或注册页面的显示语言，可能值为 AU | No|
|night_phone_a| 美国电话号码或国家的区号。美国境外电话号码的代码。填充买家的家庭电话号码。| No|
|night_phone_b|美国电话号码的三位数字前缀，或全部非美国电话号码以外的号码,不包括国家代码。这将预先填充买家家庭电话号码。注意: 对于非美国数字使用此变量。| No|
|night_phone_c| 美国电话号码的四位数电话号码。这个预填充买家的家庭电话号码。|No|
|notify_url| PayPal 发布有关即时支付通知书形式的交易，确保输入完整的 URL，包括协议头| No|
|paymentaction| 指示交易是否用于在最终销售或最终销售的授权(延迟捕获). - 允许值: 授权或销售 - 默认值: 销售 | Yes|
|return| 购买者在付款完成后，浏览器被重定向到的 URL，输入完整的 URL | No |
|shipping| 已付运费。这个数额加在总金额。| No|
|state| 装运地址状态。|No|
|subtotal|交易所收取的金额。如果运输，装卸,和税款没有指定，这是总额收费。| Yes |
|tax| 已征收的税款。这个数额加在总金额| No|
|zip| 送货地址的邮政编码。| No |
