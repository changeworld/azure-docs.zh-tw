---
title: Bing 自訂搜尋 C# 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: b722fd34a78f1e9c2f4a660c205cf4a1e163a5d7
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253088"
---
開始使用適用於 C# 的 Bing 自訂搜尋用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 Bing 自訂搜尋 API 可讓您針對感興趣的主題，建立量身訂做且無廣告的搜尋經驗。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch) 上找到。

使用適用於 C# 的 Bing 自訂搜尋用戶端程式庫：
* 從您的 Bing 自訂搜尋執行個體，在 Web 上尋找搜尋結果。

[參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Prerequisites

- 「Bing 自訂搜尋」執行個體。 請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](../../quick-start.md)，以取得詳細資訊。
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- [Visual Studio 2017 或更新版本](https://www.visualstudio.com/downloads/)的任何版本
- 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/)來執行此應用程式。
- [Bing 自訂搜尋](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet 套件。 
    - 在 Visual Studio 的 [方案總管]  中，以滑鼠右鍵按一下專案，然後從功能表選取 [管理 NuGet 套件]  。 安裝 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 套件。 安裝 NuGet 自訂搜尋套件也會一併安裝下列組件：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在 Visual Studio 中，建立新的 C# 主控台應用程式。 然後，將下列套件新增至您的專案。

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. 在您應用程式的主要方法中，使用 API 金鑰具現化搜尋用戶端。

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>傳送搜尋要求並接收回應
    
1. 使用用戶端的 `SearchAsync()` 方法傳送搜尋查詢，並儲存回應。 請務必將 `YOUR-CUSTOM-CONFIG-ID` 取代為執行個體的組態識別碼 (您可以在 [Bing 自訂搜尋入口網站](https://www.customsearch.ai/)中找到該識別碼)。 此範例會搜尋 "Xbox"。

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. `SearchAsync()` 方法會傳回 `WebData` 物件。 使用物件來逐一查看找到的任何 `WebPages`。 此程式碼會找出第一個網頁結果，並列印網頁的 `Name` 和 `URL`。

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](../../tutorials/custom-search-web-page.md)
