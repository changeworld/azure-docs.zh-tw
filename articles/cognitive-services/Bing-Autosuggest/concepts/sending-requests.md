---
title: 將要求傳送至 Bing 自動建議 API
titleSuffix: Azure Cognitive Services
description: Bing 自動建議 API 會根據搜尋方塊中的部分查詢字串，傳回建議的查詢清單。 深入瞭解傳送要求。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: 3aa8d691eb62b94c5784d8a6efc35b53a3762d7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294113"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>將要求傳送至 Bing 自動建議 API。

如果應用程式將查詢傳送給任何 Bing 搜尋 API，您就可以使用 Bing 自動建議 API 來改善使用者的搜尋體驗。 Bing 自動建議 API 會根據搜尋方塊中的部分查詢字串，傳回建議的查詢清單。 隨著您在應用程式的搜尋方塊中輸入字元，下拉式清單中便會顯示建議。 使用本文來深入了解如何將要求傳送至此 API。 

## <a name="bing-autosuggest-api-endpoint"></a>Bing 自動建議 API 端點

**Bing 自動建議 API** 內含一個端點，會根據部份搜尋字詞傳回建議的查詢清單。

若要使用 Bing API 取得建議的查詢，請傳送 `GET` 要求到下列端點。 使用標頭和 URL 參數以進一步定義規格。

**端點：** 以 JSON 結果的形式，傳回根據 `?q=""` 之定義與使用者輸入內容相關的搜尋建議。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

如需標頭、參數、市場代碼、回應物件、錯誤等詳細資料，請參閱 [Bing 自動建議 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) (英文)。

**Bing** API 支援根據類型傳回結果的搜尋動作。所有搜尋端點會傳回作為 JSON 回應物件的結果。
所有端點均可支援依照經度、緯度和搜尋半徑傳回特定語言及/或位置的查詢。

如需每個端點支援的參數完整相關資訊，請參閱每種類型的參考頁面。
如需使用自動建議 API 之基本要求的範例，請參閱[自動建議快速入門](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest) (英文)。

## <a name="bing-autosuggest-api-requests"></a>Bing 自動建議 API 要求

> [!NOTE]
> * 對 Bing 自動建議 API 的要求必須使用 HTTPS 通訊協定。

建議讓所有要求來自伺服器。 將金鑰作為用戶端應用程式的一部份散佈，會讓惡意第三方有更多機會存取金鑰。 此外，從伺服器進行呼叫，未來就能以單一升級點進行更新。

要求必須指定 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) 查詢參數，其包含使用者的部份搜尋字詞。 雖是選擇性，但請在要求中指定 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) 查詢參數，其可識別您希望從哪個市場取得結果。 如需選擇性查詢參數的清單，請參閱 [查詢參數](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters)。 所有查詢參數值均須為 URL 編碼。

要求必須指定 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) 標頭。 雖然是選擇性的，但我們仍建議使用以下標頭：

- [使用者代理程式](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-X-msedge-clientid-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

用戶端 IP 和位置標頭對於傳回位置感知內容很重要。

如需所有要求和回應標頭的清單，請參閱[標頭](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers)。

> [!NOTE]
> 當您從 JavaScript 呼叫 Bing 自動建議 API 時，瀏覽器的內建安全性功能可能會讓您無法存取這些標頭的值。

若要解決此問題，您可以透過 CORS Proxy 提出 Bing 自動建議 API 要求。 來自這類 proxy 的回應會有一個 `Access-Control-Expose-Headers` 標頭，可篩選回應標頭，並將其提供給 JavaScript 使用。

您可以輕鬆地安裝 CORS Proxy，讓我們的[教學課程應用程式](../tutorials/autosuggest.md)存取選擇性用戶端標頭。 首先，請[安裝 Node.js](https://nodejs.org/en/download/) (若尚未安裝)。 在命令提示字元中，輸入下列命令。

```console
npm install -g cors-proxy-server
```

接下來，將 HTML 檔案中的 Bing 自動建議 API 端點變更為：

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

最後，使用下列命令啟動 CORS Proxy：

```console
cors-proxy-server
```

當您使用教學課程應用程式時，請保持開啟命令視窗；關閉視窗會停止 Proxy。 在可展開的 [HTTP 標頭] 區段搜尋結果下，您現在可以看到 `X-MSEdge-ClientID` 標頭 (及其他標頭)，並確認每個要求的此標頭都相同。

要求應該包含所有建議的查詢參數和標頭。 

下列範例顯示針對 *sail* 傳回建議查詢字串的要求。

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

如果這是您第一次呼叫任何的 Bing API，請勿包含用戶端識別碼標頭。 如果您先前已呼叫 Bing API 且 Bing 傳回了使用者和裝置組合的用戶端識別碼，則只要包含用戶端識別碼標頭。

下列 Web 建議群組是上述要求的回應。 此群組包含一份搜尋查詢建議清單，且每個建議內都包含 `displayText`、`query` 和 `url` 欄位。

`displayText` 欄位包含建議的查詢，您可以用來填入搜尋方塊的下拉式清單。 您必須依提供的順序顯示回應包含的所有建議。  

如果使用者從下拉式清單選取查詢，您可以使用該查詢來呼叫其中一個 [Bing 搜尋 API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) 並自行顯示結果，或使用傳回的 `url` 欄位將使用者送到 Bing 結果頁面。

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>後續步驟

- [什麼是 Bing 自動建議？](../get-suggested-search-terms.md)
- [Bing 自動建議 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [從 Bing 自動建議 API 取得建議的搜尋字詞](get-suggestions.md)
