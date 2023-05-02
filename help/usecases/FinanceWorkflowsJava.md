---
title: 在Java中管理财务文档工作流程
description: '"[!DNL Adobe Acrobat Services] 提供所有必要的工具、服务和功能，以便从PDF财务文档中处理和提取数据”'
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-7482.jpg
kt: 7482
exl-id: 3bdc2610-d497-4a54-afc0-8b8baa234960
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1315'
ht-degree: 0%

---

# 在Java中管理财务文档工作流程

![用例英雄横幅](assets/UseCaseFinancialHero.jpg)

金融业广泛使用PDF文件来交换数据，因为它有助于维护文档格式、设计和结构。 这种强大的格式使财务分析师和顾问能够帮助其客户做出明智的决定。

但是，PDF格式的处理和自动化可能具有一定的难度，尤其是当组合多个数据源时 — 这是金融行业中的常见用例。 构建处理PDF文档的自定义解决方案是一个选择，但不需要在软件和基础架构上投入太多时间和资金。 [!DNL Adobe Acrobat Services] 提供所有必要的工具、服务和功能，以便从PDF文档中处理和提取数据。

## 您可以学到的内容

在本实操教程中，了解如何使用 [!DNL Adobe Acrobat Services] 适用于 [!DNL Java Spring Boot] 应用程序。 您可以构建一个模型 — 视图 — 控制器(MVC)应用程序，它从PDF文档中提取内容，将其转换为其他数据格式（如Excel），合并多个PDF，并对资源进行密码保护。 本教程介绍了如何处理PDF文档，以及如何使用Adobe在您的网站上显示这些文档 [PDF嵌入API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html)的

## 相关API和资源

