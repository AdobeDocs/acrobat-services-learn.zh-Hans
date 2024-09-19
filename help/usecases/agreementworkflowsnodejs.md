---
title: Node.js中的协议工作流
description: “[!DNL Adobe Acrobat Services] API可轻松地将PDF功能合并到您的Web应用程序中”
feature: Use Cases
role: Developer
level: Beginner
type: Tutorial
jira: KT-7473
thumbnail: KT-7473.jpg
keywords: 精选
exl-id: 44a03420-e963-472b-aeb8-290422c8d767
source-git-commit: f8a31b8f98d99bf1f3787e0f0f19cc9f26e24d8d
workflow-type: tm+mt
source-wordcount: '2094'
ht-degree: 0%

---

# Node.js中的协议工作流

![用例主横幅](assets/UseCaseAgreementHero.jpg)

许多业务应用程序和流程都需要文档，如提议和协议。 PDF文档可确保文件更安全，更不易修改。 它们还提供数字签名支持，因此您的客户可以快速而轻松地填写其文档。 [!DNL Adobe Acrobat Services] API可轻松地将PDF功能合并到您的Web应用程序中。

## 您可以学到的内容

在本实际操作教程中，了解如何将PDF服务添加到Node.js应用程序以数字化协议流程。

## 相关的API和资源

