---
title: Java中的HR文档工作流程
description: '"[!DNL Adobe Acrobat Services] API可以轻松地将PDF功能整合到您的人力资源网络应用程序中”'
type: Tutorial
role: Developer
level: Intermediate
thumbnail: KT-7474.jpg
jira: KT-7474
exl-id: add4cc5c-06e3-4ceb-930b-e8c9eda5ca1f
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1899'
ht-degree: 2%

---

# Java中的HR文档工作流程

![用例英雄横幅](assets/UseCaseHRHero.jpg)

许多企业需要有关新员工的文件，例如在家办公的员工的工作场所协议。 传统上，企业以难于管理和存储的表单来实际管理这些文档。 当切换到电子文档时，PDF文件是理想之选，因为它们比其他文件类型更安全，更不易修改。 此外，它们还支持数字签名。

## 您可以学到的内容

在本实操教程中，了解如何在一个简单的Java Spring MVC应用程序中实施基于Web的HR表单，该表单可保存要通过注销PDF的工作区协议。

## 相关API和资源

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [项目代码](https://github.com/dawidborycki/adobe-sign)

## 生成API凭据

首先注册Adobe PDF Services API免费试用版。 转到 [Adobe](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK) [网站](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK) 然后点击 *开始使用* 按钮 *创建新凭据*&#x200B;的 免费试用提供1,000个文档事务，可在六个月内使用。 在下一页上（见下文），选择服务(PDF服务API)，设置凭据名称（例如，HRDocumentWFCredentials），然后输入说明。

选择语言（本示例使用Java）并选中 *创建个性化代码示例*&#x200B;的 最后，应确保代码示例已包含您使用的预填充的pdftools-api-credentials.json文件，以及可在API中对应用程序进行身份验证的私钥。

最后，单击 *创建凭据* 按钮。 此时将生成凭据，并且示例会自动开始下载。

![“创建新凭据”屏幕截图](assets/HRWJ_1.png)

要确保凭据正常工作，请打开下载的示例。 在这里，您正在使用IntelliJ IDEA。 打开源代码时，集成开发环境(IDE)会要求生成引擎。 此示例中使用Maven，但您也可以根据自己的喜好使用Gradle。

接下来，执行 `mvn clean install` Maven构建jar文件的目标。

最后，运行CombinePDF范例，如下所示。 代码在输出文件夹中生成PDF。

![用于运行CombinePDF示例屏幕截图的菜单](assets/HRWJ_2.png)

## 创建Spring MVC应用程序

给定凭据，然后创建应用程序。 本示例使用Spring Initializer。

首先，配置项目设置以使用Java 8语言和Jar打包（请参阅下面的屏幕截图）。

![春季版初始化的屏幕截图](assets/HRWJ_3.png)

其次，添加Spring Web（来自Web）和Thymeleaf（来自Template Engine）：

![用于添加Spring Web和Thymeleaf的屏幕截图](assets/HRWJ_4.png)

创建项目后，转到pom.xml文件，并使用pdftools-sdk和log4j-slf4j-impl补充依赖关系部分：

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

</dependencies>
```

然后，使用下载的两个带有示例代码的文件补充项目根文件夹：

* pdftools-api-credentials.json

* private.key

## 呈现Web表单

要呈现Web表单，请使用呈现个人数据表单的控制器修改应用程序并处理表单的发布。 因此，首先使用PersonForm模型类修改应用程序：

```
package com.hr.docsigning;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

public class PersonForm {
    @NotNull
    @Size(min=2, max=30)
    private String firstName;

    @NotNull
    @Size(min=2, max=30)
    private String lastName;

    public String getFirstName() {
            return this.firstName;
    }


    public void setFirstName(String firstName) {
            this.firstName = firstName;
    }

    public String getLastName() {
           return this.lastName;
    }

    public void setLastName(String lastName) {
            this.lastName = lastName;
    }

    public String GetFullName() {
           return this.firstName + " " + this.lastName;
    }
}
```

此类包含两个属性： `firstName` 和 `lastName`的 此外，使用此简单验证检查它们是否介于2到30个字符之间。

给定模型类，您可以创建控制器（请参阅伴侣代码中的PersonController.java）：

```
package com.hr.docsigning;
import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import javax.validation.Valid;


@Controller
public class PersonController {
    @GetMapping("/")
    public String showForm(PersonForm personForm) {
        return "form";
    }
}
```

控制器只有一种方法：showForm。 它负责使用位于resources/templates/form.html中的HTML模板呈现表单：

```
<html>
<head>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
</head>
 
<body>
<div class="w3-container">
    <h1>HR Department</h1>
</div>
 
<form class="w3-panel w3-card-4" action="#" th:action="@{/}"
        th:object="${personForm}" method="post">
    <h2>Personal data</h2>
    <table>
        <tr>
            <td>First Name:</td>
            <td><input type="text" class="w3-input"
                placeholder="First name" th:field="*{firstName}" /></td>
            <td class="w3-text-red" th:if="${#fields.hasErrors('firstName')}"
                th:errors="*{firstName}"></td>
        </tr>
        <tr>
            <td>Last Name:</td>
            <td><input type="text" class="w3-input"
                placeholder="Last name" th:field="*{lastName}" /></td>
            <td class="w3-text-red" th:if="${#fields.hasErrors('lastName')}"
                th:errors="*{lastName}"></td>
        </tr>
        <tr>
            <td><button class="w3-button w3-black" type="submit">Submit</button></td>
        </tr>
    </table>
</form>
</body>
</html>
```

要渲染动态内容，可使用Thymeleaf模板渲染引擎。 因此，运行应用程序后，您应看到以下内容：

![渲染内容的屏幕截图](assets/HRWJ_5.png)

## 使用动态PDF生成内容

现在，通过在呈现PDF数据表单后动态填充选定字段来生成包含虚拟合同的用户文档。 具体来说，您必须将人员数据填充到预先创建的合同中。

为简单起见，您只有标题、子标题和字符串常量读数：“这份合同是为了\&lt;full name=&quot;&quot; of=&quot;&quot; the=&quot;&quot; person=&quot;&quot;>“。

要实现这一目标，请从Adobe的 [从动态PDF创建HTML](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf-from-dynamic-html) 示例。 通过分析示例代码，您可以看到动态HTML字段填充过程如下所示。

首先，必须准备HTML页，其中包含静态和动态内容。 使用JavaScript更新动态部分。 即，PDF服务API将JSON对象注入您的HTML。

然后，使用加载HTML文档时调用的JavaScript函数获取JSON属性。 此JavaScript函数更新选定的DOM元素。 下面是一个包含person数据的填充span元素的示例(请参阅伴侣代码的src\\main\\resources\\contract\\index.html):

```
<html>
<head>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
</head>
 
<body onload="updateFullName()">
    <script src="./json.js"></script>
    <script type="text/javascript">
        function updateFullName()
        {
            var document = window.document;
            document.getElementById("personFullName").innerHTML = String(
                window.json.personFullName);
        }
    </script>
 
    <div class="w3-container ">
        <h1>HR Department</h1>
 
        <h2>Contract details</h2>
 
        <p>This contract was prepared for:
            <strong><span id="personFullName"></span></strong>
        </p>
    </div>
</body>
</html>
```

然后，您必须将HTML与所有依赖的JavaScript和CSS文件一起压缩。 PDF服务API不接受HTML文件。 相反，需要使用zip文件作为输入。 在本例中，将压缩文件存储在src\\main\\resources\\contract\\index.zip中。

之后，你可以补充 `PersonController` 使用其他方法处理POST请求：

```
@PostMapping("/")
public String checkPersonInfo(@Valid PersonForm personForm,
    BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        return "form";
    }
 
    CreateContract(personForm);
 
    return "contract-actions";
}
```

上述方法使用提供的PDF数据创建合同合同，并呈现合同操作视图。 后者提供到生成的PDF的链接并用于对PDF签名。

现在，我们来了解一下 `CreateContract` 方法有效（完整列表如下所示）。 此方法依赖于两个字段：

* `LOGGER`从log4j到调试有关任何异常的信息

* `contractFilePath`，包含指向生成的路径的文件PDF

在 `CreateContract` 方法设置凭据并从PDF创建HTML。 要在合同中传递和填充人员数据，请使用 `setCustomOptionsAndPersonData` 帮手。 此方法从表单中检索人物数据，然后通过上面说明的JSON对象将其发送到生成的PDF。

此外， `setCustomOptionsAndPersonData` 显示如何通过禁用PDF页眉和页脚来控制文档外观。 完成这些步骤后，将PDF文件保存到output/contract.pdf，并最终删除以前生成的文件。

```
private static final Logger LOGGER = LoggerFactory.getLogger(PersonController.class);
private String contractFilePath = "output/contract.pdf"; 
private void CreateContract(PersonForm personForm) {
    try {
        // Initial setup, create credentials instance.
        Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
                .fromFile("pdftools-api-credentials.json")
                .build();

        //Create an ExecutionContext using credentials 
       //and create a new operation instance.
        ExecutionContext executionContext = ExecutionContext.create(credentials);
        CreatePDFOperation htmlToPDFOperation = CreatePDFOperation.createNew();

        // Set operation input from a source file.
        FileRef source = FileRef.createFromLocalFile(
           "src/main/resources/contract/index.zip");
       htmlToPDFOperation.setInput(source);

        // Provide any custom configuration options for the operation
        // You pass person data here to dynamically fill out the HTML
        setCustomOptionsAndPersonData(htmlToPDFOperation, personForm);

        // Execute the operation.
        FileRef result = htmlToPDFOperation.execute(executionContext);

        // Save the result to the specified location. Delete previous file if exists
        File file = new File(contractFilePath);
        Files.deleteIfExists(file.toPath());

        result.saveAs(file.getPath());

    } catch (ServiceApiException | IOException | 
             SdkException | ServiceUsageException ex) {
        LOGGER.error("Exception encountered while executing operation", ex);
    }
}
 
