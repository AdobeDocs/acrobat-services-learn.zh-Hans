---
title: '[!DNL Adobe Acrobat Services]个APITutorials'
description: ' [!DNL Adobe Acrobat Services]的概述页面'
feature: Acrobat Sign API, PDF Services API, PDF Embed API, Document Generation API, PDF Electronic Seal API, PDF Extract API, PDF Accessibility Auto-Tag API
role: Developer
level: Beginner, Intermediate, Experienced
jira: KT-7463
type: Tutorial
thumbnail: KT-7463.jpg
exl-id: c73feb77-4057-42fd-831c-a5004c7637c1
source-git-commit: 2e23ffef7e4438a9287c909d2fdb13968195c31f
workflow-type: tm+mt
source-wordcount: '353'
ht-degree: 2%

---

# [!DNL Adobe Acrobat Services] API教程

[!DNL Adobe Acrobat Services]有六个主要API：

* [!DNL Adobe PDF Services API]
* [!DNL Adobe PDF Embed API]
* [!DNL Adobe Document Generation API]
* [!DNL Adobe PDF Electronic Seal API]
* [!DNL Adobe PDF Extract API]
* [!DNL Adobe PDF Accessibility Auto-Tag API]

后两个API及其SDK作为付费产品的一部分捆绑到[!DNL Adobe PDF Services API]。 [!DNL PDF Embed API]是免费产品。 这些API通过一组现代的基于云的Web服务自动生成、操作和转换文档内容。 它们可帮助您提供更简单、更快和品牌化的体验，以便您控制用户与文档的交互，简化PDF工作流程并提高使用率和保留率。 这些教程可帮助您了解如何使用[!DNL Adobe Acrobat Services] API提供更简单、更快的品牌化体验。

<!-- Comment -->
<!-- CARDS

