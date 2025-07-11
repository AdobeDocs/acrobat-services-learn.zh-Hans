---
title: 管理员工聘用信
description: 了解如何生成可发送给新员工以供其签名的聘用信
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8096
thumbnail: KT-8096.jpg
exl-id: 92f955f0-add5-4570-aa3a-ea63055dadb2
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1714'
ht-degree: 0%

---

# 管理员工聘用信

![用例主横幅](assets/UseCaseOfferHero.jpg)

员工工作邀请函是员工最初在公司中获得的体验之一。 因此，要确保优惠字母符合品牌标准，但您不必每次都从头开始在文字处理器中构建字母。 [!DNL Adobe Acrobat Services] API提供了一种快速、轻松且有效的方法来处理[生成优惠信函并向新员工提供优惠信函](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/employee-offer-letters)中的关键部分。

## 您可以学到的内容

此实际操作教程逐步指导您设置一个Node Express项目，该项目会显示Web表单，以供用户填充员工详细信息。 这些详细信息使用[!DNL Acrobat Services]通过Web生成优惠信函，作为PDF，可交付给使用Adobe Sign API进行签名的客户。

## 相关的API和资源

* [PDF服务API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe的Document Generation API](https://developer.adobe.com/document-services/apis/doc-generation)

* [Adobe Sign API](https://developer.adobe.com/adobesign-api/)

* [Document Generation Tagger Word加载项](https://developer.adobe.com/document-services/docs/overview/document-generation-api/wordaddin)

* [项目示例](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/employee-offer-letters)

## 开始使用

[Node.js](https://nodejs.org/)是编程平台。 它随附了大量的库，如Express Web服务器。 [下载Node.js](https://nodejs.org/en/download/)并按照步骤安装这个出色的开源开发环境。

要在Node.js中使用Adobe的Document Generation API，请转到[Document Generation API](https://developer.adobe.com/document-services/apis/doc-generation)站点以访问您的帐户或注册新帐户。 您的帐户[可免费使用六个月，之后即可即用即付](https://developer.adobe.com/document-services/pricing/main)，每笔文档交易只需支付0.05美元，因此您可以试用该帐户，无需承担任何风险，之后只需随着公司发展支付费用即可。

登录[Adobe Developer Console](https://developer.adobe.com/console/)后，单击&#x200B;**[!UICONTROL 创建新项目]**。 默认情况下，项目被命名为“项目1”。 单击&#x200B;**[!UICONTROL 编辑项目]**&#x200B;按钮并将名称更改为“聘用信函生成器”。 屏幕中央是&#x200B;**[!UICONTROL 开始使用新项目]**&#x200B;部分。 要为项目启用安全性，请执行以下步骤：

单击&#x200B;**添加API**。 您会看到一些API可供选择。 在&#x200B;**[!UICONTROL 按产品筛选]**&#x200B;部分中，选择&#x200B;**[!UICONTROL Document Cloud]**，然后单击&#x200B;**[!UICONTROL 下一步]**。

现在，生成凭据以访问API。 凭据采用JSON Web令牌([JWT](https://jwt.io/))的形式：安全通信的开放标准。 如果您熟悉JWT并且已经生成密钥，则可以在此处上传公钥。 或者，选择&#x200B;**选项1**&#x200B;以让Adobe为您生成密钥。

![生成凭据的屏幕截图](assets/offer_1.png)

单击&#x200B;**[!UICONTROL 生成密钥对]**&#x200B;按钮。 您将获得要下载的config.zip文件。 解压缩存档文件。 它包含两个文件：certificate_pub.crt和private.key。 确保后者是安全的，因为它包含您的私人凭据，并且在您控制之外可用于生成虚假文档。

单击&#x200B;**[!UICONTROL 下一步]**。否，启用对PDF生成API的访问。 在&#x200B;**[!UICONTROL 选择产品配置文件]**&#x200B;屏幕上，选中&#x200B;**[!UICONTROL 企业PDF服务开发人员]**，然后单击&#x200B;**[!UICONTROL 保存配置的API]**&#x200B;按钮。 现在，您可以开始使用API了。

## 设置项目

设置Node项目以运行代码。 此示例使用[Visual Studio代码](https://code.visualstudio.com/) （VS代码）作为编辑器。 创建一个名为“letter-generator”的文件夹，然后在VS代码中打开它。 从&#x200B;**[!UICONTROL 文件]**&#x200B;菜单中，选择&#x200B;**[!UICONTROL 终端]** \> **[!UICONTROL 新建终端]**&#x200B;以打开此文件夹中的shell。 通过输入以下内容，检查节点是否已安装并位于您的路径上：

```
node -v
```

您应该会看到所安装的Node的版本。

现在您已安装了开发环境，您可以继续创建项目了。

首先，使用节点包管理器(npm)初始化项目。 键入以下内容：

```
npm init
```

我们将询问您有关Node项目的一些问题。 您可以跳过大部分这类问题，但请确保项目名称是“letter-generator”，入口点是&#x200B;**index.js**。 选择“**是**”以完成项目初始化。

您现在有一个package.json文件。 Node使用此文件组织项目。 在创建index.js之前，必须添加包含以下内容的Adobe库
命令：

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

应将名为node_modules的新文件夹添加到项目中。 此文件夹是下载所有库（在节点中称为依赖项）的位置。 package.json文件也通过引用Adobe PDF服务进行了更新。

现在，您要将Express安装为您的轻量级Web框架。 输入以下命令：

```
npm install express –save
```

和以前一样，package.json的依赖关系部分也进行了相应更新。

## 创建聘用信模板

现在，在项目根目录中，创建一个名为“app.js”的文件。 我们在这里放入以下起始代码：

```
const express = require('express');
const bodyParser = require('body-parser');
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk')
const path = require('path');
const app = express();
const port = 8000;
app.use(bodyParser.urlencoded({ extended: true }));
app.get('/', (req, res) => {
res.sendFile(path.join(__dirname + '/index.html'));
});
app.post('/', (req, res) => {
console.log('Got body:', req.body);
res.sendStatus(200);
});
app.listen(port, () => {
console.log(`Candidate offer letter app listening on port ${port}!`)
});
```

请注意，获取路由返回&#x200B;**index.html**&#x200B;文件。 让我们创建一个具有以下简单形式的HTML文件，其名称是： 以后可以根据需要添加CSS样式和其他设计元素。 此表单采用应聘者生成欢迎信的基本详细信息：

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Offer Letter Generator</title>
</head>
<body>
<h1>Enter Candidate Details</h1>
<form action="" method="post">
<div>
<label for="firstname">First name: </label>
<input type="text" name="firstname" id="firstname" required>
</div>
<div>
<label for="lastname">Last name: </label>
<input type="text" name="lastname" id="lastname" required>
</div>
<div>
<label for="salary">Salary ($): </label>
<input type="number" name="salary" id="salary" required>
</div>
<div>
<label for="startdate">Start Date: </label>
<input type="date" name="startdate" id="startdate" required>
</div>
<div>
<input type="submit" value="Generate Letter">
</div>
</form>
</body>
</html>
```

使用以下命令运行Web服务器：

```
node app.js
```

您应该会看到“应聘者聘用信应用侦听端口8000”的消息。 如果您打开浏览器访问<http://localhost:8000/>，则表单应如下所示：

![Web表单的屏幕截图](assets/offer_2.png)

请注意，该表单会发布到其自身。 如果填写数据并单击&#x200B;**“生成信函”，**，您应该会在控制台上看到以下信息：

```
Got body: { firstname: 'John',
lastname: 'Doe',
salary: '887888',
startdate: '2021-04-01' }
```

将此控制台日志记录替换为对[!DNL Acrobat Services]的Web服务调用。 首先，您必须创建基于JSON的信息模型。 此模型的格式如下所示：

```
{
    "offer_letter": {
    "firstname": "John",
    "lastname": "Doe",
    "salary": "887888",
    "startdate": "2021-04-01"
    }
}
```

如果您愿意，您可以使此模型更加详细，但在本教程中，请继续看这个简单的示例。 此表单没有经过验证，因为这超出了本文的范围。 要将表单正文转换为上述数据模型，请将app.post handler方法更改为以下代码：

```
app.post('/', (req, res) => {
const docModel = {'offer_letter': req.body};
generateLetter(docModel);
res.sendStatus(200);
});
```

第一行以所需格式放置JSON数据。 现在将此数据传递给generateLetter函数。 停止服务器，并将以下代码粘贴到app.js的末尾。 此代码以Word文档作为模板，使用JSON文档中的信息填充占位符。

```
// Letter generation function
function generateLetter(jsonDataForMerge) {
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
// Create a new DocumentMerge options instance
const documentMerge = PDFToolsSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge,
documentMergeOptions.OutputFormat.PDF);
// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)
// Set operation input document template from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(
'resources/OfferLetter-Template.docx');
documentMergeOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile('output/OfferLetter.pdf'))
.catch(err => {
if(err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log(
'Exception encountered while executing operation', err);
} else {
console.log(
'Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

那里有很多代码要解压缩 我们先来了解一下主要部分：`documentMergeOperation`。 在此部分中，您可以获取JSON数据并将其与Word文档模板合并。 您可以使用Adobe站点[&#128279;](https://developer.adobe.com/document-services/apis/doc-generation#sample-blade)上的示例作为参考，但让我们创建您自己的简单示例。 打开Word并创建一个新的空白文档。 你可以随意进行自定义，但至少有以下特点：

尊敬的X：

我们很高兴为您提供每年$X的职位。 你的开始日期是X。

欢迎

将文档作为“OfferLetter-Template.docx”保存在项目根目录下名为“resources”的文件夹中。 请注意文档中的三个X。 这些X是您的JSON信息的临时占位符。 虽然可以使用特殊语法替换这些占位符，但Adobe提供了一个简化此任务的Word插件。 要安装加载项，请转到Adobe[Document Generation Tagger Word加载项](https://developer.adobe.com/document-services/docs/overview/document-generation-api/wordaddin)站点。

在您的OfferLetter-Template中，单击新的&#x200B;**Document Generation**&#x200B;按钮。 侧面板随即打开。 单击&#x200B;**开始使用**。 为您提供了要在示例JSON数据中粘贴的文本区域。 将JSON的“offer-data”片段从上方复制到文本区域。 它应该如下所示：

![字母和代码的屏幕截图](assets/offer_3.png)

单击&#x200B;**生成标签**&#x200B;按钮。 此时会显示一个标签下拉菜单，可将其插入到文档中的相应点上。 突出显示文档中的第一个X并选择&#x200B;**[!UICONTROL firstname]**。 单击&#x200B;**[!UICONTROL 插入文本]**，“尊敬的X，”已更改为“尊敬的```{{`offer_letter`.firstname}}```”。 此标记是`documentMergeOperation`的正确格式。 请继续操作，将其余三个标签添加到相应的X。 不要忘记保存OfferLetter-template.docx。 它应该如下所示：

尊敬的 ```{{`offer_letter`.firstname}} {{`offer_letter`.lastname}}```，

我们很荣幸为您提供每年$ ```{{`offer_letter`.salary}}```的职位。 您的开始日期将是```{{`offer_letter`.startdate}}```。

欢迎

现在，Word模板具有与JSON格式匹配的标记。 例如，将Word文档开头的```{{`offer_letter`.`firstname`}}```替换为JSON数据“firstname”部分中的值。

返回到您的`generateLetter`函数。 要保护REST调用的安全，请在项目根目录中创建一个名为pdftools-api-credentials.json的新文件。 粘贴以下JSON数据，并使用[开发人员控制台](https://developer.adobe.com/console/)的“服务帐户(JWT)”部分中的详细信息调整该数据。

```
{
"client_credentials": {
"client_id": "<YOUR_CLIENT_ID>",
"client_secret": "<YOUR_CLIENT_SECRET>"
},
"service_account_credentials": {
"organization_id": "<YOUR_ORGANIZATION_ID>",
"account_id": "<YOUR_TECHNICAL_ACCOUNT_ID>",
"private_key_file": "<PRIVATE_KEY_FILE_PATH>"
}
}
```

* 客户端ID、客户端密钥和组织ID可以直接从控制台的&#x200B;**[!UICONTROL 凭据详细信息]**&#x200B;部分中复制。

* 帐户ID是&#x200B;**技术帐户ID**。

* 将之前生成的private.key文件复制到项目中，并在的private_key_file部分输入其名称
pdftools-api-credentials.json文件。 如果您愿意，可以将私钥文件的路径放在此处。 请记得确保它安全，因为它一旦失控，就会被误用。

要生成已填写JSON数据的PDF，请返回到&#x200B;**[!UICONTROL 输入应聘者详细信息]** Web表单并发布一些数据。 必须从Adobe下载文档还需要一些时间，但标题为output的新文件夹中应包含一个标题为OfferLetter.pdf的文件。

## 后续步骤

就是这样！ 这只是开始。 如果您学习了Word插件的“Document Generation”选项卡的“高级”部分，则会注意到并非所有占位符标记都来自关联的JSON数据。 您还可以添加签名标签。 这些标记允许您获取生成的文档，并将其上传到[Adobe Sign](https://www.adobe.com/ca/sign.html)以进行传递并签名给新员工。 请阅读Adobe Sign API快速入门以了解如何执行该操作。 此过程与此类似，因为您使用的是使用JWT令牌保护的REST调用。

当组织必须[增加跨多个地点的季节性雇用](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/employee-offer-letters)员工时，上面提供的单个文档示例可用作申请的基础。 如图所示，主要流程是通过在线应用程序从候选者那里获取数据。 数据用于填充聘用信的字段，并将其发送以供电子签名。

[!DNL Adobe Acrobat Services]可免费使用六个月，然后[即付即用](https://developer.adobe.com/document-services/pricing/main)，每个文档交易仅需0.05 USD，因此您可以试用它并根据业务增长扩展您的优惠信工作流程。 [开始使用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
要构建您自己的模板，请[注册您的开发人员帐户](https://developer.adobe.com/)。
