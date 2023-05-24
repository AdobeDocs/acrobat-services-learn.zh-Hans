---
title: 管理员工聘用信
description: 了解如何生成一封录用通知书，将其发送给新员工进行签名
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8096.jpg
kt: 8096
exl-id: 92f955f0-add5-4570-aa3a-ea63055dadb2
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1794'
ht-degree: 2%

---

# 管理员工聘用信

![用例英雄横幅](assets/UseCaseOfferHero.jpg)

员工聘用信是员工与您的组织建立关系的首批体验之一。 因此，您希望确保您的优惠信与品牌相符，但您不希望每次都需要在文字处理器中从头开始构建信件。 [!DNL Adobe Acrobat Services] API提供了一种快速、轻松且有效的方法来处理 [为新员工生成和提供录用通知书](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)的

## 您可以学到的内容

此实际操作教程将逐步介绍如何设置Node Express项目，该项目将显示Web表单，供用户填充员工详细信息。 这些详细信息使用 [!DNL Acrobat Services] 通过Web以PDF身份生成聘用信，此聘用信可以使用Adobe Sign API发送给客户以供签名。

## 相关API和资源

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe文档生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [文档生成标记文字加载项](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin)

* [项目示例](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)

## 开始使用

[Node.js](https://nodejs.org/) 是编程平台。 它随附了大量的库，如Express Web服务器。 [下载Node.js](https://nodejs.org/en/download/) 然后按照以下步骤安装这个强大的开源开发环境。

要在Node.js中使用Adobe文档生成API，请转至 [文档生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html) 访问您的帐户或注册一个新帐户。 您的帐户是 [免费延期六个月，然后按使用付费](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 每个文档交易只需0.05美元，因此您可以尝试无风险操作，然后仅根据公司发展情况付费。

登录 [Adobe Developer Console](https://console.adobe.io/)，请单击 **[!UICONTROL 创建新项目]**&#x200B;的 默认情况下，项目命名为“项目1”。 单击 **[!UICONTROL 编辑项目]** 按钮，并将名称更改为“录用通知书生成器”。 屏幕中心有一个 **[!UICONTROL 开始使用新项目]** 部分。 要为项目启用安全性，请执行以下步骤：

单击 **添加API**&#x200B;的 您会看到许多可供选择的API。 在 **[!UICONTROL 按产品筛选]** 部分，选择 **[!UICONTROL Document Cloud]**，然后单击。 **[!UICONTROL 下一个]**&#x200B;的

现在，可生成用于访问API的凭据。 凭据为JSON Web令牌([JWT](https://jwt.io/))图标：安全通信的开放标准。 如果您熟悉JWT并且已经生成密钥，则可以在此处上传公钥。 或者，通过选择 **选项1** 让Adobe为您生成密钥。

![生成凭据的屏幕截图](assets/offer_1.png)

单击 **[!UICONTROL 生成密钥对]** 按钮。 您将获得一个要下载的config.zip文件。 解压缩存档文件。 它包含两个文件：certificate_pub.crt和private.key。 确保后者是安全的，因为它包含您的私有凭据，并且可能用于在超出您的控制时生成虚假文档。

单击&#x200B;**[!UICONTROL 下一步]**。否，启用对PDF生成API的访问。 在 **[!UICONTROL 选择产品配置文件]** 屏幕，检查 **[!UICONTROL 企业PDF服务开发人员]**，然后单击 **[!UICONTROL 保存配置的API]** 按钮。 现在，您就可以开始使用API了。

## 设置项目

设置Node项目以运行代码。 本示例使用 [Visual Studio代码](https://code.visualstudio.com/) （VS代码）作为编辑器。 创建一个名为“letter-generator”的文件夹，并在VS代码中打开它。 从 **[!UICONTROL 文件]** 菜单，选择 **[!UICONTROL 终端]** \> **[!UICONTROL 新终端]** 打开此文件夹中的shell。 输入以下内容，检查是否已安装Node且是否已安装到您的路径上：

```
node -v
```

您应该会看到所安装的节点的版本。

现在您已安装开发环境，接下来可以创建项目。

首先，使用Node Package Manager(npm)初始化项目。 键入以下内容：

```
npm init
```

系统会向您询问有关Node项目的一些问题。 您可以跳过大多数问题，但请确保项目名称为“letter-generator”，而入口点为 **index.js**&#x200B;的 选择 **是** 完成项目初始化。

您现在有package.json文件。 Node使用此文件来组织项目。 在创建index.js之前，必须使用以下命令添加Adobe库：

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

项目中应该添加一个名为node_modules的新文件夹。 此文件夹是下载所有库（在节点中称为依赖项）的地方。 package.json文件也会更新，并引用Adobe PDF Services。

现在，您希望将Express安装为您的轻量级Web框架。 输入以下命令：

```
npm install express –save
```

与以前一样，package.json的依赖项部分会相应地更新。

## 创建录用通知书模板

现在，在项目根目录中，创建一个名为“app.js”的文件。 在这里放入以下起始代码：

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

注意get route返回 **index.html** 文件。 我们创建一个HTML文件，使用该名称和以下简单表单。 您以后可以根据需要添加CSS样式和其他设计元素。 此表单获取候选人用来生成欢迎信的基本详细信息：

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

您应看到消息“Candidate offer letter app listening on port 8000”。 如果您打开浏览器，可以 <http://localhost:8000/>时，表单应如下所示：

![Web表单屏幕截图](assets/offer_2.png)

请注意，表单将发布到自身。 如果您填写数据并单击 **生成字母，** 您应在控制台上看到以下信息：

```
Got body: { firstname: 'John',
lastname: 'Doe',
salary: '887888',
startdate: '2021-04-01' }
```

将此控制台日志记录替换为Web服务调用 [!DNL Acrobat Services]的 首先，您必须对信息建立基于JSON的模型。 此模型的格式如下所示：

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

您可以根据需要使此模型更加精细，但是对于本教程，请坚持使用这个简单的示例。 此表单没有验证，因为这超出了本文的范围。 要将表单正文转换为上述数据模型，请将app.post处理函数方法更改为使用以下代码：

```
app.post('/', (req, res) => {
const docModel = {'offer_letter': req.body};
generateLetter(docModel);
res.sendStatus(200);
});
```

第一行以所需的格式放置您的JSON数据。 现在，您可以将此数据传递给generateLetter函数。 停止服务器，将以下代码粘贴到app.js末尾。 此代码将Word文档作为模板，并使用JSON文档中的信息填充占位符。

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

有很多代码要解压缩 让我们先讨论主要部分：在 `documentMergeOperation`的 在此部分，您可以提取您的JSON数据并将其与Word文档模板合并。 您可以使用 [示例Adobe站点](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) 作为参考，下面我们就来创建一个简单的示例 打开Word并创建新的空白文档。 您可以根据自己的喜好对其进行自定义，但至少可以具有如下内容：

尊敬的X:

我们非常乐意为您提供每年X美元的职位。 开始日期将是X。

欢迎

将文档另存为“OfferLetter-Template.docx”，并保存在项目根文件夹中名为“resources”的文件夹中。 注意文档中的三个X。 这些X是您的JSON信息的临时占位符。 虽然可以使用特殊语法来替换这些占位符，但Adobe提供了一个可简化此任务的Word插件。 要安装此加载项，请转到Adobe [文档生成标记文字加载项](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin) 站点。

在您的OfferLetter-Template中，单击新的 **文档生成** 按钮。 侧面板随即打开。 单击&#x200B;**开始**。系统会为您提供一个要在示例JSON数据中粘贴的文本区域。 将上面的JSON的“offer-data”片段复制到文本区域。 其外观应如下所示：

![字母和代码的屏幕截图](assets/offer_3.png)

单击 **生成标签** 按钮。 您会看到一个标记的下拉菜单，可将其插入到文档中的相应点中。 高亮显示文档中的第一个X，然后选择 **[!UICONTROL firstname]**&#x200B;的 单击 **[!UICONTROL 插入文本]** “尊敬的X，”更改为“尊敬的 ```{{`offer_letter`.firstname}}```，”。 此标签的格式正确 `documentMergeOperation`的 请继续操作，在相应的X处添加其余三个标记。 别忘了保存OfferLetter-template.docx。 它应该如下所示：

尊敬的 ```{{`offer_letter`.firstname}} {{`offer_letter`.lastname}}```，

我们非常乐意为您提供一个$ ```{{`offer_letter`.salary}}``` 一年。 您的开始日期将为 ```{{`offer_letter`.startdate}}```的

欢迎

现在，Word模板具有与JSON格式匹配的标记。 例如， ```{{`offer_letter`.`firstname`}}``` 在Word文档的开头，将替换为JSON数据的“firstname”部分中的值。

返回到 `generateLetter` 函数。 要保护您的REST调用，请在项目根目录中创建一个名为pdftools-api-credentials.json的新文件。 粘贴以下JSON数据，并使用您网站的“服务帐户(JWT)”部分中的详细信息调整该数据 [开发人员控制台](https://console.adobe.io/)的

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

* 可直接从 **[!UICONTROL 凭据详细信息]** 部分。

* 帐户ID是 **技术帐户ID**&#x200B;的

* 将之前生成的private.key文件复制到项目中，并在pdftools-api-credentials.json文件的private_key_file部分输入其名称。 如果需要，可以在此处放置一个私钥文件的路径。 请记住确保它的安全，因为它可能会在失控后被滥用。

要生成PDF并填写JSON数据，请返回 **[!UICONTROL 输入候选人详细信息]** Web表单并发布一些数据。 由于需要从Adobe下载文档，这可能需要一些时间，但您应该将名为OfferLetter.pdf的文件放入名为“输出”的新文件夹中。

## 后续步骤

就是这样！ 这只是开始。 如果您研究Word加载项的“文档生成”选项卡的“高级”部分，您会注意到，并非所有占位符标记都来自关联的JSON数据。 您还可以添加签名标签。 这些标签允许您获取生成的文档并将其上载到 [Adobe Sign](https://acrobat.adobe.com/ca/en/sign.html) 以便交付和签名给新员工。 阅读Adobe Sign API快速入门以了解如何执行此操作。 此过程类似，因为您正在使用受JWT令牌保护的REST调用。

当组织必须 [增加季节性雇佣](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html) 跨多个位置的员工的数量。 如所示，主要的流程是通过在线应用程序从候选人那里获取数据。 数据用于填充录用通知书的字段，并发送以供电子签名。

[!DNL Adobe Acrobat Services] 可免费使用6个月，然后 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 每个文档交易仅需0.05美元，因此您可以试用该计划，并根据业务的增长来调整录用通知书工作流程。 要 [开始使用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
构建自己的模板， [注册您的开发人员帐户](https://www.adobe.io/)的
