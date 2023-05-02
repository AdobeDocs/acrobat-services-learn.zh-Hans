---
title: 创建NDA
description: 了解如何为协作创建动态NDAPDF
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8098.jpg
kt: 8098
exl-id: f4ec0182-a46e-43aa-aea3-bf1d19f1a4ec
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1164'
ht-degree: 3%

---

# 创建NDA

![用例英雄横幅](assets/UseCaseNDAHero.jpg)

组织与外部投稿人协作以构建其服务和产品。 保密协议(NDA)是这些协作的重要部分。 它使所有当事方不得发布可能损害任何实体的机密信息。

最广泛使用的NDA格式是PDF文档。 组织准备保密协议并将其发送给所有各方。 然后，一旦每个人都签署了合同，他们就会启动合同。 在高速度团队中，手动创建PDF会降低进度。

## 您可以学到的内容

本实际操作教程介绍了如何为您的公司创建专门的Microsoft Word NDA模板。 AdobeMicrosoft Word的免费插件， [Adobe文档生成标记](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)，插入“标签”以输入动态值。 了解如何将JSON数据传递给模板并创建动态PDF。 生成的PDF可通过电子邮件发送，或在协作者的浏览器中显示给协作者，具体取决于您的业务要求和目标。 要继续操作，您只需使用Node.js、JavaScript、Express.js、HTML和CSS获得一点经验。

## 相关API和资源

