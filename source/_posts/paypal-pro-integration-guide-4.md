---
title: PayPal 网站支付专业版托管解决方案集成指南 (4)
top: false
cover: false
toc: true
mathjax: true
date: 2022-04-01 21:11:36
password:
summary:
tags: [PayPal, Guide, 翻译]
categories:
---

[原文](https://www.paypalobjects.com/webstatic/en_AU/developer/docs/pdf/hostedsolution_au.pdf#page=41&zoom=100,84,126)

# 集成 iframe 到站点中

PayPal 提供了一种紧凑的付款方式，可以集成到您网站上的 iFrame 中。由于此表单已集成在您的网站上，因此买家永远不会离开您的网站，因此减少潜在的流失。你也可以在 紧凑的支付表单周围的主框架中保持你的结账外观和感觉。信用卡字段是紧凑型支付表单的一部分，所以你不必单独收集这些信息。

> 重要提示: 由于涉及到 iFrame 的安全问题，以下浏览器被支持并安全使用—— internet explorer 7.0、8.0 和 9.0、 Firefox 24、 Chrome 30、 Safari 4.x 和 5.x。涉及其他浏览器用户的交易不应该使用 iFrame 流或放弃交易。此外，除了 iFrame 流之外还有另一个风险——如果 PayPal iFrame 成为攻击源，那么攻击似乎来自商家网站; 如果你希望避免这个额外的风险，就不要使用 iFrame 流。

> 注意: 由于欺骗关注，表单不包含任何 PayPal 品牌。

如果你想集成 iFrame，你必须使用 MiniLayout 模板。你可以从你的 PayPal 账户的自定义页面中选择 MiniLayout。或者你可以在交易时传递 HTML 变量 Template = TemplateD。 本章的例子使用后面的 HTML 变量方法来设置 MiniLayout 模板。

对于 MiniLayout，当从移动浏览器查看支付页面时，PayPal 不会自动显示移动优化的支付流程。原因是，如果 PayPal 自动显示一个移动优化嵌入式模板在一个商业网页，可能不是移动优化，这可能会产生意想不到的和不受欢迎的结果。为了显示一个移动优化流程，在交易时间在模板 HTML 变量中传递 mobile 或 mobile-iframe。

MiniLayout 模板(紧凑型支付表单)包含以下字段:
- 信用卡号码
- 过期日期
- Cvv2 号码(如果适用，根据卡的类型)
- 其他卡类型所需的任何额外字段，如 Maestro 或 Switch 的开始日期和发行号码。

此模板还提供以下选项:
- 移除 PayPal 支付按钮。虽然表单默认提供了使用 PayPal 账户支付的选项，但是你可以联系你的账户经理或客户支持来关闭这个选项。
- 手动自定义 paynow 按钮的颜色。

重要提示: 这个简洁的付款表单不显示买家的账单地址，即使 showBillingAddress = true 被传递。然而，对于一些商家来说，可能需要传递账单地址来成功处理交易。

## 集成 iFrame
选择以下方法之一，将紧凑型支付形式集成到您的网站中:
- 手动集成
- API 集成

重要提示: 为了获得最佳性能，PayPal 建议您先加载 iFrame 资源，然后再加载其他资源，如图像和 javascript。如果在你加载 iFrame 的时候，你的支付页面上有太多的资源在运行，那么对 iFrame 的请求可能不会被放置或者延迟。这可能会导致买家看到一个空白的 iFrame。

### 手动集成
若要在网站中手动整合紧凑型付款表格，请执行以下步骤:
- 1. 在您希望紧凑支付表单出现在您的网站上的位置输入 iFrame 标记。例如:
```html
<iframe name="hss_iframe" width="570px" height="540px"></iframe>
```
紧凑型支付表单的允许大小为 570 像素宽度到 540 像素高度。
- 2. 以下是 iFrame 代码，添加用适当的 Hosted Solution 变量(包括要支付的总金额)填充的 隐藏表单，并指定变量 TemplateD。例如,
```html
<form style="display:none" target="hss_iframe" name="form_iframe"
method="post"
action="https://securepayments.paypal.com/webapps/HostedSoleSolutionApp/
webflow/sparta/hostedSoleSolutionProcess">
<input type="hidden" name="cmd" value="_hosted-payment">
<input type="hidden" name="subtotal" value="50">
<input type="hidden" name="business" value="HNZ3QZMCPBAAA">
<input type="hidden" name="paymentaction" value="sale">
<input type="hidden" name="template" value="templateD">
<input type="hidden" name="return"
value="https://yourwebsite.com/receipt_page.html">
</form>
```
> 注意: 如果 iFrame 交易失败，请传递账单地址。有关 HTML 变量表的更多信息，请参见付款页面设置的 HTML 变量。

- 3. 确保目标名称与 iFrame 名称匹配，如下例所示:
```html
<iframe name="hss_iframe" width="570px" height="540px"></iframe>
<form style="display:none" target="hss_iframe" name="form_iframe"
method="post"
action="https://securepayments.paypal.com/webapps/HostedSoleSolutionApp/
webflow/sparta/hostedSoleSolutionProcess">
```

- 4. 使用 JavaScript 提交表单。例如:
``` html
<script type="text/javascript">
 document.form_iframe.submit();
</script>
```

- 手动集成的例子
按照以上步骤完成的示例如下:
```html
<iframe name="hss_iframe" width="570px" height="540px"></iframe>
<form style="display:none" target="hss_iframe" name="form_iframe"
method="post"
action="https://securepayments.paypal.com/webapps/HostedSoleSolutionApp/web
flow/sparta/hostedSoleSolutionProcess">
<input type="hidden" name="cmd" value="_hosted-payment">
<input type="hidden" name="subtotal" value="50">
<input type="hidden" name="business" value="HNZ3QZMCPBAAA">
<input type="hidden" name="paymentaction" value="sale">
<input type="hidden" name="template" value="templateD">
<input type="hidden" name="return"
value="https://yourwebsite.com/receipt_page.html">
</form>
<script type="text/javascript">
 document.form_iframe.submit();
</script>
```

### API 集成
要使用 API 在网站中集成紧凑的支付表单, 参考”将 Button Manager API 与托管解决方案结帐一起使用“
注意: 使用 template = templateD 来指定集成类型。

启动托管解决方案付款流程的响应中有两个选项：
- 使用在响应中返回的 URL
- 使用表单 POST

#### 使用在响应中返回的 URL
在响应中标识为 EMAILLINK 的 URL 中，按照下面的示例为 iFrame 添加“src”， 以重定向买方并启动支付流。
```html
<iframe src="https://securepayments.paypal.com/...?hosted_button_id=HSSS-
.." width="570px" height="540px"></iframe>
```
紧凑型支付表格的允许大小为 570 像素宽度至 540 像素高度。
> 重要提示: Safari 浏览器不支持此选项。使用下面描述的 Form POST 选项。

#### 使用表单 POST
确定响应中的 WEBSITECODE，并使用该代码在评论页面上创建一个 Pay Now 按钮。 当你的买家点击这个按钮时，他们会被重定向到 PayPal 托管的支付页面。和 URL 一样，这个按钮可以使用大约两个小时，或者直到付款成功

- 1. 在您希望紧凑支付表单出现在您的网站上的位置输入 iFrame 标记。例如:
```html
<iframe name="hss_iframe" width="570px" height="540px"></iframe>
```
紧凑型支付表单的允许大小为 570 像素宽度到 540 像素高度。

- 2. 在 iFrame 标签中插入以下内容:
```html
WEBSITECODE=<form
action="https://securepayments.paypal.com/webapps/HostedSoleSolutionApp/
webflow/sparta/hostedSoleSolutionProcess" method="post">
<input type="hidden" name="hosted_button_id" value="HSSSGDrPDzuW-ADwkFDMjQmpUK1gTDdR.tv5alaGS6l.XWVVB1MTMQEnGNoLakufQb89zTjf6">
<input type="image" src="https://www.paypal.com/i/btn/btn_paynow_LG.gif"
border="0" name="submit" alt="PayPal - The safer, easier way to pay
online.">
<img alt="" border="0" src="https://www.paypal.com/i/scr/pixel.gif"
width="1" height="1">
</form>
```

- 3. 使用 JavaScript 提交表单。例如:
```html
<script type="text/javascript">
 document.form_iframe.submit();
</script>
```

- API (form post)集成示例
按照以上步骤完成的示例如下:
```html
<iframe name="hss_iframe" width="570px" height="540px"></iframe>
WEBSITECODE=<form
action="https://securepayments.paypal.com/webapps/HostedSoleSolutionApp/web
flow/sparta/hostedSoleSolutionProcess" method="post">
<input type="hidden" name="hosted_button_id" value="HSSSGDrPDzuW-ADwkFDMjQmpUK1gTDdR.tv5alaGS6l.XWVVB1MTMQEnGNoLakufQb89zTjf6">
<input type="image" src="https://www.paypal.com/i/btn/btn_paynow_LG.gif"
border="0" name="submit" alt="PayPal - The safer, easier way to pay
online.">
<img alt="" border="0" src="https://www.paypal.com/i/scr/pixel.gif"
width="1" height="1">
</form>
<script type="text/javascript">
 document.form_iframe.submit();
</script>
```