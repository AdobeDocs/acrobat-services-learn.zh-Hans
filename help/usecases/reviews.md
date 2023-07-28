---
title: 审阅和批准
description: 了解如何为跨团队协作构建文档审阅和批准工作流程
type: Tutorial
role: Developer
level: Intermediate
feature: Use Cases
thumbnail: KT-8094.jpg
jira: KT-8094
exl-id: d704620f-d06a-4714-9d09-3624ac0fcd3a
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '1623'
ht-degree: 1%

---

# 审阅和批准

![用例主横幅](assets/UseCaseReviewsHero.jpg)

在COVID-19疫情期间，许多公司需要远程跨团队协作， [共享和审阅数字文档](https://www.adobe.io/apis/documentcloud/dcsdk/review-and-approval.html) 对团队和跨职能资源提出了一系列挑战。

这些挑战包括共享不同文件格式的文档、有效审阅和评论内容以及与最新编辑同步。 [!DNL Adobe Acrobat Services] API旨在使应用程序开发人员能够为其用户解决这些难题。

## 您可以学到的内容

本实际操作教程介绍了如何在Node.js和Express Web应用程序中构建文档审阅和批准工作流程。 若要学习本教程，您需要对Node.js有一定经验。

该应用程序具有以下功能：

* 将不同的文件类型转换为PDF

* 启用文件上传

* 允许用户添加注释和批注

* 显示PDF以及这些注释

* 启用用户配置文件以识别评论作者

* 将文件合并到用户可以下载的最终PDF中

## 相关的API和资源

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [嵌入式APIPDF](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [项目代码](https://github.com/contentlab-io/adobe_reviews_and_approvals)

## 创建AdobeAPI凭据

在启动代码之前，您必须 [创建凭据](https://www.adobe.com/go/dcsdks_credentials) 用于Adobe PDF Embed API和Adobe PDF Services API。 PDFEmbed API可免费使用。 PDF服务API免费使用六个月，然后您可以切换到 [即付即用计划](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 只需每个文档事务0.05 USD。

为PDF服务API创建凭据时，请选择 **创建个性化代码示例** 选项并选择Node.js作为语言。 保存ZIP文件并将pdftools-api-credentials.json和private.key提取到Node.js Express项目的根目录中。

## 设置项目和依赖项

设置Node.js和Express项目以从名为“public”的文件夹提供静态文件。 您可以根据自己的偏好设置项目方式。 要快速启动并运行，您可以使用 [Express应用程序生成器](https://expressjs.com/en/starter/generator.html). 或者，如果你想保持简单，你可以 [从头开始](https://expressjs.com/en/starter/hello-world.html) 并将代码保存在单个JavaScript文件中。 在上面链接的示例项目中，您使用的是单文件方法，并将所有代码保存在index.js中。

复制 `pdftools-api-credentials.json` 和 `private.key` 从个性化代码示例到项目根目录的文件。 此外，如果您有.gitignore文件，请将其添加到.gitignore文件，这样您的凭据文件就不会意外发送到存储库。

下一步，运行 `npm install @adobe/documentservices-pdftools-node-sdk` 安装用于PDF服务的Node.js SDK。 导入此模块，并在您的代码（示例项目中的index.js）内创建API凭据对象，然后导入其余依赖项，如下所示：

```
  const PDFToolsSdk = require( "@adobe/documentservices-pdftools-node-sdk" );

  // Create Credentials
  const credentials =  PDFToolsSdk.Credentials
      .serviceAccountCredentialsBuilder()
      .fromFile( "pdftools-api-credentials.json" )
      .build();
```

您的起始代码应如下所示：

```
  
  const express = require( "express" );
  const PDFToolsSdk = require( "@adobe/documentservices-pdftools-node-sdk" );

  // Create Credentials
  const credentials =  PDFToolsSdk.Credentials
      .serviceAccountCredentialsBuilder()
      .fromFile( "pdftools-api-credentials.json" )
      .build();

  const app = express();

  app.use( express.static( "public" ) );

  app.listen( 8889, function() {
      console.log( "Server started on port", 8889 );
  } );
```

现在，您可以开始使用 [!DNL Acrobat Services] API。

## 正在将文件转换为PDF

对于文档工作流程的第一部分，最终用户必须上传要共享的文档。 要启用此功能，您可以添加上传功能，并将各种文档文件格式合并到PDF中，为审阅过程做好准备。

PDF首先创建一个函数，用于根据 [PDF服务API的示例片段](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html). 此示例还显示了许多其他重要功能的片段，包括光学字符识别(OCR)、密码保护和移除以及压缩。

```
function fileToPDF( filename, outputFilename, callback ) {
      // Create an ExecutionContext using credentials and create a new operation
  instance.
      const executionContext = PDFToolsSdk.ExecutionContext.create( credentials ),
          createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();

      // Set operation input from a source file.
      const input = PDFToolsSdk.FileRef.createFromLocalFile( filename );
      createPdfOperation.setInput( input );

      // Execute the operation and Save the result to the specified location.
      createPdfOperation.execute( executionContext )
          .then( result => {
              result.saveAsFile( outputFilename );
              callback( outputFilename );
          } );
  }
```

现在，您可以使用此功能从上传的PDF创建文档。

## 处理文件上传

接下来，服务器需要在Web服务器上有一个文件上传端点来接收和处理文档。

首先，在uploads文件夹中创建文件夹，并将其命名为“草稿”。 您可以将上传的文件和转换后的PDF文件存储在此处。 下一步，运行 `npm install express-fileupload` 要在代码中安装Express-FileUpload模块并将中间件添加到Express，请执行以下操作：

```
const fileUpload = require( "express-fileupload" );
app.use( fileUpload() );
```

现在，添加一个 `/upload `端点并使用相同文件名将上传的文件保存在“草稿”文件夹中。 然后，调用您之前编写的函数以创建同一PDF的PDF文件（如果文档格式尚不是）。 您可以根据原始上传PDF的名称为新的文档文件生成文件名：

```
// Create a PDF file from an uploaded file
app.post( "/upload", ( req, res ) => {
    if( !req.files || Object.keys( req.files ).length === 0 ) {
        return res.status( 400 ).send( "No files were uploaded." );
    }
    
    // Create PDF from the uploaded file
    let file = req.files.myFile;
    file.mv( __dirname + "/uploads/drafts/" + file.name, ( err ) => {
        if( err ) {
            return res.status( 500 ).send( err );
        }
        if( file.name.endsWith( ".pdf" ) ) {
            res.redirect( "/" );
        }
        else {
            // Convert to PDF
            fileToPDF( __dirname + "/uploads/drafts/" + file.name, __dirname + "/uploads/drafts/" + file.name.replace( /\./g, "-" ) + ".pdf", ( file ) => {
                res.redirect( "/" );
            } );
        }
    });
} );
```

## 创建上传页面

现在，要从Web应用程序上传文件，请创建 `index.html` uploads文件夹中的网页。 在该页面上，添加一个文件上传表单，用于将文件发送到/upload端点：

```
<form ref="uploadForm" 
      action="/upload"
      method="post" 
      encType="multipart/form-data">
      <input type="file" name="myFile" accept=".doc,.docx,.ppt,.pptx,.xls,.xlsx,.txt,.rtf,.bmp,.jpg,.gif,.tiff,.png">
      <input type="submit" value="Upload File" />
  </form>
```

![网页上上载文件功能的屏幕截图](assets/reviews_1.png)

现在可以将文档上载到Node.js服务器。 服务器将该文件保存在uploads/draft文件夹中，并创建一个PDF格式版本。

您现在已准备好嵌入已上传的文档，因此使用PDFEmbed API可让用户轻松地将注释和批注添加到文档。

## 正在枚举PDF文件

由于典型的文档工作流程可能涉及多个文档，因此您必须显示文档列表，并将每个文档链接到应用程序中的新文档审阅页面。

首先，在服务器代码中添加/files端点，该端点获取并返回存储在uploads/draft文件夹中的所有PDF文件的列表：

```
const fs = require( "fs" );

app.get( "/files", ( req, res ) =\> {

fs.readdir( \_\_dirname + "/uploads/drafts/", ( err, files ) =\> {

if( err ) {

return res.status( 500 ).send( err );using

}

return res.json( files.filter( f =\> f.endsWith( ".pdf" ) ) );

} );

} );
```

添加 `/download/:file` 路由，允许您访问上传的PDF文件以嵌入到网页中。

>[!NOTE]
>
>在生产应用程序中，必须添加身份验证和授权，以确保请求来自有效用户且允许用户访问文档。

```
app.get( "/download/:file", function( req, res ){
    // Note: In production code, this should check authentication and user access permissions
    res.download( __dirname + "/uploads/drafts/" + req.params[ "file" ] );
});
```

使用在加载时填充的文件列表元素更新index.html页。 每个项目都可以链接到draft.html网页，并且可以使用查询字符串参数将文件名传递给网页。

>[!NOTE]
>
>使用jQuery追加每个项目，因此必须在网页上加载jQuery库或使用其他方法追加元素。

```
  <ul id="filelist">
      <li>Loading documents...</li>
  </ul>

  ...

  <script>
      // Load current files
      fetch( "/files" )
      .then( r => r.json() )
      .then( files => {
          if( files && files.length > 0 ) {
              $( "#filelist" ).empty();
              files.forEach( file => {
                  $( "#filelist" ).append( `<li><a
  href="/draft.html?file=${file}">${file}</a></li>` );
              })
          } else {
                  $("#filelist").append("<div>No documents found.</div>");
                }
      });
  </script>
```

![选择文件以供审阅的屏幕截图](assets/reviews_2.png)

## 嵌入PDF

您已准备好在Web应用程序中嵌入和显示PDF文件。

创建一个名为“draft.html”的网页，并在该网页上为嵌入的PDF添加一个div元素：

```
  <div id="adobe-dc-view"></div>
```

包括 [!DNL Acrobat Services] 库：

```
  <script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

在自定义脚本标记内，从查询字符串参数中解析文件名，以便知道要在页面上嵌入哪个文件：

```
  <script type="text/javascript">
          let params = new URLSearchParams( window.location.search );
          let filename = params.get( "file" );
  </script>
```

为adobe_dc_view_sdk.ready事件添加一个文档事件侦听器，以将指定的PDF文件加载到div元素内的嵌入视图中。 从PDFEmbed API凭据使用您的客户端ID。 要启用注释和批注，请以FULL_WINDOW模式嵌入视图，并将showAnnotationsTools选项设置为true。

```
  document.addEventListener( "adobe_dc_view_sdk.ready", () => { 
      var adobeDCView = new AdobeDC.View( { 
          clientId: "YOUR CLIENT ID HERE",
          divId: "adobe-dc-view",
          locale: "en-US",
      } );
      adobeDCView.previewFile( {
          content: { location: { url: "download/" + filename } },
          metaData: { fileName: "Draft Version.pdf" }
      }, {
          embedMode: "FULL_WINDOW",
          showAnnotationTools: true,
          showPageControls: true
      } );
  });
```

## 创建用户配置文件

默认情况下，此视图中的注释和批注显示为“访客”。 通过将页面代码中的用户配置文件回调注册到PDF视图，可以为注释和批注设置当前审阅者的名称。 以下是配置文件示例。 在包括用户身份验证的完整应用程序中，登录的用户会话的配置文件信息可以按此方式设置，以标识审阅工作流程中文档的每个注释者。

```
  adobeDCView.registerCallback(
      AdobeDC.View.Enum.CallbackType.GET_USER_PROFILE_API,
      () => {
          return new Promise( ( resolve, reject ) => {
              resolve({
                  code: AdobeDC.View.Enum.ApiResponseCode.SUCCESS,
                  data: {
                      userProfile: {
                          name: "YOUR NAME",
                          firstName: "FIRST",
                          lastName: "LAST",
                          email: "document.editor@adobe.com"
                      }
                  }
              });
          });
      }
  );
```

当您使用此网页查看和注释任何上传的文档时，您的个人资料会将您标识为特定用户。

## 保存文档反馈

用户在文档上添加注释后，单击 **保存。** 默认情况下，单击 **保存** 下载更新后的PDF文件。 更改此操作以更新服务器上的当前PDF文件。

添加 `/save` 一个端点，用于覆盖uploads/draft文件夹中的PDF文件：

```
  // Overwrite the PDF file with latest PDF changes and annotations
  app.post( "/save", ( req, res ) => {
      if( !req.files || Object.keys( req.files ).length === 0 ) {
          return res.status( 400 ).send( "No files were uploaded." );
      }

      let file = req.files.pdf;
      file.mv( __dirname + "/uploads/drafts/" + file.name, ( err ) => {
          if( err ) {
              return res.status( 500 ).send( err );
          }
          res.send( "File uploaded" );
      });
  } );
```

为SAVE_API注册PDF视图回调，该视图会将内容上传到/save端点。 您可以更改autoSaveFrequency值，使应用程序能够自动将更改保存到计时器，并在完成后将其他元数据添加到当前嵌入的文件（如果需要）。

```
  adobeDCView.registerCallback(
      AdobeDC.View.Enum.CallbackType.SAVE_API,
      ( metaData, content, options ) => {
          return new Promise( ( resolve, reject ) => {
              let formData = new FormData();
              formData.append( "pdf", new Blob( [ content ] ), "drafts/" + filename
  );
              fetch( "/save", {
                  method: "POST",
                  body: formData
              }).then( resp => {
                  resolve({
                      code: AdobeDC.View.Enum.ApiResponseCode.SUCCESS,
                      data: {
                          /* Updated file metadata after successful save operation */
                          metaData: Object.assign( metaData, {} )
                      }
                  });
              });
          });
      },
      {
          autoSaveFrequency: 0,
          enableFocusPolling: false,
          showSaveButton: true
      }
  );
```

现在，草稿文档上的注释和批注会保存在服务器上。 您可以 [阅读有关回电方式的更多信息](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#callbacks-workflows) 适合您的工作流程。 例如， [状态回调](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#status-callback) 如果多人同时审阅和评论同一文档，可帮助处理文件冲突。

最后，使用PDF服务API将所有编辑的文档合并为一个PDF文件。

## 合并PDF文件

PDF组合代码类似于PDF创建代码，但使用CombineFiles操作并将每个文件添加为输入。

```
  function combineFilesToPDF( files, outputFilename, callback ) {
      // Create an ExecutionContext using credentials and create a new operation
  instance.
      const executionContext = PDFToolsSdk.ExecutionContext.create( credentials ),
          combineFilesOperation = PDFToolsSdk.CombineFiles.Operation.createNew();

      // Set operation inputs from source files.
      files.forEach( file => {
          const input = PDFToolsSdk.FileRef.createFromLocalFile( file );
          combineFilesOperation.addInput( input );
      } );

      // Execute the operation and Save the result to the specified location.
      combineFilesOperation.execute( executionContext )
          .then( result => {
              result.saveAsFile( outputFilename );
              callback( outputFilename );
          } );
 }
```

## 下载最终PDF

添加一个名为/finalize的终结点，该终结点调用函数以合并内部的所有PDF文件 `uploads/drafts` 文件夹到 `Final.pdf` 文件，然后下载它。

```
  app.get( "/finalize", ( req, res ) => {
      fs.readdir( __dirname + "/uploads/drafts/", ( err, files ) => {
          if( err ) {
              return res.status( 500 ).send( err );
          }
          combineFilesToPDF(
              files.filter( f => f.endsWith( ".pdf" ) ).map( f => __dirname + 
  "/uploads/drafts/" + f ),
              __dirname + "/uploads/Final.pdf", ( file ) => {
              res.download( file );
          } );
      } );
  } );
```

最后，在主index.html网页中添加一个链接到此/finalize终结点。 此链接使用户能够下载文档工作流程的结果。

```
<a href="/finalize">Download final PDF</a>
```

![下载最终文档的屏幕截图](assets/reviews_3.png)

## 后续步骤

此实际操作教程演示了如何 [!DNL Acrobat Services] API集成 [文档共享和审阅工作流程](https://www.adobe.io/apis/documentcloud/dcsdk/review-and-approval.html) 到Web应用程序中。 该应用程序允许远程员工共享文件并与队友协作，这对在家工作的员工和承包商尤其有帮助。

您可以使用这些技巧在应用程序中启用协作或浏览 [PDF服务节点SDK示例](https://github.com/adobe/pdftools-node-sdk-samples) 和 [PDF嵌入API示例](https://github.com/adobe/pdf-embed-api-samples) 在GitHub上寻找有关如何使用AdobeAPI的灵感。

准备好在自己的应用程序中启用文档共享和审阅了吗？ 注册您的 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 开发人员帐户。 免费访问Adobe PDF Embed，并享受其他API的6个月免费试用期。 试用结束后，您可以 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 随着您的业务增长，每次文档交易只需0.05美元。
