---
title: Digital Document Publishing
description: 了解如何使用Adobe PDF Embed API在网页中显示嵌入的PDF文档
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8090
thumbnail: KT-8090.jpg
exl-id: 3aa9aa40-a23c-409c-bc0b-31645fa01b40
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1903'
ht-degree: 0%

---

# 数字文档发布

![用例主横幅](assets/UseCaseDigitalHero.jpg)

电子文档无处不在 — 事实上，可能有 [数万亿PDF](https://itextpdf.com/en/blog/technical-notes/do-you-know-how-many-pdf-documents-exist-world) 从全球来看，这个数字每天都在上升。 通过在网页中嵌入PDF查看器，用户可以查看文档，而无需重新设计HTML和CSS或阻止访问您的网站。

让我们来了解一个大众情景。 A公司帖子 [其网站上的白皮书](https://www.adobe.io/apis/documentcloud/dcsdk/digital-content-publishing.html)
为其应用程序和服务提供上下文。 该网站的营销人员希望更好地了解用户如何与基于PDF的内容进行交互，并将其与网页和品牌相结合。 他们决定把白皮书公布为 [门禁内容](https://whatis.techtarget.com/definition/gated-content-ungated-content#:~:text=Gated%20content%20is%20online%20materials,about%20their%20jobs%20and%20organizations.)，控制谁可以下载它们。

## 您可以学到的内容

在本实际操作教程中，了解如何使用显示网页中的嵌入PDF文档 [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html)，免费且易于使用。 这些示例使用一些JavaScript、Node.js、Express.js、HTML和CSS。 您可以在以下位置查看完整项目代码： [GitHub](https://www.google.com/url?q=https://github.com/marcelooliveira/EmbedPDF/tree/main/pdf-app&amp;sa=D&amp;source=editors&amp;ust=1617129543031000&amp;usg=AOvVaw2rzSwYuJ_JI7biVIgbNMw1).

## 相关的API和资源

* [嵌入式APIPDF](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [项目代码](https://www.google.com/url?q=https://github.com/marcelooliveira/EmbedPDF/tree/main/pdf-app&amp;sa=D&amp;source=editors&amp;ust=1617129543031000&amp;usg=AOvVaw2rzSwYuJ_JI7biVIgbNMw1)

## 创建节点Web应用程序

首先，让我们使用Node.js和Express创建一个网站，该网站使用外观精美的PDF并提供多个下载模板。

首先， [下载并安装Node.js](https://nodejs.org/en/download/).

要以最小的Web应用程序结构轻松创建Node.js项目，请安装应用程序生成器工具 `` `express-generator` ``.

```
npm install express-generator -g
```

接下来，创建名为pdf-app的新Express应用程序，选择作为视图引擎。

```
express pdf-app --view=ejs
```

现在，移至\\pdf-app目录并安装所有项目依赖项。

```
cd pdf-app
npm install
```

然后，启动本地Web服务器并运行应用程序。

```
npm start
```

最后，打开网站： <http://localhost:3000>.

![基本网站的屏幕截图](assets/ddp_1.png)

您现在拥有一个基本网站。

## 正在渲染白皮书数据

为了向网站发布白皮书，在网站上定义并准备白皮书数据以显示这些文档。 首先，在项目根目录下创建一个新的\\data文件夹。 有关可用白皮书的信息来自名为 [data.json](https://github.com/marcelooliveira/EmbedPDF/blob/main/pdf-app/data/data.json)，该文件夹将被添加到data文件夹中。

要使Web应用程序具有优美精致的外观，请安装 [Bootstrap](https://getbootstrap.com/) 和 [字体棒极了](https://fontawesome.com/) 前端库。

```
npm install bootstrap
npm install font-awesome
```

打开app.js文件并包括这些目录作为静态文件的源，将其放置在现有目录之后 `` `express.static` `` 行。

```
app.use(express.static(path.join(__dirname, '/node_modules/bootstrap/dist')));
app.use(express.static(path.join(__dirname, '/node_modules/font-awesome')));
```

要包含PDF文档，请在项目的\\public文件夹下创建一个名为\\pdf的文件夹。 您可以从此处复制PDF和缩览图，而不是自己创建 [GitHub存储库文件夹](https://github.com/marcelooliveira/EmbedPDF/tree/main/pdf-app/public) 到\\pdf和\\image文件夹。

\\public\\pdf文件夹现在包含PDF文档：

![PDF文件图标的屏幕截图](assets/ddp_2.png)

而\\public\\images文件夹应包含每个PDF文档的缩览图：

![PDF缩略图的屏幕截图](assets/ddp_3.png)

现在，打开\\routes\\index.js文件，其中包含用于路由主页的逻辑。 要使用data.json文件中的白皮书数据，必须加载负责访问文件系统并与之交互的Node.js模块。 然后，声明 `fs` 常量，如下所示：

```
const fs = require('fs');
```

然后，读取并解析data.json文件，将其存储在论文变量中：

```
let rawdata = fs.readFileSync('data/data.json');
let papers = JSON.parse(rawdata);
```

现在，修改该行以调用索引视图的渲染方法，将论文集作为索引视图的模型传递。

```
res.render('index', { title: 'Embedding PDF', papers: papers });
```

要在主页上呈现白皮书集合，请打开\\views\\index.ejs文件，并将现有代码替换为您项目的 [索引文件](https://github.com/marcelooliveira/EmbedPDF/blob/main/pdf-app/views/index.ejs).

现在，重新运行npm start并打开 <http://localhost:3000> 查看可用的白皮书集合。

![白皮书缩略图的屏幕截图](assets/ddp_4.png)

接下来的部分将介绍如何增强网站功能并使用 [嵌入式APIPDF](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) 以在Web页上显示PDF文档。 PDFEmbed API可免费使用 — 您只需获取API凭据即可。

## 获取PDF的嵌入API凭据

要获取免费PDF的Embed API凭据，请访问 [开始使用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 新帐户或登录到现有帐户后的页面。

点击 **创建新凭据** 然后 **开始使用：**

![有关如何创建新凭据的屏幕截图](assets/ddp_5.png)

此时，如果您没有免费帐户，则要求您注册一个。

选择 **嵌入式APIPDF**，然后键入您的凭据名称和应用程序域。 使用 **本地主机** 域，因为正在本地测试Web应用程序。

![为PDFEmbed API创建新凭据的屏幕截图](assets/ddp_6.png)

单击 **创建凭据** 按钮以访问PDF凭据并获取客户端ID （API密钥）。

![有关如何复制新凭据的屏幕截图](assets/ddp_7.png)

在Node.js项目中，在应用程序的根文件夹中创建名为.ENV的文件，并使用上一步骤中的API KEY凭据值声明PDF嵌入客户端ID的环境变量。

```
PDF_EMBED_CLIENT_ID=**********************************************
```

之后，您可以使用此客户端ID访问PDFEmbed API。 安装dotenv包以使用Node.js代码访问此环境变量。

```
npm install dotenv
```

现在，打开app.js文件并在文件顶部添加以下行，以便Node.js可以加载dotenv模块：

```
require('dotenv').config();
```

## 在Web应用程序中显示PDF

现在，使用PDFEmbed API在网站上显示PDF。 打开实时内容 [PDFEmbed API演示](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf).

![实时PDFEmbed API演示的屏幕截图](assets/ddp_8.png)

在左侧面板上，您可以选择最适合您网站需求的嵌入模式：

* **全窗口**：该PDF涵盖所有网页空间

* **大小容器**：PDF以有限大小的div形式显示在网页内部，一次显示一页

* **In-Line**：整个PDF在网页内部的div中显示

* **Lightbox**：PDF在网页顶部显示为图层

建议对白皮书使用内嵌模式，并在以后使用代码生成器将PDF嵌入到应用程序中。

## 创建内嵌模式页面

要在网页中嵌入PDF查看器并同时显示所有页面，请使用内嵌模式创建一个新页面。

使用EJS视图引擎在文件\\views\\in-line.ejs中创建一个新视图。

```
<! html DOCTYPE >
<html>
<head>
<title>
<%= title %>
</title>
<link rel='stylesheet' href='/stylesheets/style.css' />
<link rel='stylesheet' href='/css/bootstrap.min.css'/>
<link rel='stylesheet' href='/css/font-awesome.min.css' />
<style type="text/css">
p {
font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif
}
</style>
</head>
<body class="m-0">
<div>
<main>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<h3>
<p class="text-center">Grow your business, establish your brand,<br
/>
```

把你的客户放在第一位。

```
</p>
</h3>
<div>
<p class="text-center">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do<br />
eiusmod tempor incididunt ut labore et dolore</p>
</div>
</div>
</main>
<footer>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<p class="text-center">Bodea Inc. Your trusted partner since 2008</p>
</div>
</div>
</footer>
</div>
</div>
</body>
</html>
```

然后，修改\\views\\index.ejs以创建用于打开串联视图的按钮。

```
<div class="card-body">
<h5 class="card-title">
<span>
<%= paper.title %>
</span>
</h5>
<p>
<a class="btn btn-sm btn btn-danger" href="/in-line/<%=
paper.id %>">
<span type="button"></span>
<span class="fa fa-file-pdf-o"></span>&nbsp;View Document</button>
</a>
</p>
</div>
```

打开app.js文件，并在indexRouter声明后声明新的路由器：

```
var indexRouter = require('./routes/index');
var inLineRouter = require('./routes/in-line');
```

然后在app.use(&#39;/&#39;， indexRouter)；之后添加此代码，以将内嵌模式视图与其路由器关联：

```
app.use('/', indexRouter);
app.use('/in-line', inLineRouter);
```

现在，在\\routes下创建一个新的in-line.js文件，以创建新的路由器逻辑。 包括Express，一个启用Web应用程序后端节点模块。

```
var express = require('express');
const fs = require('fs');
var router = express.Router();
```

接下来，创建一个端点，以处理特定白皮书ID的GET请求并呈现in-line.ejs视图。

```
router.all('/:id', function(req, res, next) {
let rawdata = fs.readFileSync('data/data.json');
let papers = JSON.parse(rawdata);
let paper = papers.filter(p => p.id == parseInt(req.params.id))[0];
res.render('in-line', { title: paper.title, paper: paper });
});
module.exports = router;
```

再看一次 [现场演示](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) 以自动生成PDF的Embed API代码。 点击 **In-Line** 在左侧面板中：

![实时PDFEmbed API演示的屏幕截图](assets/ddp_8.png)

点击 **生成代码** 查看显示“大小为”的HTMLPDF查看器所需的容器代码。

![代码预览的屏幕截图](assets/ddp_9.png)

点击 **复制代码** 并将代码粘贴到in-line.ejs文件中。

```
<div>
<p class="text-center">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do<br />
eiusmod tempor incididunt ut labore et dolore</p>
</div>
<div class="row align-items-center border border-primary">
<div id="adobe-dc-view" style="width: 800px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
document.addEventListener("adobe_dc_view_sdk.ready", function(){
var adobeDCView = new AdobeDC.View({clientId: "<YOUR_CLIENT_ID>", divId: "adobe-dc-view"});
adobeDCView.previewFile({
content:{location: {url: "https://documentcloud.adobe.com/view-sdk-demo/PDFs/Bodea Brochure.pdf"}},
metaData:{fileName: "Bodea Brochure.pdf"}
}, {embedMode: "IN_LINE"});
});
</script>
</div>
```

但是，文档参数仍采用硬编码。 让我们用EJS括号语法(\&lt;%= someValue %\>)替换它们，以根据白皮书模型数据呈现页面。

```
<div id="adobe-dc-view" style="width: 800px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
document.addEventListener("adobe_dc_view_sdk.ready", function () {
var adobeDCView = new AdobeDC.View({ clientId: "<%=process.env.PDF_EMBED_CLIENT_ID %>", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url: "<%= paper.pdf %>" } },
metaData: { fileName: "<%= paper.fileName %>" }
}, {
embedMode: "IN_LINE"
});
});
</script>
```

现在使用npm start命令运行应用程序并打开网站 <http://localhost:3000>.

![PDF白皮书缩览图的屏幕快照](assets/ddp_10.png)

最后，选择一张白皮书并单击 **查看文档** 要打开包含内置嵌入PDF的新页面，请执行以下操作：

![PDF白皮书的屏幕截图 ](assets/ddp_11.png)

请注意下载PDF和打印PDF选项现在是如何显示的。

![下载和打印选项的屏幕截图](assets/ddp_12.png)

您想要在后端控制这些标志。 稍后，您可以根据用户身份实施授权控制，并根据业务规则限制访问。 这里不需要这种复杂性，所以让我们修改\\routes\\in-line.js以在model对象中包含authenticated和permissions属性。

```
let authenticated = false;
res.render('in-line', {
title: paper.title,
paper: paper,
authenticated: authenticated,
permissions: {
showDownloadPDF: true,
showPrintPDF: true,
showFullScreen: true
}
});
```

然后，修改\\views\\in-line.ejs ，以便您的网页可以呈现来自后端的标志值。

```
embedMode: "IN_LINE",
showDownloadPDF: <%= permissions.showDownloadPDF %>,
showPrintPDF: <%= permissions.showPrintPDF %>,
showFullScreen: <%= permissions.showFullScreen %>
Now, open the in-line.js route file and modify it to disallow the printing, downloading, and full-screen controls.
permissions: {
showDownloadPDF: false,
showPrintPDF: false,
showFullScreen: false
}
```

然后，重新运行应用程序以查看此更改在PDF查看器中反映的方式。

![PDF文件的屏幕截图](assets/ddp_13.png)

## 创建封闭内容

根据最终用户场景，公司网站的营销人员希望更好地了解用户如何与基于PDF的内容进行交互，并将内容与其网页和品牌的其余部分合并。

我们的重点是嵌入PDF，因此您并未创建用户身份验证功能。 相反，只需使用一个接受某些用户信息的Web表单实现一个简单、虚假的付费墙，然后在用户提交表单后显示PDF文档。

将\\routes\\in-line.js文件替换为以下内容，以便为视图模型提供用户信息：

```
var express = require('express');
const fs = require('fs');
var router = express.Router();
router.all('/:id', function(req, res, next) {
let rawdata = fs.readFileSync('data/data.json');
let papers = JSON.parse(rawdata);
let paper = papers.filter(p => p.id == parseInt(req.params.id))[0];
let authenticated = false;
let user = {};
if (req.body.firstName) {
user = {
firstName: req.body.firstName,
lastName: req.body.lastName,
jobTitle: req.body.jobTitle,
email: req.body.email,
};
authenticated = true;
}
res.render('in-line', {
title: paper.title,
paper: paper,
user: user,
authenticated: authenticated,
permissions: {
showDownloadPDF: false,
showPrintPDF: false,
showFullScreen: false
}
});
});
module.exports = router;
```

然后将\\views\\in-line.ejs内容替换为以下代码。 根据用户是否为经过身份验证的用户，它会显示用户数据表单或PDF查看器。

```
<!DOCTYPE html>
<html>
<head>
<title>
<%= title %>
</title>
<link rel='stylesheet' href='/css/bootstrap.min.css'/>
<link rel='stylesheet' href='/css/font-awesome.min.css' />
<style type="text/css">
p {
font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif
}
</style>
</head>
<body class="m-0">
<% if (authenticated) { %>
<header class="bg-dark text-white">
<div class="text-right mr-4">Hello, <%= user.firstName %> <%= user.lastName%></div>
</header>
<% } %>
<div>
<main>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<h3>
<p class="text-center">Grow your business, establish your brand,<br
/>
```

把你的客户放在第一位。

```
</p>
</h3>
<div>
<p class="text-center">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do<br />
eiusmod tempor incididunt ut labore et dolore</p>
</div>
<% if (!authenticated) { %>
<div class="row">
<form method="POST" class="center-panel text offset-md-3 col-md-6 border">
<fieldset class="offset-md-1">
<legend>Submit your info to<br/>access the whitepaper</legend>
<p><input name="firstName" placeholder="first name"/></p>
<p><input name="lastName" placeholder="last name"/></p>
<p><input name="jobTitle" placeholder="job title"/></p>
<p><input name="email" placeholder="email"/></p>
<p><button type="submit" class="btn btn-sm btn btn-primary">Submit</button></p>
</fieldset>
</form>
</div>
<% } %>
<% if (authenticated) { %>
<div class="row align-items-center border border-primary">
<div id="adobe-dc-view" style="width: 800px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
document.addEventListener("adobe_dc_view_sdk.ready", function () {
var adobeDCView = new AdobeDC.View({ clientId: "<%=process.env.PDF_EMBED_CLIENT_ID %>", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url: "<%= paper.pdf %>" } },
metaData: { fileName: "<%= paper.fileName %>" }
}, {
embedMode: "IN_LINE",
showDownloadPDF: <%= permissions.showDownloadPDF %>,
showPrintPDF: <%= permissions.showPrintPDF %>,
showFullScreen: <%= permissions.showFullScreen %>
});
});
</script>
<% } %>
</div>
</div>
</main>
<footer>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<p class="text-center">Bodea Inc. Your trusted partner since 2008</p>
</div>
</div>
</footer>
</div>
</div>
</body>
</html>
```

![闸门内容的屏幕截图](assets/ddp_14.png)

站点访问者现在只能在提交其信息后访问PDF：

![嵌入式查看器中PDF内容的屏幕截图](assets/ddp_15.png)

## 启用事件

让我们看看如何轻松地将PDF查看器事件与您的应用程序集成在一起，以便为营销人员收集分析数据。 要使用PDFEmbedAPI扩展查看器，请在声明adobeDCView变量之后和调用previewFile方法之前添加以下代码行：

```
var adobeDCView = new AdobeDC.View({ clientId: "<%=process.env.PDF_EMBED_CLIENT_ID %>", divId: "adobe-dc-view" });
adobeDCView.registerCallback(
AdobeDC.View.Enum.CallbackType.EVENT_LISTENER,
function(event) {
console.log(event);
},
{ enablePDFAnalytics: true }
);
```

现在，重新运行该应用程序，并打开Web浏览器的开发者工具以查看事件数据。

![代码屏幕截图](assets/ddp_16.png)

您可以将此数据发送到 [Adobe Analytics](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=view) 或其他分析工具。

## 后续步骤

[!DNL Acrobat Services] API可帮助开发人员使用以PDF为中心的工作流程轻松解决数字发布难题。 您已了解如何创建示例Node Web应用程序以显示白皮书集合。 然后，获取 [免费API凭据](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 并构建对白皮书的有限访问权，这些白皮书的展示方式可以分为四种 [嵌入模式](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf).

将此工作流程整合在一起有助于 [虚拟营销人员](https://www.adobe.io/apis/documentcloud/dcsdk/digital-content-publishing.html) 收集潜在客户联系信息以交换白皮书下载情况，并查看与相关PDF交互的人员统计信息。 您可以将这些功能纳入您的网站，以推动和监控用户参与情况。

如果您是Angular或React开发人员，您可能喜欢尝试 [其他样本](https://github.com/adobe/pdf-embed-api-samples) 介绍了如何将PDFEmbed API与React和Angular项目集成。

通过Adobe，您可以使用创新解决方案打造端到端客户体验。 签出 [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/viesdk) 免费。 要探索您还能做什么，请试用具有以下功能的Adobe PDF Services API [gopr即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)[结冰](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html).

[开始使用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 与 [!DNL Adobe Acrobat Services] 今天的API。