使用 [!DNL Adobe Acrobat Services]，您可以PDF使用动态数据快速生成文档。 [!DNL Acrobat Services] 提供了一套PDF工具，包括AdobeDocument Generation API以实现自动化 [创建NDA](https://www.adobe.io/apis/documentcloud/dcsdk/nda-creation.html)的

* [Adobe文档生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Adobe文档生成标记](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

* [项目代码](https://github.com/afzaal-ahmad-zeeshan/adobe-docugen-sample)

* [[!DNL Acrobat Services] 键](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getcred)

## 创建JSON模型

Microsoft Word模板依赖于JSON模型，因此您首先创建该模板。 在本教程中，您将使用包含公司详细信息（例如联系信息）的基本JSON结构。

```
{
"vendor": {
"companyName": "GlobalCorp",
"street": "123 Any Street",
"street2": "",
"city":"Anywhere",
"state":"CA",
"primaryContact": {
"firstName":"John",
"lastName":"Doe",
"email":"john-doe@example.com",
"phone":"123-456-7890"
}
},
"authorizedSigner": {
"firstName": "Sarah",
"lastName": "Rose",
"email": "sarah@example.com",
"phone":"555-555-1234"
}
}
```

您可以在Microsoft Word中使用此结构来生成模板。 这些数据可以来自任何数据源（只要其格式为JSON格式）。 为简单起见，您可以在Node.js应用程序内创建多个文件，但用例可能需要数据库连接才能提取供应商信息。

## 创建Microsoft Word模板

在Microsoft Word文档中创建NDA模板。 Adobe PDF Services API需要Microsoft Word文档包含标记，服务可以在这些标记中插入JSON文档中的值。 尽管所有请求的Adobe都是相同的，但JSON中的动态数据会更改。 在这种情况下，这些标签有助于使用单个Microsoft Word模板为每个供应商创建PDF文档，并通过自动生成NDA文档来加快该过程。

您可以安装 [free Document Generation Tagger加载项](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo) 到Microsoft Word。 如果您是组织的一部分，则可以请求您的Microsoft Office管理员安装适用于所有人的免费插件。

安装此加载项后，您可以在“主页”选项卡中的“Adobe”类别下找到它。 要打开选项卡，请选择 **文档生成**:

![Word文档生成加载项的屏幕截图](assets/nda_1.png)

在该选项卡中，您可以上传示例JSON文档。 此文档可以是一个示例，因为您仅使用它来创建Microsoft Word模板。

![Document Generation加载项中示例数据的屏幕截图](assets/nda_2.png)

选择 **生成标签** 查看可在模板中使用的项目。 以下是从JSON结构提取的可在模板中使用的属性：

![文档生成加载项中的文本标记的屏幕截图](assets/nda_3.png)

以下是 `authorizedSigner` 字段。 其他字段将被封包，您可以在Microsoft Word中展开视图。 该加载项还提供了高级数据选项，例如表格、列表、计算值等。

## 创建标签

您可以任意创建模板或导入 [现有模板](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) 到Microsoft Word中。 设置文档后，可通过单击加载项中的相应令牌向每个字段添加标签。

Microsoft Word文件中的以下模板：

![示例模板的屏幕截图](assets/nda_4.png)

此文件包含多个标签。 运行程序时，这些字段将填充供应商信息。

Document Generation Tagger与Adobe Sign API集成。 由于此集成，您可以自动创建Sign文本标记，以便随后将生成的文档发送到Adobe Sign进行签名。

## 为供应商生成NDA

在范例应用程序中，为输入和输出准备文件夹。 如前所述，您使用JSON文件，因此有两个文件可显示系统中的可用供应商。 文件显示在浏览器上打印的表单中：

```
<h1><b>NDA</b>: Generate for vendor.</h1>
<hr />
<p>Following ({{files.length}}) vendors are ready, select to generate NDA and deliver for signature:</p>
<form method="POST">
<ul>
{{#each files }}
<li><input type="checkbox" name="vendor" value="{{this}}" id="file-{{@index}}" /> <label for="file-{{@index}}">{{this}}</label></li>
{{/each}}
</ul>
<input type="submit" value="Create NDA" />
</form>
```

此代码在浏览器中生成以下用户界面(UI):

![创建NDA用户界面的屏幕截图](assets/nda_5.png)

管理员选择人员后，应用程序会使用Adobe PDF Services随时随地生成保密协议。

```
async function compileDocFile(json, inputFile, outputPdf) {
try {
// configurations
const credentials = adobe.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("./src/pdftools-api-credentials.json")
.build();
// Capture the credential from app and show create the context
const executionContext = adobe.ExecutionContext.create(credentials);
// create the operation
const documentMerge = adobe.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(json, documentMergeOptions.OutputFormat.PDF);
const operation = documentMerge.Operation.createNew(options);
// Pass the content as input (stream)
const input = adobe.FileRef.createFromLocalFile(inputFile);
operation.setInput(input);
// Async create the PDF
let result = await operation.execute(executionContext);
await result.saveAsFile(outputPdf);
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

在Express路由器中使用以下代码：

```
// Create one report and send it back
try {
console.log(`[INFO] generating the report...`);
const fileContent = fs.readFileSync(`./public/documents/raw/${vendor}`, 'utf-8');
const parsedObject = JSON.parse(fileContent);
await pdf.compileDocFile(parsedObject, `./public/documents/template/Adobe-NDA-Sample.docx`, `./public/documents/processed/output.pdf`);
console.log(`[INFO] sending the report...`);
res.status(200).render("preview", { page: 'nda', filename: 'output.pdf' });
} catch(error) {
console.log(`[ERROR] ${JSON.stringify(error)}`);
res.status(500).render("crash", { error: error });
}
```

您可以查看 [完整的示例代码](https://github.com/afzaal-ahmad-zeeshan/adobe-docugen-sample) 在GitHub上。

此代码使用JSON文档和Microsoft Word模板进行API调用 [!DNL Adobe Acrobat Services] SDK。 在响应中，您将收到输出并将其保存到应用程序的文件系统中。 您可以通过电子邮件将生成的文档转发给客户，或者使用免费的 [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html)的

此调用将创建以下NDA文档：

![NDA文档预览的屏幕截图](assets/nda_6.png)

[!DNL Adobe Acrobat Services] API插入内容以创建PDF文档。 如果没有这些工具，您可能需要编写代码来处理Office文档和处理原始PDF文件格式。 借助Adobe PDF Services，您只需调用一个API，即可完成所有这些步骤。

现在使用 [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html) 在NDA上请求签名，并将最终签名文档提交给所有各方。 Adobe Sign通知您 [使用Webhook](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/webhooks.md)的 侦听此Webhook，您可以获取NDA的状态。

要更深入地解释Adobe Sign进程， [请参阅相关文档](https://www.adobe.io/apis/documentcloud/sign/docs.html) 或阅读这篇深入的博客文章。

## 后续步骤

在本实操教程中，Adobe文档生成标记用于使用Microsoft Word模板和JSON数据文件动态生成PDF文档。 此加载项有助于 [自动创建NDA](https://www.adobe.io/apis/documentcloud/dcsdk/nda-creation.html) 为每个参与方自定义签名，然后使用Sign API收集签名。

您可以使用这些技术动态创建您自己的NDA或其他文档，让您的团队有时间专注于高效的工作。 浏览 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html) 查找您选择的语言和运行时的API和SDK，以便您可以将PDF函数直接添加到应用程序中以快速创建PDF文档。 [开始使用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 六个月的免费试用
[即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 每个文档事务只需0.05美元。
