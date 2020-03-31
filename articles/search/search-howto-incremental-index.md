---
title: 配置緩存和增量擴充（預覽）
titleSuffix: Azure Cognitive Search
description: 啟用緩存並保留豐富內容的狀態，以便在認知技能集中進行受控處理。 此功能目前為公開預覽狀態。
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989547"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中配置緩存以增量擴充

> [!IMPORTANT] 
> 增量擴充當前處於公共預覽版中。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 此時沒有門戶或 .NET SDK 支援。

本文介紹如何向擴充管道添加緩存，以便無需每次都重新生成即可增量修改步驟。 預設情況下，技能集是無狀態的，更改其組合的任何部分都需要完全重新運行索引子。 使用增量擴充，索引子可以根據技能集或索引子定義中檢測到的更改確定需要刷新文檔樹的哪些部分。 盡可能保留和重用現有的已處理輸出。 

緩存的內容使用您提供的帳戶資訊放置在 Azure 存儲中。 運行索引子時`ms-az-search-indexercache-<alpha-numerc-string>`將創建名為 的容器。 它應被視為由搜索服務管理的內部元件，不得修改。

如果您不熟悉設置索引子，請從[索引子概述](search-indexer-overview.md)開始，然後繼續[技能集](cognitive-search-working-with-skillsets.md)以瞭解濃縮管道。 有關關鍵概念的更多背景，請參閱[增量豐富](cognitive-search-incremental-indexing-conceptual.md)。

## <a name="enable-caching-on-an-existing-indexer"></a>在現有索引子上啟用緩存

如果您有一個已有技能集的現有索引子，請按照本節中的步驟添加緩存。 作為一次性操作，您必須重新重置和重新運行索引子，然後才能對增量處理生效。

> [!TIP]
> 作為概念證明，您可以運行此[門戶快速入門](cognitive-search-quickstart-blob.md)以創建必要的物件，然後使用 Postman 或閘戶進行更新。 您可能希望附加計費的認知服務資源。 多次運行索引子將耗盡每天的免費分配，然後才能完成所有步驟。

### <a name="step-1-get-the-indexer-definition"></a>第 1 步：獲取索引子定義

從具有這些元件的有效現有索引子開始：資料來源、技能集和索引。 索引子應可運行。 