* https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfservices/overview-pdfservices
  {target = _self}
  {title = PDF Services API}
  {description = PDF APIs with SDKs for node.js, .Net, and Java to create, convert, OCR PDFs, and more}
  {image = https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1a7b3ae4fc2b8c33c920f81a3eee05dc358108a74.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/docgen/overview-docgen
  {target = _self}
  {title = Document Generation API}
  {description = Generate PDF and Word documents from Word templates and JSON data}
  {image = https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1e4df708e549cf00ce0fb7fa1782957786ad4886b.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfaccessibility/overview-accessibility
  {target = _self}
  {title = Adobe PDF Accessibility Auto-Tag API tutorials}
  {description = This AI-powered API automatically tags documents making it easy to scale PDF accessibiity}
  {image = https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1441e8f0ef7b4a044f3e6355d66fa8f88146f9394.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfextract/overview-extract
  {target = _self}
  {title = PDF Extract API}
  {description = Unlock the structure and content elements of any PDF with a web service powered by Adobe Sensi's machine learning}
  {image = https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1441e8f0ef7b4a044f3e6355d66fa8f88146f9394.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/eseal/overview-electronic-seal
  {target = _self}
  {title = PDF Electronic Seal API}
  {description = Learn how to apply a tamper-evident electronic seal to PDFs at scale}
  {image = https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1144424efd29c8faaf22aceff3f73d80fb7fb3ac1.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfembed/overview-embed
  {target = _self}
  {title = PDF Embed API}
  {description = Free Javascript API to embed high-fidelity PDFs, enable collaboration, and see analytics}
  {image = https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_100c64db6899f092f8a30e0d153091398242f8abc.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/acrobatsign/overview-sign
  {target = _self}
  {title = Acrobat Sign API}
  {description = Integrate e-signatures into your platform or application}
  {image = https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1f579a346e7d3a7647236d7b81daf49d45dafde35.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/usecases/overview-usecases
  {target = _self}
  {title = Adobe Acrobat Services API use cases}
  {description = A wide variety of Acrobat Services API use cases}
  {image = https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_13219295abc6d94806a7cccf552effa9b54f25ecf.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}

-->
<!-- End Comment -->

<!-- START CARDS HTML - DO NOT MODIFY BY HAND -->
<div class="columns">
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="PDF Services API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfservices/overview-pdfservices" title="PDF服务API" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1a7b3ae4fc2b8c33c920f81a3eee05dc358108a74.png?width=400&format=webply&optimize=medium" alt="PDF服务API"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfservices/overview-pdfservices" target="_self" rel="referrer" title="PDF服务API">PDF服务API</a>
                    </p>
                    <p class="is-size-6">使用SDK为node.js、.Net和JavaPDFAPI以创建、转换、OCRPDF等</p>
                </div>
                <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfservices/overview-pdfservices" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">浏览教程</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="Document Generation API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/docgen/overview-docgen" title="Document Generation API" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1e4df708e549cf00ce0fb7fa1782957786ad4886b.png?width=400&format=webply&optimize=medium" alt="Document Generation API"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/docgen/overview-docgen" target="_self" rel="referrer" title="Document Generation API">Document Generation API</a>
                    </p>
                    <p class="is-size-6">从WordPDF和JSON数据生成模板和Word文档</p>
                </div>
                <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/docgen/overview-docgen" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">浏览教程</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="Adobe PDF Accessibility Auto-Tag API tutorials">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfaccessibility/overview-accessibility" title="Adobe PDF辅助功能自动标记API教程" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1441e8f0ef7b4a044f3e6355d66fa8f88146f9394.png?width=400&format=webply&optimize=medium" alt="Adobe PDF辅助功能自动标记API教程"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfaccessibility/overview-accessibility" target="_self" rel="referrer" title="Adobe PDF辅助功能自动标记API教程">Adobe PDF辅助功能自动标记API教程</a>
                    </p>
                    <p class="is-size-6">此AI支持的API可自动为文档添加标签，从而轻松扩展PDF辅助功能</p>
                </div>
                <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfaccessibility/overview-accessibility" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">浏览教程</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="PDF Extract API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfextract/overview-extract" title="PDF提取API" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1441e8f0ef7b4a044f3e6355d66fa8f88146f9394.png?width=400&format=webply&optimize=medium" alt="PDF提取API"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfextract/overview-extract" target="_self" rel="referrer" title="PDF提取API">PDF提取API</a>
                    </p>
                    <p class="is-size-6">使用由AdobeSensi的机器学习提供支持的Web服务，解锁任何PDF的结构和内容元素</p>
                </div>
                <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfextract/overview-extract" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">浏览教程</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="PDF Electronic Seal API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/eseal/overview-electronic-seal" title="PDF电子签章API" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1144424efd29c8faaf22aceff3f73d80fb7fb3ac1.png?width=400&format=webply&optimize=medium" alt="PDF电子签章API"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/eseal/overview-electronic-seal" target="_self" rel="referrer" title="PDF电子签章API">PDF电子签章API</a>
                    </p>
                    <p class="is-size-6">了解如何大规模对PDF应用防篡改电子密封</p>
                </div>
                <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/eseal/overview-electronic-seal" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">浏览教程</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="PDF Embed API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfembed/overview-embed" title="嵌入式APIPDF" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_100c64db6899f092f8a30e0d153091398242f8abc.png?width=400&format=webply&optimize=medium" alt="嵌入式APIPDF"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfembed/overview-embed" target="_self" rel="referrer" title="嵌入式APIPDF">PDF的嵌入API</a>
                    </p>
                    <p class="is-size-6">免费的Javascript API可嵌入高保真PDF、启用协作和查看分析</p>
                </div>
                <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfembed/overview-embed" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">浏览教程</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="Acrobat Sign API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/acrobatsign/overview-sign" title="Acrobat Sign API" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_1f579a346e7d3a7647236d7b81daf49d45dafde35.png?width=400&format=webply&optimize=medium" alt="Acrobat Sign API"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/acrobatsign/overview-sign" target="_self" rel="referrer" title="Acrobat Sign API">Acrobat Sign API</a>
                    </p>
                    <p class="is-size-6">将电子签名集成到您的平台或应用程序中</p>
                </div>
                <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/acrobatsign/overview-sign" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">浏览教程</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="Adobe Acrobat Services API use cases">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/usecases/overview-usecases" title="Adobe Acrobat Services API用例" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/media_13219295abc6d94806a7cccf552effa9b54f25ecf.png?width=400&format=webply&optimize=medium" alt="Adobe Acrobat Services API用例"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/usecases/overview-usecases" target="_self" rel="referrer" title="Adobe Acrobat Services API用例">Adobe Acrobat Services API用例</a>
                    </p>
                    <p class="is-size-6">各种Acrobat Services API用例</p>
                </div>
                <a href="https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/usecases/overview-usecases" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">浏览教程</span>
                </a>
            </div>
        </div>
    </div>
</div>
<!-- END CARDS HTML - DO NOT MODIFY BY HAND -->
