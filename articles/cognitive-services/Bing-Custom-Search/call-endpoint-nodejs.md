---
title: 快速入門：使用 Node.js 來呼叫您的 Bing 自訂搜尋端點 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 您可以使用本快速入門，開始使用 Node.js 要求 Bing 自訂搜尋執行個體所產生的搜尋結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 43710407386995bde6d3505286e96b0737e06f08
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309759"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>快速入門：使用 Node.js 來呼叫您的 Bing 自訂搜尋端點

使用本快速入門了解如何要求 Bing 自訂搜尋執行個體所產生的搜尋結果。 雖然此應用程式是以 JavaScript 撰寫的，但「Bing 自訂搜尋 API」是一種與大多數程式設計語言都相容的 RESTful Web 服務。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js) 上有此範例的原始程式碼。

## <a name="prerequisites"></a>Prerequisites

- 「Bing 自訂搜尋」執行個體。 如需詳細資訊，請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。

- [Node.js JavaScript 執行階段](https://www.nodejs.org/)。

- [JavaScript 要求程式庫](https://github.com/request/request)。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

- 在您最愛的 IDE 或編輯器中建立新的 JavaScript 檔案，然後為要求程式庫新增 `require()` 陳述式。 為您的訂用帳戶金鑰、自訂設定識別碼和搜尋字詞建立變數。

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>傳送及接收搜尋要求 

1. 建立變數以儲存在您要求中傳送的資訊。 將搜尋字詞附加至 `q=` 查詢參數，並將搜尋執行個體的自訂設定識別碼附加至 `customconfig=` 參數，以建構要求 URL。 請以 `&` 符號分隔參數。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源顯示的[自訂子網域](../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. 使用 JavaScript 要求程式庫將搜尋要求傳送至 Bing 自訂搜尋執行個體，並列印結果的相關資訊，包括其名稱、URL 及上次搜耙網頁的日期。

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](./tutorials/custom-search-web-page.md)
