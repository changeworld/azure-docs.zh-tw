---
title: 教學課程：使用 Bing 圖像式搜尋 SDK 裁剪影像
description: 使用 Bing 圖像式搜尋 SDK 從影像上的特定區域取得深入解析。
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.openlocfilehash: 4778a4089c7374c1ac6a9312064dcfb1e0325b63
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478489"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>教學課程：使用適用於 C# 的 Bing 圖像式搜尋 SDK 裁剪影像

Bing 圖像式搜尋 SDK 可讓您先裁剪影像再尋找類似的線上影像。 此應用程式會從包含許多人的影像裁剪一人，然後傳回包含線上所找到類似影像的搜尋結果。

[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs) 上有提供此應用程式的完整原始程式碼，其中含有其他的錯誤處理和註釋。

本教學課程說明如何：

> [!div class="checklist"]
> * 使用 Bing 圖像式搜尋 SDK 傳送要求
> * 使用 Bing 圖像式搜尋裁剪要搜尋的影像區域
> * 接收和處理回應
> * 在回應中尋找動作項目的 URL

## <a name="prerequisites"></a>Prerequisites

* 任何版本的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。
* 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/)來執行此應用程式。
* 已安裝 [NuGet 自訂搜尋](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)套件。
    - 在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下專案，然後從功能表選取 [管理 NuGet 套件]  。 安裝 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 套件。 安裝 NuGet 自訂搜尋套件也會一併安裝下列組件：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>指定影像的裁剪區域

此應用程式會裁剪這個 Microsoft 資深領導團隊影像的區域。 這個裁剪區域會使用左上和右下座標來加以定義，並以整個影像的百分比來表示：  

![Microsoft 資深領導團隊](./media/MS_SrLeaders.jpg)

系統會藉由從裁剪區域建立 `ImageInfo` 物件，並將該 `ImageInfo` 物件載入至 `VisualSearchRequest`，來裁剪此影像。 `ImageInfo` 物件也會包含該影像的 URL：

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>搜尋類似裁剪區域的影像

`VisualSearchRequest` 變數包含影像裁剪區域的相關資訊及其 URL。 `VisualSearchMethodAsync()` 方法會取得結果：

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>從 `ImageModuleAction` 取得 URL 資料

Bing 圖像式搜尋的結果為 `ImageTag` 物件。 每個標記都包含 `ImageAction` 物件清單。 每個 `ImageAction` 都包含 `Data` 欄位，這是會取決於動作類型的值清單。

您可以透過下列程式碼來列印各種類型：

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

完整應用程式會傳回：

|ActionType  |URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entity -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2f www.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2f www.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2f www.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

如上所示，`Entity` ActionType 所包含的 Bing 搜尋查詢，會傳回可辨識人員、地點或物體的相關資訊。 `TopicResults` 和 `ImageResults` 類型包含針對相關影像的查詢。 清單中的 URL 會連結至 Bing 搜尋結果。

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>取得 `PagesIncluding` `ActionType` 影像的 URL

取得實際的影像 URL 需要進行轉換，以將 `ActionType` 讀取為 `ImageModuleAction`，其中包含具有值清單的 `Data` 元素。 每個值都是某個影像的 URL。 下列程式碼會將 `PagesIncluding` 動作類型轉換為 `ImageModuleAction`，並讀取那些值：

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [建立圖像式搜尋單頁 Web 應用程式](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>另請參閱
> [什麼是 Bing 圖像式搜尋 API？](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
