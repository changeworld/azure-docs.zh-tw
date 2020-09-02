---
title: 快速入門：將搜尋要求傳送至以 C# 撰寫的 REST API - Bing 實體搜尋
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用 C# 來傳送要求給「Bing 實體搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 131c282ab61f04ffa94921c2816886a35ea4b7ac
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936821"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>快速入門：使用 C# 將搜尋要求傳送至 Bing 實體搜尋 REST API

使用本快速入門以第一次呼叫 Bing 實體搜尋 API，並檢視 JSON 回應。 這個簡單的 C# 應用程式會將新聞搜尋查詢傳送給 API，並顯示回應。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs) 上找到此應用程式的原始程式碼。

雖然此應用程式是以 C# 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。


## <a name="prerequisites"></a>Prerequisites

- [Visual Studio 2017 或更新版本](https://www.visualstudio.com/downloads/)的任何版本。

- [Json.NET](https://www.newtonsoft.com/json) 架構 (以 NuGet 套件形式提供)。 在 Visual Studio 中安裝 NuGet 套件：

   1. 以滑鼠右鍵按一下 [方案總管] 中的專案。
   2. 選取 [管理 NuGet 套件]。
   3. 搜尋並選取 Newtonsoft.Json，然後安裝此套件。

- 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/) 來執行此應用程式。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在 Visual Studio 中建立新的 C# 主控台解決方案。 然後將下列命名空間新增至主要程式碼檔案：
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. 建立新的類別，然後新增 API 端點變數、您的訂用帳戶金鑰和您想要搜尋的查詢。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>傳送要求並取得 API 回應

1. 在該類別內建立稱為 `Search()` 的函式。 在此函式中，建立新的 `HttpClient` 物件，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

2. 藉由結合主機和路徑，來建構要求的 URI。 然後新增您的市場，並為查詢進行 URL 編碼。

3. 等待 `client.GetAsync()` 取得 HTTP 回應，然後藉由等待 `ReadAsStringAsync()` 來儲存 JSON 回應。

4. 以 `JsonConvert.DeserializeObject()` 格式化 JSON 字串，並將其列印到主控台。

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

5. 在應用程式的 `Main()` 方法中，呼叫 `Search()` 函式。
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>範例 JSON 回應

如以下範例所示，成功的回應會以 JSON 格式來傳回： 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置單頁 Web 應用程式](../tutorial-bing-entities-search-single-page-app.md)

* [什麼是 Bing 實體搜尋 API？](../overview.md )
* [Bing 實體搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)。
