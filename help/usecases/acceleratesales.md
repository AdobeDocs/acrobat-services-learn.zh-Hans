---
title: 加快销售流程
description: 了解如何通过将文档体验与 [!DNL Adobe Acrobat Services]
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-10222.jpg
kt: 10222
exl-id: 9430748f-9e2a-405f-acac-94b08ad7a5e3
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1755'
ht-degree: 0%

---

# 加快销售流程

![用例英雄横幅](assets/UseCaseAccelerateSalesHero.jpg)

从白皮书到合同和协议，整个购买过程中都需要大量的文档。 在本教程中，了解如何 [[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/) 可以在整个旅程中集成文档体验，以帮助加快销售。

## 根据数据生成协议和销售订单

销售协议、合同和其他文档可能会因特定条件而有很大差异。 例如，销售协议可能仅包括基于唯一标准的某些条款 — 例如位于特定的国家或州，或将某些产品作为协议的一部分。 手动创建这些文档或维护许多不同的模板变体可以显著增加与手动查看更改相关的法律费用。

[Adobe文档生成API](https://developer.adobe.com/document-services/apis/doc-generation/) 允许您从CRM或其他数据系统中获取数据，以根据这些数据动态生成销售文档。

## 获取凭据

首先注册免费的Adobe PDF服务凭据：

1. 导航 [此处](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) 注册您的凭据。
1. 使用您的Adobe ID登录。
1. 设置您的凭据名称（例如，销售协议演示）。

   ![设置凭据名称的屏幕截图](assets/accsales_1.png)

1. 选择用于下载示例代码的语言（例如，Node.js）。
1. 选中以同意 **[!UICONTROL 开发人员条款]**&#x200B;的
1. 选择 **[!UICONTROL 创建凭据]**的
使用包含用于身份验证的示例文件、pdfservices-api-credentials.json和private.key的ZIP文件将文件下载到您的计算机。

   ![凭据屏幕截图](assets/accsales_2.png)

1. 选择 **[!UICONTROL 获取Microsoft Word加载项]** 或转到 [AppSource](https://appsource.microsoft.com/en-cy/product/office/WA200002654) 以安装。

   >[!NOTE]
   >
   >安装Word加载项需要您具有在Microsoft 365内安装加载项的权限。 如果您没有权限，请联系您的Microsoft 365管理员。

## 您的数据

如果要从特定数据系统提取数据，必须将该数据输出为JSON数据或生成您自己的架构。 此场景将使用以下预先创建的示例数据集：

```
{
    "salesOrder": {
        "comment": "Make sure to call 555-555-1234 when you arrive. The front door is broken."
    },
    "company": {
        "name":"Home Services Co.",
        "address": {
            "city": "Homestead",
            "state": "NY",
            "zip": "14623",
            "streetAddress": "123 Demohome Street"
        }
    },
    "customer": {
        "address": {
            "city": "Seattle",
            "state": "WA",
            "zip": "98052",
            "streetAddress": "20341 Whitworth Institute 405 N. Whitworth"
        },
        "email": "mailto:jane-doe@xyz.edu",
        "jobTitle": "Professor",
        "name": "Jane Doe",
        "telephone": "(425) 123-4567",
        "url": "http://www.janedoe.com"
    },
    "tax": {
        "state":"WA",
        "rate": 0.08
    },
    "referencesOrder": [
        {
            "description": "Carpet Cleaning Service - 3BR 2BA",
            "totalPaymentDue": {
                "price": 359.54
            },
            "orderedItem": {
                "description": "Carpet Cleaning Service"
            }
        },
        {
            "description": "Home Cleaning Service - 3BR 2BA",
            "totalPaymentDue": {
                "price": 299.99
            },
            "orderedItem": {
                "description": "House Cleaning Service"
            }
        }
    ]
}
```

## 向文档中添加基本标签

此方案使用可以下载的销售订单文档 [此处](https://github.com/benvanderberg/adobe-document-generation-samples/blob/main/SalesOrder/Exercise/SalesOrder_Base.docx?raw=true)的

![示例销售订单文档的屏幕截图](assets/accsales_3.png)

1. 打开 *SalesOrder.docx* Microsoft Word中的示例文档。
1. 如果已安装“文档生成”插件，请选择 **[!UICONTROL 文档生成]** 在功能区中。 如果您在功能区中看不到“文档生成”，请按照以下说明进行操作。
1. 选择 **[!UICONTROL 开始使用]**&#x200B;的
1. 将上面编写的JSON示例数据复制到 *JSON数据* 字段。

   ![复制JSON数据的屏幕截图](assets/accsales_4.png)

接下来，导航到“文档生成标记”面板，将标记置入文档中。

1. 选择要替换的文本(例如， *公司名称*)。
1. 在 *文档生成标记* 面板中，搜索“name”。
1. 在标签列表中，选择公司下的名称。
1. 选择 **[!UICONTROL 插入文本]**&#x200B;的

   ![插入标记的屏幕截图](assets/accsales_5.png)

   此过程会放置一个名为 {{company.name}} 因为标记位于JSON中的路径下。

   ```
   {
   …
   "company": {
       "name":"Home Services Co.",
       …
   },
   …
   }
   ```

对文档中的某些其他标签（如STREET ADDRESS、CITY、STATE、ZIP等）重复这些操作。

## 预览生成的文档

您可以直接在Microsoft Word中，根据示例JSON数据预览生成的文档。

1. 在 *文档生成标记* 面板中，选择 **[!UICONTROL 生成文档]**&#x200B;的 系统可能会首次提示您使用Adobe ID登录。 选择 **[!UICONTROL 登录]** 并完成提示以使用您的凭据登录。

   ![如何预览生成的文档的屏幕截图](assets/accsales_6.png)

1. 选择 **[!UICONTROL 查看文档]**&#x200B;的

   ![“查看文档”按钮的屏幕截图](assets/accsales_7.png)

1. 将打开一个浏览器窗口，允许您预览文档结果。

   ![浏览器窗口中文档的屏幕截图](assets/accsales_8.png)

您可以在文档中看到被原始采样数据中的数据替换的标签。

![用数据替换的标签的屏幕截图](assets/accsales_9.png)

## 向模板添加表

在下一个方案中，将产品列表添加到文档中的表中。

1. 在必须放置表的位置插入光标。
1. 在 *文档生成标记* 面板中，选择 **[!UICONTROL 高级]**&#x200B;的
1. 扩展 **[!UICONTROL 表和列表]**&#x200B;的
1. 在 *表记录* 字段，选择 *referencesOrder*，这是一个列出所有产品项目的数组。
1. 在选择列记录字段中，键入以包括 *说明* 和 *totalPaymentDue.price* 字段。
1. 选择 **[!UICONTROL 插入表]**&#x200B;的

   ![插入表的屏幕截图](assets/accsales_10.png)

编辑该表以调整样式、大小和其他参数，就像在Microsoft Word中编辑任何其他表一样。

## 添加数值计算

数值计算允许您根据数据集合（如数组）来计算总和和其他计算。 在此方案中，添加一个字段以计算小计。

1. 选择 *$ 0.00* 在小计标题旁边。
1. 在 *[!UICONTROL 文档生成标记]* 面板，扩展 **[!UICONTROL 数值计算]**&#x200B;的
1. 在 *[!UICONTROL 选择计算类型]*，请选择 **[!UICONTROL 聚合]**&#x200B;的
1. 在 *[!UICONTROL 选择文字]*，请选择 **[!UICONTROL 总和]**&#x200B;的
1. 在 *[!UICONTROL 选择记录]*，请选择 **[!UICONTROL 引用顺序]**&#x200B;的
1. 在*[!UICONTROL 选择要执行聚合的项]**，选择 **[!UICONTROL totalPaymentsDue.price]**&#x200B;的
1. 选择 **[!UICONTROL 插入计算]**&#x200B;的

此过程将插入提供值总和的计算标签。 可以使用JSONata计算进行更高级的计算。 例如：

* 小计： `${{expr($sum(referencesOrder.totalPaymentDue.price))}}`
计算referencesOrder.totalPaymentDue.price的总和。

* 销售税： `${{expr($sum(referencesOrder.totalPaymentDue.price)*0.08)}}`
计算价格并乘以8%计算税额。

* 应付款总额： `${{expr($sum(referencesOrder.totalPaymentDue.price)*1.08)}}`
按1.08计算价格和倍数，以计算小计+税金。

## 添加条件项

条件部分仅允许您在满足特定条件时包含句子或段落。 在这种情况下，只有与特定状态匹配的节才会包含在内。

1. 在文档中，找到名为的部分 *加州隐私声明*&#x200B;的
1. 使用光标选择部分。

   ![所选内容的屏幕截图](assets/accsales_11.png)

1. 在 *[!UICONTROL 文档生成标记]*，请选择 **[!UICONTROL 高级]**&#x200B;的
1. 扩展 **[!UICONTROL 条件内容]**&#x200B;的
1. 在 *[!UICONTROL 选择记录]* 字段，搜索并选择 **[!UICONTROL customer.address.state]**&#x200B;的
1. 在 *[!UICONTROL 选择运算符]* 字段，选择 **=**&#x200B;的
1. 在 *[!UICONTROL 值字段]*，文字 *CA*&#x200B;的
1. 选择 **[!UICONTROL 插入条件]**&#x200B;的

仅当customer.address.state = CA时， California部分才会显示在生成的文档中。

接下来，选择“WASHINGTON隐私声明”部分并重复上述步骤，将值CA替换为WA。

## 添加动态图像

文档生成API允许您从数据动态插入图像。 当您拥有不同的子品牌，并且希望更改徽标、肖像图像或图像，以使其更适合特定行业时，此功能非常有用。

图像可以通过data或base64内容中的URL传递。 此示例使用URL。

1. 将光标放在要包含图像的地方。
1. 在 *[!UICONTROL 文档生成标记]* 面板中，选择 **[!UICONTROL 高级]**&#x200B;的
1. 扩展 **[!UICONTROL 图像]**&#x200B;的
1. 在 *[!UICONTROL 选择标签]* 字段，选择 **[!UICONTROL 徽标]**&#x200B;的
1. 在 *[!UICONTROL 可选替代文本]* 字段，请提供说明（即徽标）。 此过程插入一个图像占位符，其外观如下：

   ![占位符图像的屏幕截图](assets/accsales_12.png)

但是，您需要在已经处于版面中的图像上动态设置图像，这可通过以下操作实现：

1. 右键单击插入的占位符图像。

   ![占位符图像的屏幕截图](assets/accsales_13.png)

1. 选择 **[!UICONTROL 编辑替代文本]**&#x200B;的
1. 在面板中，复制如下所示的文本：
   `{ "location-path": "logo", "image-props": { "alt-text": "Logo" }}`
1. 在文档中选择要动态化的其他图像。

   ![文档中新图像的屏幕截图](assets/accsales_14.png)

1. 右键单击图像并选择 **[!UICONTROL 编辑替代文本]**&#x200B;的
1. 将值粘贴到面板中。

此过程会将图像替换为数据中徽标变量中的图像。

## 为Acrobat Sign添加标签

Adobe Acrobat Sign允许您在文档中捕获电子签名。 Acrobat Sign提供了一种在Web界面中轻松拖放字段的方法，但您也可以通过使用文本标记来控制签名和其他字段的位置。 使用Adobe文档生成标记，您可以轻松放置这些文本标记字段。

1. 导航到示例文档中需要签名的位置。
1. 在需要签名的位置插入光标。
1. 在 *[!UICONTROL Adobe文档生成标记]* 面板中，选择 **[!UICONTROL Adobe Sign]**&#x200B;的
1. 在 *[!UICONTROL 指定收件人数量]* 字段中，设置收件人的数量（在本例中为1）。
1. 在 *[!UICONTROL 收件人]* 字段，选择 **[!UICONTROL Signer-1]**&#x200B;的
1. 在 *[!UICONTROL 字段]* 文字，选择 **[!UICONTROL 签名]**&#x200B;的
1. 选择 **[!UICONTROL 插入Adobe Sign文本标记]**&#x200B;的

标签将插入到文档中。

![文档中的签名标记的屏幕截图](assets/accsales_15.png)

Acrobat Sign提供了一些可以放置的其他类型的字段，例如日期字段。
1. 在 *字段* 文字，选择 **[!UICONTROL 日期]**&#x200B;的
1. 将光标移动到文档中的“日期”位置上方。
1. 选择 **[!UICONTROL 插入Adobe Sign文本标记]**&#x200B;的

![文档中的日期标记的屏幕截图](assets/accsales_16.png)

## 生成协议

现在，您已经为您的文档添加了标签，并且准备开始使用。 下一节将介绍如何使用Node.js的文档生成API示例来生成文档，但这些示例将适用于任何语言。

打开注册凭据时下载的pdfservices-node-sdk-samples-master。 pdfservices-api-credentials.json和private.key文件应包含在这些文件中。

1. 打开终端以使用npm install安装依赖项。
1. 将示例data.json复制到resources文件夹中。
1. 将Word模板复制到资源文件夹中。
1. 在samples文件夹的根目录中创建一个名为generate-salesOrder.js的新文件。

```
const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');
const fs = require('fs');
const path = require('path');

var dataFileName = path.join('resources', '<INSERT JSON FILE');
var outputFileName = path.join('output', 'salesOrder_'+Date.now()+".pdf");
var inputFileName = path.join('resources', '<INSERT DOCX>');

//Loads credentials from the file that you created.
const credentials =  PDFServicesSdk.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdfservices-api-credentials.json")
    .build();

// Setup input data for the document merge process
const jsonString = fs.readFileSync(dataFileName),
jsonDataForMerge = JSON.parse(jsonString);

// Create an ExecutionContext using credentials
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials);

// Create a new DocumentMerge options instance
const documentMerge = PDFServicesSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);

// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)

// Set operation input document template from a source file.
const input = PDFServicesSdk.FileRef.createFromLocalFile(inputFileName);
documentMergeOperation.setInput(input);

// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile(outputFileName))
.catch(err => {
    if(err instanceof PDFServicesSdk.Error.ServiceApiError
        || err instanceof PDFServicesSdk.Error.ServiceUsageError) {
        console.log('Exception encountered while executing operation', err);
    } else {
        console.log('Exception encountered while executing operation', err);
    }
});
```

1. 替换 `<INSERT JSON FILE>` 在/resources中使用JSON文件的名称。
1. 替换 `<INSERT DOCX>` DOCX文件的名称。
1. 若要运行，请使用终端执行节点generate-salesOrder.js。

输出文件应位于/output文件夹中，并且文档生成正确。

## 更多选项

生成文档后，您可以采取其他操作，例如：

* 使用密码保护文档
* 压缩PDF（如果有大型图像）
* 捕获文档中的电子签名

要了解有关其他可用操作的更多信息，请查看示例文件中/src文件夹中的脚本。 您还可以通过查看不同操作的文档来了解更多信息。

## 其他使用案例

[!DNL Adobe Acrobat Services] 通过数字文档工作流程，可以帮助简化销售周期的很多环节：

* 使用Adobe PDF Embed API在网站上嵌入白皮书和其他内容，同时测量和收集关于收视率的分析
* 使用Acrobat Sign在生成的协议上捕获电子签名
* 使用Adobe PDF Extract API从PDF文档中提取协议数据

## 进一步学习

有兴趣了解更多信息？ 了解一些其他使用方法 [!DNL Adobe Acrobat Services]:

* 了解更多信息 [文档](https://developer.adobe.com/cn/document-services/docs/overview/)
* 查看有关Adobe Experience League的更多教程
* 使用/src文件夹中的示例脚本查看如何利用PDF
* 关注 [Adobe技术博客](https://medium.com/adobetech/tagged/adobe-document-cloud) 了解最新提示和技巧
* 订购 [纸质剪辑（每月实时流）](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) 要了解如何使用 [!DNL Adobe Acrobat Services]的=======
* 了解更多信息 [文档](https://developer.adobe.com/cn/document-services/docs/overview/)
* 查看有关Adobe Experience League的更多教程
* 使用/src文件夹中的示例脚本查看如何利用PDF
* 关注 [Adobe技术博客](https://medium.com/adobetech/tagged/adobe-document-cloud) 了解最新提示和技巧
* 订购 [纸质剪辑（每月实时流）](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) 要了解如何使用 [!DNL Adobe Acrobat Services]
