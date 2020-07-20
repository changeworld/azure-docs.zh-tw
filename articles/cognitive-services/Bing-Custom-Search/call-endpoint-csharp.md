---
title: 快速入門：使用 C# 呼叫您的 Bing 自訂搜尋端點 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 您可以使用本快速入門，開始透過 C# 要求 Bing 自訂搜尋執行個體所產生的搜尋結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: e1084c1962db3c04b951245361da80bee098329a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199826"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>快速入門：使用 C# 呼叫您的 Bing 自訂搜尋端點 

使用本快速入門了解如何要求 Bing 自訂搜尋執行個體所產生的搜尋結果。 雖然此應用程式是以 C# 撰寫的，但 Bing 自訂搜尋 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs) 上有此範例的原始程式碼。

## <a name="prerequisites"></a>Prerequisites

- 「Bing 自訂搜尋」執行個體。 如需詳細資訊，請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。
- [Microsoft .NET Core](https://www.microsoft.com/net/download/core)。
- [Visual Studio 2019 或更新版本](https://www.visualstudio.com/downloads/)的任何版本。
- 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/) 來執行此應用程式。
- [Bing 自訂搜尋](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) NuGet 套件。 

   若要在 Visual Studio 中安裝此套件： 
     1. 在**方案總管**中以滑鼠右鍵按一下您的專案，然後選取 [管理 NuGet 套件]。 
     2. 搜尋並選取 *Microsoft.Azure.CognitiveServices.Search.CustomSearch*，然後安裝套件。

   當您安裝 Bing 自訂搜尋 NuGet 套件時，Visual Studio 也會安裝下列套件：
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在 Visual Studio 中，建立新的 C# 主控台應用程式。 然後，將下列套件新增至您的專案：

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. 建立下列類別以儲存 Bing 自訂搜尋 API 所傳回的搜尋結果：

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. 在您專案的主要方法中，為 Bing 自訂搜尋 API 訂用帳戶金鑰、搜尋執行個體的自訂組態識別碼和搜尋字詞建立下列變數：

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. 將搜尋字詞附加至 `q=` 查詢參數，並將搜尋執行個體的自訂設定識別碼附加至 `customconfig=` 參數，以建構要求 URL。 請以 `&` 符號分隔參數。 對於 `url` 變數值，您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>傳送及接收搜尋要求 

1. 建立要求用戶端，並將您的訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. 執行搜尋要求並取得 JSON 物件形式的回應。

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>處理並檢視結果

- 逐一查看回應物件以顯示每個搜尋結果的相關資訊，包括其名稱、URL，以及上次搜耙網頁的日期。

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](./tutorials/custom-search-web-page.md)