private static void setCustomOptionsAndPersonData(
    CreatePDFOperation htmlToPDFOperation, PersonForm personForm) {
    //Set the dataToMerge field that needs to be populated 
    //in the HTML before its conversion
    JSONObject dataToMerge = new JSONObject();
    dataToMerge.put("personFullName", personForm.GetFullName());
 
    // Set the desired HTML-to-PDF conversion options.
    CreatePDFOptions htmlToPdfOptions = CreatePDFOptions.htmlOptionsBuilder()
        .includeHeaderFooter(false)
        .withDataToMerge(dataToMerge)
        .build();
    htmlToPDFOperation.setOptions(htmlToPdfOptions);
}
```

在生成合同时，您还可以合并具有固定合同条款的动态、特定于人员的数据。 为此，请遵循 [从静态PDF创建HTML](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf-from-dynamic-html) 示例。 或者，您可以 [合并两个PDF](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf-from-static-html)的

## 显示PDF文件以供下载

现在，您可以提供指向生成的PDF的链接以供用户下载。 为此，请先创建contract-actions.html文件（请参阅伴侣代码的resources/templates contract-actions.html）：

```
<html>
<head>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
</head>
 
<div class="w3-container ">
    <h1>HR Department</h1>
 
    <h2>Contract file</h2>
 
    <p>Click <a href="/pdf">here</a> to download your contract</p>
