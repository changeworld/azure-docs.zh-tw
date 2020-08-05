---
title: Bing Web 搜尋 JavaScript 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 0a505e5fb11e776a90c8e198b2568e2ac0bd1854
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406364"
---
Bing Web 搜尋用戶端程式庫可讓您輕鬆地將 Bing Web 搜尋整合到 Node.js 應用程式。 在本快速入門中，您將了解如何具現化用戶端、傳送要求，以及列印回應。

要立即查看程式碼嗎？ GitHub 中提供[適用於 JavaScript 的 Bing 搜尋用戶端程式庫](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/tree/master/Samples)的範例。

## <a name="prerequisites"></a>Prerequisites
以下是執行本快速入門之前的幾個必備項目：

* [Node.js 6](https://nodejs.org/en/download/) (英文) 或更新版本
* 訂用帳戶金鑰  

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>設定開發環境

現在就開始為我們的 Node.js 專案設定開發環境。

1. 為您的專案建立新目錄：

    ```console
    mkdir YOUR_PROJECT
    ```

1. 建立新的套件檔案：

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. 現在，讓我們安裝一些 Azure 模組並新增至 `package.json`：

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>建立專案，並宣告所需的模組

在 `package.json` 的同一個目錄中，使用您慣用的 IDE 或編輯器，建立新的 Node.js 專案。 例如： `sample.js` 。

接下來，將此程式碼複製到您的專案。 此程式碼會載入上一節中所安裝的模組。

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>具現化用戶端

此程式碼會具現化用戶端並使用 `azure-cognitiveservices-websearch` 模組。 請務必輸入 Azure 帳戶的有效訂用帳戶金鑰再繼續。

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>提出要求並列印結果

您可以使用此用戶端將搜尋查詢傳送至 Bing Web 搜尋。 如果回應包含 `properties` 陣列中任何項目的結果，`result.value` 會列印至主控台。

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>執行程式

最後一步就是執行您的程式！

## <a name="clean-up-resources"></a>清除資源

此專案使用完畢時，請務必從程式的程式碼中移除訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [認知服務 Node.js SDK 範例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) (英文)

## <a name="see-also"></a>另請參閱

* [Azure Node SDK 參考](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/) (英文)
