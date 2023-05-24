---
title: 搜索和索引
description: 了解如何通过扫描的PDF创建可搜索的文档文件
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8095.jpg
kt: 8095
exl-id: a22230b5-1ff2-4870-84da-f06a904c99e1
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1364'
ht-degree: 1%

---

# 搜索和索引

![用例英雄横幅](assets/UseCaseSearchingHero.jpg)

组织必须经常将其硬拷贝文档和扫描的文件数字化。 考虑一下 [情景](https://docs.google.com/document/d/11jZdVQAw-3fyE3Y-sIqFFTlZ4m02LsCC/edit)的 一家律所拥有数千份法律合同，他们通过扫描来创建数字文件。 它们要确定这些法律合同中是否有任何一项有它们必须修订的特定条款或补充条款。 准确性对于实现合规性是必要的。 解决方案是清点数字文档，使文本可搜索，并创建索引来查找此信息。

创建数字归档以检索信息以进行编辑或下游操作这一难题对于大多数组织而言都是一场噩梦。

## 您可以学到的内容

此实际操作教程探索了 [!DNL Adobe Acrobat Services] API的功能，并且可以方便地用于归档文档和数字化文档。 您可以通过构建Express NodeJS应用程序，然后集成来探索这些功能 [!DNL Acrobat Services] 用于归档、数字化和文档转换的API。

要跟进，您需要 [Node.js](https://nodejs.org/) 安装并基本了解Node.js和 [ES6语法](https://www.w3schools.com/js/js_es6.asp)的

## 相关API和资源

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [项目代码](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)

## 项目设置

首先，设置应用程序的文件夹结构。 您可以检索源代码 [此处](https://github.com/agavitalis/AdobeDocumentAPI.git)的

## 目录结构

创建一个名为AdobeDocumentServicesAPI的文件夹，并在您选择的编辑器中打开它。 使用创建基本NodeJS应用程序 `npm init` 命令：

```
AdobeDocumentServicesAPIs
config
default.json
controllers
createPDFController.js
makeOCRController.js
searchController.js
models
document.js
output
.gitkeep
routes
web.js
services
upload.js
views
index.hbs
ocr.hbs
search.hbs
index.js
```

您正在将MongoDB用作此应用程序的数据库。 因此，要进行配置，请将您的默认数据库配置放在config/文件夹中，方法是将下面的代码片段粘贴到此文件夹的default.json文件中，然后添加数据库的URL。

```
### config/default.json and config/dev.json
{ "DBHost": "YOUR_DB_URI" }
```

## 包安装

现在，使用npm install命令安装某些包，如下面的代码片断所示：

```
{
    "name": "adobedocumentservicesapis",
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
    "start": "set NODE_ENV=dev && node index.js"
    },
    "repository": {
    "type": "git",
    "url": "git+https://github.com/agavitalis/AdobeDocumentServicesAPIs.git"
    },
    "author": "Ogbonna Vitalis",
    "license": "ISC",
    "bugs": {
    "url": "https://github.com/agavitalis/AdobeDocumentServicesAPIs/issues"
    },
    "homepage": "https://github.com/agavitalis/AdobeDocumentServicesAPIs#readme"
}
```

```
###bash
npm install express mongoose config body-parser morgan multer hbs path pdf-parse
Ensure that the content of your package.json file is similar to this code snippet:
###package.json
{
```

这些代码片断安装应用程序的依赖项，包括视图的Handlebars模板引擎。 在scripts标签中，配置应用程序的运行时参数。

## 集成 [!DNL Acrobat Services] API

[!DNL Acrobat Services] 包括三个API:

* Adobe PDF Services API

* Adobe PDF Embed API

* Adobe文档生成API

这些API通过一组基于PDF的Web服务来自动生成、处理和转换云内容。

要获取所需的凭据，请执行以下操作： [注册](https://www.adobe.com/go/dcsdks_credentials?ref=getStartedWithServicesSDK) 并完成工作流。 PDF嵌入API可免费使用。 PDF服务API和文档生成API免费延期6个月。 试用结束后，您可以 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 每个文档交易只需0.05美元。 只有在公司发展并处理更多合同时，您才能付款。

![创建凭据的屏幕截图](assets/searching_1.png)

完成注册后，一个代码示例将下载到您的PC，其中包含您的API凭据。 提取此代码示例，并将private.key和pdftools-api-credentials.json文件放在应用程序的根目录中。

现在，安装 [PDF服务Node.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk) 通过运行 ` npm install --save @adobe/documentservices-pdftools-node-sdk ` 使用应用程序根目录中的终端执行的命令。

## 创建PDF

[!DNL Acrobat Services] 支持从Microsoft Office文档（Word、Excel和PowerPoint）和其他 [支持的文件格式](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) 例如.txt、.rtf、.bmp、.jpg、.gif、.tiff和.png。

要基于受支持的PDF格式创建文档文件，请使用此表单上传文档。 您可以访问HTML和CSS文件，表单位于 [GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)的

![Web表单的屏幕截图](assets/searching_2.png)

现在，将以下代码片段添加到controllers/createPDFController.js文件。 此代码检索文档并将其转换为PDF。

原始文件和转换后的文件保存在应用程序内的文件夹中。

```
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
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
// Create an ExecutionContext using credentials and create a new operation
instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
createPdfOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
createPdfOperation.execute(executionContext)
.then((result) => {
result.saveAsFile('output/createPDFFromDOCX.pdf')
//download the file
res.redirect('/?response=PDF Successfully created')
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation',
err);
} else {
console.log('Exception encountered while executing operation',
err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { createPDF, createPDFPost };
```

此代码片段需要 [PDF服务Node.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk)的 使用以下函数：

* createPDF，其中显示上载文档表单

* createPDFPost，将上载的文档转换为PDF

转换后的PDF文档保存在输出目录中，而原始文件保存在上传目录中。

## 使用文本识别

光学字符识别(OCR)将图像和扫描的文档转换为可搜索的文件。 您可以将 [!DNL Acrobat Services] 将API、图像和扫描的文档转换为可搜索的PDF。 执行OCR操作后，文件将变为可编辑且可搜索。 可以将文件内容存储在数据存储中，以便编制索引和进行其他使用。

请记住，搜索和索引扫描的文档对于许多组织而言都至关重要，在这些组织中，文件管理和信息处理至关重要。 OCR功能可消除这些挑战。

要实现此功能，您必须设计一个与上面内容类似的上传表单。 这一次，您可以将表单限制为PDF文件，因为您只能对PDF文档使用OCR功能。

以下是此示例的上传表单：

![用于上传文件的表单的屏幕截图](assets/searching_3.png)

现在，要处理上载的PDF并执行某些OCR操作，请将下面的代码片段添加到controllers/makeOCRController.js文件中。 此代码对上传的文件实现OCR过程，然后将文件保存到应用程序的文件系统中。

```
const fs = require('fs')
const pdf = require('pdf-parse');
const mongoose = require('mongoose');
const Document = require('../models/document');
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
/*
* GET /makeOCR route to show the makeOCR form.
*/
function makeOCR(req, res) {
//catch any response on the url
let response = req.query.response
res.render('ocr', { response })
}
/*
* POST /makeOCRPost to create a new PDF File.
*/
function makeOCRPost(req, res) {
let filePath = req.file.path;
let fileName = req.file.filename;
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials and create a new operation
instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
ocrOperation = PDFToolsSdk.OCR.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
ocrOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
ocrOperation.execute(executionContext)
.then(async (result) => {
let newFileName = `createPDFFromDOCX-${Math.random() * 171}.pdf`;
await result.saveAsFile(`output/${newFileName}`);
let documentContent = fs.readFileSync(
require("path").resolve("./") + `\\output\\${newFileName}`
);
pdf(documentContent)
.then(function (data) {
//Creates a new document
var newDocument = new Document({
documentName: fileName,
documentDescription: description,
documentContent: data.text,
url: require("path").resolve("./") + `\\output\\${newFileName}`
});
//Save it into the DB.
newDocument.save((err, docs) => {
if (err) {
res.send(err);
} else {
//If no errors, send it back to the client
res.redirect(
"/makeOCR?response=OCR Operation Successfully performed on
the PDF File"
);
}
});
})
.catch(function (error) {
// handle exceptions
console.log(error);
});
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation',
err);
} else {
console.log('Exception encountered while executing operation',
err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { makeOCR, makeOCRPost };
```

您需要 [!DNL Acrobat Services] 节点SDK和mongoose、pdf-parse和fs模块以及您的文档模型架构。 这些模块是将转换文件的内容保存到MongoDB数据库所必需的。

现在创建两个函数：makeOCR可显示上传的表单，然后使用makeOCRost处理上传的文档。 将原始表单保存到数据库，然后将转换后的表单保存到应用程序的输出文件夹。

在转换Adobe之前，将在每个案例中加载pdftools-api-credentials.json文件中提供的凭据。

>[!NOTE]
>
>OCR功能仅支持PDF文档。

此外，请将下面的代码片段添加到应用程序的Modes/Document.js文件中。

在代码片断中，定义一个mongoose模型，然后描述要保存在数据库中的文档属性。 此外，为documentContent字段编制索引可使搜索文本变得简单而高效。

```
const mongoose = require("mongoose");
const Schema = mongoose.Schema;
//Document schema definition
var DocumentSchema = new Schema(
{
documentName: { type: String, required: false },
documentDescription: { type: String, required: false },
documentContent: { type: String, required: false },
url: { type: String, required: false },
status: {
type: String,
enum : ["active","inactive"],
default: "active"
}
},
{ timestamps: true }
);
//for text search
DocumentSchema.index({
documentContent: "text",
});
//Exports the DocumentSchema for use elsewhere.
module.exports = mongoose.model("document", DocumentSchema);
```

## 搜索文本

现在，您实施了一项简单的搜索功能，使用户能够执行一些简单的文本搜索。 您还可以添加下载功能来下载PDF文件。

此功能需要一个简单的表单和卡来显示搜索结果。 您可以在 [GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)的

以下屏幕截图说明了搜索功能和搜索结果。 您可以下载任何搜索结果。

![搜索功能的屏幕截图](assets/searching_4.png)

若要实现搜索功能，请在应用程序的controller文件夹中创建searchController.js文件，并粘贴以下代码片段：

```
const fs = require('fs')
const mongoose = require('mongoose');
const Document = require('../models/document');
/*
* GET / route to show the search form.
*/
function search(req, res) {
//catch any response on the url
let response = req.query.response
res.render('search', { response })
}
/*
* POST /searchPost to search the contents of your saved file.
*/
function searchPost(req, res) {
let searchString = req.body.searchString;
Document.aggregate([
{ $match: { $text: { $search: searchString } } },
{ $sort: { score: { $meta: "textScore" } } },
])
.then(function (documents) {
res.render('search', { documents })
})
.catch(function (error) {
let response = error
res.render('search', { response })
});
}
//export all the functions
module.exports = { search, searchPost, downloadPDF };
```

现在实施下载功能，以便下载从用户的搜索中返回的文档。

## 下载文档

实施下载功能与您已完成的操作类似。 在controllers/earchController.js文件中的searchPost函数之后添加以下代码片段：

```
/*
* POST /downloadPDF To Download PDF Documents.
*/
async function downloadPDF(req, res) {
console.log("here")
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.download(download.link);
}
```

## 后续步骤

在本实际操作教程中，您将 [!DNL Acrobat Services] API转换为Node.js应用程序，还使用API实现文档转换，以将文件转换为PDF。 您添加了OCR功能，使图片和扫描的文件可搜索。 然后，您将文件保存到文件夹，以便可以下载它们。

接下来，您添加了一项搜索功能，用于搜索通过OCR转换为文本的文档。 最后，您实现了下载功能，以便轻松下载这些文件。 完整的应用程序使法律公司可以更轻松地查找和处理特定文本。

使用 [!DNL Acrobat Services] 强烈建议使用for document transformation，因为与其他服务相比，它具有强大的功能且易于使用。 您可以快速创建帐户以开始享受 [!DNL Acrobat Services] 用于文档转换和管理的API。

现在您已对如何使用 [!DNL Acrobat Services] API，您可以通过练习来提升技能。 您可以克隆本教程中使用的存储库，并尝试学习一些刚刚学到的技能。 更妙的是，您可以尝试重建此应用程序，同时探索 [!DNL Acrobat Services] API。

是否准备好在自己的应用程序中启用文档共享和审阅？ 注册您的 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
开发人员帐户。 享受六个月的免费试用，然后 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 随着业务的增长，每个文档交易只需\$0.05。
