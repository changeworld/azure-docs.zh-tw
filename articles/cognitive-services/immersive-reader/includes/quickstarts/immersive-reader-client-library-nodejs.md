---
title: 沈浸式閱讀程式 Node.js 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您會從頭開始建置 Web 應用程式，並且新增沈浸式閱讀程式 API 功能。
services: cognitive-services
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/20/2020
ms.author: pasta
ms.custom: devx-track-javascript
ms.openlocfilehash: 91adff9a8330defb6c2b9f1e915c46886649fed3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602491"
---
[沈浸式閱讀程式](https://www.onenote.com/learningtools)是經過全面設計的工具，可實作經實證的技術以改善閱讀理解程度。

在本快速入門中，您會從頭開始建置 Web 應用程式，並使用沈浸式閱讀程式用戶端程式庫來整合沈浸式閱讀程式。 本快速入門的完整工作範例可以在[這裡](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs)取得。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>必要條件

* 為 Azure Active Directory 驗證所設定的沈浸式閱讀程式資源。 遵循[這些指引](../../how-to-create-immersive-reader.md)來設定。 設定環境屬性時，您需要這裡建立的一些值。 將工作階段的輸出儲存到文字檔中，以供日後參考。
* [Node.js](https://nodejs.org/) 和 [Yarn](https://yarnpkg.com)
* 整合式開發環境，例如 [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>使用 Express 建立 Node.js Web 應用程式

使用 `express-generator` 工具建立 Node.js Web 應用程式。

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

安裝 yarn 的相依項目，並新增相依性 `request` 和 `dotenv`，以在本快速入門稍後使用。

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>設定驗證

### <a name="configure-authentication-values"></a>設定驗證值

在專案的根目錄中，建立名為 .env 的新檔案。 將下列程式碼貼入其中，然後使用您建立沈浸式閱讀程式資源時提供的值。
請勿包含引號或 "{" 和 "}" 字元。

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

### <a name="update-the-router-to-acquire-the-token"></a>更新路由器以取得權杖
開啟 routes\index.js 檔案，並以下列程式碼取代自動產生的程式碼。

此程式碼會建立 API 端點，其可使用服務主體密碼取得 Azure AD 驗證權杖。 也會擷取子網域。 然後會傳回包含權杖和子網域的物件。

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
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
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

**GetTokenAndSubdomain** API 端點應在某種形式的驗證 (例如 [OAuth](https://oauth.net/2/)) 後方受到保護，以防止未經授權的使用者取得權杖，而使用您的沈浸式閱讀程式服務和計費；該工作不在本快速入門的討論範圍內。

## <a name="add-sample-content"></a>新增範例內容

現在，我們會將範例內容新增至此 Web 應用程式。 開啟 views\index.pug，並且以下列範例取代自動產生的程式碼：

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


請注意，所有文字都會有一個 **lang** 屬性，用於描述文字的語言。 此屬性可協助沈浸式閱讀程式提供相關的語言和文法功能。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

Web 應用程式現在已經準備好了。 執行下列動作以啟動應用程式︰

```bash
npm start
```

開啟瀏覽器並巡覽至 _http://localhost:3000_ 。 您應該會看見下列內容：

![範例應用程式](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>啟動沈浸式閱讀程式

當您按一下 [沈浸式閱讀程式] 按鈕時，您會看到沈浸式閱讀程式啟動且其內容顯示在頁面上。

![沈浸式閱讀程式](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](../../reference.md)