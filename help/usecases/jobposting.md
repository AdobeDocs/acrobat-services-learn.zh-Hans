---
title: 职位发布
description: 了解如何为求职者和雇主开发顺畅而一致的Web体验
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8092
thumbnail: KT-8092.jpg
exl-id: 0e24c8fd-7fda-452c-96f9-1e7ab1e06922
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1527'
ht-degree: 1%

---

# 职位发布

![用例主横幅](assets/UseCaseJobHero.jpg)

在操作具有多个用户的网站时，设计一种确保为每个人提供流畅体验的体验至关重要。

想象一下以下场景：您有一个网站，允许雇主 [上载职位发布](https://www.adobe.io/apis/documentcloud/dcsdk/job-posting.html). 对于求职者来说，轻松查看与张贴相关的所有文档非常方便。 不过，雇主可以方便地以自己碰巧拥有的任何文件格式附加信息。 为了给这两种类型的用户带来方便，您可以自动将所有上传的文档转换为PDF，并将其内嵌到发布中。

## 您可以学到的内容

本实操教程逐步演示了使用的Node.js示例 [!DNL Adobe Acrobat Services] 及其 [Node.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk) 将这些权能添加到职位发布站点。 这创造了一个更容易使用的网站，对雇主和求职者都更具吸引力。 下面是 [完成](https://github.com/contentlab-io/adobe_job_posting) [项目代码](https://github.com/contentlab-io/adobe_job_posting)，以便您能够按照自己的想法继续阅读。

首先，设置一个简单的基于Express的Node.js Web应用程序。 [Express](https://expressjs.com/) 是一个极简的Web应用程序框架，提供路由和模板等功能。 该应用程序的代码位于 [GitHub](https://github.com/contentlab-io/adobe_job_posting). 此外，请安装 [PostgreSQL数据库](https://www.postgresql.org/) 并设置它以存储PDF。

## 相关 [!DNL Acrobat Services] API

* [嵌入式APIPDF](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

## 创建AdobeAPI凭据

首先，您必须 [创建凭据](https://www.adobe.com/go/dcsdks_credentials) 适用于Adobe PDF Embed API（免费使用）和Adobe PDF Services API（免费使用六个月） [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 每个文档事务仅需\$0.05)。 为PDF服务API创建凭据时，请选择“创建个性化代码示例”选项。 保存ZIP文件并将pdftools-api-credentials.json和private.key提取到Node.js Express项目的根目录中。

您还需要免费提供的Embed API的API密钥。 发件人 [项目](https://console.adobe.io/projects)，转到您创建的项目。 然后，单击 **添加到项目** 并选择 **API**. 最后，单击 **嵌入式APIPDF**.

指定PDF嵌入式API的域。 API密钥必须是公共的（可在浏览器执行的代码中查找它）。 通过指定域，可确保其他域中的其他人无法使用API密钥。

您不能使用“localhost”作为域。 指定域（如“testing.local”），并编辑计算机上的hosts文件以将该域重定向到127.0.0.1，即您的计算机。 然后，您可以在testing.local：3000上测试应用程序，而不是在localhost：3000上测试应用程序。 完成后，在项目页面上找到PDFEmbed API的API密钥。

## 添加上载表单和处理程序

利用有效的Express应用程序和API凭据，您还需要一个表单，使用户能够将其文档上传到网站。 为此目的编辑index.jade模板。

为上传的职位发布名称和包含更多信息的文档创建输入字段。

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

接下来，将POST请求的处理程序添加到/upload操作。 然后，将/upload的路由添加到routes/index.js文件。 您可以为此路由创建新文件，但必须更新app.js文件以反映新文件。 在此路由处理程序内，您可以访问给定的名称和上传的文件。

```
router.post('/upload', async function (req, res, next) {
    const name = req.body.name;
    const fileContents = req.files.attachment.data;

    // code to work with the uploaded document
  });
```

函数是异步的，因此您可以在函数中使用await关键字，这在调用执行API调用的方法时非常方便。

![招聘信息网站的屏幕截图](assets/jobs_1.png)

## 使用PDF服务API

在使用PDF服务API之前，必须将以下导入添加到路由文件的顶部：

```
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
  const { Readable } = require('stream');
```

在导入正下方，您可以加载API凭据并创建 [执行内容](https://www.javascripttutorial.net/javascript-execution-context/). 由于您可以针对不同的操作重复使用执行上下文，因此只执行一次是很有意义的。

```
  const credentials = PDFToolsSdk.Credentials
  .serviceAccountCredentialsBuilder()
  .fromFile("pdftools-api-credentials.json")
  .build();

  const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
```

现在，返回到在请求处理程序中的注释处编写代码 `router.post` 屏蔽。 首先将文档转换为PDF。

```
  const createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();

  const input = PDFToolsSdk.FileRef.createFromStream(Readable.from(fileContents),
  req.files.attachment.mimetype);

  createPdfOperation.setInput(input);

  let result = await createPdfOperation.execute(executionContext);

  result.saveAsFile('output-pdf' + new Date().getTime() + '.pdf');
  return res.send('success!');
```

大多数操作都采用相同的四个步骤。 首先，使用相应类的createNew方法初始化操作类型。 然后，创建操作的输入，即FileRef。 后续操作可跳过此步骤，因为操作的结果也是FileRef。 对于此初始操作，根据上传文件的字节数创建一个FileRef。 第三，必须将输入分配给操作。 最后，以执行上下文作为执行方法中的参数来执行操作。 此方法返回Promise，以便您可以等待结果。

代码将返回的PDF保存到文件，并向浏览器发送简单的“成功”响应。 文件名的“日期”部分保证一个唯一的文件名。 如果目标文件存在，saveAsFile将返回错误。

## 将图像转换为文本并压缩PDF

现在，使用光学字符识别(OCR)将图像转换为文本，然后压缩结果。 这可以使用OCR和CompressPDF操作，与CreatePDF操作类似。 将以下内容添加到路由文件中，位于 `router.post`：

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

只需执行一次此操作，因为结果为FileRef，代码可将其传递给setInput。

有一种更好的替代方法可将文件保存到硬盘并返回过于简化的HTTP响应。 而是应将PDF存储在数据库中，并使用Adobe的免费PDFEmbed API显示嵌入PDF的网页。 这样一来，雇主的招聘启事或宣传册就会在网站上出现，供求职者查找和查看，并加上公司标志和其他设计元素。

## 在数据库中存储PDF

将PDF存储在PostgreSQL数据库中。 获取node-postgres包以连接到Node.js中的Postgres。 安装流缓冲区包，因为有时您必须将PDF的内容存储在缓冲区中，而FileRef只能用于流。 因此，使用流缓冲区包将内容写入缓冲区。

```
npm install pg stream-buffers
```

现在，为职位发布创建数据库表。 它需要一列作为唯一标识符，一列作为名称，一列作为附加PDF。 可以从Postgres命令行界面(CLI)创建数据库表：

```
CREATE TABLE job_postings (id TEXT PRIMARY KEY, name TEXT NOT NULL, attachment
BYTEA NOT NULL);
```

返回到Node.js文件。 在文件顶部添加一些导入：

```
  const { Client } = require('pg');
  const streamBuffers = require('stream-buffers');
```

要将PDF存储在数据库表中，请修改上载功能。 将最后两行（saveAsFile和send）替换为以下代码片段：

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

要写入内容，请创建一个WritableStreamBuffer。 完成finish事件后，就是执行SQL查询的时候了。 node-postgres包自动将Buffer参数转换为BYTEA格式。 查询会将用户重定向到/job/{id}，随后创建了一个端点。

对于PDFEmbed API，您还需要一个仅返回PDF内容的端点：

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

现在，创建/job/{id} 终结点，这将呈现一个模板，其中包含所请求的职位发布名称和嵌入的PDF。

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

在views/目录中，创建一个包含以下内容的job.jade文件：

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

第一个脚本是Adobe的View SDK，它使嵌入PDF变得容易。 第二个脚本是内嵌单行脚本，它将window.embedUrl的值设置为Express路由处理程序提供的PDF的URL。 自行创建第三个脚本，如下所示：

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

现在，您可以测试上传文档、重定向到/job/id页面以及查看嵌入式PDF的整个过程。 您的用户按照相同的步骤向您的网站添加招聘启事或其他文档。

![测试已上传PDF文档的屏幕截图](assets/jobs_2.png)

要查看内嵌的实际操作，请查看以下内容 [现场演示](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/IN_LINE/Bodea%20Brochure.pdf).

## 后续步骤

本实际操作教程逐步介绍了如何将Node.js与 [!DNL Acrobat Services] 转换已上传的 [职位发布](https://www.adobe.io/apis/documentcloud/dcsdk/job-posting.html) PDF的各种格式。 然后将生成的PDF嵌入到网页中。 现在，您可以将相同的功能添加到您的网站，让雇主可以更轻松地上传工作说明、手册以及更多内容，以供求职者查找。 这些功能可帮助每个人获得必要的信息以找到他们梦想的工作。

[!DNL Acrobat Services] 帮助您将关键文档处理功能添加到您的网站或应用程序。 如果您想深入了解这些API的功能，请参阅以下快速入门文档：

* [嵌入式APIPDF](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

要开始为您的网站添加用户友好的文档处理功能， [注册以免费试用](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html). Adobe PDF Embed API始终可免费使用，而Adobe PDF Services API在六个月内可免费使用，那么只需每个文档事务\$0.05，您便可以 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 随着您的业务增长。
