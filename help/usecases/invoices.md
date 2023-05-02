---
title: 处理发票
description: 了解如何自动生成、密码保护和交付客户发票
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8145.jpg
kt: 8145
exl-id: 5871ef8d-be9c-459f-9660-e2c9230a6ceb
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1427'
ht-degree: 1%

---

# 处理发票

![用例英雄横幅](assets/UseCaseInvoicesHero.jpg)

在商业蓬勃发展时，这一点很棒，但在准备所有这些账单时，生产率就会受到影响。 手动生成发票非常耗时，而且您还面临以下风险：出现错误，有可能蒙受损失，或者因为金额不正确而惹怒了客户。

比如说Danielle [会计部门](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html) [一家医药供应公司的款项](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html)的 这是月底，所以她从几个不同的系统中提取信息，反复核查其准确性并格式化账单。 完成所有这些工作后，她终于可以将文档转换为PDF（这样一来，任何人都无需购买特定的软件即可查看文档），并为每位客户发送个性化的账单。

即使月度账单已完成，Danielle也无法摆脱这些账单。 有些客户有非按月计费周期，因此她总是为某人创建发票。 有时，客户会编辑其账单并支付不足的费用。 然后，Danielle花时间解决这个发票不匹配问题。 按照这个速度，她需要雇一个助理来应付所有的工作！

Danielle需要一种快速准确生成发票的方法，即在一个月底分批出具发票，以及临时支付发票。 理想情况下，如果能够避免编辑这些账单，就不必担心错配金额的故障排除。

## 您可以学到的内容

在本实际操作教程中，学习如何使用Adobe文档生成API自动生成发票、对PDF进行密码保护，以及向每个客户发送发票。 只需要了解Node.js、JavaScript、Express.js、HTML和CSS。

此项目的完整代码为 [在GitHub上可用](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation)的 您必须使用模板和原始数据文件夹设置公共目录。 在生产环境中，必须从外部API提取数据。 您还可以浏览包含模板资源的应用程序的此存档版本。

## 相关API和资源

