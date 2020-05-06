---
title: 教學課程：整合多個沈浸式閱讀程式資源
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將建立可使用多個沈浸式閱讀程式資源啟動沈浸式閱讀程式的 Node.js 應用程式。
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: f68112095bc8a8fd9bcc1bd67ff77827d6d00fd7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195616"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>教學課程：整合多個沈浸式閱讀程式資源

在[概觀](./overview.md)中，您會了解什麼是沈浸式閱讀程式，及其如何實作經實證的技術，針對語言學習者、沈浸式讀者及存在學習差異的學生改善其閱讀理解程度。 在 [快速入門](./quickstart-nodejs.md)中，您已了解如何搭配單一資源使用沈浸式閱讀程式。 本教學課程將說明如何在同一個應用程式中整合多個沈浸式閱讀程式資源。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在現有的資源群組下建立多個沈浸式閱讀程式資源
> * 使用多個資源啟動沈浸式閱讀程式

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* 遵循[快速入門](./quickstart-nodejs.md)，以使用 NodeJS 建立可啟動沈浸式閱讀程式的 Web 應用程式 在該快速入門中，您會設定單一沈浸式閱讀程式資源。 在本教學課程中，我們將以該資源作為基礎。

## <a name="create-the-immersive-reader-resources"></a>建立沈浸式閱讀程式資源

請依照[這些指示](./how-to-create-immersive-reader.md)來建立每個沈浸式閱讀程式資源。 **ImmersiveReaderResource** 指令碼會以 `ResourceName`、`ResourceSubdomain` 和 `ResourceLocation` 作為參數。 這些參數在每個建立的資源上都應該是唯一的。 其餘的參數應該與您在設定第一個沈浸式閱讀程式資源時所使用的值相同。 如此一來，每個資源都可以連結至相同的 Azure 資源群組及 Azure AD 應用程式。

下列範例會說明如何建立兩個資源，一個在 WestUS，另一個在 EastUS。 請注意 `ResourceName`、`ResourceSubdomain` 和 `ResourceLocation` 的唯一值。

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>將資源新增至環境設定

在快速入門中，您已建立環境設定檔，其中包含 `TenantId`、`ClientId`、`ClientSecret` 和 `Subdomain` 參數。 因為您的所有資源都使用相同 Azure AD 應用程式，所以我們可以針對 `TenantId`、`ClientId` 和 `ClientSecret` 使用相同的值。 唯一需要進行的變更是列出每個資源的每個子域。

您的新 __.env__ 檔案現在看起來應該類似下面內容：

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

請勿將此檔案認可到原始檔控制，因為它包含不應公開的機密資料。

接下來，我們將修改為支援多個資源所建立的 routes\index.js  檔案。 以下列程式碼取代其內容。

與之前一樣，此程式碼會建立 API 端點，其可使用服務主體密碼取得 Azure AD 驗證權杖。 這次，該程式碼可讓使用者指定資源位置，並將其傳入做為查詢參數。 然後會傳回包含權杖和對應子網域的物件。

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

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

**getimmersivereaderlaunchparams** API 端點在某種形式的驗證 (例如 [OAuth](https://oauth.net/2/)) 後面應受到保護，以防止未經授權的使用者取得權杖，用於沈浸式閱讀程式服務和計費；該工作已超出本教學課程的範圍。

## <a name="launch-the-immersive-reader-with-sample-content"></a>使用範例內容啟動沈浸式閱讀程式

1. 開啟 _views\index.pug_，並以下列程式碼取代其內容。 此程式碼會將某些範例內容填入頁面，並新增啟動沈浸式閱讀程式的兩個按鈕。 分別用於啟動 EastUS 資源和 WestUS 資源的沈浸式閱讀程式。

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
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

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
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
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

3. Web 應用程式現在已經準備好了。 執行下列動作以啟動應用程式︰

    ```bash
    npm start
    ```

4. 開啟瀏覽器並巡覽至 `http://localhost:3000`。 在頁面上您應看到上述內容。 按一下 [EastUS 沉浸式閱讀程式]  按鈕或 [WestUS 沉浸式閱讀程式]  按鈕，以使用這些個別的資源來啟動沈浸式閱讀程式。

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)
* 檢視 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp) 上的程式碼範例