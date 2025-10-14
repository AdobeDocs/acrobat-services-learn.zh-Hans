---
title: 使用Adobe PDF Services API对PDF文件执行OCR
description: 借助OCR（光学字符识别），您可以解锁扫描的PDF以提取文本并创建可搜索的文件
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6677
thumbnail: KT-6677.jpg
exl-id: 61a9a2d1-94c3-41c2-8f90-a56a938ef245
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '567'
ht-degree: 0%

---

# 使用Adobe PDF Services API对PDF文件执行OCR

![创建PDF主图](assets/OCR_hero.jpg)

借助OCR（光学字符识别），您可以解锁扫描的PDF以提取文本并创建可搜索的文件。 使用我们功能强大的基于云的API，将OCR集成到任何文档工作流程中，为归档、复制文本和创建可搜索的文档索引提供了完美的解决方案。 从扫描的PDF存储库创建可搜索的归档文件，以快速搜索功能解锁重要信息并节省时间。 或者，对上传的扫描文档中的PDF应用OCR，以允许对其进行编辑以在入门培训工作流程中使用。

开发人员只需几分钟即可开始使用，并准备好运行为OCR提供的示例文件。

本教程介绍如何使用Node.js、Java和.Net语言的示例文件运行第一个PDF服务API OCR操作的基础知识。

## 步骤1：创建凭据并设置环境

使用下面的快速入门教程创建API凭据、下载示例文件和设置您的环境。

[PDF服务API和Java快速入门](gettingstartedjava.md)

[PDF服务API和.Net快速入门](gettingstartednet.md)

[PDF服务API和Node.js快速入门](createpdffromhtml.md)

## 运行示例文件中提供的OCR示例

我们的OCR操作默认支持英语语言环境，但也支持德语、法语、丹麦语和[其他语言](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#ocr-with-explicit-language)。 默认值是美元语言环境。

在传递包含特定区域设置的OCR操作选项时，该方法还接受具有两个选项的“type”参数：

* SEARCHABLE_IMAGE：在清理过程中修改原始图像（例如，使图像倾斜），然后将不可见文本图层放在图像上。 此类型会删除不需要的对象，并且在某些情况下可能会生成可读性更高的文档。

* SEARCHABLE_IMAGE_EXACT：确保文本可搜索且可选择。 此选项会保留原始图像，并在其上放置一个不可见的文本图层。 建议用于需要原始图像的最大保真度的情况。

**Java**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如，C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples>。

1. 运行以下命令：

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.ocrpdf.OcrPDF`

将在src/main/resources目录中创建您的PDF。

**.Net**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如，C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. 再次将目录更改为OcrPDF目录。

1. 运行以下命令：

   `dotnet run OcrPDF.csproj`

将在同一目录中创建您的PDF。

**Node.js**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如，C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. 运行以下命令：

   `node src/ocr/ocr-pdf.js`

将在输出中指定的位置（缺省为输出目录）创建PDF。

## 最后想法

通过使用示例文件完成这些简单步骤，您应该拥有一个可以构建的工作示例。 除了我们在本教程中使用的OCR示例之外，还有使用前面讨论的支持类型和区域设置选项的OCR示例。

在这里，您只需替换位于示例中的输入和输出文件，以使用您自己的PDF完成您自己的用例的概念验证。

![概念验证](assets/OCR_poc.png)

## 资源和后续步骤

* 如需其他帮助和支持，请访问Adobe[[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&sort=latest_replies&filter=all)社区论坛

* PDF服务API [文档](https://www.adobe.com/go/pdftoolsapi_doc)

* 有关PDF服务API问题的[常见问题解答](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197)

* 有关许可和定价的问题，请[联系我们](https://www.adobe.com/go/pdftoolsapi_requestform)
