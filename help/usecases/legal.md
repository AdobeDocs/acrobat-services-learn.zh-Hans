---
title: 管理法律合同
description: 了解如何使用自定义数据输入自动生成和保护法律文档
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8097
thumbnail: KT-8097.jpg
exl-id: e0c32082-4f8f-4d8b-ab12-55d95b5974c5
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1890'
ht-degree: 0%

---

# 管理法律合同

![用例主横幅](assets/UseCaseLegalHero.jpg)

数字化带来了挑战。 今天，大多数组织具有许多类型的[法律合同](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/legal-contracts)，他们必须创建、编辑、批准这些法律合同，并且这些合同已由各方签署。 这些法律合同通常需要独特的定制和品牌化。 组织还可能需要在签名后以受保护的格式保存这些文档，以确保其安全。 要进行所有这些操作，他们需要强健的文档生成和管理解决方案。

许多解决方案都提供一些文档生成，但无法自定义数据输入和条件逻辑，例如仅应用于特定场景的子句。 随着文档越来越广泛，手动更新公司的法律模板不仅困难重重，而且容易出错。 实现这些过程自动化是相当大的。

## 您可以学到的内容

在本实际操作教程中，探索在文档中生成自定义输入字段时[[!DNL Adobe Acrobat Services] API](https://developer.adobe.com/document-services/apis/doc-generation)的功能。 此外，探索如何轻松地将这些生成的文档转换为受保护的便携文档格式(PDF)，以防止数据操作。

本教程涉及在探索将合同转换为PDF时进行一些编程。 若要有效跟随，应在你的电脑上安装[Microsoft Word](https://www.microsoft.com/en-us/download/office.aspx)和[Node.js](https://nodejs.org/)。 还建议基本了解Node.js和[ES6语法](https://www.w3schools.com/js/js_es6.asp)。

## 相关的API和资源

* [Adobe的Document Generation API](https://developer.adobe.com/document-services/apis/doc-generation)

* [PDF的嵌入API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://developer.adobe.com/adobesign-api/)

* [项目代码](https://github.com/agavitalis/adobe_legal_contracts.git)

## 创建模板文档

您可以使用Microsoft Word应用程序或通过下载Adobe的[示例Word模板](https://developer.adobe.com/document-services/apis/doc-generation#sample-blade)来创建法律文档。 但是，如果不使用某些辅助工具(如适用于Microsoft Word的[AdobeDocument Generation Tagger加载项](https://developer.adobe.com/document-services/docs/overview/document-generation-api/wordaddin))，则自定义输入并以数字方式签署这些文档并不容易。

Document Generation Tagger是一款Microsoft Word插件，可使用标签无缝自定义文档。 它允许在使用JSON数据动态填充的文档模板中创建动态字段。

![如何在Word中添加Adobe Document Generation Tagger的屏幕截图](assets/legal_1.png)

要说明如何使用Document Generation Tagger，请安装此插件，然后创建一个JSON数据模型，该模型用于标记简单法律合同文档。

单击&#x200B;**插入**&#x200B;选项卡，在Word中安装Document Generation Tagger，然后在“加载项”组中，单击&#x200B;**我的加载项**。 在“Office加载项”菜单上，搜索“AdobeDocument Generation”，然后单击“**添加**”并完成此过程。 您可以在上面的屏幕捕捉中看到这些步骤。

安装适用于Word的Document Generation Tagger插件后，请创建一个简单的JSON数据模型来标记法律文档。

要继续，请打开您选择的任何编辑器，创建一个名为Agreement.json的文件，然后将下面的代码片段粘贴到您创建的JSON文件中。

```
{
"Agreement": {
"Date": "1/24/2021",
"Prime Contractor Name": "Ogbonna Vitalis Corp",
"Prime State": "Lagos",
"Address": "Maryland Ave, Lagos State, Ng",
"Sub Contractor Name": "Vivvaa Soln",
"Sub Contractor State": "California",
"Sub Contractor Address": "Molusi Avenue, Dallas Texas, CA",
"Agreement Date": "1/24/2021",
"Length": 5
}
}
```

保存此JSON文档后，将其导入到Document Generation Tagger加载项。 单击Word屏幕右上角Adobe组中的&#x200B;**“Document Generation”**，导入文档，如下面屏幕捕捉所示。

![Word中的AdobeDocument Generation Tagger加载项的屏幕截图](assets/legal_2.png)

此时会显示引导您的视频。 您可以通过单击&#x200B;**开始使用**&#x200B;来观看或直接进入标记字段。 单击&#x200B;**开始使用**&#x200B;后，会显示一个上传表单。 单击&#x200B;**上传JSON文件**，然后选择刚刚创建的JSON文件。 导入完成后，单击“**生成标签**”以生成标签。

导入并生成标签后，您可以将这些标签添加到文档中。 若要添加标签，请将光标放在希望显示标签的确切位置。 然后从Document Generation API中选择一个标签并单击&#x200B;**插入文本**。 下面的屏幕截图概述了此过程。

![向文档添加标记的屏幕截图](assets/legal_3.png)

除了使用导入的JSON数据模型创建的基本标签之外，您还可以使用高级功能获得更多选项，例如图像、条件逻辑、计算、重复元素和条件短语。 您可以通过在Document Generation Tagger面板中单击&#x200B;**高级**&#x200B;来访问这些功能。 您可以在下面的屏幕截图中看到这一点。

![AdobeDocument Generation Tagger“高级”选项卡的屏幕截图](assets/legal_4.png)

这些高级功能与基本标签并无区别。 要包含条件逻辑，请选择要填充的文档部分。 然后，配置确定标签插入的规则。

为了进一步说明，例如在协议中，您只想无条件地包括一个部分。 在“选择内容类型”字段中，选择“**分区”。**&#x200B;在“选择记录”字段中，选择用于确定是否显示条件节的选项。 选择所需的条件运算符，并在“值”字段中设置要测试的值。 然后单击“**插入条件”。**&#x200B;下面的屏幕截图说明了此过程。

![插入条件内容的屏幕截图](assets/legal_5.png)

对于计算，选择算术或聚合，然后根据可用的模板标签包括相关的第一条记录、运算符和第二条要使用的记录。 然后单击&#x200B;**插入计算**。

另外，法律合同通常要求当事各方签字。 您可以使用“数值计算”部分正下方的Adobe Sign文本标记插入电子签名。 要包含电子签名，您必须指定收件人的数量，选择&#x200B;**签名者**，然后从下拉列表中相应地选择字段类型。 完成后，单击&#x200B;**插入Adobe Sign文本标记**&#x200B;以完成此过程。

为确保数据的完整性，请以受保护的格式保存法律文档。 借助[!DNL Acrobat Services]个API，您可以快速将文档转换为PDF格式。 您可以构建一个简单的express Node.js应用程序，将Document Generation API集成到该应用程序中，并使用这个简单的应用程序将已标记文档从Word转换为PDF格式。

## 项目设置

首先，设置Node.js应用程序的文件夹结构。 在本例中，将此简单应用程序命名为AdobeLegalContractAPI。 您可以在[此处](https://github.com/agavitalis/adobe_legal_contracts.git)检索源代码。

### 目录结构

创建一个名为AdobeLegalContractAPI的文件夹，然后在您选择的编辑器中打开它。 使用以下文件夹结构通过```npm init```命令创建基本Node.js应用程序：

```
###Directory Structure
AdobeLegalContractAPI
-----config
----------default.json
-----controllers
----------createPDFController.js
----------previewController.js
-----models
----------document.js
-----routes
----------web.js
-----services
-----------upload.js
-----uploads
-----views
-----index.js
```

以上是应用程序的一个简单的Node.js应用程序结构。 现在继续安装必需的npm程序包。

### 包安装

使用npm install命令安装所需的程序包，如下面的代码片段所示：

```
npm install express body-parser morgan multer hbs path config mongoose
```

安装包后，确保package.json文件的内容与以下代码片段相同：

```
###package.json
{
"name": "adobelegalcontractapi",
"version": "1.0.0",
"description": "",
"main": "index.js",
"directories": {
"test": "test"
},
"dependencies": {
"body-parser": "^1.19.0",
"config": "^3.3.6",
"express": "^4.17.1",
"hbs": "^4.1.1",
"mongoose": "^5.12.1",
"morgan": "^1.10.0",
"multer": "^1.4.2",
"path": "^0.12.7"
},
"devDependencies": {},
"scripts": {
"start": "node index.js"
},
"repository": {
"type": "git",
"url": "https://github.com/agavitalis/adobe_legal_contracts.git"
},
"author": "Ogbonna Vitalis",
"license": "ISC",
"bugs": {
"url": "https://github.com/agavitalis/adobe_legal_contracts/issues"
},
"homepage": "https://github.com/agavitalis/adobe_legal_contracts#readme"
}
```

在这些代码片段中，安装了应用程序依赖项，包括视图的手柄模板引擎。

本教程的主要重点是使用[[!DNL Acrobat Services] API](https://developer.adobe.com/document-services/homepage/)将文档转换为PDF。 因此，对于如何构建此Node.js应用程序，没有分步过程。 但是，您可以在[GitHub](https://github.com/agavitalis/adobe_legal_contracts.git)上检索完整的工作的Node.js应用程序代码。

## 将[!DNL Adobe Acrobat Services] API集成到Node.js应用程序中

[!DNL Adobe Acrobat Services]个API是基于云的可靠服务，旨在实现文档的无缝操作。 它提供三个API：

* Adobe PDF Services API

* Adobe PDF Embed API

* Document Generation APIAdobe

您需要凭据才能使用[!DNL Acrobat Services] API(不同于您的PDFEmbed API凭据)。 如果您没有有效的凭据，请[注册](https://www.adobe.com/go/dcsdks_credentials?ref=getStartedWithServicesSDK)并完成工作流程，如下面的屏幕捕获所示。 享受[免费的6个月试用期，然后即付即用](https://developer.adobe.com/document-services/pricing/main)，每次文档交易仅需0.05 USD。

![创建新凭据的屏幕截图](assets/legal_6.png)

注册过程完成后，代码示例将自动下载到您的PC以帮助您启动。 您可以提取此代码示例并按照说明操作。 不要忘记将解压缩的代码示例中的pdftools-api-credentials.json和private.key文件复制到Node.js项目的根目录中。 需要凭据才能访问[!DNL Acrobat Services] API端点。 您还可以下载包含个性化凭据的SDK示例，这样您就不必更新示例代码中的密钥。

现在，通过使用应用程序根目录中的终端运行```npm install \--save @adobe/documentservices-pdftools-node-sdk```命令来安装Adobe PDF Services Node SDK。 成功安装后，您可以使用[!DNL Acrobat Services] API来处理应用程序中的文档。

## 创建PDF文档

[!DNL Acrobat Services]个API支持从Microsoft OfficePDF（Word、Excel和PowerPoint）和其他[支持的文件格式](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf)（例如.txt、.rtf、.bmp、.jpeg、.gif、.tiff和.png）创建文档。 使用Acrobat Service API，您可以轻松地将法律合同从任何其他文件格式转换为PDF。

利用AdobeDocument Generation API，可以转换为Word文件或PDF。 例如，您可以使用Word模板生成合同，包括为文本添加红线以标记编辑后的文本。 然后，将其转换为PDF，并使用PDF服务API使用密码保护文档，发送以请求签名等。

若要从可用的受支持文件格式实现PDF文档的创建，可使用[!DNL Acrobat Services]上传要转换的文档。

设计的上传表单显示在下面的屏幕捕获中，您可以在[GitHub](https://github.com/agavitalis/adobe_legal_contracts.git)上访问HTML和CSS文件。

![表单上传的屏幕截图](assets/legal_7.png)

现在，将以下代码片段添加到控制器/createPDFController.js文件中。 此代码检索已上传的文档并将其转换为PDF。 [!DNL Acrobat Services]将原始上载的文件和转换后的文件保存在不同的文件夹中。

```
###controllers/createPDFController.js
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
const Document = require('../models/document');
/*
* GET / route to show the createPDF form.
*/
function createPDF(req, res) {
//catch any response on the url
let response = req.query.response
res.render('index', { response })
}
/*
* POST /createPDF to create a new PDF File.
*/
function createPDFPost(req, res) {
let filePath = req.file.path;
let fileName = req.file.filename;
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials and create a new operation instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
createPdfOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
createPdfOperation.execute(executionContext)
.then(async(result) => {
let newFileName = `createPDFFromDOCX-${Math.random() * 171}.pdf`
let newFilePath = require('path').resolve('./') + `\\output\\${newFileName}`
await result.saveAsFile(`views/output/${newFileName}`)
//Creates a new document
let newDocument = new Document({
documentName: newFileName,
url: newFilePath
});
//Save it into the DB.
newDocument.save((err, docs) => {
if (err) {
res.send(err);
}
else {
res.redirect('/?response=PDF Successfully created')
}
});
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation', err);
} else {
console.log('Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { createPDF, createPDFPost };
```

上述代码片段需要您之前安装的文档模型和[!DNL Acrobat Services]节点SDK。 有两个功能：

* createPDF显示上传文档表单。

* createPDFPost将上传的文档转换为PDF。

这些函数将转换后的PDF文档保存在views/output目录中，您可以在其中将这些文档下载到PC。

您也可以使用免费的PDFEmbed API预览经过转换的PDF文件。 使用PDF的嵌入式API，您可以在[此处](https://www.adobe.com/go/dcsdks_credentials)生成Adobe凭据（不同于您的[!DNL Acrobat Services]凭据）并注册允许的域以访问该API。 按照以下过程并为您的应用程序生成PDF的嵌入API凭据。 你还可以在[此处](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf)查看演示，从中可轻松地生成代码以快速入门。

返回到应用程序，在应用程序的view文件夹中创建list.hbs和preview.hbs文件，并将下面的代码片段分别粘贴到list.hbs和preview.hbs文件中。

```
###views/list.hbs
<!DOCTYPE html>
<html lang="en">
<head>
<title>Adobe Legal Contract</title>
<!-- Meta tags -->
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,
initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<!-- //Meta tags -->
<link
href=".min.css" rel="stylesheet" integrity="sha384-eOJMYsd53ii+scO/
bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6" crossorigin="anonymous">
<link rel="stylesheet" href="css/style.css" type="text/css"
media="all" /><!-- Style-CSS -->
<link href="css/font-awesome.css" rel="stylesheet" /><!--
font-awesome-icons -->
</head>
<body>
<section>
<div class="form-36-mian section-gap">
<div class="wrapper">
<div class="container">
<div class="row">
{{#each documents}}
<div class="col-md-4 mb-2">
<div class="card" style="width:
18rem;">
<img class="card-img-top"
src="./images/pdf.png"
alt="Card image cap">
<div class="card-body">
<h5
class="card-title">{{documentName}}</h5>
<a
href="/downloadPDF/{{_id}}" class="btn btn-primary"><i class="fa
fa-download" aria-hidden="true"></i> Download</a>
<a
href="/previewPDF/{{_id}}" class="btn btn-info"><i class="fa fa-eye"
aria-hidden="true"></i> Preview</a>
</div>
</div>
</div>
{{/each}}
</div>
</div>
<!-- copyright -->
<div class="copy-right">
<p>(c) 2021 Vitalis</p>
</div>
<!-- //copyright -->
</div>
</div>
</section>
</body>
</html>
###views/preview.hbs
<!DOCTYPE html>
<html lang="en">
<head>
<title>[!DNL Adobe Acrobat Services] PDF Embed API</title>
<meta charset="utf-8" />
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
<meta id="viewport" name="viewport" content="width=device-width,
initial-scale=1" />
</head>
<body style="margin: 0px">
<input type="hidden" id="pdfDocumentName"
value={{document.documentName}} />
<input type="hidden" id="pdfDocumentUrl" value={{document.url}} />
<div id="adobe-dc-view"></div>
<script
src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
let pdfDocumentName =
document.getElementById("pdfDocumentName").value;
let pdfDocumentUrl =
document.getElementById("pdfDocumentUrl").value;
document.addEventListener("adobe_dc_view_sdk.ready", function
() {
var adobeDCView = new AdobeDC.View({ clientId:
"XXXXXXXXXXXXXXXX", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url:
`http://localhost:5000/output/${pdfDocumentName}` } },
metaData: { fileName: pdfDocumentName }
}, {});
});
</script>
</body>
</html>
```

此外，创建一个controller/previewController.js文件并将下面的代码片段粘贴到该文件中。

```
const Document = require('../models/document');
/*
* GET /listFiles route to show PDF file lists.
*/
async function listFiles(req, res) {
let documents = await Document.find({});
res.render('lists', { documents })
}
/*
* GET /previewPDF route to show PDF file in AdobeEmbedAPI.
*/
async function previewPDF(req, res) {
//catch any response on the url
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.render('preview', { document })
}
/*
* GET /downloadPDF To Download PDF Documents.
*/
async function downloadPDF(req, res) {
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.download(document.url);
}
//export all the functions
module.exports = {listFiles, previewPDF, downloadPDF };
```

在上述控制器文件中，有三个功能：listFiles、previewPDF和downloadPDF。 listFiles函数列出了到目前为止使用[!DNL Acrobat Services] API生成的所有PDF文件。 previewPDF函数允许您使用PDFEmbed API预览PDF文件，而downloadPDF函数允许您将生成的PDF文件下载到您的电脑。 以下屏幕截图显示了使用PDFEmbed API的PDF预览示例。

![PDF预览的屏幕截图](assets/legal_8.png)

## 摘要

在本实际操作教程中，您使用Document Generation Tagger Microsoft Word插件为文档添加了标签。 然后，将[!DNL Acrobat Services]个API集成到Node.js应用程序中，并
将标记文档转换为可下载的PDF格式，但也可以将法律合同直接创建为PDF。 最后，您使用Adobe PDF Embed API预览生成的PDF以供验证和签名。

借助已完成的应用程序，可以更轻松地用动态字段标记[法律合同模板](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/legal-contracts)，将它们转换为PDF，预览它们，并使用[!DNL Acrobat Services] API对其进行签名。 您的团队可以自动将正确的合同发送给每个客户，而不是花时间创建独特的合同，然后花更多时间发展您的业务。

组织使用[!DNL Adobe Acrobat Services]个API来保证其完整性和易用性。 最棒的是，您可以享受[6个月的免费试用，然后按需付费](https://developer.adobe.com/document-services/pricing/main)。 您只需为使用的内容付费。 此外，PDFEmbed API始终免费。

是否准备好通过改进文档流提高工作效率？ 今天[开始使用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)。