* [PDF服务API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF的嵌入API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [项目代码](https://github.com/adobe/pdftools-node-sdk-samples)

## 正在设置[!DNL Adobe Acrobat Services]

若要开始，请设置凭据以使用[!DNL Adobe Acrobat Services]。 注册帐户并使用[Node.js Quickstart](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#node-js)验证您的凭据是否有效，然后再将该功能集成到更大的应用程序中。

首先，获取Adobe开发人员帐户。 然后，在[开始使用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK)页面，选择“创建新凭据”下的&#x200B;*开始使用*&#x200B;选项。 您可以注册他们的免费试用版，该版本提供了1,000个文档事务，可在六个月以上使用。

![创建新凭据的图像](assets/AWNjs_1.png)

在以下“创建新凭据”页面上，系统会提示您在PDFEmbed API和PDF服务API之间进行选择。

选择&#x200B;*PDF服务API*。

输入应用程序的名称，并选中标记为&#x200B;*创建个性化代码示例*&#x200B;的框。 选中此框后，您的凭据会自动嵌入到代码示例中。 如果未选中此框，则必须手动将凭据添加到应用程序。

为应用程序类型选择&#x200B;*Node.js*，然后单击&#x200B;*创建凭据*。

片刻后，包含凭据的示例项目开始下载.zip文件。 [!DNL Acrobat Services]的Node.js包已作为示例项目代码的一部分包括在内。

选择PDF服务API凭据的![图像](assets/AWNjs_2.png)

## 手动配置示例项目

如果您选择不从创建新凭据页面下载示例项目，您还可以手动设置项目。

从[GitHub](https://github.com/adobe/pdftools-node-sdk-samples)下载代码（未嵌入您的凭据）。 如果您使用此版本的代码，则必须先将凭据添加到pdftools-api-credentials.json文件，然后再使用：

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

对于您自己的应用程序，您需要将私钥文件和凭据文件复制到您的应用程序源。

必须安装[!DNL Acrobat Services]的Node.js包。 要安装程序包，请使用以下命令：

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

## 设置日志记录

此处的示例使用Express作为应用程序框架。 它们还使用log4js进行应用程序日志记录。 使用log4js，您可以轻松地直接将日志记录到控制台或输出到文件：

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

上述代码将记录的数据写入中的文件。/logs/applicationlog.txt. 如果希望它写入控制台，可以注释掉对log4js.configure的调用。

## 将Word文件转换为PDF

协议和建议通常是在工作效率应用程序(如Microsoft Word)中编写的。 要接受此格式的文档并将文档转换为PDF，您可以为应用程序添加功能。 让我们看看如何在Express应用程序中上传并保存文档，然后将其保存到文件系统。

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

选择一个文件夹以接受已上传的文件。 应用程序需要此文件夹的路径。 使用与\_\_dirname连接的相对路径查找绝对路径：

```
const uploadFolder = path.join(__dirname, "../uploads");
```

由于文件是通过post提交的，因此您必须在服务器端响应post消息：

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

执行此函数后，文件将保存在应用程序上载文件夹中，可供进一步处理。

接下来，将文件从其本机格式转换为PDF。 您之前下载的示例代码包含一个名为`create-pdf-from-docx.js`的脚本，用于将文档转换为PDF。 以下函数`convertDocumentToPDF`获取上载的文档，并将其转换为其他文件夹中的PDF：

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

您可能会注意到代码中存在一个常规模式：

代码将建立一个凭据对象和一个执行上下文，初始化一些操作，然后执行具有执行上下文的操作。 在整个示例代码中都可以看到此模式。

通过对upload函数进行一些添加，使其调用此函数，用户上传的Word文档现在自动转换为PDF。

以下代码为转换后的PDF构建目标路径并启动转换：

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

文档工具包将其他格式转换为PDF，如静态HTML（另一种常用文档类型）。 该工具包接受将文档打包为.zip文件的HTML文档，并在同一.zip文件中包含该文档引用的所有资源（CSS文件、图像和其他文件）。 HTML文档本身必须命名为index.html并放置在.zip文件的根目录下。

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

函数`setCustomOptions`指定其他PDF设置，如页面大小。 在这里，您可以看到该函数将页面大小设置为11.5 x 11英寸：

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

打开包含某些术语的HTML文档，您会在浏览器中看到以下内容：

![计算机术语图像](assets/AWNjs_3.png)

此文档的源由CSS文件和HTML文件组成：

![CSS和HTML文件的图像](assets/AWNjs_4.png)

处理HTML文件后，您将看到相同的PDF文本：

![PDF的计算机术语文件](assets/AWNjs_5.png)

## 附加页面

另一个常见的PDF文件操作是将可能包含标准文本（如术语列表）的页面附加到末尾。 该文档工具包可以将多个PDF文档合并为单个文档。 如果您有文档路径列表（此处为`sourceFileList`），则可以将每个文件的文件引用添加到合并操作的对象中。

执行合并操作时，它会提供一个包含源内容的单个文件。 您可以在对象上使用`saveAsFile`将文件持久化到存储中。

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

您已对PDF文件执行了多次操作，但最终，您的用户必须查看这些文档。 您可以使用Adobe的PDFEmbed API将文档嵌入到网页中。

在显示PDF的页面上，添加一个`<div />`元素以容纳文档并为其指定ID。 您稍后会使用此ID。 在网页中，包含对AdobeJavaScript库的`<script />`引用：

```
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

所需的最后一位代码是一个函数，它在加载Adobe PDF Embed API JavaScript后显示文档。 当您收到脚本通过adobe_dc_view\_sdk.ready事件加载的通知时，请创建一个新的AdobeDC.View对象。 此对象需要您的客户端ID和之前创建的元素的ID。 在[Adobe Developer Console](https://console.adobe.io/)中查找您的客户端ID。 当您查看之前生成凭据时创建的应用程序的设置时，客户端ID将显示在此处。

API客户端密钥的![图像](assets/AWNjs_6.png)

## 其他PDF选项

通过[Adobe PDF Embed API演示](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf)，您可以预览用于嵌入PDF文档的各种其他选项。

![嵌入PDF选项](assets/AWNjs_7.png)的图像

您可以打开和关闭各种选项，并立即查看它们的渲染方式。 当您找到喜欢的组合时，单击“*\&lt;/\>生成代码*”按钮，以使用这些选项生成实际的HTML代码。

![代码预览图像](assets/AWNjs_8.png)

## 添加数字签名和安全性

文档准备就绪后，您可以使用Adobe Sign添加数字签名以供审批。 此功能与迄今为止使用的功能稍有不同。 对于数字签名，应用程序必须配置为使用OAuth进行用户身份验证。

设置应用程序的第一步是[注册应用程序](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/create_app.md)以使用Adobe Sign的OAuth。 登录后，导航到用于通过单击&#x200B;*帐户*&#x200B;创建应用程序的屏幕，然后打开&#x200B;*Adobe Sign API*&#x200B;部分，并单击&#x200B;*API应用程序*&#x200B;以打开已注册的应用程序列表。

![注册应用程序第一步的图像](assets/AWNjs_9.png)

要创建新的应用程序条目，请单击右上角的加号图标。

![屏幕右上角的加号图标的图像](assets/AWNjs_10.png)

在打开的窗口中，输入应用程序名称和显示名称。 为域选择&#x200B;*客户*，然后单击&#x200B;*保存*。

![输入应用程序名称和显示名称的位置的图像](assets/AWNjs_11.png)

创建应用程序后，可从列表中选择它，然后单击&#x200B;*为应用程序配置OAuth*。 选择选项。 在重定向URL中，输入应用程序的URL。 您可以在此处输入多个URL。 对于要测试的应用程序，其值为：

```
http://localhost:3000/signed-in 
```

使用OAuth获取令牌的过程是标准流程。 您的应用程序会将用户定向到用于登录的URL。 用户成功登录后，
它们将被重定向回应用程序，该应用程序的页面查询参数中包含其他信息。

对于登录URL，您的应用程序必须传递您的客户端ID、重定向URL和所需范围的列表。

URL的模式如下所示：

```
https://secure.adobesign.com/public/oauth?
  redirect_uri=&
  response_type=code&
  client_id=&
  scope=
```

系统会提示用户登录其ID以使用Adobe Sign。 登录后，系统会询问他们是否提供对应用程序的权限。

![确认访问屏幕的图像](assets/AWNjs_12.png)

如果用户单击重定向URL上的&#x200B;*允许访问*，则名为code的查询参数将传递授权代码：

https://YourServer.com/?code=**\&lt;authorization_code\>**\&amp;api_access_point=https://api.adobesign.com&amp;web_access_point=https://secure.adobesign.com

将此代码与客户端ID和客户端密钥一起发布到Adobe Sign服务器，将提供访问该服务的访问令牌。 保存参数`api_access_point`和`web_access_point`中的值。 这些值用于进一步的请求。

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

当文档需要签名时，必须首先上传该文档。 您的应用程序可以将文档上传到请求OAUTH令牌时收到的`api_access_point`值。 终结点为`/api/rest/v6/transientDocuments`。 请求数据如下所示：

```
POST /api/rest/v6/transientDocuments HTTP/1.1
Host: api.na1.adobesign.com
Authorization: Bearer MvyABjNotARealTokenHkYyi
Content-Type: multipart/form-data
Content-Disposition: form-data; name=";File"; filename="MyPDF.pdf"
<PDF CONTENT>
```

在您的应用程序中，使用以下代码构建请求：

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

请求返回`transientID`值。 文档已上传，但尚未发送。 若要发送文档，请使用`transientID`请求发送文档。

首先，生成一个包含待签名文档信息的JSON对象。 在以下内容中，变量`transientDocumentId`包含来自上述代码的ID，而`agreementDescription`包含描述需要签名的协议的文本。 `participantSetsInfo`中按电子邮件地址和角色列出了要签署该文档的人员。

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

发送此Web请求将生成签名请求，并返回一个带有协议请求ID的JSON对象：

```
request(requestBody, function (error, response) {
  if (error) throw new Error(error);
  var JSONResponse = JSON.parse(response.body);
  var requestId = JSONResponse.id;
});
```

如果签名者忘记签名，并且需要另一封通知电子邮件，请使用之前收到的ID重新发送通知。 唯一的区别是，您还必须添加参与方的参与者ID。 您可以通过向`/agreements/{agreementID}/members`发送GET请求来获取参与者ID。

要请求发送提醒，请首先构建描述请求的JSON对象。 最小对象需要参与者ID列表和提醒状态（“活动”、“完成”或“已取消”）。

请求可以选择具有其他信息，例如向用户显示的“备注”值。 或者，延迟（以小时为单位）以等待发送提醒（以`firstReminderDelay`为单位），以及提醒频率（在“frequency”字段中），该频率接受DAILY_UNTIL_SIGNED、EVERY_THIRD_DAY_UNTIL_SIGNED或WEEKLY_UNTIL_SIGNED等值。

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

只需这些即可发送提醒请求。

![Web表单的图像](assets/AWNjs_13.png)

## 创建Web表单

您还可以使用Adobe Sign API创建Web表单。 通过Web表单，可以将表单嵌入到网页中或直接链接到网页。 创建Web表单后，它还会显示在Adobe Sign控制台的Web表单中。 您可以创建处于“草稿”状态的Web表单，以进行增量构建；处于“创作”状态以编辑Web表单字段；处于“活动”状态以立即托管表单。

![Adobe Sign“管理”屏幕中的Web表单图像](assets/AWNjs_14.png)

若要创建Web表单，请使用表单`transientDocumentId`。 确定窗体的标题和初始化窗体时的状态。

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

您现在可以嵌入文档或链接到文档。

## 后续步骤

从快速入门和提供的代码中可以看到，使用Node和[!DNL Adobe Acrobat Services] API可以轻松实现PDF和数字文档审批流程。 Adobe的API可无缝集成到现有客户端应用程序中。

要发现调用的所需范围或查看如何生成调用，可从[Rest API文档](https://secure.na4.adobesign.com/public/docs/restapi/v6)中生成示例调用。 [Quickstarts](https://github.com/adobe/pdftools-node-sdk-samples)还演示了[!DNL Adobe Acrobat Services] API进程的其他功能和文件格式。

您可以向应用程序添加多种PDF功能，让用户能够快速、轻松地查看和签署他们的文档等等。 若要开始，请今天查看[[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/)。
