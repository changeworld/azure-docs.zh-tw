---
title: Bing 影片搜尋 JavaScript 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: b642d981b79d13857881ec8cc5796e6365003ace
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80289910"
---
使用本快速入門，透過適用於 JavaScript 的 Bing 影片搜尋用戶端程式庫開始搜尋新聞。 雖然 Bing 影片搜尋具有與大部分程式設計語言相容的 REST API，但此用戶端程式庫可提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) 上找到。 其中包含更多的註解和功能。

## <a name="prerequisites"></a>Prerequisites

- [Node.js](https://www.nodejs.org/)

若要使用 Bing 影片搜尋用戶端程式庫來設定主控台應用程式：
* 在開發環境中執行 `npm install ms-rest-azure`。
* 在開發環境中執行 `npm install azure-cognitiveservices-videosearch`。

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您慣用的 IDE 或編輯器中建立新的 JavaScript 檔案，然後為 Bing 影片搜尋用戶端程式庫新增 `require()` 陳述式，以及新增 `CognitiveServicesCredentials` 模組。 建立訂用帳戶金鑰的變數。 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. 以您的金鑰建立 `CognitiveServicesCredentials` 的執行個體。 然後用其建立影片搜尋用戶端的執行個體。

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>傳送搜尋要求

1. 使用 `client.videosOperations.search()` 將搜尋要求傳送到 Bing 影片搜尋 API。 當搜尋結果傳回時，使用 `.then()` 來記錄結果。
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>另請參閱 

* [什麼是 Bing 影片搜尋 API？](../../overview.md)
* [認知服務 .NET SDK 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)