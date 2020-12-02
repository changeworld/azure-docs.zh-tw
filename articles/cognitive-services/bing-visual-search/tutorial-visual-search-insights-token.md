---
title: 使用影像深入解析權杖和 Bing 圖像式搜尋 API 尋找先前搜尋中的類似影像
titleSuffix: Azure Cognitive Services
description: 使用 Bing 圖像式搜尋用戶端程式庫，從先前的搜尋中取得影像的 URL。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a9005082148803423ac20b4f18dd974bb5b43b80
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486856"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>教學課程：使用影像深入解析權杖尋找先前搜尋中的類似影像

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

圖像式搜尋用戶端程式庫可讓您從先前傳回 `ImageInsightsToken` 的搜尋中，線上尋找影像。 此應用程式可取得 `ImageInsightsToken`，並在後續搜尋中使用權杖。 然後會將 `ImageInsightsToken` 傳送到 Bing 並傳回結果，結果中包含 Bing 搜尋 URL 和線上所找到類似影像的 URL。

在 [Github](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs) 上可找到此教學課程的完整原始程式碼，其中含有其他錯誤處理和註釋。

## <a name="prerequisites"></a>Prerequisites

* 任何版本的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。
* 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/) 來執行此應用程式。
* NuGet 圖像式搜尋和 Bing 影像搜尋套件。
    - 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下專案，然後從功能表選取 [管理 NuGet 套件]  。 安裝 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 套件和 `Microsoft.Azure.CognitiveServices.Search.ImageSearch` 套件。 安裝 NuGet 套件也會安裝：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>從 Bing 影像搜尋用戶端程式庫取得 ImageInsightsToken

此應用程式會使用透過 [Bing 影像搜尋用戶端程式庫](../bing-image-search/quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)所取得的 `ImageInsightsToken`。 在新的 C# 主控台應用程式中，建立會使用 `ImageSearchClient()` 來呼叫 API 的用戶端。 然後搭配使用 `SearchAsync()` 與您的查詢：

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

使用 `imageResults.Value.First()` 儲存第一個搜尋結果，然後儲存影像深入解析的 `ImageInsightsToken`。

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

這個 `ImageInsightsToken` 會在要求中傳送至 Bing 圖像式搜尋。

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>在圖像式搜尋要求中新增 ImageInsightsToken

藉由從 Bing 圖像式搜尋回應中所包含的 `ImageInsightsToken` 建立 `ImageInfo` 物件，來指定圖像式搜尋要求的 `ImageInsightsToken`。

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>使用 Bing 圖像式搜尋來尋找 ImageInsightsToken 中的影像

`VisualSearchRequest` 物件包含所要搜尋 `ImageInfo` 中影像的相關資訊。 `VisualSearchMethodAsync()` 方法會取得結果。 影像會以權杖來表示，所以您不需要提供影像二進位檔。

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>逐一查看圖像式搜尋結果

圖像式搜尋的結果為 `ImageTag` 物件。 每個標記都包含 `ImageAction` 物件清單。 每個 `ImageAction` 都包含 `Data` 欄位，這是會取決於動作類型的值清單。 例如，您可以逐一查看 `visualSearchResults.Tags` 中的 `ImageTag` 物件，並從中取得 `ImageAction` 標記。 下列範例會列印 `PagesIncluding` 動作的詳細資料：

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionType

從動作類型取得實際的影像 URL 需要進行轉換，以將 `ActionType` 讀取為 `ImageModuleAction`，其中包含具有值清單的 `Data` 元素。 每個值都是某個影像的 URL。  下列程式碼會將 `PagesIncluding` 動作類型轉換為 `ImageModuleAction`，並讀取那些值：

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

如需這些資料類型的詳細資訊，請參閱[影像 - 圖像式搜尋](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)。

## <a name="returned-urls"></a>傳回的 URL

完整的應用程式會傳回下列 URL：

|ActionType  |URL  |
|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2f www.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2f www.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

如上所示，`TopicResults` 和 `ImageResults` 類型包含針對相關影像的查詢。 URL 會連結至 Bing 搜尋結果。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立圖像式搜尋單頁 Web 應用程式](tutorial-bing-visual-search-single-page-app.md)