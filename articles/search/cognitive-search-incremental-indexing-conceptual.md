---
title: 增量擴充（預覽）
titleSuffix: Azure Cognitive Search
description: 從 Azure 儲存體中的 AI 擴充管線快取中繼內容和增量變更，以保留現有已處理檔的投資。 此功能目前為公開預覽狀態。
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: f0e7c3bbbdcd1edad24422163fde38e3fdce7e27
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988646"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure 認知搜尋中的增量擴充和快取簡介

> [!IMPORTANT] 
> 增量擴充目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有入口網站或 .NET SDK 支援。

增量擴充會將快取和 statefulness 新增至擴充管線，以保留您對現有輸出的投資，同時只變更受特定修改影響的檔。 這不僅會保留您在處理中的金錢投資（特別是 OCR 和影像處理），也可讓系統更有效率。 快取結構和內容時，索引子可以判斷哪些技能已變更，並只執行已修改的技能，以及任何下游相依技能。 

## <a name="indexer-cache"></a>索引子快取

增量擴充會將快取新增至擴充管線。 索引子會快取檔破解的結果，以及每個檔的每項技能輸出。 技能集有所更新時，將只會重新執行已變更或下游的技能。 更新的結果會寫入至快取，而且會在搜尋索引或知識存放區中更新檔。

實際上，快取會儲存在您 Azure 儲存體帳戶的 blob 容器中。 搜尋服務中的所有索引可共用相同的儲存體帳戶，以進行索引子快取。 每個索引子都會指派一個唯一且不可變的快取識別碼給它所使用的容器。

## <a name="cache-configuration"></a>快取組態

您將需要在索引子上設定 `cache` 屬性，以從累加擴充開始受益。 下列範例說明已啟用快取的索引子。 下列各節將說明這項設定的特定部分。 如需詳細資訊，請參閱[設定累加式擴充](search-howto-incremental-index.md)。

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

在現有的索引子上設定這個屬性，將會要求您重設並重新執行索引子，這會導致資料來源中的所有檔再次被處理。 若要排除由舊版技能集擴充的任何檔，必須執行此步驟。 

## <a name="cache-management"></a>快取管理

快取的生命週期由索引子所管理。 如果索引子上的 `cache` 屬性設定為 null，或連接字串已變更，則會在下一次索引子執行時刪除現有的快取。 快取生命週期也與索引子生命週期有關。 如果索引子遭到刪除，相關聯的快取也會一併刪除。

雖然累加式擴充的設計是要偵測並回應變更，而不需介入，但您還是可以使用參數來覆寫預設行為：

+ 設定新檔的優先順序
+ 略過技能集檢查
+ 略過資料來源檢查
+ 強制技能集評估

### <a name="prioritize-new-documents"></a>設定新檔的優先順序

設定 `enableReprocessing` 屬性，以控制已在快取中表示的傳入檔處理。 當 `true` （預設值）時，當您重新執行索引子時，已在快取中的檔會被重新處理，前提是您的技能更新會影響該 doc 

當 `false`時，不會重新處理現有的檔，而是會有效地排列現有內容的新內送內容優先順序。 您應該只將 `enableReprocessing` 設定為暫時 `false`。 為了確保主體間的一致性，`enableReprocessing` 應該在大部分的時間 `true`，確保所有檔（包括新的和現有的）都是根據目前的技能集定義來有效。

### <a name="bypass-skillset-evaluation"></a>略過技能集評估

修改技能集和重新處理該技能集通常會直接處理。 不過，技能集的某些變更不會導致重新處理（例如，將自訂技能部署到新位置或使用新的存取金鑰）。 最有可能的是，對技能集本身的物質沒有真正影響的周邊修改。 

如果您知道技能集的變更確實是表面，您應該將 `disableCacheReprocessingChangeDetection` 參數設定為 `true`，以覆寫技能集評估：

1. 呼叫 Update 技能集並修改技能集定義。
1. 在要求上附加 `disableCacheReprocessingChangeDetection=true` 參數。
1. 提交變更。

設定此參數可確保只會認可技能集定義的更新，而且不會針對現有主體的效果評估變更。

下列範例顯示具有參數的更新技能集要求：

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>略過資料來源驗證檢查

