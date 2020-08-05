---
title: Bing 新聞搜尋 JavaScript 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: cc96233ea6e2d02f3c3a2036466e3934aa234f5b
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407963"
---
使用本快速入門，透過適用於 JavaScript 的 Bing 新聞搜尋用戶端程式庫開始搜尋新聞。 雖然 Bing 新聞搜尋具有與大部分程式設計語言相容的 REST API，但此用戶端程式庫可提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) 上找到。

## <a name="prerequisites"></a>Prerequisites

* [Node.js](https://nodejs.org/en/)

若要使用 Bing 新聞搜尋用戶端程式庫來設定主控台應用程式：
1. 在開發環境中執行 `npm install ms-rest-azure`。
2. 在開發環境中執行 `npm install azure-cognitiveservices-newssearch`。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 建立 `CognitiveServicesCredentials` 執行個體。 針對您的訂用帳戶金鑰和搜尋字詞建立變數。

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. 具現化用戶端：
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>傳送搜尋查詢

1. 使用用戶端以使用查詢字詞搜尋，本例中使用 "Winter Olympics"：
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

程式碼會將 `result.value` 項目列印到主控台，而不會剖析任何文字。 各個類別的結果 (若有的話) 將會包含：

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../../tutorial-bing-news-search-single-page-app.md)
