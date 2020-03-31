---
title: Bing 影片搜尋 C# 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: d50e1acd104916d68f7fbb84ff568cf4efc0b46b
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80289908"
---
使用本快速入門，透過適用於 C# 的 Bing 影片搜尋用戶端程式庫開始搜尋新聞。 雖然 Bing 影片搜尋具有與大部分程式設計語言相容的 REST API，但此用戶端程式庫可提供簡單的方法，將服務整合到您的應用程式。 在 [Github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) 上可找到此範例的完整原始程式碼，以及其他註釋和功能。

## <a name="prerequisites"></a>Prerequisites

* [Visual Studio 2017 或更新版本](https://visualstudio.microsoft.com/downloads/)的任何版本。
* Json.NET 架構 ([以 NuGet 套件形式](https://www.nuget.org/packages/Newtonsoft.Json/)提供)。

若要將 Bing 影片搜尋用戶端程式庫新增至您的專案，請在 Visual Studio 中選取 [方案總管]  中的 [管理 NuGet 套件]  。 新增 `Microsoft.Azure.CognitiveServices.Search.VideoSearch` 套件。

安裝 [[NuGet 影片搜尋 SDK 套件]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) 也會安裝下列相依性：

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>建立專案並將其初始化

1. 在 Visual Studio 中建立新的 C# 主控台解決方案。 然後將下列內容新增至主要程式碼檔案。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. 藉由使用訂用帳戶金鑰建立新的 `ApiKeyServiceClientCredentials` 物件，並呼叫建構函式，來將用戶端具現化。

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>傳送搜尋要求並處理結果

1. 使用用戶端傳送搜尋要求。 將 "SwiftKey" 用於搜尋查詢。

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. 如果傳回任何結果，取得第一個具有 `videoResults.Value[0]` 的結果。 然後列印影片的識別碼、標題和 URL。

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>另請參閱 

* [什麼是 Bing 影片搜尋 API？](../../overview.md)
* [認知服務 .NET SDK 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
