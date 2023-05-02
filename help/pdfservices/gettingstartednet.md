---
title: Adobe PDF Services API和.Net入门
description: 开发人员只需几分钟即可开始使用准备好的示例文件，这些文件为访问所有可用的Web服务而提供
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6675.jpg
kt: 6675
keywords: 特色
exl-id: 22c59c75-fd99-4467-a6f6-917fb246469a
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '524'
ht-degree: 0%

---

# Adobe PDF Services API和.Net入门

![创建PDF英雄图像](assets/GettingStartedJava_hero.jpg)

开发人员只需几分钟即可开始使用准备好的示例文件，这些文件为访问所有可用的Web服务而提供。 本教程将引导您完成使用PDF服务.Net SDK开始运行示例的所有步骤：

## 第1步：获取凭据和下载示例文件

第一步是获取凭据（API密钥）以解除使用锁定。 [在此处注册免费试用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 然后单击“开始使用”以创建新凭据。

![步骤 1](assets/GettingStartedJava_step1.png)

选择“个人帐户”来注册免费试用版非常重要：

![个人](assets/GettingStartedJava_personal.png)

在下一步中，您将选择PDF服务API服务，然后为您的凭据添加名称和说明。

有一个“创建个性化代码示例”复选框。 选择此选项可将新凭据自动添加到示例文件，这将保存您手动将其添加到项目的步骤。

接下来，选择Node.js作为接收特定于Node.js示例的语言，然后单击“创建凭据”按钮。

![凭据](assets/GettingStartedJava_credentials.png)

您将收到一个名为PDFToolsSDK-.NetSamples.zip的.zip文件，该文件可保存到您的本地文件系统。

## 第2步：设置.Net环境并运行示例代码

1. 下载并安装 [.Net SDK](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
1. 提取下载的 **[!UICONTROL PDFToolsSDK-.NetSamples.zip]** 并解压缩内容
1. cd到samples根目录 **[!UICONTROL adobe-DC.PDFTools.SDK.NET.Samples]**
1. 从示例根目录中，运行 `dotnet build`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>dotnet build

   现在，您可以运行示例文件了！

   这些最终步骤向您展示了如何使用“从Word创建PDF”操作运行第一个示例：

1. 从示例根目录将目录更改为CreatePDFFromDocx文件夹，cd CreatePDFFromDocx/

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>cd CreatePDFFromDocx/

1. 运行 `dotnet run CreatePDFFromDocx.csproj`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples\CreatePDFFromDocx>dotnet run CreatePDFFromDocx.csproj

您的PDF将在输出中指定的位置创建，默认情况下，该位置为同一文件夹。

## 最终想法

PDF服务API可以自动执行常见的工作流程并将处理负担转移至云端，从而帮助您消除手动流程。 在每个浏览器以不同方式处理PDF的世界中，利用Adobe PDF Embed API和PDF服务API，您可以创建流畅、可靠且可预测并可正确运行和显示的流程 **每次** 无论使用何种平台或设备。

## 资源和后续步骤

* 有关更多帮助和支持，请访问 [[!DNL Adobe Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) 社区论坛

* PDF服务API [文档](https://www.adobe.com/go/pdftoolsapi_doc)

* [常见问题](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) 有关PDF服务API问题

* [联系我们](https://www.adobe.com/go/pdftoolsapi_requestform) 有关许可和定价的问题

* 相关文章

   [新的PDF服务API为文档工作流提供了更多功能](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

   [7月版 [!DNL Adobe Acrobat Services]:PDF嵌入和PDF服务](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)
