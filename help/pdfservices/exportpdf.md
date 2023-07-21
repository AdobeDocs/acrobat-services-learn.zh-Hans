---
title: 使用PDF服务API将PDF导出到Word、PowerPoint等
description: 了解如何使用Node.js、Java和.Net语言的示例文件运行PDF服务API导出操作
type: Tutorial
role: Developer
level: Intermediate
thumbnail: KT-6674.jpg
kt: 6674
exl-id: 55f5b04e-0249-47d9-9131-2f9ec01db7e8
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '500'
ht-degree: 5%

---

# 使用PDF服务API将PDF导出到Word、PowerPoint等

![创建PDF英雄图像](assets/ExportPDF_hero.jpg)

Adobe PDF Services API使用API将PDF文件转换为MS Office、文本和图像。 有很多常见用例可以解锁现有的PDF以进行内容编辑和分析，借助PDF服务，API开发人员可以轻松地将此功能集成到现有系统和应用程序中。 将PDF文件转换为MS Word，用于编辑内容、审批以及稍后发送以供签名，以创建自定义合同工作流程。 或将PDF内容导出为MS Excel格式，以进行发票和财务计算或数据分析。

导出操作支持以下PDF文件转换：

* PDFMicrosoft Word(DOC、DOCX)
* PDFMicrosoft PowerPoint(PPTX)
* PDF到Microsoft Excel(XLSX)
* PDF到文本(RTF)
* PDF到图像(JPEG、PNG)

在本教程中，学习如何使用用于Node.js、Java和.Net语言的示例文件运行第一个PDF服务API导出操作的基础知识。

## 第1步：创建凭据并设置环境：

使用下面的快速入门教程创建API凭据、下载示例文件并设置环境。

[开始使用PDF服务API和Java](gettingstartedjava.md)

[开始使用PDF服务API和.Net](gettingstartednet.md)

[开始使用PDF服务API和Node.js](createpdffromhtml.md)

## 第2步：使用示例文件运行export pdf操作

**Java**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如， C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples

1. 运行以下命令:

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.exportpdf.ExportPDFToDOCX`

您的PDF在src/main/resources目录下创建。

**.Net**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如， C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. 再次将目录更改为ExportPDFtoDocx目录。

1. 运行以下命令:

   `dotnet run ExportPDFToDocx.csproj`

您的PDF是在同一目录中创建的。

**Node.js**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如， C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. 运行以下命令:

   `node src/ocr/ocr-pdf.js`

您的PDF将在输出中指定的位置创建，默认情况下为pdfServicesSdkResult目录。

## 最终想法

现在，您应该有一个可导入现有应用程序的工作示例，用于开始概念验证。 在每个示例目录中，您可以看到另一个将PDF文件导出为图像格式的示例。 以上相同的步骤同样允许您运行该示例。 要更改到其他格式，您可以将代码更新为所需的新格式：

SupportedTargetFormats.PPTX

目标结果为：

output/exportPdfOutput.PPTX

转换为另一种格式。

## 资源和后续步骤

* 有关更多帮助和支持，请访问 [[!DNL Adobe Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) 社区论坛

* PDF服务API [文档](https://www.adobe.com/go/pdftoolsapi_doc)

* [常见问题](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) 有关PDF服务API问题

* [联系我们](https://www.adobe.com/go/pdftoolsapi_requestform) 有关许可和定价的问题