使用 API 用戶端，構造[GET 索引子請求](https://docs.microsoft.com/rest/api/searchservice/get-indexer)以獲取索引子的當前配置。 當您將預覽 API 版本用於 GET 索引子時，`cache`將一個設置為 null 的屬性添加到定義中。

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

從回應複製索引子定義。

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>步驟 2：修改索引子定義中的緩存屬性

預設情況下，`cache`該屬性為空。 使用 API 用戶端設置緩存配置（門戶不支援此微粒更新）。 

修改緩存物件以包括以下必需和可選屬性： 

+ `storageConnectionString`是必需的，必須將其設置為 Azure 存儲連接字串。 
+ 布林`enableReprocessing`屬性是可選的（`true`預設情況下），它指示已啟用增量擴充。 在需要時，您可以將其設置為`false`掛起增量處理，同時其他資源密集型操作（如索引新文檔）正在進行，然後將其翻轉回`true`以後。

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>第 3 步：重置索引子

在為現有索引子設置增量擴充時，需要重置索引子，以確保所有文檔處於一致狀態。 可以為此任務使用門戶或 API 用戶端和[重置索引子 REST API。](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>第 4 步：保存更新的定義

使用 PUT 請求[更新索引子](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer)，請求的正文應包含具有緩存屬性的更新索引子定義。 如果得到 400，請檢查索引子定義以確保滿足所有要求（資料來源、技能集、索引）。

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

如果現在在索引子上發出另一個 GET 請求，則服務的回應將在緩存物件`ID`中包含一個屬性。 字母數位字串追加到容器的名稱中，其中包含此索引子處理的每個文檔的所有緩存結果和中間狀態。 ID 將用於在 Blob 存儲中唯一具名快取。

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>第 5 步：運行索引子

要運行索引子，可以使用門戶或 API。 在門戶中，從索引子清單中選擇索引子並按一下"**運行**"。 使用門戶的一個優點是您可以監視索引子狀態、記錄作業的持續時間以及處理的文檔數。 門戶頁面每隔幾分鐘刷新一次。

或者，您可以使用 REST[來運行索引子](https://docs.microsoft.com/rest/api/searchservice/run-indexer)：

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

索引子運行後，可以在 Azure Blob 存儲中找到緩存。 容器名稱採用以下格式：`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> 重置和重新運行索引子會導致完全重建，以便可以緩存內容。 所有認知豐富將重新運行在所有文檔上。
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>第 6 步：修改技能集並確認增量擴充

要修改技能集，可以使用門戶或 API。 例如，如果您使用的是文本翻譯，則從`en`到`es`另一種語言的簡單內聯更改足以用於增量擴充的概念驗證測試。

再次運行索引子。 僅更新富集文檔樹的這些部分。 如果您使用[門戶快速入門](cognitive-search-quickstart-blob.md)作為概念驗證，將文本翻譯技能修改為"es"，您會注意到只有 8 個文檔更新，而不是原始 14 個文檔。 不受轉換過程影響的影像檔將從緩存中重用。

## <a name="enable-caching-on-new-indexers"></a>在新索引子上啟用緩存

要為新索引子設置增量擴充，在調用[Create Indexer （2019-05-06-預覽版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer)時，您所要做的就是將`cache`屬性包括在索引子定義負載中。 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>檢查緩存的輸出

緩存由索引子創建、使用和管理，其內容不以人類可讀的格式表示。 確定是否使用緩存的最佳方法是運行索引子並比較執行時間和文檔計數的前後指標。 

例如，假設一個技能集，該技能集以掃描文檔的圖像分析和光學字元辨識 （OCR） 開頭，然後是對結果文本的下游分析。 如果修改下游文本技能，索引子可以從緩存中檢索以前處理的所有圖像和 OCR 內容，僅更新和處理編輯中指示的文本相關更改。 在文檔計數中，您可以看到的文檔數（例如，在原始運行中為 8/8 而不是 14/14），執行時間較短，帳單上的費用也更少。

## <a name="working-with-the-cache"></a>使用緩存

緩存運行後，每當調用[Run Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer)時，索引子都會檢查緩存，以查看可以使用現有輸出的哪些部分。 

下表總結了各種 API 與緩存的關係：

| API           | 緩存影響     |
|---------------|------------------|
| [創建索引子 （2019-05-06-預覽版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | 首次使用時創建並運行索引子，包括在索引子定義指定時創建緩存。 |
| [執行索引子](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | 按需執行濃縮管道。 此 API 如果存在，則從緩存中讀取，如果向更新的索引子定義添加緩存，則創建緩存。 當您運行已啟用緩存的索引子時，如果可以使用緩存的輸出，則索引子將省略步驟。 您可以使用此 API 的一般可用或預覽 API 版本。|
| [重設索引子](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| 清除索引子的任何增量索引資訊。 下一個索引子運行（按需或計畫）是從頭開始完全重新處理，包括重新運行所有技能並重建緩存。 它在功能上等效于刪除索引子並重新創建索引子。 您可以使用此 API 的一般可用或預覽 API 版本。|
| [重置技能 （2019-05-06-預覽）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | 指定在下一個索引子上運行時重新運行哪些技能，即使您尚未修改任何技能也是如此。 緩存將相應地更新。 輸出（如知識存儲或搜索索引）使用緩存中的可重用資料以及根據更新的技能的新內容刷新。 |

有關控制緩存發生的情況的詳細資訊，請參閱[緩存管理](cognitive-search-incremental-indexing-conceptual.md#cache-management)。

## <a name="next-steps"></a>後續步驟

增量擴充適用于包含技能集的索引子。 作為下一步，請訪問技能集文檔以瞭解概念和組成。 

此外，啟用緩存後，您需要瞭解因素分解緩存的參數和 API，包括如何重寫或強制特定行為。 如需詳細資訊，請參閱下列連結。

+ [技能集概念和組合](cognitive-search-working-with-skillsets.md)
+ [如何創建技能集](cognitive-search-defining-skillset.md)
+ [增量濃縮和緩存簡介](cognitive-search-incremental-indexing-conceptual.md)