</div>
</body>
</html>
```

然后，实施 `downloadContract` 方法 `PersonController` 类：

```
@RequestMapping("/pdf")
public void downloadContract(HttpServletResponse response)
{
    Path file = Paths.get(contractFilePath);
 
    response.setContentType("application/pdf");
    response.addHeader(
        "Content-Disposition", "attachment; filename=contract.pdf");

    try
    {
        Files.copy(file, response.getOutputStream());
        response.getOutputStream().flush();
    }
    catch (IOException ex) 
    {
        ex.printStackTrace();
    }
}
```

运行应用程序后，您将获得以下流程。 第一个屏幕显示个人数据表单。 要进行测试，请用介于2和30个字符之间的任意值填充它：

![数据值的屏幕截图](assets/HRWJ_6.png)

单击 *提交* 按钮，表单将验证，PDF将基于HTML(resources/contract/index.html)生成。 应用程序将显示另一个视图(contract-details)，您可以在其中下载PDF:

![从中可以下载PDF](assets/HRWJ_7.png)

PDF在Web浏览器中渲染后，如下所示。 即，您输入的个人数据将传播到PDF:

![使用个人数据PDF呈现的屏幕截图](assets/HRWJ_8.png)

## 启用签名和安全性

协议准备就绪后，Adobe Sign可以添加表示批准的数字签名。 Adobe Sign身份验证的工作方式与OAuth略有不同。 现在，我们来了解一下如何将应用程序与Adobe Sign集成。 为此，您必须为应用程序准备访问令牌。 然后，使用Adobe Sign Java SDK编写客户端代码。

要获取授权令牌，必须执行以下几个步骤：

首先，注册 [开发人员帐户](https://acrobat.adobe.com/cn/zh-Hans/sign/developer-form.html)的

在 [Adobe Sign portal](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/create_app.md)的

按所述为应用程序配置OAuth [此处](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/configure_oauth.md) 和 [此处](https://secure.eu1.adobesign.com/public/static/oauthDoc.jsp)的 记下您的客户端标识符和客户端密钥。 然后，您可以使用 `https://www.google.com` 作为重定向URI和以下范围：

