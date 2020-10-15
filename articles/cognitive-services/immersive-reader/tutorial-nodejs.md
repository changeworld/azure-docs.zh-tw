---
title: 教學課程：使用 Node.js 啟動沈浸式閱讀程式
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將建立會啟動沈浸式閱讀程式的 Node.js 應用程式。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-js
ms.openlocfilehash: 1ac23ad66cadc553095ff869b665a6bba2fba6f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262275"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>教學課程：啟動沈浸式閱讀程式 (Node.js)

在[概觀](./overview.md)中，您會了解什麼是沈浸式閱讀程式，及其如何實作經實證的技術，針對語言學習者、沈浸式讀者及存在學習差異的學生改善其閱讀理解程度。 本教學課程涵蓋如何建立會啟動沈浸式閱讀程式的 Node.js Web 應用程式。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 Express 建立 Node.js Web 應用程式
> * 取得存取權杖
> * 使用範例內容啟動沈浸式閱讀程式
> * 指定內容的語言
> * 指定沈浸式閱讀程式介面的語言
> * 使用數學內容啟動沈浸式閱讀程式

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>Prerequisites

* 為 Azure Active Directory 驗證所設定的沈浸式閱讀程式資源。 遵循[這些指引](./how-to-create-immersive-reader.md)來設定。 設定環境屬性時，您需要這裡建立的一些值。 將工作階段的輸出儲存到文字檔中，以供日後參考。
* [Node.js](https://nodejs.org/) 和 [Yarn](https://yarnpkg.com)
* 整合式開發環境，例如 [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>使用 Express 建立 Node.js Web 應用程式

使用 `express-generator` 工具建立 Node.js Web 應用程式。

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

安裝 yarn 的相依項目，並新增相依性 `request` 和 `dotenv`，其在本教學課程稍後將會使用。

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>取得 Azure AD 驗證權杖

接下來，撰寫後端 API 以擷取 Azure AD 驗證權杖。

這一部分，您需要上述 Azure AD 驗證組態先決步驟中的一些值。 請回頭參考您在該工作階段儲存的文字檔。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

擁有這些值之後，請建立名為 _.env_ 的新檔案，然後將下列程式碼貼入其中，並提供上述的自訂屬性值。 請勿包含引號或 "{" 和 "}" 字元。

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

請勿將此檔案認可到原始檔控制，因為它包含不應公開的機密資料。

接著，開啟 _app.js_ 並將下列新增至檔案開頭處。 這會將 .env 檔案中定義的屬性當作環境變數載入 Node 中。

```javascript
require('dotenv').config();
```

開啟 _routes\index.js_ 檔案，並以下列程式碼取代其內容。

此程式碼會建立 API 端點，其可使用服務主體密碼取得 Azure AD 驗證權杖。 也會擷取子網域。 然後會傳回包含權杖和子網域的物件。

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

**getimmersivereaderlaunchparams** API 端點在某種形式的驗證 (例如 [OAuth](https://oauth.net/2/)) 後面應受到保護，以防止未經授權的使用者取得權杖，用於沈浸式閱讀程式服務和計費；該工作已超出本教學課程的範圍。

## <a name="launch-the-immersive-reader-with-sample-content"></a>使用範例內容啟動沈浸式閱讀程式

1. 開啟 _views\layout.pug_，並在 `head` 標籤底下、`body` 標籤之前新增下列程式碼。 這些 `script` 標籤會載入[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和 jQuery。

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. 開啟 _views\index.pug_，並以下列程式碼取代其內容。 此程式碼會將某些範例內容填入頁面，並新增啟動沈浸式閱讀程式的按鈕。

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Web 應用程式現在已經準備好了。 執行下列動作以啟動應用程式︰

    ```bash
    npm start
    ```

4. 開啟瀏覽器並巡覽至 _http://localhost:3000_ 。 在頁面上您應看到上述內容。 按一下 [沈浸式閱讀程式]  按鈕，將您的內容以沈浸式閱讀程式啟動。

## <a name="specify-the-language-of-your-content"></a>指定內容的語言

沈浸式閱讀程式支援許多不同的語言。 遵循下列步驟，即可指定內容的語言。

1. 開啟 _views\index.pug_，並在上一個步驟所新增的 `p(id=content)` 標籤下方新增下列程式碼。 此程式碼會將某些西班牙文的內容新增至您的頁面。

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. 在 JavaScript 程式碼中，在 `ImmersiveReader.launchAsync` 呼叫上方新增下列內容。 此程式碼會將西班牙文的內容傳遞至沈浸式閱讀程式。

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. 再次瀏覽至 _http://localhost:3000_ 。 您應該會在頁面上看到西班牙文文字，當您按一下 [沈浸式閱讀程式]  ，它也會顯示在沈浸式閱讀程式中。

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>指定沈浸式閱讀程式介面的語言

根據預設，沈浸式閱讀程式介面的語言會符合瀏覽器的語言設定。 您也可以使用下列程式碼指定沈浸式閱讀程式介面的語言。

1. 在 _views\index.pug_ 中，以下列程式碼取代 `ImmersiveReader.launchAsync(token, subdomain, content)` 呼叫。

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. 瀏覽至 _http://localhost:3000_ 。 當您啟動沈浸式閱讀程式，介面會以法文顯示。

## <a name="launch-the-immersive-reader-with-math-content"></a>使用數學內容啟動沈浸式閱讀程式

您可以使用 [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML) 在沈浸式閱讀程式中包含數學內容。

1. 修改 _views\index.pug_ 以在 `ImmersiveReader.launchAsync` 呼叫上方包含下列程式碼：

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. 瀏覽至 _http://localhost:3000_ 。 當您啟動沈浸式閱讀程式並捲動至底部時，您會看到數學公式。

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)
* 檢視 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp) 上的程式碼範例
