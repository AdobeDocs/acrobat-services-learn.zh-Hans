---
title: 员工入职现代化
description: 了解如何利用以下工具实现员工入职现代化： [!DNL Adobe Acrobat Services] API
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-10203.jpg
kt: 10203
exl-id: 0186b3ee-4915-4edd-8c05-1cbf65648239
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1514'
ht-degree: 1%

---

# 员工入职现代化

![用例英雄横幅](assets/usecaseemployeeonboardinghero.jpg)

在大型组织中，员工入职可能是一个大而缓慢的过程。 通常，其中包含多种自定义文档以及样板材料，这些材料必须由新员工提供并签名。 这种定制材料和样板材料的混合需要多个步骤 — 从参与流程的人员那里花费宝贵的时间。 [!DNL Adobe Acrobat Services] 而Acrobat Sign可以将此方法现代化和自动化，让您的人力资源人员腾出时间处理更重要的任务。 我们来看看它是如何实现的。

## 您可以 [!DNL Adobe Acrobat Services]?

[[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/homepage) 是一组与处理文档(而不仅仅是PDF)相关的API。 概括地说，这一套服务可分为三大类：

* 首先是 [PDF服务](https://developer.adobe.com/document-services/apis/pdf-services/) 工具集。 这些是处理PDF和其他文档的“实用”方法。 这些服务包括转入PDF、执行OCR和优化、合并和拆分PDF等。 这是文档处理功能的工具箱。
* [PDF提取API](https://developer.adobe.com/document-services/apis/pdf-extract/) 使用强大的AI/ML技术分析PDF并返回有关内容的大量细节。 其中包括文本、样式和位置信息，还可以返回CSV/XLS格式的表格数据以及检索图像。
* 最后， [文档生成API](https://developer.adobe.com/document-services/apis/doc-generation/) 允许开发人员将Microsoft Word用作“模板”，与其数据（来自任何来源）混合，并生成动态的个性化文档(PDF和Word)。

开发人员可以 [注册](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) 免费试用所有这些服务。 在 [!DNL Acrobat Services] 平台使用基于REST的API，但也支持用于Node、Java、.NET和Python的SDK（目前仅适用于Extract）。

虽然不是API，但开发人员也可使用 [PDF嵌入API](https://developer.adobe.com/document-services/apis/pdf-embed/)，它通过您的网页提供一致、灵活的文档查看体验。

## 什么是 Acrobat Sign？

[Acrobat Sign](https://www.adobe.com/cn/sign.html) 是电子签名服务的世界领先者。 您可以使用不同的工作流程（包括多个签名）发送文档以供签名。 Acrobat Sign还支持需要签名和其他信息的工作流程。 所有这些功能都由功能强大的仪表板及灵活的创作系统支持。

与 [!DNL Acrobat Services],Acrobat Sign有一个 [免费试用](https://www.adobe.com/sign.html#sign_free_trial) 这样，开发人员既可以通过仪表板测试签名流程，也可以通过简单易用的基于REST的API测试签名流程。

## 入职情况

让我们考虑一个真实的情况，它展示了Adobe的服务可以如何提供帮助。 新员工加入公司时，他们需要根据自己的角色量身定制的信息。 此外，它们还需要整个公司的材料。 最后，他们必须签署文件，以表明接受公司政策。 下面我们将其分解为几个具体步骤：

* 首先，需要按名称欢迎新员工的自定义随函。 信件应包含有关员工姓名、角色、薪金和位置的信息。
* 必须将自定义信函与包含公司范围内基本信息的PDF（考虑各种人力资源政策、福利等）相结合
* 必须包含要求员工签名和日期的最终文档。
* 应将上述所有内容作为一个文档显示，并发送给员工进行签名。

我们来详细了解一下如何执行此操作。

## 生成动态文档

Adobe [文档生成](https://developer.adobe.com/document-services/apis/doc-generation/) 借助API，开发人员可以使用Microsoft Word和简单的模板语言创建动态文档，以此作为生成PDF和Word文档的基础。 下面是一个操作示例。

让我们从具有硬编码值的Word文档开始。 可以任意设置文档样式，包括图形、表格等。 这是初始文档。

![初始文档的屏幕截图](assets/onboarding_1.png)

文档生成功能是将“令牌”添加到用数据替换的Word文档。 虽然可以手动输入这些令牌，但有一个 [Microsoft Word加载项](https://developer.adobe.com/cn/document-services/docs/overview/document-generation-api/wordaddin/) 这样操作起来更容易。 将其打开可以为作者提供一种工具，用于定义可以在文档中使用的标签或数据组。

![Document Tagger的屏幕截图](assets/onboarding_2.png)

您可以从本地文件上传JSON信息、以JSON文本进行复制，或者选择以继续使用初始数据。 这样，您就可以根据自己的特定需求临时定义标签。 在此示例中，仅需要名称、角色、薪金和位置的标签。 这是通过使用 **创建标签** 按钮：

![定义标签的屏幕截图](assets/onboarding_3.png)

定义第一个标签后，您可以继续定义所需数量的标签：

![已定义标记的屏幕截图](assets/onboarding_4.png)

定义标签后，选择文档中的文本，并在适当时将其替换为标签。 在此示例中，为名称、角色和薪金添加标签。

![标记的屏幕截图](assets/onboarding_5.png)

文档生成不仅支持简单的标签，还支持逻辑表达式。 文档的第二段包含仅适用于路易斯安那州人的文本。 通过进入“文档标记”的“高级”选项卡并定义条件，可以添加条件表达式。 下面介绍如何定义简单的相等条件，但请注意，也支持数字比较和其他比较类型。

![条件屏幕截图](assets/onboarding_6.png)

然后，可将此内容插入段落并绕排在段落周围：

![文档中条件的屏幕截图](assets/onboarding_7.png)

要测试其工作方式，请选择 **生成文档**&#x200B;的 首次执行此操作时，您必须使用Adobe ID登录。 登录后，会显示可以手动编辑的默认JSON。

![数据的屏幕截图](assets/onboarding_8.png)

将生成PDF，您可以查看或下载该模板。

![生成的PDF](assets/onboarding_9.png)

虽然Document Tagger允许您在制作完成之后快速设计和测试，但您可以使用其中一个SDK来自动完成此过程。 尽管实际代码因特定需求而异，但以下是此代码在Node.js中的显示示例：

```js
 const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');

const credentials =  PDFServicesSdk.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdfservices-api-credentials.json")
    .build();

// Data would be dynamic...
let data = {
    "name":"Raymond Camden",
    "role":"Lead Developer",
    "salary":9000,
    "location":"Louisiana"
}

// Create an ExecutionContext using credentials.
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials);

// Create a new DocumentMerge options instance.
const documentMerge = PDFServicesSdk.DocumentMerge,
    documentMergeOptions = documentMerge.options,
    options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);

// Create a new operation instance using the options instance.
const documentMergeOperation = documentMerge.Operation.createNew(options);

// Set operation input document template from a source file.
const input = PDFServicesSdk.FileRef.createFromLocalFile('documentMergeTemplate.docx');
documentMergeOperation.setInput(input);

// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
    .then(result => result.saveAsFile('documentOutput.pdf'))
    .catch(err => {
        if(err instanceof PDFServicesSdk.Error.ServiceApiError
            || err instanceof PDFServicesSdk.Error.ServiceUsageError) {
            console.log('Exception encountered while executing operation', err);
        } else {
            console.log('Exception encountered while executing operation', err);
        }
    });
```

简而言之，代码设置凭据，创建操作对象并设置输入和选项，然后调用操作。 最后，将结果保存为PDF。 （结果也可以输出为Word。）

文档生成支持更复杂的用例，包括具有完全动态的表和图像的功能。 请参阅 [文档](https://developer.adobe.com/cn/document-services/docs/overview/document-generation-api/) 以获取更多详细信息。

## 执行PDF操作

在 [PDF服务API](https://developer.adobe.com/document-services/apis/pdf-services/) 提供大量“实用程序”操作来处理PDF。 这些操作包括：

* 从Office文档创建PDF
* 将PDF导出到Office文档
* 合并和拆分PDF
* 将OCR应用于PDF
* 设置、删除和修改对PDF的保护
* 删除、插入、重新排序和旋转页面
* 通过压缩或线性化优化PDF
* 获取PDF属性

在这种情况下，文档生成调用的结果必须与标准PDF合并。 借助SDK，此操作非常简单。 以下是Node.js中的示例：

```js
const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');
 
// Initial setup, create credentials instance.
const credentials = PDFServicesSdk.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdfservices-api-credentials.json")
    .build();
 
// Create an ExecutionContext using credentials and create a new operation instance.
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials),
    combineFilesOperation = PDFServicesSdk.CombineFiles.Operation.createNew();
 
// Set operation input from a source file.
const combineSource1 = PDFServicesSdk.FileRef.createFromLocalFile('documentOutput.pdf'),
      combineSource2 = PDFServicesSdk.FileRef.createFromLocalFile('standardCorporate.pdf');

combineFilesOperation.addInput(combineSource1);
combineFilesOperation.addInput(combineSource2);
 
// Execute the operation and Save the result to the specified location.
combineFilesOperation.execute(executionContext)
    .then(result => result.saveAsFile('combineFilesOutput.pdf'))
    .catch(err => {
        if (err instanceof PDFServicesSdk.Error.ServiceApiError
            || err instanceof PDFServicesSdk.Error.ServiceUsageError) {
            console.log('Exception encountered while executing operation', err);
        } else {
            console.log('Exception encountered while executing operation', err);
        }
    });
```

此代码获取两个PDF，将其合并，并将结果保存到新PDF中。 简单轻松！ 请参阅 [docs](https://developer.adobe.com/document-services/docs/overview/pdf-services-api/) 以了解如何操作。

## 签名过程

在登记流程的最后一站，员工必须签署协议，声明他们已阅读并同意其中定义的所有策略。 [Acrobat Sign](https://www.adobe.com/cn/sign.html) 支持许多不同的工作流程和集成，包括通过 [API](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html)的 总的来说，方案的最后部分可以完成如下：

首先，设计包含需要签名的表单的文档。 为此，有多种方法，包括在Adobe Sign用户仪表板中设计的视觉效果。 另一个选项是使用文档生成Word加载项为您插入标签。 此示例请求签名和日期。

![带有Sign标签的文档的屏幕截图](assets/onboarding_10.png)

可以将此文档另存为PDF，并使用上述相同方法，将所有文档连接在一起。 此流程将创建一个内聚包，其中包含个性化的问候语、标准公司文档和适合签名的最后一页。

模板可以上传到Acrobat Sign主控制区，然后用于新协议。 通过使用REST API，可以将此文档发送给预期的员工，以请求他们签名。

![签名文档的屏幕截图](assets/onboarding_11.png)

## 亲自体验

本文中描述的所有内容都可以立即进行测试。 在 [!DNL Adobe Acrobat Services] API [免费试用](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) 目前，在六个月内可收到1,000个免费请求。 Acrobat Sign [免费试用](https://www.adobe.com/sign.html#sign_free_trial) 允许您发送带水印的协议以进行测试。

有问题吗？在 [支持论坛](https://community.adobe.com/t5/document-services-apis/ct-p/ct-Document-Cloud-SDK) 由Adobe开发人员和支持人员每天监控。 最后，要想获得更多灵感，一定要抓住下一个目标 [纸质剪辑](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) 剧集。 我们将定期与新闻、演示和与客户交谈实时会议。
