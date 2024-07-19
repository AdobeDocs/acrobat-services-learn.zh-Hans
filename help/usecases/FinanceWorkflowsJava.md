---
title: 在Java中管理财务文档工作流
description: “[!DNL Adobe Acrobat Services]提供了所有必要的工具、服务和功能，以处理和提取PDF财务文档中的数据”
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-7482
thumbnail: KT-7482.jpg
exl-id: 3bdc2610-d497-4a54-afc0-8b8baa234960
source-git-commit: 558bd677d3b357a98488ada9dda1054bb21b81af
workflow-type: tm+mt
source-wordcount: '1204'
ht-degree: 0%

---

# 在Java中管理财务文档工作流

![用例主横幅](assets/UseCaseFinancialHero.jpg)

金融行业广泛使用PDF文件来交换数据，因为它有助于维护文件的格式、设计和结构。 这一稳健的模式让金融分析师和顾问能够帮助客户做出明智决策。

但是，PDF格式在处理和自动化方面可能具有一定的难度，尤其是在合并多个数据源时 — 这是金融业的一个常见用例。 构建自定义解决方案以处理PDF文档是一个选项，但无需在软件和基础架构上投入太多时间和资金。 [!DNL Adobe Acrobat Services]提供所有必要的工具、服务和功能，以便处理和提取PDF文档中的数据。

## 您可以学到的内容

在本实际操作教程中，了解如何将[!DNL Adobe Acrobat Services] API用于[!DNL Java Spring Boot]个应用程序。 您可以构建模型 — 视图 — 控制器(MVC)应用程序，该应用程序可以从PDF文档提取内容，将其转换为其他数据格式（如Excel），合并多个PDF，并使用口令保护资源。 本教程介绍如何使用Adobe[PDF嵌入API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html)处理PDF文档并在您的网站上显示它们。

## 相关的API和资源

