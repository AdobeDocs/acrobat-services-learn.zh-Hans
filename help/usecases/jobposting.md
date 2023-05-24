---
title: 职务发布
description: 了解如何为求职者和雇主开发顺畅、一致的Web体验
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8092.jpg
kt: 8092
exl-id: 0e24c8fd-7fda-452c-96f9-1e7ab1e06922
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1527'
ht-degree: 1%

---

# 工作发布

![用例英雄横幅](assets/UseCaseJobHero.jpg)

在运营一个包含多个用户的网站时，设计一种确保为每个用户带来流畅体验的体验至关重要。

设想以下情景：你有一个网站，允许雇主 [上传作业发布](https://www.adobe.io/apis/documentcloud/dcsdk/job-posting.html)的 对于求职者来说，以一致的格式轻松查看所有与发布相关的文档非常方便。 然而，雇主可以方便地附加他们碰巧拥有的任何文件格式的信息。 为了方便这两类用户，您可以自动将所有上传的文档转换为PDF，并将它们嵌入电子公告中。

## 您可以学到的内容

此实际操作教程将介绍一个使用 [!DNL Adobe Acrobat Services] 和 [Node.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk) 将这些功能添加到发布作业站点。 这样创建的网站更容易使用，对雇主和求职者都更具吸引力。 以下是 [完成](https://github.com/contentlab-io/adobe_job_posting) [项目代码](https://github.com/contentlab-io/adobe_job_posting)，以便在阅读时继续阅读。

若要开始，请设置一个简单的基于Express的Node.js Web应用程序。 [Express](https://expressjs.com/) 是一个极简的web应用程序框架，提供路由和模板等功能。 应用程序的代码位于 [GitHub](https://github.com/contentlab-io/adobe_job_posting)的 此外，安装 [PostgreSQL数据库](https://www.postgresql.org/) 并设置以存储PDF。

## 相关 [!DNL Acrobat Services] API

* [PDF嵌入API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

## 创建AdobeAPI凭据

首先，你必须 [创建凭据](https://www.adobe.com/go/dcsdks_credentials) 适用于Adobe PDF Embed API（免费使用）和Adobe PDF Services API(免费使用6个月，然后 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 每个文档事务只需\$0.05)。 在为PDF服务API创建凭据时，选择“创建个性化代码示例”选项。 保存ZIP文件，并将pdftools-api-credentials.json和private.key提取到Node.js Express项目的根目录中。

您还需要一个API密钥才能免费使用嵌入API。 从 [项目](https://console.adobe.io/projects)转到您创建的项目。 然后，单击 **添加到项目** 然后选择 **API**&#x200B;的 最后，单击 **PDF嵌入API**&#x200B;的

指定“PDF嵌入API”的域。 API密钥必须是公共密钥（可在浏览器执行的代码中找到它）。 通过指定域，可以确保其他域中的其他人无法使用API密钥。

不能将“localhost”用作域。 指定一个域，如“testing.local”，并编辑您计算机上的hosts文件，将该域重定向到您的计算机上的127.0.0.1。 然后，可以在testing.local:3000上测试应用程序，而不是在localhost:3000上测试应用程序。 完成后，在项目页面上找到PDF嵌入API的API密钥。

## 添加上载表单和处理程序

使用正常运行的Express应用程序和API凭据，您还需要一个允许用户将其文档上载到网站的表单。 为此，请编辑index.jade模板。

为已上载的职务发布名称和包含更多信息的文档创建输入字段。

在模板的内容块中，添加以下表单：

```
extends layout

block content
  h1= title

  form(action="/upload", enctype="multipart/form-data", method="POST")
    label Job posting name:&nbsp;
    input(type="text", name="name", required="required")
    br
    br
    label Describing document:&nbsp;
    input(type="file", name="attachment", required="required")
    br
    br
    input(type="submit", value="Submit job posting")
```

接下来，为/upload操作添加POST请求的处理函数。 然后，为/upload添加一个路由到routes/index.js文件。 您可以为此路由创建新文件，但是必须更新app.js文件才能反映新文件。 在此路由处理程序中，您可以访问给定的名称和上传的文件。

```
router.post('/upload', async function (req, res, next) {
    const name = req.body.name;
    const fileContents = req.files.attachment.data;

    // code to work with the uploaded document
  });
```

该函数是异步的，因此您可以在该函数中使用await关键字，这在调用执行API调用的方法时非常方便。

![工作发布网站的屏幕截图](assets/jobs_1.png)

## 使用PDF服务API

在使用PDF服务API之前，您必须将以下导入添加到路由文件的顶部：

```
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
  const { Readable } = require('stream');
```

在导入的正下方，您可以加载API凭据并创建 [执行内容](https://www.javascripttutorial.net/javascript-execution-context/)的 由于您可以为不同的操作重复使用执行上下文，因此只执行一次是有意义的。

```
  const credentials = PDFToolsSdk.Credentials
  .serviceAccountCredentialsBuilder()
  .fromFile("pdftools-api-credentials.json")
  .build();

  const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
```

现在，返回到 `router.post` 阻止。 首先将文档转换为PDF。

```
  const createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();

  const input = PDFToolsSdk.FileRef.createFromStream(Readable.from(fileContents),
  req.files.attachment.mimetype);

  createPdfOperation.setInput(input);

  let result = await createPdfOperation.execute(executionContext);

  result.saveAsFile('output-pdf' + new Date().getTime() + '.pdf');
  return res.send('success!');
```

大多数操作都采取相同的四个步骤。 首先，使用相应类的createNew方法初始化操作类型。 然后，创建操作的输入，即FileRef。 后续操作可以跳过此步骤，因为操作的结果也是FileRef。 对于此初始操作，从上传文件的字节创建FileRef。 第三，必须将输入分配给操作。 最后，执行操作，执行上下文作为execute方法中的参数。 此方法返回一个承诺，以便您可以等待结果。

代码将返回的PDF保存到文件中，并向浏览器发送简单的“成功”响应。 文件名的“日期”部分可保证唯一的文件名。 如果目标文件存在，saveAsFile将返回错误。

## 将图像转换为文本并压缩PDF

现在，可使用光学字符识别(OCR)功能将图像转换为文本，然后压缩结果。 这时，您将使用OCR和CompressPDF操作完成，与CreatePDF操作类似。 将以下内容添加到路由文件中： `router.post`:

```
  const name = req.body.name;
  const fileContents = req.files.attachment.data;

  const createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
  const input = PDFToolsSdk.FileRef.createFromStream(Readable.from(fileContents),
  req.files.attachment.mimetype);
  createPdfOperation.setInput(input);

  let result = await createPdfOperation.execute(executionContext);

  const ocrOperation = PDFToolsSdk.OCR.Operation.createNew();
  ocrOperation.setInput(result);
  result = await ocrOperation.execute(executionContext);

  const compressPdfOperation = PDFToolsSdk.CompressPDF.Operation.createNew();
  compressPdfOperation.setInput(result);
  result = await compressPdfOperation.execute(executionContext);

  result.saveAsFile('output-pdf' + new Date().getTime() + '.pdf');
  return res.send('success!');
```

只需执行此操作一次，因为结果是FileRef，代码可以传递给setInput。

除了将文件保存到硬盘上并返回过度简化的HTTP响应外，还有一种更好的替代方法。 而是将PDF存储在数据库中，并显示一个使用Adobe的免费PDF嵌入API嵌入PDF的网页。 这样，可以在网站上看到雇主的招聘信息或小册子，供求职者查找和查看，并附上公司徽标和其他设计元素。

## 将PDF存储在数据库中

将PDF存储在PostgreSQL数据库中。 获取node-postgres包以连接到Node.js中的Postgres。 安装stream-buffers包，因为在某些时刻必须将PDF的内容存储在缓冲区中，而FileRef只适用于流。 因此，使用流缓冲区包将内容写入缓冲区。

```
npm install pg stream-buffers
```

现在，为作业发布创建数据库表。 它需要一个唯一标识符列，一个名称列和一个附加的PDF。 可以从Postgres命令行界面(CLI)创建数据库表：

```
CREATE TABLE job_postings (id TEXT PRIMARY KEY, name TEXT NOT NULL, attachment
BYTEA NOT NULL);
```

返回Node.js文件。 在文件顶部添加一些导入内容：

```
  const { Client } = require('pg');
  const streamBuffers = require('stream-buffers');
```

要将该PDF存储在数据库表中，请修改上载函数。 将最后两行（saveAsFile和send）替换为以下代码片段：

```
  const pgClient = new Client();
  pgClient.connect();

  const id = Math.random().toString(36).substr(2, 6); // not securely random at all,
  but serves the purpose for this demo

  const writableStream = new streamBuffers.WritableStreamBuffer();
  writableStream.on("finish", async () => {    
    await pgClient.query("INSERT INTO job_postings VALUES ($1, $2, $3)", [
      id,
      name,
      writableStream.getContents()
    ]);
    res.redirect(`/job/${id}`);
  })
  result.writeToStream(writableStream);
```

要写入内容，请创建一个WritableStreamBuffer。 使用finish事件时，执行SQL查询的时间到了。 node-postgres包会自动将Buffer参数转换为BYTEA格式。 查询将用户重定向到/job/{id}，之后创建的终结点。

对于PDF嵌入API，您还需要仅返回PDF内容的端点：

```
  router.get('/pdf/:id', async function (req, res, next) {
    const id = req.params.id;
 
    const pgClient = new Client();
    pgClient.connect();

  const pgResult = await pgClient.query("SELECT attachment FROM job_postings WHERE id
  = $1", [id]);
  const buffer = pgResult.rows[0].attachment;
  res.type('pdf');
    return res.send(buffer);
  });
```

## 嵌入PDF

现在，创建/job/{id}终结点，该终结点渲染的模板包含请求的作业发布名称和嵌入的PDF。

```
router.get('/job/:id', async function(req, res, next) {
    const id = req.params.id;

    const pgClient = new Client();
    pgClient.connect();

    const pgResult = await pgClient.query("SELECT name FROM job_postings WHERE id =
  $1", [id]);
    const name = pgResult.rows[0].name;

    res.render('job', { pdf_url: `/pdf/${id}`, name });
  });
```

在“视图/目录”中，创建包含以下内容的job.jade文件：

```
  extends layout

  block content
    h1= name
    div(id='adobe-dc-view')
    script(src='https://documentcloud.adobe.com/view-sdk/main.js')
    script.
      window.embedUrl = "!{pdf_url}";
    script(src='/javascripts/embed-pdf.js')
```

第一个脚本是Adobe的View SDK，它可以轻松嵌入PDF。 第二个脚本是一个内嵌单行脚本，该脚本将window.embedUrl的值设置为由Express route handler提供的PDF的URL。 请按如下方式自行创建第三个脚本：

```
  document.addEventListener("adobe_dc_view_sdk.ready", function () {
    var adobeDCView = new AdobeDC.View({ clientId: "YOUR API KEY HERE", divId:
   "adobe-dc-view" });
    adobeDCView.previewFile({
      content: { location: { url: '//' + window.location.host + window.embedUrl }
         },
      metaData: { fileName: "Job posting" }
    });
  });
```

现在，您可以测试上传文档、重定向到/job/id页面以及查看嵌入式PDF的整个过程。 用户通过相同的步骤将工作发布或其他文档添加到网站。

![测试上传的PDF文档的屏幕截图](assets/jobs_2.png)

要查看内嵌在操作中的效果，请查看此 [实时演示](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/IN_LINE/Bodea%20Brochure.pdf)的

## 后续步骤

本实际操作教程介绍了如何将Node.js与 [!DNL Acrobat Services] 转换已上传的 [工作发布](https://www.adobe.io/apis/documentcloud/dcsdk/job-posting.html) 多种格式导出到PDF。 生成的PDF随后被嵌入到网页中。 现在，您可以将相同的功能添加到您的网站，从而让雇主可以更轻松地为求职者上传工作说明、手册等内容。 这些功能可帮助每个人获得找到理想工作所需的信息。

[!DNL Acrobat Services] 帮助您将重要的文档处理功能添加到您的网站或应用程序。 如果您想更深入地了解这些API的作用，请参阅以下快速入门文档：

* [PDF嵌入API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

要开始向网站添加用户友好的文档处理功能， [注册免费试用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)的 Adobe PDF Embed API始终可以免费使用，Adobe PDF Services API可以免费使用6个月，但每个文档交易仅需\$0.05，因此您可以 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 随着业务的发展。
