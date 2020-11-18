---
title: Bing 影像搜尋 C# 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: 247aea49d60c2d953d8b6dff37d22188a5442a1f
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625502"
---
您可以透過此快速入門，完成初次使用此 SDK 進行的影像搜尋。 

Bing 影像搜尋用戶端程式庫是 API 的包裝函式，而且功能相同， 

您可以建立 C# 應用程式來傳送影像搜尋查詢、剖析 JSON 回應，以及顯示第一個傳回影像的 URL。

此範例的原始程式碼可從 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) 取得，其中含有其他錯誤處理和註釋。

## <a name="prerequisites"></a>必要條件

* 如果您使用 Windows，則任何版本的 [Visual Studio 2017 或更新版本](https://visualstudio.microsoft.com/vs/whatsnew/) 均可。
* 如果您使用 macOS 或 Linux，則 [VS Code](https://code.visualstudio.com) 已隨附 [.NET Core 安裝](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
* [免費的 Azure 訂用帳戶](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-a-console-project"></a>建立主控台專案

首先建立新的 C# 主控台應用程式。

## <a name="create-a-console-project"></a>建立主控台專案

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. 在 Visual Studio 中建立一個名為 BingImageSearch 的新主控台解決方案。
    
1. 新增 [認知影像搜尋 NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch)
    1. 以滑鼠右鍵按一下 [方案總管] 中的專案。
    1. 選取 [管理 NuGet 套件]。
    1. 搜尋並選取 *Microsoft.Azure.CognitiveServices.Search.ImageSearch*，然後安裝套件。
    
# <a name="vs-code"></a>[VS 程式碼](#tab/vscode)

1. 在 VS Code 中開啟終端機視窗。
1. 在終端機視窗中輸入下列程式碼，以建立名為 *BingImageSearch* 的新主控台專案：
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. 在 VS Code 中開啟 BingImageSearch 資料夾。
1. 在終端機視窗中輸入下列程式碼，以新增 [認知影像搜尋 NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) NuGetPackage：

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>初始化應用程式


1. 使用下列程式碼取代 *Program.cs* 中的所有 `using` 陳述式：

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. 在專案的 `Main` 方法中，針對有效訂用帳戶金鑰、要由 Bing 傳回的影像結果和搜尋字詞建立變數。 然後使用金鑰來將影像搜尋用戶端具現化。

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>使用用戶端傳送搜尋查詢
    
仍然在 `Main` 方法中，使用用戶端來搜尋查詢文字：
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>剖析並檢視第一個影像結果

剖析回應中所傳回的影像結果。 

如果回應包含搜尋結果，請儲存第一個結果，並印出其部分詳細資料。

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing 影像搜尋單頁應用程式教學課程](../../tutorial-bing-image-search-single-page-app.md) (英文)

## <a name="see-also"></a>另請參閱

* [什麼是 Bing 影像搜尋？](../../overview.md)  
* [試用線上互動式示範](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure 認知服務 SDK 的 .NET 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Azure 認知服務文件](../../../index.yml)
* [Bing 影像搜尋 API 參考](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)