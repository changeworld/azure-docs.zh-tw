---
title: .NET 範例
titleSuffix: Azure Cognitive Search
description: '尋找使用 .NET 用戶端程式庫 Azure 認知搜尋示範 c # 程式碼範例。'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5567cf3bf606b08ce430f9189467d796498ae691
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953898"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>適用于 Azure 認知搜尋的 .NET (c # ) 程式碼範例

深入瞭解 c # 程式碼範例，示範 Azure 認知搜尋解決方案的功能和工作流程。 這些範例會使用適用于 [**AZURE SDK for .net**](/dotnet/azure/)的 [**Azure 認知搜尋用戶端程式庫**](/dotnet/api/overview/azure/search)，您可以透過下列連結流覽。

| 目標 | 連結 |
|--------|------|
| 套件下載 | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API 參考資料 | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API 測試案例 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/測試](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| 原始程式碼 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK 範例

來自 Azure SDK 開發小組的程式碼範例會示範 API 的使用方式。 您可以在 [**azure/azure sdk 中找到這些範例-.net/tree/master/sdk/search/Azure.Search.Documents/範例**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) （位於 GitHub）。

| 範例 | 描述 |
|---------|-------------|
| ["Hello world"，同步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 示範如何使用同步方法來建立用戶端、驗證和處理錯誤。|
| ["Hello world"，非同步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 示範如何使用非同步方法來建立用戶端、驗證和處理錯誤。  |
| [服務層級作業](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | 示範如何建立索引、索引子、資料來源、技能集和同義字地圖。 此範例也會說明如何取得服務統計資料，以及如何查詢索引。  |
| [索引作業](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 示範如何在現有的索引上執行動作，在此案例中，取得儲存在索引中的檔計數。  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | 示範使用不支援的資料類型的技巧。  |
| [ (推送模型) 編制檔索引 ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | 「推送」模型編制索引，您可以在其中將 JSON 承載傳送至服務上的索引。   |
| [加密金鑰範例](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | 示範如何使用客戶管理的加密金鑰，在機密內容上新增額外的保護層。  |

## <a name="doc-samples"></a>Doc 範例

來自認知搜尋團隊的程式碼範例會示範功能和工作流程。 教學課程、快速入門和操作說明文章中都會參考許多這些範例。 您可以在 Azure 中找到這些範例 [**-範例/azure-搜尋-dotnet-範例**](https://github.com/Azure-Samples/azure-search-dotnet-samples) 和 [**azure 中的範例/搜尋-Dotnet-**](https://github.com/Azure-Samples/search-dotnet-getting-started/) 在 GitHub 上入門。

| 範例 | 發行項  |
|---------|-------------|
| [快速入門](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | [快速入門的原始程式碼：建立搜尋索引](search-get-started-dotnet.md)。 本文涵蓋使用範例資料建立、載入和查詢搜尋索引的基本工作流程。 |
| [>dotnethowto](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [如何使用 .net 用戶端程式庫](search-howto-dotnet-sdk.md)的原始程式碼。 本文將逐步解說基本工作流程，但更詳細地討論 API 的使用方式。  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | 原始程式碼 [範例：在 c # 中加入同義字](search-synonyms-tutorial-sdk.md)。 同義字清單用於查詢展開，提供索引外部的相符詞彙。 |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | 教學課程的原始程式碼 [：使用 .NET SDK 為 AZURE SQL 資料編制索引](search-indexer-tutorial.md)。 本文說明如何設定具有排程、欄位對應和參數的 Azure SQL 索引子。  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [如何針對資料加密設定客戶管理金鑰的](search-security-manage-encryption-keys.md)原始程式碼。 |
| [在 C 中建立您的第一個應用程式#](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  教學課程的原始程式碼 [：建立您的第一個搜尋應用程式](tutorial-csharp-create-first-app.md)。 雖然大部分的範例都是主控台應用程式，但此 MVC 範例會使用網頁來前端範例飯店索引，以示範基本搜尋、分頁、自動完成和建議的查詢、facet 和篩選準則。 |
| [多個資料來源](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | 教學課程的原始程式碼 [：來自多個資料來源的索引](tutorial-multiple-data-sources.md)。 |
|  [優化-資料索引](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | 教學課程的原始程式碼 [：使用推送 API 優化索引](tutorial-optimize-indexing-push-api.md)。  |
| [教學課程-ai-擴充](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | 教學課程的原始程式碼 [： AI-使用 .NET SDK 從 Azure blob 產生](cognitive-search-tutorial-blob-dotnet.md)可搜尋的內容。  |

> [!Tip]
> 請嘗試 [範例瀏覽器](/samples/browse/?languages=csharp&products=azure-cognitive-search) ，以搜尋 Github 中的 Microsoft 程式碼範例，並依產品、服務和語言進行篩選。

## <a name="other-samples"></a>其他範例

下列範例也會由認知搜尋小組發佈，但不會在檔中參考。 相關聯的讀我檔案會提供使用指示。

| 範例 | 描述 |
|---------|-------------|
| [azure-搜尋-技術能力](https://github.com/Azure-Samples/azure-search-power-skills)  | 您可以納入您的贏解決方案中之取用自訂技能的原始程式碼。  |
| [知識採礦解決方案加速器](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | 包含範本、支援檔案及分析報表，可協助您建立端對端知識挖掘解決方案的原型。  |
| [Covid-19-19 搜尋應用程式存放庫](https://github.com/liamca/covid19search) | 以認知搜尋為基礎的[covid-19-19 搜尋應用程式](https://covid19search.azurewebsites.net/)的原始程式碼儲存機制 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | 深入瞭解 [jfk 飛往解決方案](https://www.microsoft.com/ai/ai-lab-jfk-files)。 |