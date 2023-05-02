---
title: 使用Adobe PDF Services API处理OCRPDF文件
description: 通过OCR（光学字符识别），您可以解锁扫描的PDF以提取文本并创建可搜索的文件
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6677.jpg
kt: 6677
keywords: Hero
exl-id: 61a9a2d1-94c3-41c2-8f90-a56a938ef245
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '600'
ht-degree: 4%

---

# 使用Adobe PDF Services API处理OCRPDF文件

![创建PDF英雄图像](assets/OCR_hero.jpg)

借助OCR（光学字符识别），您可以解锁扫描的PDF以提取文本并创建可搜索的文件。 使用我们功能强大的基于云的API，将OCR集成到任何文档工作流程中，从而获得归档、复制文本和创建可搜索文档索引的完美解决方案。 从扫描的PDF存储库创建可搜索的存档，以解锁重要信息并快速搜索节省时间。 或者，通过上传的扫描内容将OCR应用于您的PDF，从而允许编辑文档以便在登记工作流程中使用。

开发人员只需几分钟，即可开始运行为OCR提供的示例文件。

在本教程中，介绍了如何使用适用于Node.js、Java和.Net语言的示例文件运行第一个PDF服务API OCR操作的基础知识。

## 第1步：创建凭据并设置环境

使用下面的快速入门教程创建API凭据、下载示例文件并设置环境。

[开始使用PDF服务API和Java](gettingstartedjava.md)
[开始使用PDF服务API和.Net](gettingstartednet.md)
[开始使用PDF服务API和Node.js](createpdffromhtml.md)

## 运行示例文件中提供的OCR示例

我们的OCR操作默认允许使用英语区域设置，但是也支持德语、法语、丹麦语和 [其他语言](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#ocr-with-explicit-language)的 默认值为en-us locale。

当您通过OCR操作（包括特定区域设置）传递选项时，该方法还接受&#39;type&#39;参数，该参数有两个选项：

* SEARCHABLE_IMAGE:先在清理过程中修改原始图像（例如，对原始图像进行扭曲），然后再将不可见文本图层放在其上。 这种类型可移去不需要的伪像，在某些情况下可能会使文档的可读性更好。

* SEARCHABLE_IMAGE_EXACT:确保文本可搜索和选择。 此选项可保留原始图像并在它上面放置一个不可见文本图层。 建议在对原始图像要求最大保真度的情况下使用。

**Java**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如，C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples>。

1. 运行以下命令:

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.ocrpdf.OcrPDF`

您的PDF将在src/main/resources目录中创建。

**.Net**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如，C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. 再次将目录更改为OcrPDF目录。

1. 运行以下命令:

   `dotnet run OcrPDF.csproj`

您的PDF将在同一目录中创建。

**Node.js**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如，C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. 运行以下命令:

   `node src/ocr/ocr-pdf.js`

您的PDF将在输出中指定的位置创建，默认情况下，该位置是输出目录。

## 最终想法

通过示例文件执行这些简单的步骤，您应有一个可以构建的工作示例。 除了我们在本教程中使用的OCR示例之外，还有另一个使用前面讨论过的受支持类型和区域设置选项的OCR示例。

在这里，您只需替换示例中的输入和输出文件，即可使用自己的PDF完成概念验证，供您自己使用。

![概念验证](assets/OCR_poc.png)

## 资源和后续步骤

* 有关更多帮助和支持，请访问Adobe [[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) 社区论坛

* PDF服务API [文档](https://www.adobe.com/go/pdftoolsapi_doc)

* [常见问题](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) 有关PDF服务API问题

* [联系我们](https://www.adobe.com/go/pdftoolsapi_requestform) 有关许可和定价的问题