* [PDF服务API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF嵌入API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [项目示例](https://github.com/adobe/pdftools-java-sdk-samples)

## 设置

[!DNL Adobe Acrobat Services] 使用身份验证系统控制资源访问。 要访问服务，您必须从Adobe为您的组织或应用程序请求API密钥。 如果您有API密钥，请继续下一部分。 要创建新的API密钥，请访问 [快速入门](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 的 [!DNL Acrobat Services] 站点。 您可以使用免费试用创建密钥，该试用版提供1,000个文档事务，最多可使用6个月。

若要跟随本教程一起操作，您需要两组API密钥：

* Adobe PDF Services — 用于处理PDF文档

* Adobe PDF Embed API

创建凭据后，将PDF服务API凭据和私钥复制到 [!DNL Spring Boot] 资源部分中的应用程序。 了解有关 [Maven和Gradle库和依赖项](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) 在 [!DNL Adobe Acrobat Services] 页面。 在继续操作之前，请确保设置所有必要的包和库。

![目录位置的PDF服务API凭据屏幕截图](assets/FAWJ_1.png)

要配置日志记录服务，请访问 [Adobe文档](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) 滚动到“记录”部分。

>[!NOTE]
>
> 在生产环境中，请勿在版本控制中保存私钥。 始终使用秘密保管库或密钥注入服务来防止未经授权使用凭据。

现在，您的 [!DNL Spring Boot] 配置PDF后，您可以继续处理应用程序并为客户生成报告。

## 提交报告数据

要使用Adobe PDF Services API，请首先设置 `ExecutionContext` 使用您提供的凭据。 由于您的应用程序中包含凭据，您可以从文件中读取这些凭据并创建上下文，如下所示：

```
Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
    .fromFile(AUTH_FILE_PATH)
    .build();

ExecutionContext executionContext = ExecutionContext.create(credentials);
```

接下来，获取上下文以处理PDF文档。 以下是您可以执行的操作：

* 转换PDF文档（为Excel、Word或图形类型）

* 创建PDF文档(从HTML、Excel、Word等)

* 合并多个PDF文档

* Protect并取消保护PDF文档（您必须拥有密码）

* 优化PDF文档以便通过网络交付

所有这些示例在 [GitHub示例](https://github.com/adobe/pdfservices-java-sdk-samples/tree/master/src/main/java/com/adobe/pdfservices/operation/samples) 存储库。

下一个，位于 [!DNL Spring Boot]，您可以使用字符串路径或文件上传到的流获取文件。 必须初始化您执行的每个操作，并设置输入文件路径。 在本教程中，您将使用来自公开可用的PDF报告， [布莱克岩](https://www.blackrock.com/us/individual/products/investment-funds)的 您可以使用任何其他源，包括您自己的报告。

首先捕获 [FileRef](https://opensource.adobe.com/pdfservices-java-sdk-samples/apidocs/latest/com/adobe/pdfservices/operation/io/FileRef.html) 对象。 为简单起见，请按照字符串路径聚焦文件。 在下面的示例中，您将创建一个操作以将路径中的文件从PDF转换为Excel:

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
ExportPDFOperation exportOperation = ExportPDFOperation.createNew(ExportPDFTargetFormat.XLSX);

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_PDF);
exportOperation.setInput(inputPdf);
```

在此步骤之后，程序已准备好对PDF运行第一个操作。 接下来，执行操作并在Excel表中获取结果：

```
try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_EXCEL);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

此场景仅处理一个PDF文件。 您也可以从多个PDF文件开始，将它们合并为一个文件。 使用多个文件在财务数据报告中很常见，因为您必须处理来自多个来源的资金才能提供综合报告。

## 生成报告

[!DNL Adobe Acrobat Services] 不支持处理即装即用的Excel文档，但是您仍然可以使用社区框架和库来处理内容。

例如，您可以使用 [Apache POI](https://poi.apache.org/) 在浏览器中处理Excel(或其他Microsoft文档) [!DNL Java Spring Boot] 应用程序，或者您可以对Excel文件执行其他手动或自动任务。

在本例中，从PDF文档开始，将提取三个基金的资产净值并将它们显示在表中。 您还可以根据您的要求和可用数据提取其他信息，如图表和表格。 您甚至可以从其他来源导入数据。

生成报告后（在此示例中，为Excel格式），您可以使用Adobe PDF Services操作将报告转换回PDF文档并对其进行保护。

若要将报告从Excel格式转换为PDF文档，请使用以下操作：

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
> 为了防止每次请求到来时都不得不重新创建对象，请使用Spring的依赖关系注入来注入 `ExecutionContext` 对象。

此代码从PDF中生成Excel格式的文档文件。

在将此PDF交付给客户之前，您可以使用密码保护它。 创建另一个为您处理此保护的操作， [ProtectPDF操作](https://opensource.adobe.com/pdfservices-java-sdk-samples/apidocs/latest/com/adobe/pdfservices/operation/pdfops/ProtectPDFOperation.html)，然后使用 [ProtectPDF选项](https://opensource.adobe.com/pdfservices-java-sdk-samples/apidocs/latest/com/adobe/pdfservices/operation/pdfops/options/protectpdf/package-summary.html) 向文档添加密码。

```
ProtectPDFOptions options = ProtectPDFOptions.passwordProtectOptionsBuilder()
                    .setUserPassword("p@55w0rd")
                    .setEncryptionAlgorithm(EncryptionAlgorithm.AES_256)
                    .build();
ProtectPDFOperation operation = ProtectPDFOperation.createNew(options);
```

接下来，指定输入并执行操作。 生成的文件应具有密码以防止未经授权的访问。

## 显示报告

现在PDF报告已生成，您可以使用Adobe PDF Embed API在网站上显示该报告。 此JavaScript API使Web开发人员能够在Web浏览器中以本机方式加载和呈现PDF文档。

>[!NOTE]
>
> 此时，您需要第二个凭据令牌，客户端ID。

在 [!DNL Spring Boot] 应用程序，请在您要渲染HTML报告的位置添加以下PDF片段：

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

此脚本加载PDF文档，并允许查看者对文档进行批注和注释。 下面是Firefox中所示的此Embed API视图：

![Firefox中的PDF文档的屏幕截图](assets/FAWJ_2.png)

PDF嵌入API提供了预览PDF和注释报告所需的所有工具。

## 后续步骤

本实际操作教程探讨了 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) 以及如何使用这些服务处理PDF数据并生成财务决策报告。 它演示了如何使用 [!DNL Java Spring Boot] 作为示例框架，可以演示快速处理PDF文档。

浏览 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) 了解Adobe PDF Services对您的业务有何作用。 要了解SDK中提供的更多功能，请参阅 [GitHub存储库](https://github.com/adobe/pdftools-java-sdk-samples) ，并了解如何 [PDF嵌入API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) 可帮助您快速显示PDF中的应用程序。

要轻松合并和处理文档，为财务客户创建有用的PDF报告，请先免费注册 [Adobe开发人员帐户](https://www.adobe.io/apis/documentcloud/dcsdk/) 今天。