對資料來源定義進行的大部分變更都會使快取失效。 不過，在您知道變更不應使快取不正確情況下（例如變更連接字串或在儲存體帳戶上輪替金鑰），請在資料來源更新上附加`ignoreResetRequirement` 參數。 將此參數設定為 `true` 可讓認可執行，而不觸發重設條件，這會導致所有物件從頭開始重建和填入。

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>強制技能集評估

快取的目的是要避免不必要的處理，但假設您變更了索引子無法偵測的技能（例如，變更外部程式碼中的某個專案，例如自訂技能）。

在此情況下，您可以使用[重設技能](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)來強制重新處理特定技能，包括任何相依于該技能輸出的下游技能。 此 API 會接受 POST 要求，其中包含應失效並標示為重新處理的技能清單。 重設技能之後，請執行索引子來叫用管線。

## <a name="change-detection"></a>變更偵測

一旦您啟用快取，索引子就會評估管線組合中的變更，以判斷可以重複使用的內容，以及需要重新處理的內容。 此區段會列舉會使快取立即失效的變更，後面接著會觸發累加式處理的變更。 

### <a name="changes-that-invalidate-the-cache"></a>使快取失效的變更

失效變更是指會讓整個快取都不再有效的變更。 舉例來說，讓您的資料來源進行更新的變更，即為失效變更。 以下是會使快取失效的完整變更清單：

* 資料來源類型的變更
* 資料來源容器的變更
* 資料來源認證
* 資料來源變更偵測原則
* 資料來源刪除偵測原則
* 索引子欄位對應
* 索引子參數
    * 剖析模式
    * 排除的副檔名
    * 已編製索引的副檔名
    * 僅針對過大的文件編製儲存體中繼資料的索引
    * 分隔符號文字標頭
    * 分隔符號文字分隔符號
    * 文件根目錄
    * 影像動作 (影像擷取方式的變更)

### <a name="changes-that-trigger-incremental-processing"></a>觸發增量處理的變更

累加式處理會評估您的技能集定義，並決定要重新執行的技能，並選擇性地更新檔樹狀結構的受影響部分。 以下是導致累加擴充的完整變更清單：

* 技能集中的技能具有不同的類型。 技能的 oData 類型已更新
* 技能的特定參數已更新，例如 URL、預設值或其他參數
* 技能輸出變更，即技能傳回了額外或不同的輸出
* 技能更新結果是不同的上階，也就是說技能鏈結已改變 技能輸入
* 如果為任何上游技能提供輸入的技能已更新，則此上游技能將會失效
* 更新知識存放區投射位置，導致文件重新投射
* 變更知識存放區投射，導致文件重新投射
* 索引子的輸出欄位對應已變更，導致文件重新投射至索引

## <a name="api-reference"></a>API 參考

REST API 版本 `2019-05-06-Preview` 會透過索引子、技能集和資料來源上的其他屬性提供累加擴充。 除了參考檔之外，如需如何呼叫 Api 的詳細資訊，請參閱[設定增量擴充](search-howto-incremental-index.md)的快取。

+ [建立索引子（api 版本 = 2019-05 06-01.5.1-Preview）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [更新索引子（api 版本 = 2019-05 06-01.5.1-Preview）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Update 技能集（api 版本 = 2019-05 06-01.5.1-Preview）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) （要求上的新 URI 參數）

+ [重設技能（api 版本 = 2019-05 06-01.5.1-Preview）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ 資料庫索引子（Azure SQL、Cosmos DB）。 某些索引子會透過查詢抓取資料。 若為抓取資料的查詢，[更新資料來源](https://docs.microsoft.com/rest/api/searchservice/update-data-source)支援要求**ignoreResetRequirement**上的新參數，當您的更新動作不應使快取失效時，應該將其設定為 `true`。 

  請謹慎使用**ignoreResetRequirement** ，因為它可能會導致不容易偵測到的資料中發生非預期的不一致。

## <a name="next-steps"></a>後續步驟

累加式擴充是一項強大的功能，可將變更追蹤延伸至技能集和 AI 擴充。 AIncremental 擴充可讓您在反復執行技能集設計時重複使用現有已處理的內容。

下一個步驟是在定義新的索引子時，在現有的索引子上啟用快取，或加入快取。

> [!div class="nextstepaction"]
> [設定增量擴充的快取](search-howto-incremental-index.md)
