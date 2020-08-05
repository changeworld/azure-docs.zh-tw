---
title: Bing 實體搜尋 JavaScript 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: f5a3b48fd6be48d468b66009a77de100962be10d
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405056"
---
使用本快速入門，透過適用於 JavaScript 的 Bing 實體搜尋用戶端程式庫開始搜尋實體。 雖然 Bing 實體搜尋具有與大部分程式設計語言相容的 REST API，但此用戶端程式庫可提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js) 上找到。

## <a name="prerequisites"></a>Prerequisites

* 最新版的 [Node.js](https://nodejs.org/en/download/)。

* [適用於 Node.js 的 Bing 實體搜尋 SDK](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

若要安裝 Bing 實體搜尋 SDK：

1. 在開發環境中執行 `npm install ms-rest-azure`。
2. 在開發環境中執行 `npm install @azure/cognitiveservices-entitysearch`。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 JavaScript 檔案，然後新增下列需求。

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. 使用您的訂用帳戶金鑰建立 `CognitiveServicesCredentials` 的執行個體。 然後，用它來建立搜尋用戶端的執行個體。

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>傳送要求並接收回應

1. 使用 `entitiesOperations.search()` 傳送實體搜尋要求。 接收到回應後，請列印 `queryContext`、傳回的結果數，和第一個結果的描述。

    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../../tutorial-bing-entities-search-single-page-app.md)

* [什麼是 Bing 實體搜尋 API？](../../overview.md)