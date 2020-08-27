---
title: 預覽功能清單
titleSuffix: Azure Cognitive Search
description: 已發行預覽功能，讓客戶可以針對其設計與公用程式提供意見反應。 本文是目前處於預覽狀態的所有功能的完整清單。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 0e83f63e3c39f2aa20cd46f098185aba523e2478
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950471"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure 認知搜尋中的預覽功能

本文是公開預覽中所有功能的完整清單。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

轉換為正式運作的預覽功能會從這份清單中移除。 如果未列出某項功能，您可以假設它已正式推出。 如需有關正式運作的公告，請參閱 [服務更新](https://azure.microsoft.com/updates/?product=search) 或 [新功能](whats-new.md)。

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 類別 | 描述 | 可用性  |
|---------|------------------|-------------|---------------|
| [**Azure Machine Learning (AML) 技能**](cognitive-search-aml-skill.md) | AI 擴充| 新的技能類型，可將推斷端點與 Azure Machine Learning 整合。 開始使用[本教學課程](cognitive-search-tutorial-aml-custom-skill.md)。 | 使用 [搜尋 REST API 2020-06-30-preview](/rest/api/searchservice/) 或 2019-05-06-preview。 也可在入口網站中的技能集設計中使用，假設認知搜尋和 Azure ML 服務都部署在相同的訂用帳戶中。 |
| [**featuresMode 參數**](/rest/api/searchservice/search-documents#featuresmode) | 相關性 (評分)  | 相關性分數擴充以包含詳細資料：每個欄位的相似性分數、每個欄位詞彙頻率，以及符合之唯一權杖的每個欄位數目。 您可以在 [自訂評分解決方案](https://github.com/Azure-Samples/search-ranking-tutorial)中使用這些資料點。 | 使用 [ (REST) ](/rest/api/searchservice/search-documents) （api 版本 = 2020-06-30-Preview-preview 或 2019-05-06-preview）的搜尋檔，來新增此查詢參數。 |
| [**受控服務識別**](search-howto-managed-identities-data-sources.md) | 索引子，安全性| 向 Azure Active Directory 註冊搜尋服務，使其成為受信任的服務，然後在 Azure 資料來源上使用 RBAC 許可權，以允許索引子進行唯讀存取。 | 使用入口網站或 [建立資料來源 (REST) ](/rest/api/searchservice/create-data-source) （api 版本 = 2020-06-30-Preview-preview 或 api 版本 = 2019-05-06-Preview-preview）時，請存取這項功能。 |
| [**Debug 會話**](cognitive-search-debug-session.md) | 入口網站、AI 擴充 (技能集)  | 會話內的技能集編輯器，用來調查和解決技能集的問題。 在 debug 會話期間套用的修正可以儲存至服務中的技能集。 | 僅限入口網站，在 [總覽] 頁面上使用中頁面連結來開啟 debug 會話。 |
| [**原生 blob 虛刪除**](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | 索引子，Azure blob| Azure 認知搜尋中的 Azure Blob 儲存體索引子會辨識處於虛刪除狀態的 blob，並在編制索引期間移除對應的搜尋檔。 | 使用 [建立索引子 (REST) ](/rest/api/searchservice/create-indexer) （api 版本 = 2020-06-30-Preview-preview 或 api 版本 = 2019-05-06-Preview-preview）來新增此設定設定。 |
| [**自訂實體查閱技能**](cognitive-search-skill-custom-entity-lookup.md ) | AI 擴充 (技能集)  | 一種認知技能，可從自訂、使用者定義的單字和片語清單中尋找文字。 使用這份清單，其會以任何相符的實體標記所有文件。 此技能也支援某種程度的模糊比對，可加以套用以尋找類似但不完全精確的相符項目。 | 在入口網站中使用技能集編輯器來參考此預覽技能，或使用 api 版本 = 2020-06-30-preview-Preview 或 api 版本 = 2019-05-06-preview-Preview [建立技能集 (REST) ](/rest/api/searchservice/create-skillset) 。 |
| [**PII 偵測技能**](cognitive-search-skill-pii-detection.md) | AI 擴充 (技能集)  | 在編制索引期間使用的認知技能，可從輸入文字中取出個人識別資訊，並可讓您選擇以各種方式遮罩該文字。 | 在入口網站中使用技能集編輯器來參考此預覽技能，或使用 api 版本 = 2020-06-30-preview-Preview 或 api 版本 = 2019-05-06-preview-Preview [建立技能集 (REST) ](/rest/api/searchservice/create-skillset) 。 |
| [**累加擴充**](cognitive-search-incremental-indexing-conceptual.md) | 索引子設定| 將快取新增至擴充管線，可讓您在目標修改（例如技能集或另一個物件的更新）不會變更內容時重複使用現有的輸出。 快取只適用于技能集所產生的擴充檔。| 使用 [建立索引子 (REST) ](/rest/api/searchservice/create-indexer) （api 版本 = 2020-06-30-Preview-preview 或 api 版本 = 2019-05-06-Preview-preview）來新增此設定設定。 |
| [**Cosmos DB 索引子： MongoDB API、Gremlin API、Cassandra API**](search-howto-index-cosmosdb.md) | 索引子資料來源 | 針對 Cosmos DB，SQL API 已正式運作，但 MongoDB、Gremlin 和 Cassandra Api 目前為預覽狀態。 | 僅適用于 Gremlin 和 Cassandra，請 [先註冊](https://aka.ms/azure-cognitive-search/indexer-preview) ，才能在後端為您的訂用帳戶啟用支援。 您可以在入口網站中設定 MongoDB 資料來源。 否則，所有三個 Api 的資料來源設定都支援使用 [Create Data source (REST) ](/rest/api/searchservice/create-data-source) ，api 版本 = 2020-06-30-Preview-preview 或 api 版本 = 2019-05-06-Preview-preview。 |
|  [**Azure Data Lake Storage Gen2 索引子**](search-howto-index-azure-data-lake-storage.md) | 索引子資料來源 | Data Lake Storage Gen2 中的索引內容和中繼資料。| 需要[註冊](https://aka.ms/azure-cognitive-search/indexer-preview)，才能在後端為您的訂用帳戶啟用支援。 使用 [Create Data source (REST) ](/rest/api/searchservice/create-data-source) （api 版本 = 2020-06-30-Preview-preview 或 api 版本 = 2019-05-06-Preview-preview）來存取此資料來源。 |
| [**moreLikeThis**](search-more-like-this.md) | 查詢 | 尋找與特定檔相關的檔。 這個功能已存在舊版預覽中。 | 使用 api 版本 = 2020-06-30-preview-Preview、2019-05-06-Preview、2016-09-01-Preview 或 2017-11-11-Preview，在搜尋檔中新增此查詢參數 [ (REST) ](/rest/api/searchservice/search-documents) 呼叫。 |

## <a name="how-to-call-a-preview-rest-api"></a>如何呼叫預覽版 REST API

Azure 認知搜尋一律先透過 REST API 預先發行實驗性功能，然後再透過 .NET SDK 的發行前版本。

預覽功能適用於測試和實驗，目標為收集有關功能設計和實作的意見反應。 基於這個原因，預覽功能可能會隨時間變更，很可能以中斷回溯相容性的方式進行。 這與 GA 版本中的功能形成鮮明對比，除了小型的回溯相容性修正和增強功能外，這些功能很穩定，不太可能變更。 此外，預覽功能不一定最終都會納入 GA 版本。

雖然某些預覽功能可能會在入口網站和 .NET SDK 中提供，但 REST API 一律具有預覽功能。

+ 針對搜尋作業， [**`2020-06-30-Preview`**](/rest/api/searchservice/index-preview) 是目前的預覽版本。

+ 若為管理作業， [**`2019-10-01-Preview`**](/rest/api/searchmanagement/index-2019-10-01-preview) 則為目前的預覽版本。

較舊的預覽仍可運作，但是經過一段時間後會變成過時。 如果您的程式碼呼叫 `api-version=2019-05-06-Preview` 或 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview` ，則這些呼叫仍然有效。 但是，只有最新預覽版本會重新整理改進功能。

下列的範例語法說明對預覽 API 版本的呼叫。

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Azure 認知搜尋服務可在多個版本中使用。 如需詳細資訊，請參閱 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>接下來的步驟

請參閱搜尋 REST 預覽 API 參考檔。 如果您遇到問題，請向我們尋求 [Stack Overflow](https://stackoverflow.com/) 或 [連絡人支援](https://azure.microsoft.com/support/community/?product=search)的協助。

> [!div class="nextstepaction"]
> [搜尋服務 REST API 參考 (預覽) ](/rest/api/searchservice/index-preview)