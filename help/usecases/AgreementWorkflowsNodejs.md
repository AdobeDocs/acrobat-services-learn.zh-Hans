---
title: Node.js中的协议工作流程
description: '"[!DNL Adobe Acrobat Services] API可以轻松地将PDF功能整合到您的Web应用程序中”'
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-7473.jpg
kt: 7473
keywords: 特色
exl-id: 44a03420-e963-472b-aeb8-290422c8d767
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '2182'
ht-degree: 1%

---

# Node.js中的协议工作流程

![用例英雄横幅](assets/UseCaseAgreementHero.jpg)

许多业务应用程序和流程都需要参考建议和协议等文档。 PDF文档可确保文件更安全、更少修改。 它们还提供数字签名支持，以便您的客户可以快速轻松地完成文档。 [!DNL Adobe Acrobat Services] API可以轻松地将PDF功能整合到您的Web应用程序中。

## 您可以学到的内容

在本实际操作教程中，学习如何将PDF服务添加到Node.js应用程序以数字化协议进程。

## 相关API和资源

* [PDF服务API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF嵌入API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [项目代码](https://github.com/adobe/pdftools-node-sdk-samples)

## 设置 [!DNL Adobe Acrobat Services]

要开始使用，请设置要使用的凭据 [!DNL Adobe Acrobat Services]的 注册帐户并使用 [Node.js Quickstart](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#node-js) 在将您的凭据集成到更大的应用程序之前，验证您的凭据是否有效。

首先，获取Adobe开发人员帐户。 然后，在 [开始使用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK) 页面，选择 *开始使用* 选项。 您可以注册他们的免费试用版，试用版提供1,000个文档事务，可在六个月内使用。

![创建新凭据的图像](assets/AWNjs_1.png)

在下面的“创建新凭据”页面中，将提示您确定PDF嵌入API和PDF服务API。

选择 *PDF服务API*&#x200B;的

输入应用程序的名称，并选中标记为 *创建个性化代码示例*&#x200B;的 选中此框会自动将您的凭据嵌入到代码示例中。 如果不选中此框，您必须手动将凭据添加到应用程序。

选择 *Node.js* ，然后单击 *创建凭据*&#x200B;的

几分钟后，.zip文件开始下载，其中包含一个包含凭据的示例项目。 的Node.js包 [!DNL Acrobat Services] 已包含在示例项目代码中。

![选择PDF服务API凭据的图像](assets/AWNjs_2.png)

## 手动配置示例项目

如果选择不从“创建新凭据”页面下载示例项目，则还可以手动设置项目。

从以下位置下载代码（未嵌入您的凭据） [GitHub](https://github.com/adobe/pdftools-node-sdk-samples)的 如果您使用此版本的代码，则必须先将凭据添加到pdftools-api-credentials.json文件，然后才能使用：

```
{
  "client_credentials": {
    "client_id": "<client_id>",
    "client_secret": "<client_secret>"
  },
  "service_account_credentials": {
    "organization_id": "<organization_id>",
    "account_id": "<technical_account_id>",
    "private_key_file": "<private_key_file_path>"
  }
}
```

对于您自己的应用程序，您需要将私钥文件和凭据文件复制到应用程序源中。

必须安装Node.js包 [!DNL Acrobat Services]的 要安装包，请使用以下命令：

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

## 设置日志记录

此处的示例将Express用于应用程序框架。 它们还使用log4js记录应用程序。 使用log4js，您可以轻松地将日志记录定向到控制台或输出到文件：

```
const log4js = require('log4js');
const logger = log4js.getLogger();
log4js.configure( {
    appenders: { fileAppender: { type:'file', filename: './logs/applicationlog.txt'}},
    categories: { default: {appenders: ['fileAppender'], level:'info'}}
});
 
logger.level = 'info';
logger.info('Application started')
```

上面的代码将记录的数据写入中的文件。/logs/applicationlog.txt。 如果您希望将其写入控制台，则可以注释掉log4js.configure的调用。

## 将Word文件转换为PDF

协议和建议通常是在工作效率应用程序中编写的，如Microsoft Word。 要接受此格式的文档并将文档转换为PDF，您可以为应用程序添加功能。 让我们看一下如何在Express应用程序中上传和保存文档，并将其保存到文件系统。

在应用程序的HTML中，添加一个文件元素和一个用于开始上传的按钮：

```
<input type="file" name="source" id="source" />
<button onclick="upload()" >Upload</button>
```

在页面的JavaScript中，使用fetch函数异步上传文件：

```
function upload()
{
  let formData = new FormData();
  var selectedFile = document.getElementById('source').files[0];
  formData.append("source", selectedFile);
  fetch('documentUpload', {method:"POST", body:formData});
}
```

选择一个文件夹以接受上传的文件。 应用程序需要指向此文件夹的路径。 使用与\_\_dirname连接的相对路径查找绝对路径：

```
const uploadFolder = path.join(__dirname, "../uploads");
```

由于文件是通过帖子提交的，因此您必须在服务器端回复帖子消息：

```
router.post('/', (req, res, next) => {
  console.log('uploading')
  if(!req.files || Object.keys(req.files).length === 0) {
  return res.status(400).send('No files were uploaded');
  }
    
  const uploadPath = path.join(uploadFolder, req.files.source.name);
  var buffer = req.files.source.data;
  var result = {"success":true};
  fs.writeFile(uploadPath, buffer, 'binary', (err)=> {
    if(err) {
      result.success = false;
    }
    res.json(result);
  });       
});
```

执行此函数后，文件将保存在applications upload文件夹中并可供进一步处理。

接下来，将文件从本机格式转换为PDF。 您之前下载的示例代码包含一个名为 `create-pdf-from-docx.js` 用于将文档转换为PDF。 以下函数， `convertDocumentToPDF`，获取已上传的文档并将其转换为其他文件夹中的PDF:

```
function convertDocumentToPDF(sourcePath, destinationPath)
{    
  try {   
    const credentials = PDFToolsSDK.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdftools-api-credentials.json")
    .build();
 
    const executionContext = 
      PDFToolsSDK.ExecutionContext.create(credentials),
    createPdfOperation = PDFToolsSDK.CreatePDF.Operation.createNew();
 
    const docxReadableStream = fs.createReadStream(sourcePath);
    const input = PDFToolsSDK.FileRef.createFromStream(
      docxReadableStream, 
      PDFToolsSDK.CreatePDF.SupportedSourceFormat.docx);
    createPdfOperation.setInput(input);
 
    createPdfOperation.execute(executionContext)
    .then(result => result.saveAsFile(destinationPath))
    .catch(err => {        
      logger.erorr('Exception encountered while executing operation');        
    })
  }
  catch(err) {        
    logger.error(err);
  }
}
```

您可能会注意到代码具有的一般模式：

代码生成凭据对象和执行上下文，初始化某些操作，然后使用执行上下文执行操作。 您可以在整个示例代码中看到此模式。

通过对上载函数进行一些添加以使其调用此函数，用户上载的Word文档现在会自动转换为PDF。

以下代码构建转换的目标路径PDF并启动转换：

```
const documentFolder = path.join(__dirname, "../docs");
var extPosition = req.files.source.name.lastIndexOf('.') - 1;
if(extPosition < 0 ) {
  extPosition = req.files.source.name.length
}
const destinationName = path.join(documentFolder,  
  req.files.source.name.substring(0, extPosition) + '.pdf');
console.log(destinationName);
 
logger.info('converting to ${destinationName}')
  convertDocumentToPDF(uploadPath, destinationName);
```

## 将其他文件类型转换为PDF

文档工具包将其他格式转换为PDF，例如静态HTML，这是另一种常见的文档类型。 该工具包接受打包为.zip文件的HTML文档，其中包含文档引用的所有资源（CSS文件、图像和其他文件），这些资源位于同一.zip文件中。 HTML文档本身必须命名为index.html，并放置在.zip文件的根目录中。

要转换包含HTML的.zip文件，请使用以下代码：

```
//Create an HTML to PDF operation and provide the source file to it
htmlToPDFOperation = PDFToolsSdk.CreatePDF.Operation.createNew();     
const input = PDFToolsSdk.FileRef.createFromLocalFile(sourceZipFile);
htmlToPDFOperation.setInput(input);
 
// custom function for setting options
setCustomOptions(htmlToPDFOperation);
 
// Execute the operation and Save the result to the specified location.
htmlToPDFOperation.execute(executionContext)
  .then(result => result.saveAsFile(destinationPdfFile))
  .catch(err => {
    logger.error('Exception encountered while executing operation');
});
```

函数 `setCustomOptions` 指定其他PDF设置，例如页面大小。 在这里，您可以看到函数将页面大小设置为11.5 x 11英寸：

```
const setCustomOptions = (htmlToPDFOperation) => {    
  const pageLayout = new PDFToolsSdk.CreatePDF.options.PageLayout();
  pageLayout.setPageSize(11.5, 8);

  const htmlToPdfOptions = 
    new PDFToolsSdk.CreatePDF.options.html.CreatePDFFromHtmlOptions.Builder()
    .includesHeaderFooter(true)
    .withPageLayout(pageLayout)
    .build();
  htmlToPDFOperation.setOptions(htmlToPdfOptions);
};
```

打开包含一些术语的HTML文档，您将在浏览器中收到以下内容：

![计算机术语的图像](assets/AWNjs_3.png)

此文档的源由CSS文件和一个HTML文件组成：

![CSS图像和HTML文件](assets/AWNjs_4.png)

处理HTML文件后，您会看到相同文本的PDF格式：

![PDF计算机术语文件](assets/AWNjs_5.png)

## 追加页面

使用PDF文件的另一种常见操作是将可能具有标准文本（如术语列表）的页面追加到末尾。 文档工具包可以将多个PDF文档合并为一个文档。 如果您有一个文档路径列表(此处位于 `sourceFileList`)中，您可以为合并操作将每个文件的文件引用添加到对象中。

执行合并操作时，它提供包含源内容的单个文件。 您可以使用 `saveAsFile` 以将文件保留到存储上。

```
const executionContext = PDFToolsSDK.ExecutionContext.create(credentials);
var combineOperation = PDFToolsSDK.CombineFiles.Operation.createNew();
 
sourceFileList.forEach(f => {
  var combinedSource = PDFToolsSDK.FileRef.createFromLocalFile(f);
  console.log(f);
  combineOperation.addInput(combinedSource);
});
    
 
combineOperation.execute(executionContext)
  .then(result=>result.saveAsFile(destinationFile))
  .catch(err => {
    logger.error(err.message);
});    
```

## 显示PDF文档

您已对PDF文件执行了多个操作，但最终，用户必须查看文档。 您可以使用Adobe的PDF嵌入API将文档嵌入到网页中。

在显示PDF的页面上， `<div />` 元素，以保存文档并为其指定ID。 您稍后将使用此ID。 在网页中，包括 `<script />` 对AdobeJavaScript库的引用：

```
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

所需的最后一个代码位是一个函数，在加载Adobe PDF Embed API JavaScript后即可显示文档。 当收到通过adobe_dc_view\_sdk.ready事件加载脚本的通知时，创建一个新的AdobeDC.View对象。 此对象需要您的客户端ID和先前创建的元素的ID。 在 [Adobe Developer Console](https://console.adobe.io/)的 当您查看先前在生成凭据时创建的应用程序的设置时，客户端ID将显示在此处。

![API客户端密钥的图像](assets/AWNjs_6.png)

## 其他PDF选项

在 [Adobe PDF Embed API演示](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) 允许您预览用于嵌入PDF文档的各种其他选项。

![嵌入PDF选项 ](assets/AWNjs_7.png)

您可以打开和关闭各种选项，并立即查看它们的渲染方式。 找到您喜欢的组合后，单击 *\&lt;/\>生成代码* 按钮以使用这些选项生成HTML的实际代码。

![代码预览图像](assets/AWNjs_8.png)

## 添加数字签名和安全性

文档准备就绪后，您可以使用Adobe Sign添加数字签名以进行审批。 这项功能与您目前使用的功能稍有不同。 对于数字签名，必须将应用程序配置为使用OAuth进行用户身份验证。

设置应用程序的第一步是 [注册应用程序](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/create_app.md) 使用OAuth for Adobe Sign。 登录后，请导航至用于创建应用程序的屏幕，方法是： *帐户*，然后打开 *Adobe Sign API* 部分，然后单击 *API应用程序* 打开已注册应用程序的列表。

![注册应用程序的第一步图像](assets/AWNjs_9.png)

要创建新的应用程序项，请单击右上角的加号图标。

![屏幕右上角的加号图标图像](assets/AWNjs_10.png)

在打开的窗口中，输入应用程序名称和显示名称。 选择 *客户* ，然后单击 *保存*&#x200B;的

![在何处输入应用程序名称和显示名称的图像](assets/AWNjs_11.png)

创建应用程序后，您可以从列表中选择该应用程序，然后单击 *为应用程序配置OAuth*&#x200B;的 选择选项。 在“重定向URL”中，输入应用程序的URL。 您可以在此处输入多个URL。 对于要测试的应用程序，其值为：

```
http://localhost:3000/signed-in 
```

使用OAuth获取令牌的过程是标准流程。 您的应用程序将用户定向到URL进行登录。 当用户成功登录后，他们将被重定向回应用程序，页面查询参数中会提供更多信息。

对于登录URL，应用程序必须传递您的客户端ID、重定向URL以及所需范围的列表。

URL的模式如下所示：

```
https://secure.adobesign.com/public/oauth?
  redirect_uri=&
  response_type=code&
  client_id=&
  scope=
```

系统会提示用户登录到其Adobe Sign ID。 登录后，系统会询问他们是否向应用程序提供权限。

![确认访问屏幕的图像](assets/AWNjs_12.png)

如果用户单击 *允许访问* 在重定向URL上，名为code的查询参数将传递授权代码：

https://YourServer.com/?code=**\&lt;authorization_code>**\&amp;api_access_point=https://api.adobesign.com&amp;web_access_point=https://secure.adobesign.com

将此代码连同您的客户端ID和客户端密钥一起发布到Adobe Sign服务器，可提供用于访问该服务的访问令牌。 保存参数中的值 `api_access_point` 和 `web_access_point`的 这些值用于进一步的请求。

```
var requestURL = ' ${api_access_point}oauth/token?code=${code}'
  +'&client_id=${client_id}'
  +'&client_secret=${client_secret}&'
  +'redirect_uri=${redirect_url}&'
  +'grant_type=authorization_code';
request.post(requestURL, {form: { }
}, (err,response,body)=>{                
    var token_response = JSON.parse(body)
    var access_token = token_response.access_token;
    console.log(access_token);
});
```

当文档需要签名时，必须先上载文档。 应用程序可以将文档上传到 `api_access_point` 请求OAUTH令牌时收到的值。 端点为 `/api/rest/v6/transientDocuments`的 请求数据如下所示：

```
POST /api/rest/v6/transientDocuments HTTP/1.1
Host: api.na1.adobesign.com
Authorization: Bearer MvyABjNotARealTokenHkYyi
Content-Type: multipart/form-data
Content-Disposition: form-data; name=";File"; filename="MyPDF.pdf"
<PDF CONTENT>
```

在应用程序中，使用以下代码构建请求：

```
var uploadRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/transientDocuments',
  'headers': {
    'Authorization': 'Bearer  ${auth_token}'
  },
  formData: {
    'File': {
      'value': fs.createReadStream(documentPath),
      'options': {
        'filename': fileName,
        'contentType': null
      }
    }
  }
};
 
request(uploadRequest, (error, response) => {
  if (error) throw new Error(error);
  var jsonResponse = JSON.parse(response.body);
  var transientDocumentId = jsonResponse.transientDocumentId;
  logger.info('transientDocumentId:', transientDocumentId)
});
```

请求返回 `transientID` 值。 文档已上传，但尚未发送。 要发送文档，请使用 `transientID` 请求发送文档。

首先，构建一个包含待签名文档信息的JSON对象。 在以下示例中，变量 `transientDocumentId` 包含来自上述代码的ID，并且 `agreementDescription` 包含描述需要签名的协议的文本。 中列出了要签名文档的人员 `participantSetsInfo` 他们的电子邮件地址和角色。

```
var requestBody = {
  "fileInfos":[
    {"transientDocumentId":transientDocumentId}],
    "name":agreementDescription,
    "participantSetsInfo":[
      {"memberInfos":[{"email":"user@domain.com"}],
       "order":1,"role":"SIGNER"}
    ],
    "signatureType":"ESIGN","state":"IN_PROCESS"
};
```

发送此Web请求将构建签名请求，并返回一个带有协议请求ID的JSON对象：

```
request(requestBody, function (error, response) {
  if (error) throw new Error(error);
  var JSONResponse = JSON.parse(response.body);
  var requestId = JSONResponse.id;
});
```

如果签名者忘记签名并且需要另一封通知电子邮件，请使用之前收到的ID再次发送通知。 唯一的区别是，您还必须添加参与方的参与者ID。 您可以通过向以下地址发送GET请求来获取参与者ID `/agreements/{agreementID}/members`的

要请求发送提醒，请首先构建描述请求的JSON对象。 最小对象需要参与者ID列表以及提醒状态（“活动”、“完成”或“已取消”）。

请求还可以选择性地包含其他信息，例如将向用户显示的“备注”值。 或者，延迟一段时间（以小时为单位）以等待发送提醒(位于 `firstReminderDelay`)以及提醒频率（在“frequency”字段中），该字段接受DAILY_UNTIL_SIGNED、EVERY_THIRD_DAY_UNTIL_SIGNED或WEEKLY_UNTIL_SIGNED等值。

```
var requestBody = {
  //participantList is an array of participant ID strings
  "recipientParticipantIds":participantList
  ,"status":"ACTIVE",
  "note":"This is a reminder to sign out important agreement."
}
 
var reminderRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/agreements/${agreementID}/reminders',
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
 
};

request(reminderRequest, function (error, response) {
});
```

发送提醒请求仅此而已。

![Web表单的图像](assets/AWNjs_13.png)

## 创建Web表单

您还可以使用Adobe Sign API创建Web表单。 使用Web表单，可以将表单嵌入到网页中或直接链接到网页中。 创建Web表单后，它还会显示在您的Adobe Sign控制台的Web表单中。 您可以创建具有以下状态的Web表单：DRAFT状态用于增量构建，AUTHORING状态用于编辑Web表单字段，ACTIVE状态用于立即托管表单。

![“Adobe Sign管理”屏幕中的Web表单图像](assets/AWNjs_14.png)

要创建Web表单，请使用表单 `transientDocumentId`的 决定表单的标题和初始化状态。

```
var requestBody = {
  "fileInfos": [
    {
      "transientDocumentId": transientDocumentId
    }
  ],
  "name": webFormTitle,
  "state": status,
  "widgetParticipantSetInfo": {
    "memberInfos": [ { "email": "" } ],
    "role": "SIGNER"
  }
}
```

```
var createWebFormRequest = {
  'method': 'POST',
  'url': `${oauthParameters.signin_domain}/api/rest/v6/widgets`,
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
}
```

```
request(createWebFormRequest, function (error, response) {
  var jsonResp = JSON.parse(response.body);
  var webFormID = jsonResp.id;
});
```

现在，您可以嵌入文档或链接到文档。

## 后续步骤

从快速启动和提供的代码中可以看到，使用Node和 [!DNL Adobe Acrobat Services] API。 Adobe的API无缝集成到您现有的客户端应用程序中。

要发现呼叫所需的范围，或查看呼叫的构建方式，您可以从 [Rest API文档](https://secure.na4.adobesign.com/public/docs/restapi/v6)的 在 [快速入门](https://github.com/adobe/pdftools-node-sdk-samples) 还演示了其他功能和文件格式 [!DNL Adobe Acrobat Services] APIs进程。

您可以向应用程序添加多种PDF功能，使用户能够快速、轻松地查看和签署文档等。 首先，请查看 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) 今天。
