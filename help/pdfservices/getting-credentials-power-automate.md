---
title: 获取Microsoft Power Automate的凭据
description: 了解如何获取凭据以开始使用或试用Adobe PDF服务
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-10382
thumbnail: KT-10382.jpg
exl-id: 68ec654f-74aa-41b7-9103-44df13402032
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '872'
ht-degree: 2%

---

# 获取Microsoft Power Automate的凭据

[Microsoft Power Automate](https://powerautomate.microsoft.com/)为公民开发人员和开发人员提供了一种强大的自动化流程来改进其业务，而无需编写代码。 作为[[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services)的一部分，[Adobe PDF Services](https://us.flow.microsoft.com/en-us/connectors/shared_adobepdftools/adobe-pdf-services/)连接器允许用户在Microsoft Power Automate中执行Adobe PDF Services API中的任何可用操作。

在本教程中，了解如何获取凭据以开始使用或试用Adobe PDF服务。 根据您是试用用户还是现有客户，本教程将逐步引导您完成获取凭据的相应步骤。

## Microsoft Power Automate用户如何开始使用Adobe PDF Services连接器？

现有Microsoft Power Automate用户可以[获取Adobe PDF Services的试用凭据](https://www.adobe.com/go/powerautomate_getstarted_cn)。 上面的链接是一个特殊的注册链接，用于专门为Microsoft Power Automate用户帮助完成此流程。

![Adobe Developer用户登录](assets/credentials_1.png)


>[!IMPORTANT]
> 如果您要登录进行试用，则必须使用Adobe ID，而非Enterprise ID。 如果您不是Adobe PDF Services API的当前订阅者，但尝试使用您的Enterprise ID登录，则可能会收到权限错误，因为您的企业未授权您使用Adobe PDF Services API。 因此，建议您使用免费的个人Adobe ID。
>

1. 登录后，系统会提示您为新凭据选择一个名称。 输入您的&#x200B;*凭据名称*。
1. 选中该复选框以同意开发人员条款。
1. 选择&#x200B;**[!UICONTROL 创建凭据]**。

   ![命名凭据](assets/credentials_2.png)

这些凭据涵盖五个不同的值：

* 客户 ID（API 密钥）
* 客户密码
* 组织 ID
* 技术帐户 ID
* Base64（编码私钥）

![新凭据](assets/credentials_3.png)

包含所有这些值的JSON文件也会自动下载到您的系统。 此文件名为`pdfservices-api-pa-credentials.json`，其外观类似于：

```json
{
 "client_id": "client id value",
 "client_secret": "client secret value",
 "organization_id": "organized id value",
 "account_id": "account id value",
 "base64_encoded_private_key": "base64 version of the private key"
}
```

将此文件存储在安全位置，因为无法再次获取私钥的副本。

### 在Microsoft Power Automate中添加连接

现在，您已经有了凭据，可以开始在Microsoft Power Automate工作流中使用它们。

1. 在侧边栏菜单中，打开&#x200B;**[!UICONTROL 数据]**&#x200B;菜单并选择&#x200B;**连接**：

   ![ Microsoft Power Automate站点中的“连接”菜单](assets/credentials_4.png)

1. 选择&#x200B;**+ [!UICONTROL 新建连接]**。

1. 下一个屏幕显示可能的连接类型列表。 在右上角输入“adobe”以筛选选项：

   ![Adobe连接列表](assets/credentials_5.png)

1. 选择&#x200B;**[!UICONTROL Adobe PDF Services （预览）]**。
1. 在模态窗口中，输入之前生成的所有五个值。 完成后选择“**[!UICONTROL 创建]**”。

   ![用于输入凭据信息的表单字段](assets/credentials_6.png)

您现在可以在Microsoft Power Automate中使用Adobe PDF服务。

### 创建凭据后访问凭据

如果您已创建凭据但将下载的凭据放错了位置，则可以在[Adobe Developer Console](https://developer.adobe.com/console)中再次检索它们。

1. 登录到[Adobe Developer Console](https://developer.adobe.com/console)后，首先查找并选择您的项目。
1. 在&#x200B;*凭据*&#x200B;下的左侧菜单中，选择&#x200B;**服务帐户(JWT)**：

   ![现有凭据](assets/credentials_7.png)

1. 请注意此处显示的五个值：*客户端ID*、*客户端密钥*、*技术帐户ID*、*技术帐户电子邮件*&#x200B;和&#x200B;*组织ID*。

很遗憾，您无法下载以前的私钥，但可以使用“生成公钥/私钥对”按钮创建新的私钥。

## 使用现有Adobe PDF Services凭据

如果您具有从[!DNL Adobe Acrobat Services]网站生成的现有Adobe PDF Services API凭据，则可以将其与Microsoft Power Automate结合使用。 如果您在注册时下载了SDK，则您的现有凭据采用JSON文件的形式，很可能名为`pdfservices-api-credentials.json`。 该JSON文件包含创建连接凭据时所需的五个密钥。 将JSON文件中的每个值复制到相应的连接字段中。

您的私钥值来自名为`private.key`的第二个文件。

您也可以从Adobe Developer Console中获取上述值。

## [!DNL Adobe Acrobat Services]用户如何开始使用Microsoft Power Automate？

若要开始使用Power Automate，请先转到<https://powerautomate.microsoft.com>并使用“免费开始”按钮。 如果您没有Microsoft帐户，则需要创建一个。 登录后，您会看到Power Automate仪表板。

![PA仪表板，初始视图](assets/credentials_8.png)

如本教程开始部分所述，创建新流程、添加步骤并查找Adobe PDF服务。 选择一个操作，系统可能会警告您需要使用高级帐户。

![高级帐户警告](assets/credentials_9.png)

如上面的屏幕截图所示，您可以切换到工作帐户，也可以设置新的组织帐户。 完成此操作后，您便可以添加Adobe PDF服务操作。

有关使用[!DNL Adobe Acrobat Services]创建第一个Microsoft Power Automate工作流的更深入的信息，请参阅[在Microsoft Power Automate中创建您的第一个工作流程](https://experienceleague.adobe.com/zh-hans/docs/acrobat-services-learn/tutorials/pdfservices/create-workflow-power-automate)。

## 其他资源

为了帮助您了解更多信息，以下是其他资源的列表：

* 首先是Adobe PDF Services Power Automate文档： <https://docs.microsoft.com/en-us/connectors/adobepdftools/>。 这些资源补充了您在此处学到的内容。
* 需要示例？ 您可以找到大量演示PDF服务的[Power Automate模板](https://powerautomate.microsoft.com/en-us/connectors/details/shared_adobepdftools/adobe-pdf-services/)。
* 我们的实时视频内容[纸质剪辑](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF)还包含演示Power Automate用法的视频。
* [Adobe技术博客](https://medium.com/adobetech/tagged/microsoft-power-automate)包含许多关于使用Power Automate的文章。
* 最后，请务必同时查阅核心[PDF服务](https://developer.adobe.com/document-services/docs/overview/)文档。
