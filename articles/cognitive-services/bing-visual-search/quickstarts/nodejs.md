---
title: 快速入門：使用 REST API 和 Node.js 來取得影像見解 - Bing 圖像式搜尋
titleSuffix: Azure Cognitive Services
description: 了解如何將影像上傳到 Bing 圖像式搜尋 API 並取得其見解。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75379703"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>快速入門：使用 Bing 圖像式搜尋 REST API 和 Node.js 來取得影像見解

使用本快速入門來進行您對 Bing 圖像式搜尋 API 的第一次呼叫並檢視搜尋結果。 這個簡單的 JavaScript 應用程式會將影像上傳至 API，並顯示傳回的相關資訊。 雖然此應用程式是以 JavaScript 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>Prerequisites

* [Node.js](https://nodejs.org/en/download/)
* 適用於 JavaScript 的要求模組。 您可以使用 `npm install request` 命令來安裝此模組。
* 表單資料模組。 您可以使用 `npm install form-data` 命令來安裝此模組。 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>初始化應用程式

1. 在您慣用的 IDE 或編輯器中建立 JavaScript 檔案，然後設定下列需求：

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. 為您的 API 端點、訂用帳戶金鑰以及您影像的路徑，建立變數。 `baseUri` 可以是下方的全域端點，也可以是 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點：

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. 建立名為 `requestCallback()` 的函式，以列印來自 API 的回應：

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>建構及傳送搜尋要求

上傳本機影像時，表單資料必須包含 `Content-Disposition` 標頭。 您必須將其 `name` 參數設為 "image"，而 `filename` 參數則可設為任何字串。 表單的內容包含影像的二進位資料。 您可以上傳的影像大小上限為 1 MB。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. 使用 `FormData()` 建立新的 **FormData** 物件，並使用 `fs.createReadStream()` 為其附加您的影像路徑：
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. 使用要求程式庫來上傳影像，並呼叫 `requestCallback()` 來列印回應。 請務必將您的訂用帳戶金鑰新增至要求標頭：

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置圖像式搜尋單頁 Web 應用程式](../tutorial-bing-visual-search-single-page-app.md)