* [PDF服务API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF的嵌入API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [项目示例](https://github.com/adobe/pdftools-java-sdk-samples)

## 设置

[!DNL Adobe Acrobat Services]使用身份验证系统来控制资源访问。 要访问服务，您必须从Adobe为您的公司或应用程序申请API密钥。 如果您有API密钥，请继续阅读下一部分。 要创建新的API密钥，请访问[!DNL Acrobat Services]站点中的[入门](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)。 您可以使用其免费试用版创建密钥，该试用版提供1,000个文档事务，试用期最长6个月。

要学习本教程，您需要两组API密钥：

* Adobe PDF Services — 用于处理PDF文档

* Adobe PDF Embed API

创建凭据后，将PDF服务API凭据和私钥复制到资源部分中的[!DNL Spring Boot]应用程序。 在[!DNL Adobe Acrobat Services]网站上详细了解[Maven和Gradle库及依赖项](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services)。 在继续之前，请确保设置所有必需的包和库。

![PDF服务API凭据的目录位置的屏幕截图](assets/FAWJ_1.png)

要配置日志记录服务，请访问[Adobe文档](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services)并滚动到“日志记录”部分。

>[!NOTE]
>
> 在生产环境中，不要在版本控制中保存私钥。 请始终使用密钥保管库或密钥注入服务来防止未经授权使用凭据。

现在[!DNL Spring Boot]应用程序已配置，您可以继续处理PDF并为客户生成报告。

## 提交报告数据

要使用Adobe PDF Services API，首先设置一个使用您提供的凭据的`ExecutionContext`。 由于您的应用程序中有凭据，因此可从文件中读取这些凭据并创建上下文，如下所示：

```
Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
    .fromFile(AUTH_FILE_PATH)
    .build();

ExecutionContext executionContext = ExecutionContext.create(credentials);
```

接下来，获取用于处理PDF文档的上下文。 以下是您可以执行的操作：

* 转换PDF文档（为Excel、Word或图形类型）

* 创建PDF文档(从HTML、Excel、Word等)

* 合并多个PDF文档

* Protect并取消对PDF文档的保护（必须输入密码）

* 优化要在网络上传递的PDF文档

所有这些示例在[GitHub示例](https://github.com/adobe/pdfservices-java-sdk-samples/tree/master/src/main/java/com/adobe/pdfservices/operation/samples)存储库中可用。

接下来，在[!DNL Spring Boot]中，您可以使用字符串路径或正在上传文件的流获取文件。 必须初始化您执行的每个操作，并设置输入文件路径。 在本教程中，您将使用来自[Blackrock](https://www.blackrock.com/us/individual/products/investment-funds)的公开PDF报告。 您可以使用任何其他源，包括您自己的报告。

首先从文件捕获FileRef对象。 为简单起见，请通过字符串路径重点分析文件。 在下面创建一个操作，以将路径中的文件从PDF转换为Excel：

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
ExportPDFOperation exportOperation = ExportPDFOperation.createNew(ExportPDFTargetFormat.XLSX);

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_PDF);
exportOperation.setInput(inputPdf);
```

执行此步骤后，您的程序已准备好在PDF上运行第一个操作。 接下来，执行该操作并在Excel表中获取结果：

```
try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_EXCEL);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

此方案仅处理一个PDF文件。 您也可以从多个PDF文件开始，将它们合并为一个文件。 使用多个文件在财务数据报告中很常见，因为您必须处理来自多个来源的资金，才能提供综合报表。

## 生成报告

[!DNL Adobe Acrobat Services]不支持处理现成的Excel文档，但您仍然可以使用社区框架和库来处理内容。

例如，您可以使用[Apache POI](https://poi.apache.org/)在[!DNL Java Spring Boot]应用程序中处理Excel(或其他Microsoft文档)，也可以对Excel文件执行其他手动或自动任务。

在此示例中，从PDF文档开始，提取三个基金的资产净值并在表中显示。 您还可以根据您的要求和可用数据提取其他信息，如图表和表格。 甚至可以从其他来源导入数据。

生成报告后（在本例中为Excel格式），您可以使用Adobe PDF Services操作将报告转换回PDF文档并保护它。

要将报表从Excel格式转换为PDF文档，请使用以下操作：

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
CreatePDFOperation exportOperation = CreatePDFOperation.createNew();

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_EXCEL);
exportOperation.setInput(inputPdf);

try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_PDF);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

>[!TIP]
>
> 为防止每次收到请求时都必须重新创建对象，请使用Spring的依赖关系注入来注入`ExecutionContext`对象。

此代码从Excel格式的报表生成PDF文档。

在将此PDF提供给客户之前，您可以使用密码保护此文档。 创建另一个为您处理此保护的操作，ProtectPDFOperation，然后使用ProtectPDFOptions将密码添加到文档。

```
ProtectPDFOptions options = ProtectPDFOptions.passwordProtectOptionsBuilder()
                    .setUserPassword("p@55w0rd")
                    .setEncryptionAlgorithm(EncryptionAlgorithm.AES_256)
                    .build();
ProtectPDFOperation operation = ProtectPDFOperation.createNew(options);
```

接下来，指定输入并执行操作。 生成的文件应包含密码，以防止未经授权的访问。

## 显示报告

现在，您的PDF报告已生成，您可以在网站上使用Adobe PDF Embed API显示该报告。 此JavaScript API允许Web开发人员在Web浏览器中以原生方式加载和渲染PDF文档。

>[!NOTE]
>
> 此时，您需要第二个凭据令牌，即客户端ID。

在[!DNL Spring Boot]应用程序中，将以下HTML片段添加到要呈现PDF报告的位置：

```
<div id="pdf-viewer"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
    document.addEventListener("adobe_dc_view_sdk.ready", function()
    {
        var adobeDCView = new AdobeDC.View({ clientId: "<your-client-id-here>", divId: "pdf-viewer" });
        adobeDCView.previewFile(
        {
            content: {
                location: {
                    url: "<your-document.pdf>"
                }
            },
            metaData: {
                fileName: "<document-name.pdf>"
            }
        });
    });
</script>
```

此脚本将加载PDF文档，并允许查看者对文档进行批注和注释。 以下是此Embed API的视图，如Firefox中所示：

![Firefox中PDF文档的屏幕截图](assets/FAWJ_2.png)

PDFEmbed API提供了预览PDF和批注报告所需的所有工具。

## 后续步骤

此实际操作教程探讨了[[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) API，并讨论了如何使用这些服务来处理PDF数据和生成财务决策报告。 它演示了如何使用[!DNL Java Spring Boot]作为示例框架将API集成到您的系统中，以展示快速处理PDF文档是多么简单。

浏览[[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/)并了解Adobe PDF Services可为您的企业提供哪些服务。 要了解SDK中可用的更多功能，请参阅[GitHub存储库](https://github.com/adobe/pdftools-java-sdk-samples)中的示例，并探索[PDFEmbed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html)如何帮助您快速显示应用程序内的PDF。

要轻松合并和处理文档，为您的金融客户创建有用的PDF报告，请立即注册免费的[Adobe开发人员帐户](https://www.adobe.io/apis/documentcloud/dcsdk/)。
