---
title: 在Microsoft Power Automate中创建您的第一个工作流
description: 了解如何在Microsoft Power Automate中使用Adobe PDF Services连接器
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-10379
thumbnail: KT-10379.jpg
exl-id: 095b705f-c380-42cc-9329-44ef7de655ee
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1955'
ht-degree: 1%

---


# 在Microsoft Power Automate中创建您的第一个工作流

了解如何使用[Microsoft Services](https://us.flow.microsoft.com/en-us/connectors/shared_adobepdftools/adobe-pdf-services/)连接器在[Adobe PDF Power Automate](https://flow.microsoft.com/zh-cn/)中创建您的第一个工作流。

在本实际操作教程中，学习如何：

* 将Word文档转换为PDF
* 将PDF文档合并为一个PDF
* 使用密码ProtectPDF文档

## 准备工作

### 您需要的

* **Adobe PDF Services的试用或生产凭据**
在[此处](https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfservices/getting-credentials-power-automate)详细了解如何在Microsoft Power Automate中获取和配置凭据。
* **具有高级连接器的Microsoft Power Automate**
了解如何在[此处](https://docs.microsoft.com/en-us/power-platform/admin/power-automate-licensing/types)检查Power Automate的许可级别。
* **OneDrive**
本教程中使用了OneDrive存储连接器，但可使用任何存储连接器替代。

### 范例文件

您需要解压缩两个[示例文件](assets/sample-assets.zip)并将其上传到OneDrive：

* WordDocument01.docx
* WordDocument02.docx

### 获取凭据

要完成本教程，您需要已在Microsoft Power Automate for Adobe PDF Services中配置您的凭据。 如果您尚未完成此步骤，请参阅[此处](https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfservices/getting-credentials-power-automate)的说明。

## 第1部分：创建新流并将Word转换为PDF

### 创建工作流

在此部分中，您使用即时流在[Microsoft Power Automate](https://flow.microsoft.com/zh-cn/)中创建一个新流、添加参数、从OneDrive获取文件并将文件转换为PDF。

1. 导航到[Microsoft Power Automate](https://flow.microsoft.com/zh-cn/)，并使用您的凭据登录。
1. 在侧边栏中选择&#x200B;**[!UICONTROL 创建]**。

   ![创建按钮](assets/createButtonPowerAutomate.png)

1. 选择&#x200B;**[!UICONTROL 即时工作流]**。
1. 为工作流命名。
1. 在&#x200B;*选择如何触发此工作流*&#x200B;下，选择&#x200B;**[!UICONTROL 手动触发工作流]**。
1. 选择&#x200B;**[!UICONTROL 创建]**。

### 获取文件的文件内容

接下来，获取示例文件的文件内容。

>[!PREREQUISITES]
>
>如果尚未将[示例文件](assets/sample-assets.zip)上传到OneDrive，请将其解压缩并上传。


1. 在[Power Automate](https://flow.microsoft.com/zh-cn/)中，选择&#x200B;**[!UICONTROL +新步骤]**。
1. 在搜索栏中搜索&#x200B;*OneDrive*。
1. 通过选择&#x200B;**[!UICONTROL OneDrive for Business]**&#x200B;或&#x200B;**[!UICONTROL OneDrive]**，选择您的工作或个人OneDrive帐户。
1. 在搜索栏中搜索&#x200B;*获取文件内容*。
1. 在&#x200B;**[!UICONTROL 文件]**&#x200B;字段中，选择文件夹图标以导航到OneDrive中的&#x200B;*WordDocument01.docx*&#x200B;文件。

   ![在Microsoft Power Automate中获取文件内容OneDrive操作](assets/getFileContentOneDrive.png)

### 将文件转换为PDF

现在您已经有了文件内容，可以将文档转换为PDF。

1. 在[Power Automate](https://flow.microsoft.com/zh-cn/)中，选择&#x200B;**[!UICONTROL +新步骤]**。
1. 在搜索栏中搜索&#x200B;*Adobe PDF服务*。
1. 选择&#x200B;**[!UICONTROL Adobe PDF服务]**。
1. 在搜索栏中搜索&#x200B;*“将单词转换为PDF”*。
1. 在&#x200B;**[!UICONTROL 文件名]**&#x200B;中，根据需要为文件命名，但文件必须以&#x200B;*.docx*&#x200B;结尾。 要将文档从Word转换为PDF，此扩展名是必需的。
1. 将光标置于&#x200B;**[!UICONTROL 文件内容]**&#x200B;字段中。
1. 使用&#x200B;**[!UICONTROL 动态内容]**&#x200B;面板，选择&#x200B;**[!UICONTROL 文件内容]**。

   ![在Microsoft Power Automate中将Word转换为PDF操作](assets/convertWordToPDFActionPowerAutomate.png)

### 将文件保存到OneDrive

文档生成后，将文件保存回OneDrive中。

1. 在[Microsoft Power Automate](https://flow.microsoft.com/zh-cn/)中，选择&#x200B;**[!UICONTROL +新步骤]**。
1. 在搜索栏中搜索&#x200B;*OneDrive*。
1. 通过选择&#x200B;**[!UICONTROL OneDrive for Business]**&#x200B;或&#x200B;**[!UICONTROL OneDrive]**，选择您的工作或个人OneDrive帐户。
1. 在搜索栏中搜索&#x200B;*获取文件内容*。
1. 在搜索栏中搜索&#x200B;*创建文件*。
1. 选择&#x200B;**[!UICONTROL 创建文件]**。
1. 在&#x200B;**[!UICONTROL 文件夹路径]**&#x200B;字段中，选择文件夹图标以指定将文件保存在OneDrive中的位置。
1. 在&#x200B;**[!UICONTROL 文件名]**&#x200B;中，根据需要为文件命名，但文件必须以&#x200B;*.docx*&#x200B;结尾。 要将文档从Word转换为PDF，此扩展名是必需的。
1. 在&#x200B;**[!UICONTROL 文件内容]**&#x200B;字段中，使用&#x200B;**[!UICONTROL 动态内容]**&#x200B;面板插入PDF的文件内容变量。

### Try流

1. 在左上角，选择&#x200B;**[!UICONTROL 无标题]**&#x200B;以重命名该流。
1. 选择&#x200B;**[!UICONTROL 保存]**。
1. 选择&#x200B;**[!UICONTROL 测试]**。
1. 选择&#x200B;**[!UICONTROL 手动]**，然后选择&#x200B;**[!UICONTROL 保存并测试]**。
1. 选择&#x200B;**[!UICONTROL “继续”]**。
1. 选择&#x200B;**[!UICONTROL 运行流]**。

在OneDrive文件夹中，您现在应该会看到转换后的PDF。

![在OneDrive中选择了转换的PDF文档](assets/selectedGeneratedFileInOneDrive.png)

## 第2部分：从模板生成动态文档

此下一部分构建于第1部分，并使用&#x200B;*从Word生成文档*&#x200B;模板将数据动态合并到您的文档中。

### 审阅文档模板

从OneDrive中的示例文件中打开&#x200B;*WordDocument02_.docx*。 Word文档包含多个不同的文本标记，这些标记表示数据填充到文档中的位置。

### 添加要触发的参数

要将动态数据推送到文档中，您需要为触发器创建一些参数以提示输入值。

1. 编辑工作流时，请选择&#x200B;**[!UICONTROL 手动触发工作流]**&#x200B;以展开操作。
1. 选择&#x200B;**[!UICONTROL 添加输入]**。
1. 选择&#x200B;**[!UICONTROL 文本]**。
1. 将字段命名为&#x200B;*名字*。

重复步骤2-4以添加以下字段：

* 姓氏
* 薪金

![使用参数字段在Power Automate中触发](assets/triggerParametersInPowerAutomate.png)

### 获取模板的文件内容

要生成文档，您首先需要获取Word模板的文件内容。

1. 在Power Automate中，选择+ **[!UICONTROL 新步骤]**。
1. 在搜索栏中搜索&#x200B;*OneDrive*。
1. 通过选择&#x200B;**[!UICONTROL OneDrive for Business]**&#x200B;或&#x200B;**[!UICONTROL OneDrive]**，选择您的工作或个人OneDrive帐户。
1. 在搜索栏中搜索&#x200B;*获取文件内容*。
1. 在&#x200B;**[!UICONTROL 文件]**&#x200B;字段中，选择文件夹图标以导航到OneDrive中的&#x200B;*WordDocument02.docx*&#x200B;文件。

![从Microsoft Power Automate中的OneDrive获取文件内容操作](assets/getFileContentAction02.png)

### 从模板生成文档

1. 在Power Automate中，选择&#x200B;**[!UICONTROL +新步骤]**。
1. 在搜索栏中搜索&#x200B;*Adobe PDF服务*。
1. 选择&#x200B;**[!UICONTROL Adobe PDF服务]**。
1. 选择&#x200B;**[!UICONTROL 从Word模板生成文档]**&#x200B;操作。
1. 在&#x200B;**[!UICONTROL 模板文件名]**&#x200B;字段中，根据需要为文件命名，但文件必须以&#x200B;*.docx*&#x200B;结尾。

#### 合并数据

使用&#x200B;*从Word模板生成文档*&#x200B;操作，您可以使用动态内容将数据从工作流中以前的任何不同变量合并到文档中。

将下面的JSON数据复制到&#x200B;**合并数据**&#x200B;字段：

```
{
    "FirstName": "",
    "LastName": "",
    "Salary": ""
}
```

1. 将光标放在&#x200B;*FirstName*&#x200B;值的两个引号之间的字段中。
1. 使用“**[!UICONTROL 动态内容]**”面板，从“手动”触发器流操作中插入&#x200B;*名字*&#x200B;值。

   ![使用JSON中的数据标记生成文档](assets/generateDocumentJSONAction.png)

1. 对&#x200B;**[!UICONTROL LastName]**&#x200B;和&#x200B;**[!UICONTROL Salary]**&#x200B;字段重复步骤7-8。
1. 在“**[!UICONTROL 模板文件内容]**”字段中，使用“**[!UICONTROL 动态内容]**”面板从“*获取文件内容*”步骤插入“**[!UICONTROL 文件内容]**”值。

![从Power Automate中的Word模板操作生成文档，所有值均已完成](assets/generateDocumentJSONActionCompleted.png)

>[!TIP]
>
>*从Word模板*&#x200B;生成文档操作使用Adobe的Document Generation API。 如果您想详细了解如何创建模板，以下是一些资源：
>
>* [了解有关AdobeDocument Generation的更多信息](https://developer.adobe.com/document-services/apis/doc-generation/)
>* [Microsoft Word的Document Generation TaggerAdobe](https://appsource.microsoft.com/en-US/product/office/WA200002654)
>* [Adobe的Document Generation API文档](https://developer.adobe.com/document-services/docs/overview/document-generation-api/)

### 将文件保存到OneDrive

生成文档后，您可以将文件保存回OneDrive。

1. 在Power Automate中，选择&#x200B;**+ [!UICONTROL 新步骤]**。
1. 在搜索栏中搜索&#x200B;*OneDrive*。
1. 通过选择&#x200B;**[!UICONTROL OneDrive for Business]**&#x200B;或&#x200B;**[!UICONTROL OneDrive]**，选择您的工作或个人OneDrive帐户。
1. 在搜索栏中搜索&#x200B;*创建文件*。
1. 选择&#x200B;**[!UICONTROL 创建文件]**。
1. 在&#x200B;**[!UICONTROL 文件夹路径]**&#x200B;字段中，选择文件夹图标以指定将文件保存在OneDrive中的位置。
1. 在&#x200B;**[!UICONTROL 文件名]**&#x200B;字段中，设置文件名。 由于输出为PDF，因此您的文件名必须以.pdf扩展名结尾。
1. 使用&#x200B;**[!UICONTROL 动态内容]**&#x200B;面板将PDF文件内容变量插入&#x200B;**[!UICONTROL 文件内容]**&#x200B;字段。

### Try流

![在Microsoft Power Automate中运行流屏幕以提示输入内容](assets/runFlowParameters.png)

1. 选择&#x200B;**[!UICONTROL 保存]**。
1. 选择&#x200B;**[!UICONTROL 测试]**。
1. 选择&#x200B;**[!UICONTROL 手动]**，然后选择&#x200B;**[!UICONTROL 保存并测试]**。
1. 选择&#x200B;**[!UICONTROL “继续”]**。
1. 输入&#x200B;*名字*、*姓氏*&#x200B;和&#x200B;*薪金*&#x200B;的值。
1. 选择&#x200B;**[!UICONTROL 运行流]**。

在OneDrive文件夹中，现在可以看到从WordPDF生成的文档。 在OneDrive中打开PDF文档时，您会看到数据合并到文本标记位置。


## 第3部分：将PDF合二为一

生成一个Word文档并将其转换为PDF后，下一步就是将多个PDF文档组合在一起。

>[!NOTE]
>
>在之前的操作中，您将文档的一个副本保存为OneDrive中的文件。 为了使用合并PDF等工具，您无需将文件保存到OneDrive。 相反，您可以将输出从一个操作直接传递到下一个操作，这比在每个操作后存储到OneDrive更好。 但出于演示目的，您正在将这些文件保存到OneDrive。

### 添加合并PDF步骤

1. 编辑工作流时，请选择&#x200B;**[!UICONTROL +下一步]**&#x200B;以在工作流末尾添加操作。
1. 在搜索栏中搜索&#x200B;*Adobe PDF服务*。
1. 选择&#x200B;**[!UICONTROL Adobe PDF服务]**。
1. 选择&#x200B;**[!UICONTROL 合并PDF]**&#x200B;操作。
1. 在&#x200B;**[!UICONTROL 合并PDF文件名]**&#x200B;字段中，输入所需的文件名（即&#x200B;*CombinedDocument.pdf*）。
1. 在&#x200B;**[!UICONTROL 文件内容–1]**&#x200B;字段中，使用&#x200B;**[!UICONTROL 动态内容]**&#x200B;面板从&#x200B;**[!UICONTROL 将Word转换为PDF]**&#x200B;步骤中插入&#x200B;*PDF文件内容*&#x200B;值。
1. 要添加下一个文档，请选择&#x200B;**+ [!UICONTROL 添加新项]**。
1. 在&#x200B;**[!UICONTROL 文件内容 — 2]**&#x200B;字段中，使用&#x200B;**[!UICONTROL 动态内容]**&#x200B;面板插入&#x200B;*从Word模板生成文档*&#x200B;步骤中的&#x200B;**[!UICONTROL 输出文件内容]**&#x200B;值。

![Microsoft Power Automate中的合并PDF操作](assets/mergePDFAction.png)

### 将合并的PDF保存到OneDrive

合并文档后，您可以将文档保存回OneDrive。

1. 在Power Automate中，选择&#x200B;**+ [!UICONTROL 新步骤]**。
1. 在搜索栏中搜索&#x200B;*OneDrive*。
1. 通过选择&#x200B;**[!UICONTROL OneDrive for Business]**&#x200B;或&#x200B;**[!UICONTROL OneDrive]**，选择您的工作或个人OneDrive帐户。
1. 在搜索栏中搜索&#x200B;*创建文件*。
1. 选择&#x200B;**[!UICONTROL 创建文件]**。
1. 在&#x200B;**[!UICONTROL 文件夹路径]**&#x200B;字段中，选择文件夹图标以指定将文件保存在OneDrive中的位置。
1. 在&#x200B;**[!UICONTROL 文件名]**&#x200B;字段中，设置文件名。 由于输出为PDF，因此您的文件名必须以.pdf结尾。
1. 在&#x200B;**[!UICONTROL 文件内容]**&#x200B;字段中，使用&#x200B;**[!UICONTROL 动态内容]**&#x200B;面板插入&#x200B;**[!UICONTROL 合并PDF]**&#x200B;步骤中的&#x200B;*PDF文件内容*&#x200B;值。

   ![Microsoft Power Automate中的流概述](assets/flowOverviewSavedMergedDocument.png)

### Try流

1. 选择&#x200B;**[!UICONTROL 保存]**。
1. 选择&#x200B;**[!UICONTROL 测试]**。
1. 选择&#x200B;**[!UICONTROL 手动]**，然后选择&#x200B;**[!UICONTROL 保存并测试]**。
1. 选择&#x200B;**[!UICONTROL “继续”]**。
1. 输入&#x200B;*名字*、*姓氏*&#x200B;和&#x200B;*薪金*&#x200B;的值。
1. 选择&#x200B;**[!UICONTROL 运行流]**。

在OneDrive文件夹中，您将看到合并后的PDF，其中包含来自第一个和第二个文档的页面。

## 第4部分：ProtectPDF文档

生成文档后，可在保存到OneDrive之前执行额外步骤，以保护文档免受编辑。

### 保护 PDF

1. 在Power Automate中编辑工作流时，在&#x200B;**[!UICONTROL 合并PDF]**&#x200B;操作和&#x200B;**[!UICONTROL 创建文件3]**&#x200B;操作之间选择&#x200B;**+**。

   ![两个动作之间加号以添加新动作](assets/addActionToProtect.png)

1. 选择&#x200B;**[!UICONTROL 添加操作]**。
1. 在搜索栏中搜索&#x200B;*Adobe PDF服务*。
1. 选择&#x200B;**[!UICONTROL Adobe PDF服务]**。
1. 选择&#x200B;**[!UICONTROL 从查看中选择ProtectPDF]**&#x200B;操作。
1. 在&#x200B;**[!UICONTROL 文件名]**&#x200B;字段中，将名称设置为所需的名称（只要它以.pdf扩展名结尾即可）。
1. 将&#x200B;**[!UICONTROL 密码]**&#x200B;字段设置为您指定的密码以打开文档。
1. 在&#x200B;**[!UICONTROL 文件内容]**&#x200B;字段中，使用&#x200B;**[!UICONTROL 动态内容]**&#x200B;面板从&#x200B;**[!UICONTROL 合并PDF]**&#x200B;步骤插入&#x200B;*PDF文件内容*&#x200B;值。

### 更新保存到OneDrive

文档受到保护后，您可以将文件保存回OneDrive中。 在此示例中，您将使用新的&#x200B;*文件内容*&#x200B;值更新先前存在的&#x200B;**创建文件3**&#x200B;操作。

1. 在&#x200B;**[!UICONTROL 创建文件3]**&#x200B;操作的&#x200B;**[!UICONTROL 文件内容]**&#x200B;字段中选择光标。
1. 使用“**[!UICONTROL 动态内容]**”面板从&#x200B;**从查看的ProtectPDF**&#x200B;步骤插入&#x200B;*PDF文件内容*&#x200B;值。

### Try流

1. 选择&#x200B;**[!UICONTROL 保存]**。
1. 选择&#x200B;**[!UICONTROL 测试]**。
1. 选择&#x200B;**[!UICONTROL 手动]**，然后选择&#x200B;**[!UICONTROL 保存并测试]**。
1. 选择&#x200B;**[!UICONTROL “继续”]**。
1. 输入&#x200B;*名字*、*姓氏*&#x200B;和&#x200B;*薪金*&#x200B;的值。
1. 选择&#x200B;**[!UICONTROL 运行流]**。

在OneDrive文件夹中，您会看到现在提示您输入密码以查看PDF的组合文档。

## 后续步骤

在本教程中，您将Word文档转换为PDF，基于数据生成文档，将文档合并在一起，并使用密码保护。 要了解更多信息，请浏览Microsoft Power Automate中Adobe PDF Services连接器中提供的一些其他操作：

* 查看Microsoft Power Automate中可用的预创建模板。
* 了解Adobe技术博客上的[文章](https://medium.com/adobetech/tagged/microsoft-power-automate)。
* 查看[文档](https://developer.adobe.com/document-services/docs/overview/document-generation-api/)以了解Document Generation APIAdobe。
