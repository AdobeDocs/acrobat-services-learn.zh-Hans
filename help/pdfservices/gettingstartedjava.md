---
title: Adobe PDF Services API和Java快速入门
description: 开发人员只需几分钟即可开始使用，并准备好运行为访问所有可用Web服务而提供的示例文件
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6676
thumbnail: KT-6676.jpg
exl-id: 4a8f2119-c464-496b-bdc8-35dd387bef25
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '457'
ht-degree: 0%

---

# Adobe PDF Services API和Java快速入门

![创建PDF主图](assets/GettingStartedJava_hero.jpg)

开发人员只需几分钟即可开始使用，并准备好运行为访问所有可用Web服务提供的示例文件。 本教程将指导您完成使用PDF服务Java SDK开始运行示例的所有步骤：

## 步骤1：获取凭据并下载示例文件

第一步是获取用于解锁使用的凭据（API密钥）。 [在此处注册免费试用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)，然后单击“开始使用”以创建新凭据。

![步骤1](assets/GettingStartedJava_step1.png)

选择“个人帐户”以注册免费试用非常重要：

![个人](assets/GettingStartedJava_personal.png)

在下一步中，您将选择PDF服务API服务，然后为您的凭据添加名称和描述。

选中了“Create personalized code sample”（创建个性化代码示例）复选框。 选择此选项可将新凭据自动添加到示例文件，这将保存将凭据添加到项目的手动步骤。

接下来，选择Java作为您用来接收Java特定示例的语言，然后单击“创建凭据”按钮。

![凭据](assets/GettingStartedJava_credentials.png)

您将收到一个名为PDFToolsSDK-JavaSamples.zip的.zip文件供您下载，该文件可以保存到您的本地文件系统。

## 步骤2：设置Java环境

1. 如果尚未安装[Java 8或更高版本](https://www.oracle.com/java/technologies/javase-downloads.html)，请安装。
1. 运行`javac -version`以验证您的安装。
1. 验证JDK bin文件夹是否包含在PATH变量中（方法因操作系统而异）。
1. 如果您尚未使用首选工具安装[Maven](https://maven.apache.org/install.html)，请安装。

个性化示例提供了从即用型示例代码、嵌入凭据json文件到预配置的依赖项连接等所有内容。

1. 下载[示例项目](https://github.com/adobe/pdftools-java-sdk-samples)。
1. 使用Maven： mvn clean install生成示例项目。
1. 在命令行或首选IDE中测试示例代码。

## 最后想法

PDF服务API可通过自动化常见工作流程并将处理负担转移到云来帮助您消除手动流程。 在每个浏览器都使用Adobe PDF Embed API和PDF服务API以不同方式处理PDF的情况下，您可以创建优化、可靠和可预测的流程，这些流程在&#x200B;**每次**&#x200B;都可以正确运行和显示，无论使用何种平台或设备。

## 资源和后续步骤

* 如需其他帮助和支持，请访问Adobe[[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&sort=latest_replies&filter=all)社区论坛

* PDF服务API [文档](https://www.adobe.com/go/pdftoolsapi_doc)

* 有关PDF服务API问题的[常见问题解答](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197)

* 有关许可和定价的问题，请[联系我们](https://www.adobe.com/go/pdftoolsapi_requestform)

* 相关文章

  [新的PDF服务API为文档工作流程提供了更多功能](https://community.adobe.com/t5/acrobat-services-api-discussions/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [&#x200B; 7月版 [!DNL Adobe Acrobat Services]：PDF嵌入和PDF服务](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)
