---
title: .NET 範例
titleSuffix: Azure Cognitive Search
description: '尋找使用 .NET 用戶端程式庫 Azure 認知搜尋示範 c # 程式碼範例。'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: ab6408621616a4be62631391456f73e90fced752
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498994"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>適用于 Azure 認知搜尋的 .NET (c # ) 程式碼範例

深入瞭解 c # 程式碼範例，以示範 Azure 認知搜尋的特性和功能。 主要存放庫如下所示：

| Repository | 描述 |
|------------|-------------|
| [azure-sdk-net/sdk/search/Azure.Search.Documents/samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Azure SDK 團隊所產生的範例，隨附于 SDK 中的 Azure.Search.Documents 用戶端程式庫。 您也可以檢查用戶端程式庫的 [單元測試](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) ，以查看如何呼叫不同的 api。 |
| [Azure-範例/azure-搜尋-dotnet-範例](https://github.com/Azure-Samples/azure-search-dotnet-samples) | 檔中伴隨著 how to 文章的範例，包括 [如何使用 .net 用戶端程式庫](search-howto-dotnet-sdk.md)。|
| [Azure-範例/搜尋-dotnet-快速入門](https://github.com/Azure-Samples/search-dotnet-getting-started) | 本檔的快速入門和教學課程隨附的範例。|

> [!Tip]
> 請嘗試 [範例瀏覽器](/samples/browse/?languages=csharp&products=azure-cognitive-search) ，以搜尋 Github 中的 Microsoft 程式碼範例，並依產品、服務和語言進行篩選。

## <a name="net-sdk-samples"></a>.NET SDK 範例

Azure SDK for .NET 包含許多範例和說明每個範例的 [讀我檔案](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) 。 為了方便起見，以下提供該清單。

| 範例 | 描述 |
|---------|-------------|
| ["Hello world"，同步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 示範如何使用同步方法來建立用戶端、驗證和處理錯誤。|
| ["Hello world"，非同步](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 示範如何使用非同步方法來建立用戶端、驗證和處理錯誤。  |
| [服務層級作業](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | 示範如何建立索引、索引子、資料來源、技能集和同義字地圖。 此範例也會說明如何取得服務統計資料，以及如何查詢索引。  |
| [索引作業](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 示範如何在現有的索引上執行動作，在此案例中，取得儲存在索引中的檔計數。  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | 示範使用不支援的資料類型的技巧。  |
| [ (推送模型) 編制檔索引 ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | 「推送」模型編制索引，您可以在其中將 JSON 承載傳送至服務上的索引。   |
| [加密金鑰範例](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | 示範如何使用客戶管理的加密金鑰，在機密內容上新增額外的保護層。  |

## <a name="documentation-samples"></a>文件和範例

下列範例有 [Azure 認知搜尋檔](./index.yml)中的相關文章。

| 範例 | 描述 |
|---------|-------------|
| [快速入門](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | [快速入門的原始程式碼：建立搜尋索引](search-get-started-dotnet.md)。  |
| [>dotnethowto](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [如何使用 .net 用戶端程式庫](search-howto-dotnet-sdk.md)的原始程式碼 |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | 同義字清單用於查詢展開，提供索引外部的相符詞彙。 範例包含此範例 [：在 c # 中加入同義字](search-synonyms-tutorial-sdk.md)。 |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | 各種文章中索引子相關程式碼片段的原始程式碼背後。 此範例顯示如何設定具有排程、欄位對應和參數的索引子。  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [如何針對資料加密設定客戶管理的金鑰的](search-security-manage-encryption-keys.md)原始程式碼 |
| [在 C 中建立您的第一個應用程式#](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  教學課程的原始程式碼 [：建立您的第一個搜尋應用程式](tutorial-csharp-create-first-app.md)。 雖然大部分的範例都是主控台應用程式，但此 MVC 範例會使用網頁來前端範例飯店索引，以示範基本搜尋、分頁、自動完成和建議的查詢、facet 和篩選準則。 |
| [多個資料來源](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | 教學課程的原始程式碼 [：來自多個資料來源的索引](tutorial-multiple-data-sources.md)。 |
|  [優化-資料索引](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | 教學課程的原始程式碼 [：使用推送 API 優化索引](tutorial-optimize-indexing-push-api.md)。  |
| [教學課程-ai-擴充](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | 教學課程的原始程式碼 [： AI-使用 .NET SDK 從 Azure blob 產生](cognitive-search-tutorial-blob-dotnet.md)可搜尋的內容。  |

## <a name="standalone-samples-and-solutions"></a>獨立範例和解決方案

| 範例 | 描述 |
|---------|-------------|
| [azure-搜尋-技術能力](https://github.com/Azure-Samples/azure-search-power-skills)  | 您可以納入您的贏解決方案中之取用自訂技能的原始程式碼。  |
| [知識採礦解決方案加速器](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | 包含範本、支援檔案及分析報表，可協助您建立端對端知識挖掘解決方案的原型。  |
| [Covid-19-19 搜尋應用程式存放庫](https://github.com/liamca/covid19search) | 以認知搜尋為基礎的[covid-19-19 搜尋應用程式](https://covid19search.azurewebsites.net/)的原始程式碼儲存機制 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | 深入瞭解 [jfk 飛往解決方案](https://www.microsoft.com/ai/ai-lab-jfk-files)。 |