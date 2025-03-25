---
title: 使用PDF服务API和Node.js，只需几分钟即可从HTML或MS Office创建PDF
description: 在PDF服务API中，有几种可用的服务可用于创建和处理PDF，或从PDF导出到MS Office和其他格式
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6673
thumbnail: KT-6673.jpg
exl-id: 1bd01bb8-ca5e-4a4a-8646-3d97113e2c51
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '673'
ht-degree: 0%

---

# 使用PDF服务API和Node.js，只需几分钟即可从HTML或MS Office创建PDF

![创建PDF主图](assets/createpdffromhtml_hero.jpg)

新的Adobe PDF Services API为开发人员提供了多种强大的PDF操作服务供其自由选择和选择，以满足复杂业务工作流程的需求，让文档工作流数字化变得前所未有的简单。 借助这些现成的基于云的Web服务，可以简化复杂的体系结构、实施策略和技术提升。

在PDF服务API中，有几种可用的服务可用于创建和处理PDF，或从PDF导出到MS Office和其他格式。

* 从静态或动态HTML、MS Word、PowerPoint、Excel等创建PDF文件
* Export PDF到MS Word、PowerPoint、Excel等
* 使用OCR识别PDF文件中的文本并启用文档搜索
* Protect在打开文档时使用密码PDF
* 将PDF页面或PDF文档合并为单个PDF
* 压缩PDF以减小通过电子邮件或在线共享的大小
* 线性化以优化PDF以便在Web上快速查看
* 使用插入、替换、重新排序、删除和旋转服务整理PDF页面

开发人员只需几分钟即可开始使用，并准备好运行为访问所有可用Web服务提供的示例文件。 下面是操作方法。

## 获取凭据并下载示例文件

第一步是获取用于解锁使用的凭据（API密钥）。 [在此处注册免费试用](https://www.adobe.com/go/dcsdks_credentials)，然后单击“开始使用”以创建新凭据。

![API密钥](assets/apikey.png)

选择“个人帐户”以注册免费试用非常重要：

![个人帐户](assets/personalaccount.png)

在下一步中，您将选择PDF服务API服务，然后为您的凭据添加名称和描述。

选中了“Create personalized code sample”（创建个性化代码示例）复选框。 选择此选项可将新凭据自动添加到示例文件，从而跳过手动步骤。

接下来，选择Node.js作为接收特定Node.js示例的语言，然后单击“创建凭据”按钮。

![创建凭据](assets/createcredentials.png)

您将收到一个名为PDFToolsSDK-Node.jsSamples.zip的.zip文件供您下载，该文件可以保存到您的本地文件系统。

## 将凭据添加到代码示例

如果您选择了“创建个性化代码示例”选项，则不必手动将客户端ID添加到代码示例文件，而是可以跳过下一步并直接转到下面的“运行代码示例”部分。

如果您没有选择“创建个性化代码示例”选项，则必须从Adobe.io控制台复制客户端ID（API密钥）：

![代码示例](assets/codesample.png)

解压缩PDFToolsSDK-Node.jsSamples.zip的内容。

转到adobe-dc-pdf-tools-sdk-node-samples文件夹下的根目录。

使用任意文本编辑器或IDE打开pdftools-api-credentials.json。

将凭据粘贴到代码中客户端ID的字段中：

```javascript
{
 "client_credentials": {
  "client_id": "abcdefghijklmnopqrstuvwxyz",
```

保存文件并继续执行下一步以运行代码示例。

## 运行您的第一个代码示例

使用命令提示符，转到adobe-dc-pdf-tools-sdk-node-samples文件夹下的根目录。

键入npm install ：

C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples>npm install

现在，您可以运行示例文件了！

对于您的第一个示例，创建一个PDF：

在命令提示符下，使用以下命令运行createPDF示例：

C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples>node src/createpdf/create-pdf-from-docx.js

输出示例：

![输出示例](assets/exampleoutput.png)

将在输出中指定的位置（默认情况下为pdfServicesSdkResult目录）创建您的PDF。

## 资源和后续步骤

* 如需其他帮助和支持，请访问Adobe[[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all)社区论坛

PDF服务API [文档](https://www.adobe.com/go/pdftoolsapi_doc)

* 有关PDF服务API问题的[常见问题解答](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197)

* 有关许可和定价的问题，请[联系我们](https://www.adobe.com/go/pdftoolsapi_requestform)

* 相关文章：
  [新的PDF服务API为文档工作流程提供了更多功能](https://community.adobe.com/t5/acrobat-services-api-discussions/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [ 7月版 [!DNL Adobe Acrobat Services]：PDF嵌入和PDF服务](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)
