---
title: 使用PDF服务API将PDF导出到Word、PowerPoint等
description: 了解如何使用Node.js、Java和.Net语言的示例文件运行PDF服务API导出操作
feature: PDF Services API
role: Developer
level: Intermediate
type: Tutorial
jira: KT-6674
thumbnail: KT-6674.jpg
exl-id: 55f5b04e-0249-47d9-9131-2f9ec01db7e8
TQID: https://experienceleague.adobe.com/CV-KH0fg1Tjnr7eCmNaFMtttWO1QUCyjfyIfwg1Vqm0
product_v2:
  - id: acdc2bde-2937-4877-90d9-031dd66278c9
feature_v2:
  - id: b1809bd0-a86b-4991-8083-2e3b517fc3b8
  - id: c4d07275-6387-4756-8bf7-681e581ffd27
subfeature_v2:
  - id: c6f72a9c-54c4-4933-93c9-d7c656ff1f14
role_v2:
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
level_v2:
  - id: b5a62a22-46f7-4f0d-b151-3fc640bef588
topic_v2:
  - id: bce87dde-a4ab-44c9-8a18-ad66e4ddb377
source-git-commit: 0110d2606056220c4236fe2f0e3afbfc112746e7
workflow-type: tm+mt
source-wordcount: 524
ht-degree: 2%

---

# 使用PDF服务API将PDF导出到Word、PowerPoint等

![创建PDF主图](assets/ExportPDF_hero.jpg)

Adobe PDF Services API使用API将PDF文件转换为MS Office、文本和图像。 解锁PDF编辑和分析现有PDF的常见用例有很多，而且使用Content Services API，开发人员可以轻松地将此功能集成到现有系统和应用程序中。 将PDF文件转换为MS Word以编辑内容、审批，并在稍后发送以供签名，以创建自定义合同工作流程。 或者将PDF内容导出为MS Excel格式，以便进行发票和财务计算或数据分析。

导出操作支持以下PDF文件转换：

* Microsoft Word(DOC、DOCX)
* Microsoft PowerPoint (PPTX)的PDF
* Microsoft Excel (XLSX)
* PDF到文本(RTF)
* PDF到图像(JPEG、PNG)

在本教程中，了解如何使用Node.js、Java和.Net语言的示例文件运行第一个PDF服务API导出操作的基础知识。

## 步骤1：创建凭据并设置环境：

使用下面的快速入门教程创建API凭据、下载示例文件和设置您的环境。

[PDF服务API和Java快速入门](gettingstartedjava.md)

[PDF服务API和.Net快速入门](gettingstartednet.md)

[PDF服务API和Node.js快速入门](createpdffromhtml.md)

## 第2步：使用示例文件运行导出pdf操作

**Java**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如，C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples

1. 运行以下命令：

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.exportpdf.ExportPDFToDOCX`

您的PDF将在src/main/resources目录下创建。

**.Net**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如，C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. 再次将目录更改为ExportPDFtoDocx目录。

1. 运行以下命令：

   `dotnet run ExportPDFToDocx.csproj`

您的PDF将在同一目录中创建。

**Node.js**

1. 打开命令提示符。

1. 将目录更改为示例代码目录。

   例如，C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. 运行以下命令：

   `node src/ocr/ocr-pdf.js`

您的PDF将在输出中指定的位置创建，默认情况下，该位置为pdfServicesSdkResult目录。

## 最后想法

您现在应该有一个可导入现有应用程序的工作示例，以开始概念验证。 在每个示例目录中，您可以看到另一个将PDF文件导出为图像格式的示例。 通过上述相同步骤，您也可以运行该示例。 要更改为其他格式，您可以将代码更新为您希望的新格式：

SupportedTargetFormats.PPTX

目标结果：

output/exportPdfOutput.PPTX

转换为另一种格式。

## 资源和后续步骤

* 如需其他帮助和支持，请访问[[!DNL Adobe Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&sort=latest_replies&filter=all)社区论坛

* PDF服务API [文档](https://www.adobe.com/go/pdftoolsapi_doc)

* 有关PDF服务API问题的[常见问题解答](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197)

* 有关许可和定价的问题，请[联系我们](https://www.adobe.com/go/pdftoolsapi_requestform)
