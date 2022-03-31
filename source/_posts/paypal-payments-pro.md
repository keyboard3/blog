---
title: PayPal Payments Pro [2.4]
top: false
cover: false
toc: true
mathjax: false
date: 2022-03-31 14:30
password:
summary:
tags: [PayPal, 翻译]
categories:
---

[原文](https://docs.magento.com/user-guide/payment/paypal-payments-pro.html)

[PayPal Payments Pro](https://developer.paypal.com/docs/paypal-payments-pro/) 为您带来商户账户和支付网关的所有好处，以及创建您自己的、完全定制的结账体验的能力。 PayPal 快速结账功能通过 PayPal Payments Pro 自动启用，因此您可以接触超过 1.1 亿活跃的 PayPal 用户。
![店面的 PayPal Payments Pro](https://docs.magento.com/user-guide/payment/assets/storefront-mini-cart-payments-pro-racer-tank.png)

> 要求:
> 自 2019 年 9 月 14 日起，欧洲银行可能会拒绝不符合 [PSD2](https://docs.magento.com/user-guide/stores/compliance-payment-services-directive.html) 要求的付款。为遵守 PSD2，PayPal Payments Pro 必须与 Cardinal Commerce 集成。要了解更多信息，请参阅 [Payflow 的 3-D 安全](https://developer.paypal.com/docs/classic/payflow/3d-secure-overview/)。

> 目前，PayPal Payments Pro 可在美国、英国和加拿大使用。

## 要求

- [PayPal 商家帐户](https://www.paypal.com/webapps/mpp/how-to-sell-online)（已激活直接付款）

## 结帐工作流程

- 顾客去结账
客户将产品添加到购物车，然后点击/点击继续结帐。
- 客户选择付款方式
在结账时，客户选择 PayPal 直接付款选项，并输入信用卡信息。 
  - 如果使用 PayPal Payments Pro 付款，客户在结账过程中会留在您的网站上。 
  - 如果使用 PayPal Express Checkout 付款，客户将被重定向到 PayPal 网站以完成交易。

应客户要求，商店管理员还可以从管理员创建订单并使用 PayPal Payments Pro 处理交易。

## 订单处理工作流程
- 订单已下
订单可以由您商店的管理员或您的 PayPal 商家帐户处理。
- 付款动作
配置中指定的付款操作将应用于订单。选项包括：
  - 授权 - Commerce 创建状态为处理中的销售订单。在这种情况下，要授权的金额有待批准。
  - 销售 - Commerce 创建销售订单和发票。
  - 捕获 - PayPal 将订单金额从客户余额、银行账户或信用卡转移到商家账户。
- 发票
PayPal 向 Commerce 发送即时付款通知消息后，会在 Commerce 中创建发票。
注意：确保您的 PayPal 商家帐户中启用了即时付款通知。
如果需要，可以为指定数量的产品开具部分订单发票。对于提交的每份部分发票，具有唯一 ID 的单独 Capture 事务可用，并生成单独的发票。
仅在获得全部订单金额后才会关闭仅授权支付交易。
在订单金额全额开具发票之前，可以随时在线取消订单。
- 退货
如果客户出于任何原因退回购买的产品并要求退款，例如订单金额捕获和发票创建，您可以从管理员或您的 PayPal 商家帐户创建在线退款。

## 配置您的 PayPal 账户
在 Commerce 中设置 PayPal Payments Pro 之前，您必须在 PayPal 网站上配置您的商家帐户。

- 登录到您的 [PayPal 企业帐户](https://manager.paypal.com/)。
- 在 PayPal 管理器菜单中，选择服务设置
- 在托管结帐页面下，单击设置。
- 在选择您的设置下，将事务处理模式设置为 Live。
- 在支付页面上的显示选项下，设置 Cancel URL 方法设置为 POST。
- 在账单信息下，为必填字段和可编辑字段选择卡安全码 CSC 复选框。
- 在 Payment Confirmation 下，将 Return URL Method 设置为 POST。
- 在安全选项下，配置以下内容：
  - AVS: No
  - CSC: No
  - Enable Secure Token: Yes
- 点击保存变更
- 在 PayPal Manager 菜单中，选择 Service Settings，然后在 Hosted Checkout Pages 下选择 Customize。
- 选中布局 C
布局 C 仅显示信用卡和借记卡字段，可以在您的网站上框起来或用作独立的弹出窗口。大小固定为 490 x 565 像素，并有额外的空间用于错误消息。在某些系统上，此设置更正了透明重定向的问题。
- 单击保存并发布。
- 在 PayPal 管理器菜单中，选择帐户管理。在管理安全性下，单击事务设置。
- 将允许参考事务设置为是。
- 单击确认。
如果您有多个 Commerce 网站，则必须为每个网站创建一个单独的 PayPal Payments Pro 帐户。
- 设置其他用户（由 PayPal 推荐）：
  - 在主菜单的第二行中，单击管理用户。
  - 要将其他用户添加到帐户，请单击添加用户。该链接位于“管理用户”标题的正上方。
  - 填写“添加用户”表单以下部分中的必填字段：
    - 管理员确认 
    - 用户信息 
    - 用户登录信息 
    - 为用户分配权限
  - 点击更新
- 确保退出您的 PayPal 帐户。

## 在 Commerce 中设置 PayPal Payments Pro
> 您可以同时激活两种 PayPal 解决方案：[PayPal Express Checkout]，以及任何一种[一体化解决方案]。如果您更改支付解决方案，之前使用的支付解决方案将自动禁用。

> 随时单击保存配置以保存您的进度。

### 第 1 步：开始配置
- 在管理侧边栏上，转到商店 > 设置 > 配置。
- 在左侧面板中，展开销售并选择付款方式。
- 如果您的 Commerce 安装有多个网站、商店或视图，请将商店视图设置为要应用此配置的商店视图。
- 在商家位置部分，选择您的商家所在的商家所在国家/地区。 此设置决定了配置中出现的 PayPal 解决方案的选择。
![Merchant Country](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-merchant-location.png)
- 展开 PayPal All-in-One Payment Solution，然后单击为 Payments Pro 配置。
![Payments Pro - Configure](https://docs.magento.com/user-guide/payment/assets/paypal-payments-pro.png)

### 第 2 步：完成所需的 PayPal 设置
- 展开 Payments Pro 和 Express Checkout 部分。
![Required PayPal Settings - PayPal Payments Pro](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-paypal-payments-pro-required.png)
- （可选）输入与您的 PayPal 商家帐户关联的电子邮件。
> 电子邮件地址区分大小写。要接收付款，电子邮件地址必须与您的 PayPal 商家帐户中指定的电子邮件地址相匹配。

如果您没有 PayPal 帐户，请单击开始通过 PayPal 接受付款。

- 输入您用于登录 PayPal 商家帐户的以下凭据之一：
  - Partner:	您的 PayPal 合作伙伴 ID。
  - Vendor:	您的 PayPal 用户登录名。
  - User:	在您的 PayPal 帐户上设置的其他用户的 ID。
- 输入与您的 PayPal 帐户关联的密码。
- 如果要运行测试事务，请将测试模式设置为是。
在沙盒中测试配置时，请仅使用 PayPal 推荐的[信用卡号](https://www.paypalobjects.com/en_AU/vhelp/paypalmanager_help/credit_card_numbers.htm)。当您准备好上线时，返回配置并将测试模式设置为否。
- 如果您的系统使用代理服务器建立与 PayPal 系统的连接，请将 Use Proxy 设置为 Yes 并执行以下操作：
  - 输入代理主机的 IP 地址。
  - 输入代理端口的端口号。

当服务器防火墙阻止直接访问 PayPal 服务器时，使用代理。在这种情况下，第三方服务器用于中继流量。
  - 将启用此解决方案设置为是。
  - 如果您想向您的客户提供 PayPal Credit，请将启用 PayPal Credit 设置为 Yes。
  - 如果您想安全地存储客户付款/信用卡详细信息，以便客户不必每次都重新输入付款信息，请将 Vault Enabled 设置为 Yes。

### 第 3 步：设置 Advertise PayPal Credit / Advertise PayPal PayLater（可选）
从 2.4.3 版本开始，在包含 PayPal 的部署中支持 PayPal Pay Later。此功能允许购物者每两周分期付款一次，而不是在购买时支付全部金额。 PayPal Credit 体验已弃用。

将启用 PayPal PayLater 体验设置为以下之一：
- 是 - 设置 Advertise PayPal PayLater 
- 否 - 设置 Advertise PayPal Credit

**Advertise PayPal Credit** 
- 展开 Advertise PayPal Credit
![](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-paypal-payments-advanced-advertise-paypal-credit.png)
- 点击从 PayPal 获取发布者 ID，然后按照说明获取您的帐户信息。
- 输入您的发布商 ID。
- 展开主页部分。
![Advertise PayPal Credit - Home Page](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-paypal-payments-advanced-advertise-paypal-credit-home-page.png)
- 要在页面上放置横幅，请将显示设置为 Yes。
- 将位置设置为以下之一：
  - Header (center)
  - Sidebar (right)
- 将大小设置为以下之一：
  - 190 x 100
  - 234 x 60
  - 300 x 50
  - 468 x 60
  - 728 x 90
  - 800 x 66
- 展开其余部分并重复前面的步骤：
  - 目录类别页面 
  - 目录产品页面 
  - 结帐购物车页面

**Advertise PayPal Pay Later**
- 展开 Advertise PayPal PayLater 部分。
- 将启用 PayPal PayLater 设置为是。 
- 展开主页部分。
![Advertise PayPal PayLater - Home Page Settings](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-paypal-payments-advanced-advertise-paypal-paylater-home-page.png)
- 要在页面上放置横幅，请将显示设置为 Yes。
- 将位置设置为以下之一：
  - Header (center)
  - Sidebar (right)
- 设置 Style Layout 为下面其中一个
  - Text
  - Flex
- 仅对于样式布局 Text，将 logo 类型设置为以下之一：
  - Primary
  - Alternative
  - Inline
  - None
- 仅对于样式布局 Text，将 logo 位置设置为以下之一：
  - Left
  - Right
  - Top
- 仅对于样式布局 Text，将 Text 颜色设置为以下之一：
  - Black
  - White
  - Monochrome
  - Grayscale
- 仅对于样式布局 Text，将 Text 大小设置为以下之一：
  - 10px
  - 11px
  - 12px
  - 13px
  - 14px
  - 15px
  - 16px
- 仅对于 Style Layout Flex，将 Ratio 设置为以下之一：
  - 1x1
  - 1x4
  - 8x1
  - 20x1
- 仅对于样式布局 Flex，将颜色设置为以下之一：
  - Blue
  - Black
  - White
  - White No Border
  - Gray
  - Monochrome
  - Grayscale
- 展开其余部分并重复前面的步骤：
  - 目录产品页面 
  - 结帐购物车页面 
  - 结帐付款步骤 
  - 目录类别页面

### 第四步：完成基本设置
- 展开基本设置 - PayPal Payments Pro 部分。
![Basic Settings - PayPal Payments Pro](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-paypal-payments-pro-basic-settings.png)
- 输入标题以在结账时识别 PayPal Payments Pro。
建议您使用标题 Debit or Credit Card.。
- 如果您提供多种付款方式，请为排序顺序输入一个数字，以确定 PayPal Payments Pro 在结账期间与其他付款方式一起列出时的显示顺序。
这与其他付款方式有关。 （0 = 第一个，1 = 第二个，2 = 第三个，依此类推。）
- 将付款操作设置为以下之一：
  - Authorization: 批准购买，但暂停资金。该金额在被商家捕获之前不会被提取。
  - Sale: 购买金额被授权并立即从客户账户中提取。
- 对于信用卡设置，选择您在商店中接受用于付款的信用卡。
要选择多张卡片，请按住 Ctrl 键 (PC) 或 Command 键 (Mac) 并单击每一张。
> 美国运通需要额外的协议。

### 第五步：完成高级设置
- 展开高级设置部分。
![Advanced Settings - PayPal Payments Pro](https://docs.magento.com/user-guide/payment/assets/paypal-payments-pro-advanced-settings.png)
- 将适用付款设置为以下选项之一：
  - All Allowed Countries: 来自您商店配置中指定的所有国家/地区的客户都可以使用此付款方式。
  - Specific Countries: 选择此选项后，将出现“来自特定国家/地区的付款”列表。按住 Ctrl 键 (PC) 或 Command 键 (Mac) 并在列表中选择客户可以从您的商店购买的每个国家/地区。
- 要将与支付系统的通信写入日志文件，请将调试模式设置为 Yes
> 根据 PCI 数据安全标准，信用卡信息不会记录在日志文件中。

- 要启用主机真实性验证，请将启用 SSL 验证设置为 Yes
- 要要求客户输入 CVV 代码，请将要求 CVV 输入设置为 Yes。
- 展开 CVV 和 AVS 设置部分。
- 要确定当地址验证系统识别出不匹配时应拒绝交易的时间，请指定如何处理以下每种情况：
  - 要根据不匹配的街道不匹配拒绝交易，请将 AVS Street 不匹配设置为 Yes。
  - 要根据不匹配的邮政编码拒绝交易，请将 AVS 邮政编码不匹配设置为是 Yes。
  - 要根据不匹配的国家/地区标识符拒绝交易，请将国际 AVS 指标不匹配设置为 Yes
  - 要根据不匹配的 CVV 代码拒绝交易，请将卡安全代码不匹配设置为 Yes
![CVV and AVS Settings - PayPal Payments Pro](https://docs.magento.com/user-guide/payment/assets/paypal-payments-pro-cvv-avs-settings.png)
- 根据您的商店的需要完成以下部分：
  - [Settlement Report Settings](https://docs.magento.com/user-guide/payment/paypal-payments-pro.html#settlement-report-settings)
  - [Frontend Experience Settings](https://docs.magento.com/user-guide/payment/paypal-payments-pro.html#frontend-experience-settings)

#### 结算报告设置
- 展开结算报告设置部分。
![Settlement Report Settings - PayPal Payments Pro](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-paypal-payments-advanced-settlement-report-settings.png)
- 对于 SFTP 凭据，请执行以下操作：
  - 如果您已注册 PayPal 的安全 FTP 服务器，请输入以下 SFTP 登录凭据：
    - Login
    - Password
  - 要在您的网站上使用 Payments Pro 之前运行测试报告，请将沙盒模式设置为 Yes。
  - 输入自定义端点主机名或 IP 地址。
默认情况下，该值为 reports.paypal.com。
  - 输入保存报告的自定义路径。
默认情况下，该值为 /ppreports/outgoing。
- 要根据计划生成报告，请完成 Scheduled Fetching 设置：
  - 将启用自动提取设置为 Yes
  - 将计划设置为以下之一：
    - Daily
    - Every 3 Days
    - Every 7 Days
    - Every 10 Days
    - Every 14 Days
    - Every 30 Days
    - Every 40 Days
PayPal 将每份报告保留 45 天。
  - 当您希望生成报告时，将时间设置为小时、分钟和秒。

#### 前端体验设置
前端体验设置让您有机会选择在您的网站上显示哪些 PayPal 徽标，并自定义您的 PayPal 商家页面的外观。
- 展开前端体验设置部分。
![Frontend Experience Settings - PayPal Payments Pro](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-paypal-payments-advanced-frontend-experience-settings1.png)
- 选择您希望在商店的 PayPal 区块中显示的 PayPal 产品徽标。
PayPal 徽标有四种样式和两种尺寸：
  - 没有标志 
  - 我们更喜欢 PayPal（150 x 60 或 150 x 40） 
  - 现在接受 PayPal（150 x 60 或 150 x 40） 
  - 通过 PayPal 付款（150 x 60 或 150 x 40） 
  - 使用 PayPal 立即购物（150 x 60 或 150 x 40）
- 要自定义 PayPal 商家页面的外观，请执行以下操作：
  - 输入您要应用于您的 PayPal 商家页面的页面样式的名称：
    - paypal:使用 PayPal 页面样式。
    - primary: 使用您在帐户配置文件中确定为主要样式的页面样式。
    - your_custom_value: 使用在您的帐户资料中指定的自定义付款页面样式。
  - 对于 Header Image URL，输入要显示在支付页面左上角的图像的 URL。最大文件大小为 750 像素宽 x 90 像素高。
> PayPal 建议图像位于安全 (https) 服务器上。否则，浏览器可能会警告该页面包含安全和非安全项目。

- 要为页面设置颜色，请为以下各项输入不带 # 符号的六字符十六进制代码：
  - Header Background Color: 结帐页面标题的背景颜色。
  - Header Border Color: 标题周围两像素边框的颜色。
  - Page Background Color: 结帐页面以及标题和付款表单周围的背景颜色。

### 第 6 步：完成 PayPal Express Checkout 的基本设置
- 展开基本设置 - PayPal Express Checkout 部分。
![](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-paypal-payments-pro-express-checkout-basic-settings.png)
- 输入标题以在结帐时识别此付款方式。
建议将每个商店视图的标题设置为 PayPal。
- 如果您提供多种付款方式，请为排序顺序输入一个数字，以确定 PayPal Express Checkout 与其他付款方式一起列出时出现的顺序。
这与其他付款方式有关。 （0 = 第一个，1 = 第二个，2 = 第三个，依此类推。）
- 将付款操作设置为以下之一：
  - Authorization: 批准购买并冻结资金。该金额在被商家捕获之前不会被提取。
  - Sale: 购买金额被授权并立即从客户的账户中提取。
- 要在产品页面上显示使用 PayPal 结帐按钮，请将在产品详细信息页面上显示设置为 Yes

### 第 7 步：完成 PayPal Express Checkout 的高级设置
- 展开高级设置部分。
![Advanced Settings - PayPal Express Checkout](https://docs.magento.com/user-guide/configuration/sales/assets/payment-methods-paypal-payments-pro-express-checkout-advanced-settings.png)
- 将在购物车上显示设置为 Yes
- 将适用付款设置为以下选项之一：
  - All Allowed Countries: 来自您商店配置中指定的所有国家/地区的客户都可以使用此付款方式。
  - Specific Countries: 选择此选项后，将出现“来自特定国家/地区的付款”列表。要选择多个国家/地区，请按住 Ctrl 键 (PC) 或 Command 键 (Mac) 并单击每个项目。
- 要将与支付系统的通信写入日志文件，请将调试模式设置为 Yes
> 根据 PCI 数据安全标准，信用卡信息不会记录在日志文件中。
- 要启用主机真实性验证，请将启用 SSL 验证设置为 Yes
- 要从 PayPal 站点按行项目显示客户订单的完整摘要，请将转移购物车行项目设置为 Yes
- 要允许客户从 PayPal 站点完成交易而无需返回您的商店进行订单审核，请将跳过订单审核步骤设置为 Yes。
- 完成后，单击保存配置。 17 天前