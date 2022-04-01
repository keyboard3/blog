---
title: PayPal 网站支付专业版托管解决方案集成指南 (6)
top: false
cover: false
toc: true
mathjax: true
date: 2022-04-01 21:20:36
password:
summary:
tags: [PayPal, Guide, 翻译]
categories:
---
# 在沙盒中测试您的集成
PayPal 沙盒是一个独立的环境，你可以在其中原型和测试 PayPal 功能。PayPal Sandbox 是 PayPal 网站的一个几乎完全相同的拷贝。It 的目的是为开发人员提供一个用于测试和集成的屏蔽环境，并帮助避免在现场测试 PayPal 集成解决方案时可能出现的问题。在将任何基于 PayPal 的应用程序投入生产之前，您应该在 Sandbox 中测试该应用程序，以确保它按照您的意愿运行，并且符合 PayPal 开发者协议规定的准则和标准。

有关使用 PayPal Sandbox 的完整细节，请参阅 Sandbox 用户指南。

## 沙盒帐户凭证
### 为您想要测试的国家创建一个 PayPal 沙盒企业帐户
- 登录 PayPal 开发者站点: https://developer.PayPal.com/。你可以使用现有的 PayPal 账户凭证登录或者注册一个新账户。
- 导航到 Applications > Sandbox 帐户并单击 create account 按钮。
- 使用 Country 下拉列表选择您想要测试集成的国家。
- 将银行验证帐户设置为 Yes。
- 完成表单的其余部分，然后点击 Create Account。

> 注意: 您可以使用任何名称的帐户，没有必要勾选登录与 PayPal 框。

### 验证你的 PayPal Sandbox 企业账户
- 使用最近创建的 PayPal Sandbox 业务帐户的电子邮件地址和密码登录 Sandbox 测试站
点(https://www.Sandbox.PayPal.com)。
- 点击“我的帐户概述”页上的“Unverified”链接。
- 单击“获得验证并提升发送限制”页上的“添加银行帐户”。
- 用虚构的信息填充所有字段。
> 注意: 排序代码和帐号必须是唯一的号码。
- 单击 Continue，然后添加 Bank Account 以添加测试银行帐户。
- 导航到“设置银行资金”页，然后单击“继续”。
- 点击 Submit 完成验证过程。

### 升级为 Pro 帐户
单击 Sandbox 企业帐户电子邮件地址旁边的箭头，然后单击出现在电子邮件地址下面 的 Profile 链接(下面突出显示)。

这将打开 Sandbox 帐户详细信息窗口。从这个窗口的 Profile 选项卡，选择 Upgrade to Pro。然后点击启用按钮。

### 设置个人沙盒帐户
从开发人员站点 Applications > Sandbox 帐户页面，您可以创建多个企业(商家)
和个人(买家)帐户，您可以在 Sandbox 测试交易中使用这些帐户。更多信息请参 见 Sandbox 用户指南。

你已经准备好在 Sandbox 测试站点上测试你的托管解决方案集成。

## 测试集成和设置
下面的部分包含测试集成的信息，以及在 Sandbox 环境中修改支付页面的外观和感觉的信息。
- 测试您的集成
- 测试你的设置

### 测试您的集成
要在 Sandbox 环境中测试集成，请按照第 15 页“简单托管解决方案集成”中指定的步
骤操作。为了测试目的，你必须在表单 POST 中做以下更改:

- 1. 将 URL 更改为指向 Sandbox 环境:
From:
```html
<form
action="https://securepayments.paypal.com/webapps/HostedSoleSolutionApp/
webflow/sparta/hostedSoleSolutionProcess" method="post">
<input type="hidden" name="cmd" value="_hosted-payment">
```
to:
```html
<form
action="https://securepayments.sandbox.paypal.com/webapps/HostedSoleSolu
tionApp/webflow/sparta/hostedSoleSolutionProcess" method="post">
<input type="hidden" name="cmd" value="_hosted-payment">
```

- 2. 将业务值更改为在 Sandbox 测试站点的 Profile 页面顶部指定的 Secure Merchant ID 值。
因此，用作测试用途的表格 POST 将是:
```html
<form
action="https://securepayments.sandbox.paypal.com/webapps/HostedSoleSolu
tionApp/webflow/sparta/hostedSoleSolutionProcess" method="post">
<input type="hidden" name="cmd" value="_hosted-payment">
<input type="hidden" name="subtotal" value="50">
<input type="hidden" name="business" value="HNZ3QZMCPBAAA">
<input type="hidden" name="paymentaction" value="sale">
<input type="hidden" name="return"
value="https://yourwebsite.com/receipt_page.html">
<input type="submit" name="METHOD" value="Pay Now">
</form>
```

### 测试您的设置
要更改付款页面的外观，请修改自定义设置和沙盒测试站点的配置文件部分中的设置页面。有关完整的详细信息，请参阅“修改您的 PayPal 帐户设置”。
