---
title: 增量擴充（預覽）
titleSuffix: Azure Cognitive Search
description: 緩存 Azure 存儲中的 AI 充實管道的中間內容和增量更改，以保留對現有已處理文檔的投資。 此功能目前為公開預覽狀態。
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024138"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure 認知搜索中增量擴充和緩存簡介

> [!IMPORTANT] 
> 增量擴充當前處於公共預覽版中。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 此時沒有門戶或 .NET SDK 支援。

增量擴充增加了濃縮管道的緩存和狀態，保留了對現有產出的投資，同時僅更改受特定修改影響的文檔。 這不僅保留了您在處理（特別是 OCR 和影像處理）方面的貨幣投資，還使系統更加高效。 緩存結構和內容時，索引子可以確定哪些技能已更改，並且僅運行已修改的技能以及任何下游相關技能。 

## <a name="indexer-cache"></a>索引子快取

增量富集向富集管道添加緩存。 索引子緩存文檔開裂的結果以及每個文檔的每個技能的輸出。 技能集有所更新時，將只會重新執行已變更或下游的技能。 更新的結果將寫入緩存，並在搜索索引或知識存儲中更新文檔。

在物理上，緩存存儲在 Azure 存儲帳戶中的 Blob 容器中。 緩存還使用表存儲進行處理更新的內部記錄。 搜尋服務中的所有索引可共用相同的儲存體帳戶，以進行索引子快取。 為每個索引子為其使用的容器分配了一個唯一且不可變的緩存識別碼。

## <a name="cache-configuration"></a>快取組態

您需要在`cache`索引子上設置屬性，以便開始從增量擴充中獲益。 下面的示例演示了啟用緩存的索引子。 此配置的特定部分在以下部分仲介紹。 有關詳細資訊，請參閱[設置增量富集](search-howto-incremental-index.md)。

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

在現有索引子上設置此屬性需要重置和重新運行索引子，這將導致資料來源中的所有文檔再次被處理。 此步驟對於消除由技能集的早期版本所豐富的任何文檔是必需的。 

## <a name="cache-management"></a>快取管理

快取的生命週期由索引子所管理。 如果索引`cache`器上的屬性設置為 null 或連接字串已更改，則在下次索引子運行時將刪除現有緩存。 快取生命週期也與索引子生命週期有關。 如果索引子遭到刪除，相關聯的快取也會一併刪除。

雖然增量擴充旨在檢測和回應更改，無需干預，但可以使用一些參數來覆蓋預設行為：

+ 確定新文檔的優先順序
+ 繞過技能集檢查
+ 繞過資料來源檢查
+ 部隊技能集評估

### <a name="prioritize-new-documents"></a>確定新文檔的優先順序

將`enableReprocessing`屬性設置為控制對緩存中已表示的傳入文檔的處理。 當`true`（預設）時，在重新運行索引子時，將重新處理緩存中已有的文檔，前提是您的技能更新會影響該文檔。 

當`false`不重新處理 現有文檔時，可以有效地將新傳入內容優先于現有內容。 您只能臨時設置`enableReprocessing``false`。 為了確保整個語料庫的一致性`enableReprocessing`，在大多數情況下`true`，確保所有文檔（無論是新的文檔還是現有文檔）都根據當前技能集定義有效。

### <a name="bypass-skillset-evaluation"></a>旁路技能集評估

修改技能集和重新處理該技能集通常齊頭並進。 但是，對技能集的某些更改不應導致後處理（例如，將自訂技能部署到新位置或使用新的訪問金鑰）。 最有可能的是，這些是週邊修改，對技能集本身的實質沒有真正的影響。 

如果您知道對技能集的更改確實是膚淺的，則應通過將`disableCacheReprocessingChangeDetection`參數設置為`true`：

1. 調用更新技能集並修改技能集定義。
1. 在`disableCacheReprocessingChangeDetection=true`請求上追加參數。
1. 提交更改。

設置此參數可確保僅提交對技能集定義的更新，並且不會評估更改對現有語料庫的影響。

下面的示例顯示了具有參數的更新技能集請求：

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>繞過資料來源驗證檢查

對資料來源定義的大多數更改將使緩存無效。 但是，對於您知道更改不應使緩存不正確情況（例如更改連接字串或旋轉存儲帳戶上的金鑰）時，會追加資料來源更新上的`ignoreResetRequirement`參數。 設置此參數以`true`允許提交通過，而不會觸發重置條件，這將導致從頭開始重新生成和填充所有物件。

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>部隊技能集評估

緩存的目的是避免不必要的處理，但假設您對索引子未檢測到的技能進行更改（例如，更改外部代碼中的內容，如自訂技能）。

在這種情況下，可以使用[重置技能](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)強制重新處理特定技能，包括依賴于該技能輸出的任何下游技能。 此 API 接受 POST 請求，該請求具有應失效並標記為後處理的技能清單。 重置技能後，運行索引子以調用管道。

## <a name="change-detection"></a>變更偵測

啟用緩存後，索引子將評估管道組合中的更改，以確定哪些內容可以重複使用，哪些內容需要重新處理。 本節列舉使緩存完全失效的更改，然後是觸發增量處理的更改。 

### <a name="changes-that-invalidate-the-cache"></a>使緩存不正確更改

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

### <a name="changes-that-trigger-incremental-processing"></a>觸發增量處理的更改

增量處理評估技能集定義並確定要重新運行的技能，有選擇地更新文檔樹的受影響部分。 下面是導致增量擴充的更改的完整清單：

* 技能集中的技能具有不同的類型。 技能的 oData 類型已更新
* 技能的特定參數已更新，例如 URL、預設值或其他參數
* 技能輸出變更，即技能傳回了額外或不同的輸出
* 技能更新結果是不同的上階，也就是說技能鏈結已改變 技能輸入
* 如果為任何上游技能提供輸入的技能已更新，則此上游技能將會失效
* 更新知識存放區投射位置，導致文件重新投射
* 變更知識存放區投射，導致文件重新投射
* 索引子的輸出欄位對應已變更，導致文件重新投射至索引

## <a name="api-reference"></a>應用程式開發介面參考

REST API`2019-05-06-Preview`版本通過索引子、技能集和資料來源上的其他屬性提供增量擴充。 除了參考文檔之外，有關如何調用 API 的詳細資訊，請參閱[配置緩存以進行增量擴充](search-howto-incremental-index.md)。

+ [創建索引子（api 版本=2019-05-06-預覽版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [更新索引子（api 版本=2019-05-06-預覽版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [更新技能集（api 版本=2019-05-06-預覽版）（](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset)請求上的新 URI 參數）

+ [重設技能 (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ 資料庫索引子（Azure SQL、宇宙資料庫）。 某些索引子通過查詢檢索資料。 對於檢索資料的查詢，[更新資料來源](https://docs.microsoft.com/rest/api/searchservice/update-data-source)支援請求**忽略Reset要求**的新參數，該參數應設置為`true`更新操作不應使緩存無效時。 

  使用**忽略 Reset 要求**，因為它可能會導致資料中意外的不一致，不容易檢測到。

## <a name="next-steps"></a>後續步驟

增量擴充是一個強大的功能，可將更改跟蹤擴展到技能集和 AI 充實。 增量擴充可在反覆運算技能集設計時重用現有已處理的內容。

作為下一步，在現有索引子上啟用緩存，或在定義新索引子時添加緩存。

> [!div class="nextstepaction"]
> [配置緩存以進行增量擴充](search-howto-incremental-index.md)