* [PDF服务API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe文档生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [项目代码](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation)

## 准备数据

本教程并未介绍如何从您的数据仓库导入数据。 您的客户订单可能存在于数据库、外部API或自定义软件中。 Adobe文档生成API需要一个包含开票数据(例如，来自客户关系管理(CRM)或电子商务平台的信息)的JSON文档。 本教程假定数据已经采用JSON格式。

为简单起见，请使用以下JSON结构开具发票：

```
{ 
    "customerName": "John Doe", 
    "customerEmail": "john-doe@example.com", 
    "order": [ 
        { 
            "productId": 26, 
            "productTitle": "Bandages", 
            "price": 15.82 
        }, 
        { 
            "productId": 54, 
            "productTitle": "Masks", 
            "price": 25 
        }, 
        { 
            "productId": 76, 
            "productTitle": "Gloves", 
            "price": 7.59 
        } 
    ] 
} 
```

JSON文档包含客户详细信息以及订单信息。 使用此结构化文档来构建您的发票并以PDF格式显示元素。

## 构建发票模板

Adobe文档生成API需要一个基于Microsoft Word的模板和JSON文档来创建动态PDF或Word文档。 为开票应用程序创建Microsoft Word模板并使用 [free Document Generation Tagger加载项](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo) 以生成模板标签。 安装此加载项并在Microsoft Word中打开选项卡。

![Document Generation Tagger加载项的屏幕截图](assets/invoices_1.png)

将JSON内容粘贴到加载项后（如上所述），单击“生成标签”。 现在，此增效工具会显示您对象的格式。 您的基本模板可以使用客户的姓名和电子邮件，但不显示订单信息。 本教程将在后面讨论顺序信息。

![文档生成标记作者模板的屏幕截图](assets/invoices_2.png)

在Microsoft Word文档中，开始编写发票模板。 将光标放在必须插入动态数据的位置，然后从Adobe加载项窗口中选择标签。 单击 **插入文本** 因此，Adobe文档生成标记插件可以生成和插入标记。 要进行个性化，我们插入客户的姓名和电子邮件。

现在，转至随每张新发票发生更改的数据。 选择 **高级** 选项卡。 要查看可根据客户订购的产品生成动态表的可用选项，请单击 **表和列表** 的

选择 **订单** 从第一个下拉菜单中选择。 在第二个下拉列表中，选择此表的列。 在本教程中，为要呈现表格的对象选择所有三列。

![“文档生成标记高级”选项卡的屏幕截图](assets/invoices_3.png)

Document Generation API还可以执行复杂的操作，如将元素聚合到数组中。 在 **高级** 选项卡，选择 **数值计算**&#x200B;和 **聚合** 选项卡，选择要应用计算的字段。

![文档生成标记数字计算的屏幕截图](assets/invoices_4.png)

单击 **插入计算** 按钮，在文档中需要插入此标签。 以下文本现在显示在您的Microsoft Word文件中：

![Microsoft Word文档中标签的屏幕截图](assets/invoices_5.png)

此发票示例包含客户信息、订购产品以及应付款总额。

## 使用Adobe文档生成API生成发票

使用Adobe PDF Services Node.js软件开发包(SDK)合并Microsoft Word和JSON文档。 构建Node.js应用程序，以使用文档生成API创建发票。

PDF服务API包含文档生成服务，因此您可以为两者使用相同的凭据。 享受 [六个月免费试用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)之后，只需为每个文档交易支付0.05美元。

以下是用于合并PDF的代码：

```
async function compileDocFile(json, inputFile, outputPdf) { 
    try { 
        // configurations 
        const credentials =  adobe.Credentials 
            .serviceAccountCredentialsBuilder() 
            .fromFile("./src/pdftools-api-credentials.json") 
            .build(); 

        // Capture the credential from app and show create the context 
        const executionContext = adobe.ExecutionContext.create(credentials); 
  
        // create the operation 
        const documentMerge = adobe.DocumentMerge, 
            documentMergeOptions = documentMerge.options, 
            options = new documentMergeOptions.DocumentMergeOptions(json, documentMergeOptions.OutputFormat.PDF);

        const operation = documentMerge.Operation.createNew(options); 
  
        // Pass the content as input (stream) 
        const input = adobe.FileRef.createFromLocalFile(inputFile); 
        operation.setInput(input); 
  
        // Async create the PDF 
        let result = await operation.execute(executionContext); 
        await result.saveAsFile(outputPdf); 
    } catch (err) { 
        console.log('Exception encountered while executing operation', err); 
    } 
} 
```

此代码从输入JSON文档和输入模板文件中获取信息。 然后，它创建一个文档合并操作，以将这些文件合并为一个单独的PDF报告。 最后，它使用您的API凭据执行操作。 如果您尚未拥有它们， [创建凭据](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getting-credentials) (文档生成和PDF服务API使用相同的凭据)。

在Express路由器中使用此代码处理文档请求：

```
// Create one report and send it back
try {
    console.log(\`[INFO] generating the report...\`);
    const fileContent = fs.readFileSync(\`./public/documents/raw/\${vendor}\`,
    'utf-8');
    const parsedObject = JSON.parse(fileContent);

    await pdf.compileDocFile(parsedObject,
    \`./public/documents/template/Adobe-Invoice-Sample.docx\`,
    \`./public/documents/processed/output.pdf\`);

    await pdf.applyPassword("p@55w0rd", './public/documents/processed/output.pdf',
    './public/documents/processed/output-secured.pdf');

    console.log(\`[INFO] sending the report...\`);
    res.status(200).render("preview", { page: 'invoice', filename: 'output.pdf' });
} catch(error) {
    console.log(\`[ERROR] \${JSON.stringify(error)}\`);
    res.status(500).render("crash", { error: error });
}
```

执行此代码后，它将提供一个PDF文档，其中包含基于所提供数据动态生成的发票。 使用示例JSON数据（上面提供），此代码的输出为：

![动态生成的PDF账单屏幕截图](assets/invoices_6.png)

此发票包括来自JSON文档的动态数据。

## 使用密码保护发票

由于Danielle是一名会计师，他很担心客户会更改发票，因此使用密码来限制编辑操作。 [PDF服务API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html) 可以自动将密码应用于文档。 在此，您可以使用Adobe PDF Services SDK通过密码保护文档。 代码为：

```
async function applyPassword(password, inputFile, outputFile) {
    try {
        // Initial setup, create credentials instance.
        const credentials = adobe.Credentials
        .serviceAccountCredentialsBuilder()
        .fromFile("./src/pdftools-api-credentials.json")
        .build();

        // Create an ExecutionContext using credentials
        const executionContext = adobe.ExecutionContext.create(credentials);
        // Create new permissions instance and add the required permissions
        const protectPDF = adobe.ProtectPDF,
        protectPDFOptions = protectPDF.options;
        // Build ProtectPDF options by setting an Owner/Permissions Password, Permissions,
        // Encryption Algorithm (used for encrypting the PDF file) and specifying the type of content to encrypt.
        const options = new protectPDFOptions.PasswordProtectOptions.Builder()
        .setOwnerPassword(password)
        .setEncryptionAlgorithm(protectPDFOptions.EncryptionAlgorithm.AES_256)
        .build();

        // Create a new operation instance.
        const protectPDFOperation = protectPDF.Operation.createNew(options);

        // Set operation input from a source file.
        const input = adobe.FileRef.createFromLocalFile(inputFile);
        protectPDFOperation.setInput(input);

        // Execute the operation and Save the result to the specified location.
        let result = await protectPDFOperation.execute(executionContext);

        result.saveAsFile(outputFile);
    } catch (err) {
        console.log('Exception encountered while executing operation', err);
    }
}
```

使用此代码时，它使用密码保护您的文档，并将新发票上载到系统。 有关此代码的使用或试用方式的更多信息，请参阅 [代码示例](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation)的

完成发票处理后，您可能希望自动通过电子邮件将其发送给客户。 有几种方法可以实现向客户自动发送电子邮件。 最快捷的方法是使用第三方电子邮件API以及辅助库，例如 [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs)的 或者，如果您已具有对SMTP服务器的访问权限，则可以使用 [nodemailer](https://www.npmjs.com/package/nodemailer) 通过SMTP发送电子邮件。

## 后续步骤

在本实操教程中，您创建了一个简单的应用程序，来帮助Danielle [账单](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html)的 使用PDF服务API和Document Generation SDK，用JSON文档中的客户订单信息填充Microsoft Word模板，从而创建PDF发票。 然后，使用密码保护服务对每个文档进行密码保护 [PDF服务API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)的

由于Danielle能自动生成发票，无须担心客户编辑他们的发票，因此她无需雇用任何助理来协助处理所有手动工作。 她可以利用自己的空闲时间在应付帐款文件中找到节约成本的方法。

现在您已经了解了它是多么简单，接下来您可以通过其他Adobe工具来扩展这个简单的应用程序，以将发票嵌入您的网站。 例如，这样客户就可以随时查看其发票或余额。 [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) 免费使用。 您甚至可以转到人力资源部或销售部，以帮助自动化他们的协议并收集电子签名。

要探索所有可能性并开始构建您自己的方便应用程序，请创建一个免费的 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 立即开始使用的帐户。 享受6个月的免费试用，然后 [即付即用](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)
随着业务的扩展，每个文档交易仅需0.05美元。
