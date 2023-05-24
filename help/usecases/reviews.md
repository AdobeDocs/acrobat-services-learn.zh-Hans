---
title: 审阅和批准
description: 了解如何为跨团队协作构建文档审阅和审批工作流程
type: Tutorial
role: Developer
level: Intermediate
thumbnail: KT-8094.jpg
kt: 8094
exl-id: d704620f-d06a-4714-9d09-3624ac0fcd3a
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1623'
ht-degree: 1%

---

# 审阅和批准

![用例英雄横幅](assets/UseCaseReviewsHero.jpg)

在新型冠状病毒肺炎疫情期间，许多公司需要远程跨团队协作。 [共享和审阅数字文档](https://www.adobe.io/apis/documentcloud/dcsdk/review-and-approval.html) 对团队和跨职能资源提出了一系列挑战。

这些难题包括共享不同文件格式的文档、有效地审阅和评论内容，以及与最新编辑内容同步。 [!DNL Adobe Acrobat Services] API旨在使应用程序开发人员能够为其用户解决这些难题。

## 您可以学到的内容

此实际操作教程介绍了如何在Node.js和Express Web应用程序中构建文档审阅和审批工作流程。 要继续学习本教程，您需要具备Node.js的一些经验。

应用程序具有以下功能：

* 将不同的文件类型转换为PDF

* 启用文件上传

* 允许用户添加注释和批注

* 显示PDF和那些注释

* 启用用户配置文件以识别注释作者

* 将文件合并为最终PDF以供用户下载

## 相关API和资源

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF嵌入API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [项目代码](https://github.com/contentlab-io/adobe_reviews_and_approvals)

## 创建AdobeAPI凭据

在启动代码之前，您必须 [创建凭据](https://www.adobe.com/go/dcsdks_credentials) 适用于Adobe PDF Embed API和Adobe PDF Services API。 PDF嵌入API可免费使用。 PDF服务API免费使用6个月，然后您可以切换到 [即付即用计划](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 每个文档事务只需\$0.05。

为PDF服务API创建凭据时，选择 **创建个性化代码示例** ，然后选择Node.js作为语言。 保存ZIP文件，并将pdftools-api-credentials.json和private.key提取到Node.js Express项目的根目录中。

## 设置项目和依赖项

将Node.js和Express项目设置为提供名为“public”的文件夹中的静态文件。 您可以根据自己的喜好设置项目方式。 要快速启动并运行，您可以使用 [Express应用程序生成器](https://expressjs.com/en/starter/generator.html)的 或者，如果您想保持简单的话，您可以 [从头开始](https://expressjs.com/en/starter/hello-world.html) 并将代码保存在单个JavaScript文件中。 在上面链接的项目示例中，您将使用单文件方法，并将所有代码保留在index.js中。

复制 `pdftools-api-credentials.json` 和 `private.key` 文件从个性化代码示例添加到项目的根目录中。 此外，请将它们添加到.gitignore文件（如果您有），这样您的凭据文件就不会意外发送到存储库。

接下来，运行 `npm install @adobe/documentservices-pdftools-node-sdk` 安装用于PDF服务的Node.js SDK。 导入此模块并在您的代码（示例项目中的index.js）内创建API凭据对象，然后导入其他依赖项，如下所示：

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

现在，您已准备好使用 [!DNL Acrobat Services] API。

## 将文件转换为PDF

在文档工作流程的第一部分，最终用户必须上传要共享的文档。 要启用此功能，您可以添加上传功能并将不同的文档文件格式合并为PDF，以便为审阅流程做好准备。

首先创建一个函数，用于根据 [示例代码片断PDF服务API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html)的 此示例还显示了许多其他重要功能的代码片段，包括光学字符识别(OCR)、密码保护和删除以及压缩。

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

接下来，服务器需要在Web服务器上有一个文件上载端点来接收和处理文档。

首先，在上传文件夹中创建一个文件夹，并将其命名为“草稿”。 您可将上传的文件和转换后的PDF文件存储在这里。 接下来，运行 `npm install express-fileupload` 要安装Express-FileUpload模块并在代码中向Express添加中间件，请执行以下操作：

```
const fileUpload = require( "express-fileupload" );
app.use( fileUpload() );
```

现在，添加 `/upload `endpoint并使用相同的文件名将上传的文件保存到draft文件夹中。 然后，调用您之前编写的函数，以创建同一文档的PDF文件(如果该文件尚未为PDF格式)。 您可以根据原始上传的PDF的名称为新的文档文件生成文件名：

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

## 创建上载页面

现在，若要从Web应用程序上传文件，请创建 `index.html` 上传文件夹中的网页。 在该页面上，添加将文件发送到/upload端点的文件上载表单：

```
<form ref="uploadForm" 
      action="/upload"
      method="post" 
      encType="multipart/form-data">
      <input type="file" name="myFile" accept=".doc,.docx,.ppt,.pptx,.xls,.xlsx,.txt,.rtf,.bmp,.jpg,.gif,.tiff,.png">
      <input type="submit" value="Upload File" />
  </form>
```

![网页上传文件功能的屏幕截图](assets/reviews_1.png)

您现在可以将文档上传到Node.js服务器。 服务器将该文件保存在uploads/draft文件夹内，并在旁边创建一个PDF格式版本。

您现在可以嵌入已上传的PDF，因此使用“文档嵌入”API使用户能够轻松向文档添加注释和批注。

## 枚举PDF文件

由于典型的文档工作流程可能会涉及多个文档，因此您必须显示文档列表，并将每个文档链接到应用程序中的新文档审阅页面。

首先，在服务器代码中添加/files端点，该端点会获取并返回存储在uploads/draws文件夹中的所有PDF文件的列表：

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

添加 `/download/:file` 用于访问上传的PDF文件以嵌入网页的路由。

>[!NOTE]
>
>在生产应用程序中，必须添加身份验证和授权，以确保请求来自有效用户，并且允许用户访问文档。

```
app.get( "/download/:file", function( req, res ){
    // Note: In production code, this should check authentication and user access permissions
    res.download( __dirname + "/uploads/drafts/" + req.params[ "file" ] );
});
```

使用在加载时填充的文件列表元素更新index.html页。 每个项目都可以链接到draft.html网页，并使用查询字符串参数将文件名传递给该页。

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

![选择要审阅的文件的屏幕截图](assets/reviews_2.png)

## 嵌入PDF

您已准备好在Web应用程序中嵌入PDF文件并显示它们。

创建名为“draft.html”的网页，并在该网页上为嵌入元素添加divPDF:

```
  <div id="adobe-dc-view"></div>
```

包括 [!DNL Acrobat Services] 库：

```
  <script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

在自定义脚本标签中，从查询字符串参数分析文件名，以便知道要在页面上嵌入哪个文件：

```
  <script type="text/javascript">
          let params = new URLSearchParams( window.location.search );
          let filename = params.get( "file" );
  </script>
```

为adobe_dc_view_sdk.ready事件添加一个PDF事件侦听器，该事件将指定的事件文件加载到div元素内的嵌入式视图中。 使用您的客户端ID(来自PDF嵌入API凭据)。 您想要启用注释和批注，请在FULL_WINDOW模式下嵌入视图，并将showAnnotationsTools选项设置为true。

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

默认情况下，注释和批注在此视图中显示为“来宾”。 您可以为注释和批注设置当前审阅人的姓名，方法是在页面代码中将用户配置文件回调注册到PDF视图。 下面是一个示例配置文件。 在包括用户身份验证的完整应用程序中，可以采用这种方式设置登录的用户会话的配置文件信息，以标识审阅工作流程中文档的每个注释者。

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

当您使用此网页查看和批注任何上载的文档时，您的配置文件会将您标识为特定用户。

## 保存文档反馈

用户为文档添加注释后，他们会单击 **保存。** 默认情况下，单击 **保存** 下载更新后的PDF文件。 更改此操作可更新服务器上当前PDF的文件。

添加 `/save` 用于覆盖uploads/draft文件夹中的PDF文件的服务器代码的端点：

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

为SAVE_API注册PDF视图回调，以便将内容上传到/save端点。 如果需要，可以更改autoSaveFrequency值，使应用程序能够自动保存计时器上的更改，并在完成时向当前嵌入的文件添加其他元数据。

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

现在，草稿文档中的注释和批注会保存到服务器上。 您可以 [阅读有关回调方式的更多信息](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#callbacks-workflows) 适合您的工作流程。 例如， [状态回调](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#status-callback) 如果多人希望同时审阅和注释同一文档，可帮助处理文件冲突。

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

添加一个名为/finalize的端点，该端点调用函数以合并PDF中的所有 `uploads/drafts` 文件夹 `Final.pdf` 文件，然后下载。

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

最后，在main index.html网页中添加一个指向此/finalize端点的链接。 用户可通过此链接下载文档工作流程的结果。

```
<a href="/finalize">Download final PDF</a>
```

![下载最终文档的屏幕截图](assets/reviews_3.png)

## 后续步骤

这个实际操作教程演示了 [!DNL Acrobat Services] API集成了 [文档共享和审阅工作流程](https://www.adobe.io/apis/documentcloud/dcsdk/review-and-approval.html) 到Web应用程序中。 该应用程序允许远程工作人员与队友共享文件并进行协作，这对于在家办公的员工和承包商尤其有用。

您可以使用这些技术来实现应用程序或浏览中的协作 [PDF服务节点SDK示例](https://github.com/adobe/pdftools-node-sdk-samples) 和 [PDF嵌入API示例](https://github.com/adobe/pdf-embed-api-samples) 在GitHub上获得灵感，了解如何使用Adobe的API。

是否准备好在自己的应用程序中启用文档共享和审阅？ 注册您的 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 开发人员帐户。 免费访问Adobe PDF Embed，并享受其他API六个月的免费试用期。 试用后，您可以 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 随着业务的增长，每个文档交易只需\$0.05。
