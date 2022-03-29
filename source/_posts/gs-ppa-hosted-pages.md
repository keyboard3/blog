---
title: 高级 PayPal 付款: 托管页面入门
top: false
cover: false
toc: true
mathjax: true
date: 2022-03-29 15:49:03
password:
summary:
tags: [paypal]
categories:
---

# 概览
高级 PayPal 付款 (PPA) 使得商家接受 PayPal 和 信用卡。 PPA 给商家提供 PayPal 商家账户。集成 PPA 的过程和 PayPal Payflow 网关的过程相似。

这个文档展示了如何从 Payflow pilot 端点中获取安全 token，然后在测试调用中提交 token。有关向你展示如何设置，自定义和测试托管页面，看[设置和测试托管页面](https://developer.paypal.com/api/nvp-soap/payflow/test-hosted-pages/)

# 核心概念
PayPal 的托管结帐页面(也称为托管结帐模板)使您可以安全的传递交易数据到服务器并收集信用卡信息。尽管下面的[第一次调用](https://developer.paypal.com/api/nvp-soap/payflow/gs-ppa-hosted-pages/#make-your-first-call)部分描述了托管页面的初始自定义，但请初始调用后参考 [Payflow 网关开发指南和建议](https://developer.paypal.com/api/nvp-soap/payflow/integration-guide/configure-hosted-checkout/)

PPA 要求使用 PayPal 的托管结帐模板来提交销售和授权数据。这些模板使商家能够避免信用卡详细信息通过其服务器的 PCI 负担

用作 PPA 集成部分的安全令牌有助于保护交易数据。您必须使用托管结帐页面的安全令牌。该令牌适用于一次交易，有效期为 30 分钟。服务器使用令牌及其令牌 ID 来检索和显示交易数据以供客户批准。

# 完成第一次调用
在完成第一次调用 Payflow pilot 端点([https://pilot-payflowpro.paypal.com/?_ga=1.159497021.1477272473.1648436854](https://pilot-payflowpro.paypal.com/?_ga=1.159497021.1477272473.1648436854)) 之前, 去[PayPal Payments Advanced](https://www.paypal.com/advanced/?_ga=1.96198428.1477272473.1648436854) 登录 PPA 账户。另请参阅以下步骤，将用户添加到帐户，以用于 API 调用。商家应为交易创建一个用户帐户。否则，如果商户登录ID的密码发生变化，API调用将失败。

因此，在您在 PayPal Payments Advanced 注册您的 PPA 帐户后，将用户添加到该帐户：
- 使用 PayPal Payments Advanced 帐户的商家登录名和密码，登录 PayPal Manager。
- 单击帐户管理。
- 在管理用户下，单击添加用户。
- 填写管理员配置、用户信息和用户登录信息下的字段。
- 在将权限分配给用户下的选择预定义角色字段中，您可以选择 FULL_TRANSACTIONS。将用户状态保留为活动。
- 单击更新按钮。

有关字段的更多信息，请单击帮助按钮。

## 设置托管结帐页面
- 使用您在上面获得的 PayPal Payments Advanced 帐户，登录到 PayPal Manager。
![](https://www.paypalobjects.com/webstatic/en_US/developer/docs/pfg/paypal_manager1.png)
- 在页面的服务摘要部分的服务下，单击托管结帐页面。
![](https://www.paypalobjects.com/webstatic/en_US/developer/docs/pfg/paypal_mngr_svc_summary.png)
- 在标题为 Hosted Checkout Pages 的登录页面上，单击设置。
- 最初，出于测试目的，我们将此设置页面上的大部分字段留空。当您稍后填写更多设置页面字段时，请务必单击同一页面上的帮助按钮以获取更多信息。另请参阅 [Payflow Gateway 开发人员指南和参考](https://developer.paypal.com/api/nvp-soap/payflow/integration-guide/)。
- 在此设置页面上，输入以下值。 （设置页面上的其他值对于您的初始测试调用是不必要的。）
 - PayPal 沙盒电子邮件地址: 您用于 PayPal 沙盒帐户的电子邮件，它是您在 PayPal 开发人员体验中的个人资料的一部分。
 - Return URL: 在页面的“付款确认”部分，输入 Return URL（供消费者继续付款时使用）。对于 Return URL 方法，指定 POST。
 - 启用安全令牌: 设置为是。
- 在此设置页面上，单击保存更改。
- 单击自定义，选择布局 C，然后单击保存并发布。

## 从 pilot URL 获取安全令牌
如 [Payflow Gateway 开发人员指南和参考](https://developer.paypal.com/api/nvp-soap/payflow/integration-guide/)中所述，您可以发送测试数据，例如作为名称-值对，发送到 Payflow pilot 端点 (https://pilot-payflowpro.paypal.com)。

> 注意: 由于 Payflow 在多个数据中心外运行，我们强烈建议所有 API 调用都使用上面的主机 URL 完成。如果您对 IP 地址进行硬编码以通过 Payflow API 发送交易，如果数据中心因任何问题或任何计划维护而离线，您的交易失败，PayPal 概不负责。

下表包含用于获取安全令牌的测试调用的参数：
|  名字   | 描述  |
|  ----  | ----  |
| PARTNER  | 支付流合作伙伴。以下示例使用 PayPal，因为 PayPal Payments Advanced 包含一个 PayPal 商家帐户。 |
| VENDOR  | 您用于登录 PayPal 管理的商家登录 ID。 |
| USER  | 您使用上面的 PayPal 管理器添加到您的帐户的用户的名称。 |
| PWD  | 您使用上面的 PayPal 管理器添加到您的帐户的用户的密码。 |
| TRXTYPE  | 交易的类型，例如S 出售。 |
| AMT | 销售金额。|
| CREATESECURETOKEN | 指定 Y 值以请求安全令牌以完成交易。|
| SECURETOKENID | 您为将从 Payflow 试点端点 (https://pilot-payflowpro.paypal.com) 返回的令牌创建的 ID。使用唯一的字母数字值，最多 36 个字符。例如，您可以指定 SECURETOKENID=9a9ea8208de1413abc3d60c86cb1f4c5。|

出于演示目的，以下示例使用 cURL 获取您将在后续测试调用中使用的安全令牌。

请参阅上表中的参数说明，了解在以下示例中替换 PARTNER、VENDOR、USER 和 PWD 的值。
```
curl https://pilot-payflowpro.paypal.com \ -s \ --insecure \ -d PARTNER=<PayPal> \ -d VENDOR=<MyMerchantID> \ -d USER=<UserID> \ -d PWD=<UserPassword> \ -d TRXTYPE=S \ -d AMT=40 \ -d CREATESECURETOKEN=Y \ -d SECURETOKENID=12528208de1413abc3d60c86cb15
```
响应应类似于以下内容。响应包含 RESULT=0（表示成功）、一个 SECURETOKEN（用于后续事务调用）、一个 SECURETOKENID（您在请求中提供以标识接收到的令牌）和一个 RESPMSG 值 Approved。
```
RESULT=0&
SECURETOKEN=123456NYslUGMy0tlKafELwct&
SECURETOKENID=12528208de1413abc3d60c86cb15&
RESPMSG=Approved
```

## 使用安全令牌并获取交易详情
可以在测试 HTML 文件中的 iframe 标记中使用安全令牌（如上所示）。 iframe 标签使您能够嵌入您在上面自定义的 PayPal 托管页面。
> 注意: 对比下面的测试代码（支付后只有iframe窗口重定向），父窗口一般应该重定向。因此，与下面的测试代码相比，您可以创建一个中间页面，PayPal 将重定向到该中间页面，并使用该中间页面捕获返回数据并强制客户的浏览器重定向到您的最终收据页面。

在下面的测试代码中，iframe 标记中使用了以下参数，该标记指向 Payflow Link 端点 https://payflowlink.paypal.com。使用下面的测试 HTML 文件后，您可以开始添加特定于 PPA 集成的参数和功能；请参阅 [Payflow Gateway 开发人员指南和参考](https://developer.paypal.com/api/nvp-soap/payflow/integration-guide/)。

以下测试 HTML 文件以测试模式提交安全令牌。此表包含 HTML 文件中 iframe 标记的参数：
|  名字   | 描述  |
|  ----  | ----  |
| MODE | 指定 TEST 以指示您的调用是测试调用。对于生产应用程序，您可以将值设置为 LIVE（这是默认值）。|
| SECURETOKENID | 指定您为安全令牌创建（如上所述）的 ID。当您提交安全令牌（如下）时，请使用与上述相同的 SECURETOKENID 值。 |
| SECURETOKEN | 指定您刚刚从 Payflow 试点端点 (https://pilot-payflowpro.paypal.com) 收到（上图）的 SECURETOKEN 的值。  |

在下面创建测试 HTML 文件。然后，在 iframe 标记的 src 属性中，替换您自己的 SECURETOKENID 和 SECURETOKEN 值。此测试 HTML 文件说明了如何在商家页面中包含托管页面，以使商家能够接受付款。

```
<html>
  <head>
    <title></title>
  </head>
  <body>
    <p>This is a test HTML file.</p>

    <iframe src="https://payflowlink.paypal.com?MODE=TEST&SECURETOKENID=MySecureTokenID&SECURETOKEN=MySecureToken"
    name="test_iframe" scrolling="no" width="570px" height="540px"></iframe>

  </body>
</html>
```

保存并在浏览器中打开上述 HTML 文件。在“这是一个商家页面”的文字下方，会显示页面的 iframe（内联框架）部分。该页面的“托管结帐”部分使客户可以选择使用 PayPal 或信用卡付款，然后进行付款。
![](https://www.paypalobjects.com/webstatic/en_US/developer/docs/pfg/payflow_iframe_with_hosted_pages.png)

## 接收交易数据
在上面页面的 iframe 部分中，其中显示了托管的结帐框架，输入测试信用卡号 4111111111111111 和到期日期值 12 和 12。然后单击立即付款。显示以下确认页面：
![](https://www.paypalobjects.com/webstatic/en_US/developer/docs/pfg/payflow_pay_confirmation.png)

该确认页面使客户能够点击“返回商家网站。如果您单击该链接，将发生以下情况： 服务器将您的浏览器重定向到您的返回 URL，即您在 PayPal 管理器的设置页面上提供的返回 URL。（请参阅上面的“设置托管结帐页面”。请注意，PayPal 管理器中设置页面上的帮助按钮包含有关自定义选项的信息。）

在将您的浏览器重定向到 return URL 时，服务器通过 POST 将交易数据发送到 return URL。

在我们的示例中，通过 POST 返回的交易数据如下，没有链接中断：
```
TYPE=S&
RESPMSG=Approved&
ACCT=1234&
COUNTRY=US&
VISACARDLEVEL=12&
TAX=0.00&
CARDTYPE=0&
PNREF=12341EE308F6&
TENDER=CC&
AVSDATA=XXN&
METHOD=CC&
SECURETOKEN=123456NYslUGMy0tlKafELwct&
SHIPTOCOUNTRY=US&
AMT=40.00&
SECURETOKENID=12528208de1413abc3d60c86cb15&
TRANSTIME=2012-03-26+14%3A07%3A59&
HOSTCODE=A&
COUNTRYTOSHIP=US&
RESULT=0&
AUTHCODE=124PNI&
EXPDATE=1212
```
您可以根据需要解析此交易数据。有关可以通过 POST 发送到您的返回 URL 的其他参数的信息，请参阅 [Payflow Gateway 开发人员指南和参考](https://developer.paypal.com/api/nvp-soap/payflow/integration-guide/)。
