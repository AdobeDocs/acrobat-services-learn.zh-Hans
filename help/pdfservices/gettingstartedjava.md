---
title: Adobe PDF Services API和Java入门
description: 开发人员只需几分钟即可开始使用准备好的示例文件，这些文件为访问所有可用的Web服务而提供
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6676.jpg
kt: 6676
exl-id: 4a8f2119-c464-496b-bdc8-35dd387bef25
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '501'
ht-degree: 0%

---

# Adobe PDF Services API和Java入门

![创建PDF英雄图像](assets/GettingStartedJava_hero.jpg)

开发人员只需几分钟即可开始使用准备好的示例文件，这些文件为访问所有可用的Web服务而提供。 本教程将引导您完成使用PDF服务Java SDK开始运行示例的所有步骤：

## 第1步：获取凭据和下载示例文件

第一步是获取凭据（API密钥）以解除使用锁定。 [在此处注册免费试用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 然后单击“开始使用”以创建新凭据。

![步骤 1](assets/GettingStartedJava_step1.png)

选择“个人帐户”来注册免费试用版非常重要：

![个人](assets/GettingStartedJava_personal.png)

在下一步中，您将选择PDF服务API服务，然后为您的凭据添加名称和说明。

有一个“创建个性化代码示例”复选框。 选择此选项可将新凭据自动添加到示例文件，这将保存您手动将其添加到项目的步骤。

接下来，选择Java作为接收特定于Java的示例的语言，然后单击“创建凭据”按钮。

![凭据](assets/GettingStartedJava_credentials.png)

您将收到一个名为PDFToolsSDK-JavaSamples.zip的.zip文件以供下载，该文件可保存到您的本地文件系统。

## 第2步：设置Java环境

1. 安装 [Java 8或更高版本](https://www.oracle.com/java/technologies/javase-downloads.html) 如果你还没有。
1. 运行 `javac -version` 以验证您的安装。
1. 验证JDK bin文件夹是否包含在PATH变量中（方法因操作系统而异）。
1. 安装 [Maven](https://maven.apache.org/install.html) 使用首选工具（如果您尚未使用）。

个性化范例提供从即装即用范例代码、嵌入凭据json文件到预配置的依赖项连接等所有功能。

1. 下载 [示例项目](https://github.com/adobe/pdftools-java-sdk-samples)的
1. 使用Maven构建示例项目：mvn全新安装。
1. 在命令行上或在首选的IDE中测试示例代码。

## 最终想法

PDF服务API可以自动执行常见的工作流程并将处理负担转移至云端，从而帮助您消除手动流程。 在每个浏览器以不同方式处理PDF的世界中，利用Adobe PDF Embed API和PDF服务API，您可以创建流畅、可靠且可预测并可正确运行和显示的流程 **每次** 无论使用何种平台或设备。

## 资源和后续步骤

* 有关更多帮助和支持，请访问Adobe [[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) 社区论坛

* PDF服务API [文档](https://www.adobe.com/go/pdftoolsapi_doc)

* [常见问题](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) 有关PDF服务API问题

* [联系我们](https://www.adobe.com/go/pdftoolsapi_requestform) 有关许可和定价的问题

* 相关文章

   [新的PDF服务API为文档工作流提供了更多功能](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

   [7月版 [!DNL Adobe Acrobat Services]:PDF嵌入和PDF服务](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)
