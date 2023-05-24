---
title: 创建和编辑报告
description: 了解如何在您的PDF网站上为客户生成客户报告
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8093.jpg
kt: 8093
exl-id: 2f2bf1c2-1b33-4eee-9fd2-5d0b77e6b0a9
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1346'
ht-degree: 1%

---

# 创建和编辑报告

![用例英雄横幅](assets/UseCaseReportHero.jpg)

金融、教育、营销和其他行业使用PDF与客户和利益相关者共享数据。 PDF使您能够以所有人都可以查看的格式轻松共享包含表格、图形和交互式内容的丰富文档。 [!DNL Adobe Acrobat Services] API可帮助这些公司通过Microsoft Word、Microsoft Excel、图形和其他各种文档格式生成可共享的PDF报告。

说你 [经营一家社交媒体追踪公司](https://www.adobe.io/apis/documentcloud/dcsdk/on-demand-report-creation.html)的 您的客户登录您的网站中受密码保护的部分，以查看其营销活动分析。 通常，他们想与高管、股东、捐赠者或其他利益相关者分享这些统计数据。 可下载PDF文档是客户共享数字、图形等内容的绝佳方式。

通过合并 [PDF服务API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html) 您可以随时随地为每个PDF生成客户报告。 您可以创建PDF，然后将它们合并为单个方便的报告供客户下载并传递给利益相关者。

## 您可以学到的内容

在本实际操作教程中，学习如何在Node.js和Express.js环境(只包含一些JavaScript、HTML和CSS)中使用PDF服务SDK，以便快速轻松地向现有网站添加面向PDF的功能。 此网站具有一个供管理员上传报告的页面、一个供客户查看可用报告列表并选择要转换为PDF的文档的区域，以及一个用于下载系统生成的PDF的有用端点。

## 相关API和资源

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF嵌入API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

## 面向客户的营销活动报告仪表板

>[!NOTE]
>
>本教程与Node.js最佳实践或如何保护Web应用程序无关。 该网站的某些区域会公开供公众使用，而且文档命名可能不利于生产。 要讨论设计此类系统的最佳可能方法，请咨询您的架构师和工程师。

在这里，您有一个基本Express.js Web应用程序，它有一个客户报告区和管理员区。 此应用程序可以展示社交媒体营销活动的报告。 例如，它可以显示单击广告的次数。

![获取个性化报告的屏幕截图](assets/report_1.png)

您可以从 [GitHub存储库](https://github.com/afzaal-ahmad-zeeshan/express-adobe-pdf-tools)的

现在，让我们了解如何发布报告。

## 上传报告

为了保持简单，请在此仅使用基于文件系统的上传和处理。 在Express.js中，可以使用fs模块列出目录下的所有可用文件。

在同一页面上，允许管理员将报告文件上传到服务器供客户查看。 这些文件可以采用许多不同的格式，如Microsoft Word、Microsoft Excel、HTML和 [其他数据格式]https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf)，包括图形文件。 管理员页面如下所示：

![管理员功能的屏幕截图](assets/report_2.png)

>[!NOTE]
>
>使用密码保护您的URL，或使用npm提供的passport包保护身份验证和授权层后的应用程序。

管理员选择并上传文件时，该文件将移至公共存储库，其他人可访问该存储库。 您使用同一个存储库从管理员页面发布文档，并列出客户可用的营销报告。 此代码为：

```
router.get('/', (req, res) => {
try {
let files = fs.readdirSync('./public/documents/raw') // read the files
res.status(200).render("reports", { page: 'reports', files: files });
} catch (error) {
res.status(500).render("crash", { error: error });
}
});
```

此代码列出所有文件并呈现文件列表的视图。

## 选择报告

在用户端，您有一个表单供客户选择他们要在社交媒体营销活动报告中包含的文档。 为简单起见，在示例页面上，仅显示文档名称和一个用于选择文档的复选框。 客户可以选择单个或多个报告以合并到一个PDF文档中。

如需更高级的用户界面，您也可以在此处显示报告的预览。

![客户功能屏幕截图](assets/report_3.png)

## 生成PDF报告

使用PDF服务SDK根据数据输入创建PDF报告。 数据（如上面的屏幕截图所示）可以来自各种数据格式，如Microsoft Word、Microsoft Excel、HTML、图形等。 首先为PDF服务SDK安装npm包。

```
$ npm install --save @adobe/documentservices-pdftools-node-sdk
```

开始之前，您必须拥有API凭据， [免费Adobe](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getcred)的 使用您的 [!DNL Acrobat Services] 帐户 [免费延期六个月，然后按使用付费](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 每个文档事务只需\$0.05。

下载存档文件并提取JSON文件以获得凭据和私钥。 在示例项目中，将该文件放在src目录中。

![src目录的屏幕截图](assets/report_4.png)

现在，您设置了凭据，可以编写PDF转换任务。 对于此演示，您必须在应用程序中执行两个操作：

* 将原始文档转换为PDF文件

* 将多个PDF文件合并到一个报告中

整体过程与运行任何操作相似。 唯一的区别就是您使用的服务。 在以下代码中，您将原始文档转换为PDF文件：

```
async function createPdf(rawFile, outputPdf) {
try {
// configurations
const credentials = adobe.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("./src/pdftools-api-credentials.json")
.build();
// Capture the credential from app and show create the context
const executionContext = adobe.ExecutionContext.create(credentials),
operation = adobe.CreatePDF.Operation.createNew();
// Pass the content as input (stream)
const input = adobe.FileRef.createFromLocalFile(rawFile);
operation.setInput(input);
// Async create the PDF
let result = await operation.execute(executionContext);
await result.saveAsFile(outputPdf);
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

在上面的代码中，读取凭据并创建执行上下文。 PDF服务SDK需要执行上下文来对您的请求进行身份验证。

然后，运行创建PDF操作，将原始文档转换为PDF格式。 最后，使用 `outputPdf` 参数来复制PDF报告。 在代码示例中，可在src/helpers/pdf.js文件下找到此代码。 在本教程的稍后部分，您将导入PDF模块并调用此方法。

如上一节所示，您的客户可以转到以下页面以选择要转换为PDF的报告：

![客户功能屏幕截图](assets/report_3.png)

当客户选择一个或多个此类报告时，您需要创建PDF文件。

首先，我们来看一个正在运行的PDF文件。 当用户选择单个报告时，您只需将其转换为PDF并提供下载链接。

```
try {
console.log(`[INFO] generating the report...`);
await pdf.createPdf(`./public/documents/raw/${reports}`, `./public/documents/processed/output.pdf`);
console.log(`[INFO] sending the report...`);
res.status(200).render("download", { page: 'reports', filename: 'output.pdf' });
} catch(error) {
console.log(`[ERROR] ${JSON.stringify(error)}`);
res.status(500).render("crash", { error: error });
}
```

此代码创建一个报告并与客户共享下载URL。 以下是输出网页：

![客户下载屏幕的屏幕截图](assets/report_5.png)

以下是输出PDF:

![通用报告的屏幕截图](assets/report_6.png)

客户可以选择多个文件以生成合并报告。 当客户选择多个文档时，您可以执行两项操作：第一个文档创建部分PDF，第二个文档将它们合并为一个PDF报告。

```
async function combinePdf(pdfs, outputPdf) {
try {
// configurations
const credentials = adobe.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("./src/pdftools-api-credentials.json")
.build();
// Capture the credential from app and show create the context
const executionContext = adobe.ExecutionContext.create(credentials),
operation = adobe.CombineFiles.Operation.createNew();
// Pass the PDF content as input (stream)
for (let pdf of pdfs) {
const source = adobe.FileRef.createFromLocalFile(pdf);
operation.addInput(source);
}
// Async create the PDF
let result = await operation.execute(executionContext);
await result.saveAsFile(outputPdf);
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

此方法位于src/helpers/pdf.js文件下，并作为模块导出的一部分公开。

```
try {
console.log(`[INFO] creating a batch report...`);
// Create a batch report and send it back
let partials = [];
for (let index in reports) {
const name = `partial-${index}-${reports[index]}`;
await pdf.createPdf(`./public/documents/raw/${reports[index]}`, `./public/documents/processed/${name}`);
partials.push(`./public/documents/processed/${name.replace('docx', 'pdf').replace('xlsx', 'pdf')}`);
}
await pdf.combinePdf(partials, `./public/documents/processed/output.pdf`);
console.log(`[INFO] sending the combined report...`);
res.status(200).render("download", { page: 'reports', filename: 'output.pdf' });
} catch(error) {
console.log(`[ERROR] ${JSON.stringify(error)}`);
res.status(500).render("crash", { error: error });
}
```

此代码为多个输入文档生成编译的报告。 唯一添加的功能是 `combinePdf` 该方法采用一列PDF文件路径名并返回单个输出PDF。

现在，您的社交媒体仪表板客户可以从帐户中选择相关报告，并将其作为一个方便的PDF下载。 通过此信息板，他们可以以一种通用且易于打开的格式显示管理和其他利益相关者，以及他们的营销活动在数据、表和图表方面的成功情况。

## 后续步骤

此动手教程介绍了如何使用PDF服务API帮助客户将相关报告下载为易于共享的PDF。 您创建了一个Node.js应用程序，以展示用于PDF报告和读取服务的PDF服务API的强大功能。 该应用程序演示了客户如何下载单个报告文档或将多个文档合并为一个单独的PDF报告。

此Adobe支持的应用程序可帮助 [社交媒体仪表板客户](https://www.adobe.io/apis/documentcloud/dcsdk/on-demand-report-creation.html) 获取和共享他们需要的报表，而不必担心收件人的设备上是否安装了Microsoft Office或其他软件。 您可以在自己的应用程序中使用相同的方法，帮助用户查看、合并和下载文档。 或者，查看Adobe的许多其他API以添加和跟踪签名等。

要开始使用，请申请免费服务 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 为员工和客户创建引人入胜的报告体验。 届时6个月可免费享用您的帐户 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 随着营销活动的扩展，每个文档交易只需\$0.05。
