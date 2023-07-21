---
title: 管理销售提案和合同
description: 了解如何构建高效的工作流程以自动化和简化销售方案
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8099.jpg
jira: KT-8099
exl-id: 219c70de-fec1-4946-b10e-8ab5812562ef
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1395'
ht-degree: 2%

---

# 管理销售提案和合同

![用例英雄横幅](assets/UseCaseSalesHero.jpg)

销售提案是企业走向客户赢取的第一步。 和一切一样，第一印象是最后的。 因此，您与客户进行的首次互动确立了他们对您的业务的期望。 你的建议必须简洁、准确和方便。

合同和提案在其文件结构中包含不同类型的数据。 它们包含动态数据（客户名称、报价量等）和静态数据（样板文本，如固定能力、团队配置文件和标准SOW条款）。 创建模板文档（如销售提案）通常涉及单调乏味的任务，例如在样板模板中手动替换项目详细信息。 在本教程中，您将使用动态数据和工作流程为 [创建销售方案](https://www.adobe.io/apis/documentcloud/dcsdk/sales-proposals-and-contracts.html)的

## 您可以学到的内容

在本实际操作教程中，您将学习如何使用几种工具来实施动态数据和工作流，其中最重要的是 [!DNL Adobe Acrobat Services] API。 这些API用于为您和您的业务提供更为便利的销售提案和合同。 本教程演示了如何自动创建、合并和显示PDF文档的动手技巧。 手动执行这些任务非常耗时且繁琐。 利用 [!DNL Acrobat Services] 这样，您就可以缩短在这些任务上花费的时间。

## 相关API和资源

* [Microsoft Word](https://www.office.com/)

* [Node.js](https://nodejs.org/en/)

* [npm](https://www.npmjs.com/get-npm)

* [[!DNL Acrobat Services] API](https://www.adobe.io/apis/documentcloud/dcsdk/)

* [Adobe文档生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Adobe文档生成标记](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

## 解决问题

现在您已安装工具，您可以开始解决这个问题。 建议书既有静态内容，又有每个客户所独有的动态内容。 出现瓶颈是因为每次提交计划书时都需要这两种类型的数据。 输入静态文本非常耗时，因此您将对其进行自动化处理，并且仅手动处理来自每个客户端的动态数据。

首先，在中创建数据捕获表单 [Microsoft Forms](https://www.office.com/launch/forms?auth=1) （或者您首选的表单构建器）。 此表单用于添加到销售提案中的来自客户的动态数据。 填写此表格，提出问题以从客户那里获取所需的详细信息 — 例如，公司名称、日期、地址、项目范围、定价以及其他评论。 要构建您自己的版本，请使用 [表单](https://forms.office.com/Pages/ShareFormPage.aspx id=DQSIkWdsW0yxEjajBLZtrQAAAAAAAAAAAAAAAN__rtiGj5UNElTR0pCQ09ZNkJRUlowSjVQWDNYUEg2RC4u&amp;sharetken=1AJeMavBAzzxuISRKmUy)。 目标是让潜在客户填写表单，然后将其响应导出为JSON文件，这些文件将传递到工作流程的下一部分。

某些表单构建器仅允许您将数据导出为CSV文件。 所以你会发现 [转换](http://csvjson.com/csv2json) 将生成的CSV文件转换为JSON文件。

每个销售提案中都会重复使用静态数据。 因此，您可以在Microsoft Word中使用销售提案模板来提供静态文本。 你可以使用这个 [模板](https://1drv.ms/w/s!AiqaN2pp7giKkmhVu2_2pId9MiPa?e=oeqoQ2)，但您可以创建自己的字体或使用 [Adobe模板](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)的

现在，您需要一种工具，既可以接受JSON格式的客户端动态数据，又可以接受Microsoft Word模板中的静态文本，以便为客户端生成唯一的销售方案。 在 [!DNL Acrobat Services] API用于合并两者并生成可签名的PDF。

要使用标签，请使用标签。 标签是易于使用的字符串，可以表示数字、单词、数组，甚至复杂对象。 标签充当动态数据的占位符，这种情况下，动态数据是在表单中输入的客户端数据。 将标签插入模板后，您可以将表单字段从JSON文件映射到Word模板。

## 使用标记

打开销售方案模板并选择 **插入** 选项卡。 在 **加载项** 组，选择 **获取加载项**&#x200B;的 然后，选择 **Adobe文档生成加载项** 添加照片。 添加后，您会在 **主页** 选项卡 **Adobe** 组。

在 **主页** 选项卡 **Adobe** 组，选择 **文档生成** 开始为文档添加标签。 窗口右侧的面板中会显示一个有用的演示视频。

![Word中Document Tagger加载项的屏幕截图](assets/sales_1.png)

选择 **开始使用**&#x200B;的 然后会要求您提供示例数据。 如下所示粘贴或上传表单响应JSON文件。

![粘贴示例代码的屏幕截图](assets/sales_2.png)

选择 **生成标签** 从粘贴或上传的JSON文件中获取字段列表。 标签显示在右侧边栏的以下位置。

![可用标签的屏幕截图](assets/sales_3.png)

生成标签后，可以将它们插入到文档中。 标签将添加到文档中的光标位置。 如上所示，您应添加 **项目范围** 标记右侧的 **项目范围** 字幕。 这样，当客户在表单中输入项目的范围时，其响应会低于 **项目范围** 字幕，替换刚刚添加的标签。 添加完标签后，文档的一部分应如下面的屏幕截图所示。

![向Word文档添加标签的屏幕截图](assets/sales_4.png)

## 使用API

转到 [!DNL Acrobat Services] API [主页](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)的 开始使用 [!DNL Acrobat Services] API，您需要应用程序的凭据。 一直向下滚动并选择 **开始免费试用** 以创建凭据。 您可以使用这些服务 [免费延期六个月，然后按使用量付费](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 每个文档交易只需0.05美元，因此您只需支付所需的费用。

选择 **PDF服务API** 并填写其他详细信息，如下所示。

![获取凭据的屏幕截图](assets/sales_5.png)

创建凭据后，您将获得一些代码示例。 选择首选语言（本教程使用Node.js）。 您的API凭据位于zip文件中。 将文件解压缩到PDFToolsSDK-Node.jsSamples。

首先，创建一个名为auto-doc\*\*的空文件夹。\*\*在该文件夹中，运行以下命令以初始化Node.js项目： `npm init`的 将您的项目命名为“auto-doc”*的*

文件夹。/PDFToolsSDK-Node.jsSamples/adobe-dc-pdf-tools-sdk-node-samples中，有一个名为pdftools-api-credentials.json的文件。 将其和private.key移至auto-doc文件夹。 它包含您的API凭据。 此外，在自动文档文件夹中，创建一个名为“resources”的子文件夹。 每当生成销售提案时，它都会保存从客户端接收的JSON格式数据。 在同一文件夹中，保存来自Microsoft Word的销售方案模板。

现在你准备好变魔术了！ 由于您在本教程中使用Node.js，因此必须安装Node.js [!DNL Acrobat Services] SDK。 为此，请在auto-doc文件夹中，运行hearn add @adobe/documentservices-pdftools-node-sdk。

现在，创建一个名为merge.js的文件，并将以下代码粘贴到该文件中。

```
javascript
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk'),
fs = require('fs');
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Setup input data for the document merge process
const jsonString = fs.readFileSync('resources/Proposal.json'),
jsonDataForMerge = JSON.parse(jsonString);
// Create an ExecutionContext using credentials
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
// Create a new DocumentMerge options instance
const documentMerge = PDFToolsSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);
// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)
// Set operation input document template from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile('resources/Proposal.docx');
documentMergeOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile('output/Proposal.pdf'))
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
```

此代码借助您使用创建的标记从Microsoft表单获取您的JSON文件 [!DNL Acrobat Services]的 然后，它将数据与您在Microsoft Word中创建的销售提案模板合并，以生成一个全新的PDF。 该PDF将保存在新创建的文件夹中。/output文件夹。

此外，代码使用 [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html) 让两家公司签下产生的销售提议。 有关此API的详细说明，请参阅此博客文章。

## 后续步骤

您一开始就是一个需要自动化的低效、乏味的流程。 您从手动为每个客户创建文档开始，到创建简化的工作流程以实现自动化和简化 [销售提案流程](https://www.adobe.io/apis/documentcloud/dcsdk/sales-proposals-and-contracts.html)的

使用Microsoft Forms，您可以获得客户的关键数据，这些数据将包含在其独特的计划书中。 您在Microsoft Word中创建了销售方案模板，以提供您每次都不想重新创建的静态文本。 然后你使用 [!DNL Acrobat Services] API，用于合并表单和模板中的数据，以便更有效地为客户创建销售方案PDF。

本实际操作教程仅简要介绍使用这些API能够做到些什么。 要发现更多解决方案，请访问 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) API页面。 您可以免费使用这些工具六个月。 然后，只需支付0.05 USD的每文档交易费 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 计划，因此您只有在您的团队将更多潜在客户添加到您的销售渠道时才会付款。