* user_login:self

* agreement_read:帐户

* agreement_write:帐户

* agreement_send:帐户

按照如下方式，使用客户端ID代替\来准备URL&lt;client_id>:

```
https://secure.eu1.adobesign.com/public/oauth?redirect_uri=https://www.google.com
&response_type=code
&client_id=<CLIENT_ID>
&scope=user_login:self+agreement_read:account+agreement_write:account+agreement_send:account
```

在Web浏览器中键入上面的URL。 您将重定向到google.com，并且代码在地址栏中显示为code=\&lt;your_code>例如：

```
https://www.google.com/?code=<YOUR_CODE>&api_access_point=https://api.eu1.adobesign.com/&web_access_point=https://secure.eu1.adobesign.com%2F
```

请注意为\提供的值。&lt;your_code> 和api_access_point。

要发送向您提供访问POST符的HTTP请求请求，请使用客户端ID、\&lt;your_code>和api_access_point值。 您可以使用 [Postman](https://helpx.adobe.com/sign/kb/how-to-create-access-token-using-postman-adobe-sign.html) 或cURL:

```
curl --location --request POST "https://**api.eu1.adobesign.com**/oauth/token"
\\

\--data-urlencode "client_secret=**\<CLIENT_SECRET\>**" \\

\--data-urlencode "client_id=**\<CLIENT_ID\>**" \\

\--data-urlencode "code=**\<YOUR_CODE\>**" \\

\--data-urlencode "redirect_uri=**https://www.google.com**" \\

\--data-urlencode "grant_type=authorization_code"
```

示例响应如下所示：

```
{
    "access_token":"3AAABLblqZhByhLuqlb-…",
    "refresh_token":"3AAABLblqZhC_nJCT7n…",
    "token_type":"Bearer",
    "expires_in":3600
}
```

记下access_token。 您需要它来授权客户端代码。

## 使用Adobe Sign Java SDK

获得访问令牌后，您可以将REST API调用发送到Adobe Sign。 要简化此过程，请使用Adobe Sign Java SDK。 源代码位于 [AdobeGitHub存储库](https://github.com/adobe-sign/AdobeSignJavaSdk)的

要将此包与应用程序集成，必须克隆该代码。 然后，创建Maven包（mvn包）并将以下文件安装到项目中（您可以在adobe-sign-sdk文件夹的伴随代码中找到这些文件）：

* target/swagger-java-client-1.0.0.jar

* target/lib/gson-2.8.1.jar

* target/lib/gson-fire-1.8.0.jar

* target/lib/hamcrest-core-1.3.jar

* target/lib/junit-4.12.jar

* target/lib/logging-interceptor-2.7.5.jar

* target/lib/okhttp-2.7.5.jar

* target/lib/okio-1.6.0.jar

* target/lib/swagger-annotations-1.5.15.jar

在IntelliJ IDEA中，您可以使用将那些文件添加为依赖项 *项目结构* （文件/项目结构）。

## 发送PDF以供签名

现在，您就可以发送协议进行签名了。 为此，请先用另一个指向发送请求的超链接来补充contract-details.html:

```
<html>
<head>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
</head>
 
<div class="w3-container ">
    <h1>HR Department</h1>
 
    <h2>Contract file</h2>
 
    <p>Click <a href="/pdf"> here</a> to download your contract</p>
 
    
</div>
</body>
</html>
```

然后，再添加一个控制器， `AdobeSignController`，您可以在其中实施 `sendContractMethod` （请参阅伴侣代码）。 此方法的工作方式如下：

首先，它使用 `ApiClient` 获取API端点。

```
ApiClient apiClient = new ApiClient();

//Default baseUrl to make GET /baseUris API call.
String baseUrl = "https://api.echosign.com/";
String endpointUrl = "/api/rest/v6";
apiClient.setBasePath(baseUrl + endpointUrl);

// Provide an OAuth Access Token as "Bearer access token" in authorization
String authorization = "Bearer ";

// Get the baseUris for the user and set it in apiClient.
BaseUrisApi baseUrisApi = new BaseUrisApi(apiClient);
BaseUriInfo baseUriInfo = baseUrisApi.getBaseUris(authorization);
apiClient.setBasePath(baseUriInfo.getApiAccessPoint() + endpointUrl);
```

然后，该方法使用contract.pdf文件创建临时文档：

```
// Get PDF file
String filePath = "output/";
String fileName = "contract.pdf";
File file = new File(filePath + fileName);
String mimeType = "application/pdf";
 
//Get the id of the transient document.
TransientDocumentsApi transientDocumentsApi =
    new TransientDocumentsApi(apiClient);
TransientDocumentResponse response = transientDocumentsApi.createTransientDocument(authorization,
    file, null, null, fileName, mimeType);
String transientDocumentId = response.getTransientDocumentId();
```

接下来，您必须创建协议。 为此，请使用contract.pdf文件并将协议状态设置为IN_PROCESS以立即发送文件。 此外，您可以选择电子签名：

```
// Create AgreementCreationInfo
AgreementCreationInfo agreementCreationInfo = new AgreementCreationInfo();
 
// Add file
FileInfo fileInfo = new FileInfo();
fileInfo.setTransientDocumentId(transientDocumentId);
agreementCreationInfo.addFileInfosItem(fileInfo);
 
// Set state to IN_PROCESS, so the agreement is be sent immediately
agreementCreationInfo.setState(AgreementCreationInfo.StateEnum.IN_PROCESS);
agreementCreationInfo.setName("Contract");
agreementCreationInfo.setSignatureType(AgreementCreationInfo.SignatureTypeEnum.ESIGN);
```

接下来，您可以按照以下方式添加协议收件人。 您在此处添加了两个收件人（请参阅“员工”和“经理”部分）：

```
// Provide emails of recipients to whom agreement is be sent
// Employee
ParticipantSetInfo participantSetInfo = new ParticipantSetInfo();
ParticipantSetMemberInfo participantSetMemberInfo = new ParticipantSetMemberInfo();
participantSetMemberInfo.setEmail("");
participantSetInfo.addMemberInfosItem(participantSetMemberInfo);
participantSetInfo.setOrder(1);
participantSetInfo.setRole(ParticipantSetInfo.RoleEnum.SIGNER);
agreementCreationInfo.addParticipantSetsInfoItem(participantSetInfo);
 
// Manager
participantSetInfo = new ParticipantSetInfo();
participantSetMemberInfo = new ParticipantSetMemberInfo();
participantSetMemberInfo.setEmail("");
participantSetInfo.addMemberInfosItem(participantSetMemberInfo);
participantSetInfo.setOrder(2);
participantSetInfo.setRole(ParticipantSetInfo.RoleEnum.SIGNER);
agreementCreationInfo.addParticipantSetsInfoItem(participantSetInfo);
```

最后，使用 `createAgreement` 来自Adobe Sign Java SDK的方法：

```
// Create agreement using the transient document.
AgreementsApi agreementsApi = new AgreementsApi(apiClient);
AgreementCreationResponse agreementCreationResponse = agreementsApi.createAgreement(
    authorization, agreementCreationInfo, null, null);
 
System.out.println("Agreement sent, ID: " + agreementCreationResponse.getId());
```

运行此代码后，您将收到一封电子邮件(发送到代码中指定的地址，邮件地址为 `<email_address>)` 附带协议签名请求。 该电子邮件包含超链接，收件人可使用该超链接转到Adobe Sign门户以执行签名。 您可以在Adobe Sign Developer Portal中查看文档（请参阅下图），也可以使用 [getAgreementInfo](https://github.com/adobe-sign/AdobeSignJavaSdk/blob/master/docs/AgreementsApi.md#getAgreementInfo) 方法。

最后，您还可以使用PDF服务API对PDF进行密码保护，如下所示 [示例](https://github.com/adobe/pdfservices-java-sdk-samples/tree/master/src/main/java/com/adobe/pdfservices/operation/samples/protectpdf)的

![合同详细信息的屏幕截图](assets/HRWJ_9.png)

## 后续步骤

正如您所看到的，通过利用快速入门，您可以实施一个简单的Web表单，以便使用Adobe PDF Services API在Java中创建经过批准的PDF。 Adobe PDF API无缝集成到您现有的客户端应用程序中。

进一步说明该示例，您可以创建收件人可以远程安全签名的表单。 当您需要多个签名时，您甚至可以自动将表单传送到工作流程中的一系列人员。 员工的入职培训得到了改进，人力资源部也会喜欢您。

签出 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) 立即为应用程序添加PDF的多种功能。
