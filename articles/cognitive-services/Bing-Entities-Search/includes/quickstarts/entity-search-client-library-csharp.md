---
title: Bing 實體搜尋 C# 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 27951a6da2c5e4a9d17e8b332d8ee9706701649d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88931877"
---
使用本快速入門，透過適用於 C# 的 Bing 實體搜尋用戶端程式庫開始搜尋實體。 雖然 Bing 實體搜尋具有與大部分程式設計語言相容的 REST API，但此用戶端程式庫可提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch) 上找到。


## <a name="prerequisites"></a>Prerequisites

* [Visual Studio 2017 或更新版本](https://www.visualstudio.com/downloads/)的任何版本。
* [Json.NET](https://www.newtonsoft.com/json) 架構 (以 NuGet 套件形式提供)。
* 如果您使用 Linux/MacOS，則可以使用 [Mono](https://www.mono-project.com/)來執行此應用程式。
* [Bing 新聞搜尋 SDK 的 NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0)。 安裝此套件也會安裝：
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

若要將 Bing 實體搜尋用戶端程式庫新增至 Visual Studio 專案，請使用 [方案總管]  中的 [管理 NuGet 套件]  選項，然後新增 `Microsoft.Azure.CognitiveServices.Search.EntitySearch` 套件。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>建立應用程式並將其初始化

1. 在 Visual Studio 中建立新的 C# 主控台解決方案。 然後將下列內容新增至主要程式碼檔案。

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>建立用戶端並傳送搜尋要求

1. 建立新的搜尋用戶端。 建立新的 `ApiKeyServiceClientCredentials` 以新增訂用帳戶金鑰。

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. 使用用戶端的 `Entities.Search()` 函式來搜尋您的查詢：
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>取得並列印實體描述

1. 如果此 API 傳回搜尋結果，請從 `entityData` 取得主要實體。

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. 列印主要實體的描述 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../../tutorial-bing-entities-search-single-page-app.md)

* [什麼是 Bing 實體搜尋 API？](../../overview.md )
