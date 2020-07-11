---
title: 預覽功能清單
titleSuffix: Azure Cognitive Search
description: 已發行預覽功能，讓客戶可以在其設計和公用程式上提供意見反應。 本文是目前處於預覽狀態的所有功能的完整清單。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: fb99530923f25541316fd159a2270f7035a1916b
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229239"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure 認知搜尋中的預覽功能

本文是預覽中所有功能的完整清單。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

轉換為正式運作的預覽功能會從此清單中移除。 如果下列功能未列出，您可以假設它已正式推出。 如需有關正式運作的公告，請參閱[服務更新](https://azure.microsoft.com/updates/?product=search)或[新功能](whats-new.md)。

|特徵&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 類別 | 描述 | 可用性  |
|---------|------------------|-------------|---------------|
| [**featuresMode 參數**](https://docs.microsoft.com/rest/api/searchservice/search-documents#featuresmode) | 相關性 (計分)  | 相關性分數擴充以包含詳細資料：每個欄位的相似性分數、每個欄位的頻率，以及相符的唯一標記的每個欄位數目。 您可以使用[自訂評分解決方案](https://github.com/Azure-Samples/search-ranking-tutorial)中的這些資料點。 | 使用[ (REST) 的搜尋檔](https://docs.microsoft.com/rest/api/searchservice/search-documents)，搭配 api 版本 = 2020-06-30-preview 或 2019-05-06-preview 來新增此查詢參數。 |
| [**受控服務識別**](search-howto-managed-identities-data-sources.md) | 索引子，安全性| 向 Azure Active Directory 註冊搜尋服務，使其成為受信任的服務，然後在 Azure 資料來源上使用 RBAC 許可權，以允許索引子進行唯讀存取。 | 使用入口網站或[建立資料來源 (REST) ](https://docs.microsoft.com/rest/api/searchservice/create-data-source)的 api 版本 = 2020-06-30-preview 或 api 版本 = 2019-05 06-01.5.1-preview 時，請存取這項功能。 |
| [**Debug 會話**](cognitive-search-debug-session.md) | 入口網站、AI 擴充 (技能集)  | 會話內的技能集編輯器，用來調查和解決技能集的問題。 在 debug 會話期間套用的修正可以儲存至服務中的技能集。 | 僅適用于入口網站，使用 [總覽] 頁面上的 [中頁] 連結來開啟 [debug] 會話。 |
| [**原生 blob 虛刪除**](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | 索引子，Azure blob| Azure 認知搜尋中的 Azure Blob 儲存體索引子可辨識處於虛刪除狀態的 blob，並在編制索引期間移除對應的搜尋檔。 | 使用 [[建立索引子] (REST) ](https://docs.microsoft.com/rest/api/searchservice/create-indexer)的 api 版本 = 2020-06-30-preview 或 api 版本 = 2019-05 06-01.5.1-preview 來新增此設定。 |
| [**自訂實體查閱技能**](cognitive-search-skill-custom-entity-lookup.md ) | AI 擴充 (技能集)  | 一種認知技能，可從自訂、使用者定義的單字和片語清單中尋找文字。 使用這份清單，其會以任何相符的實體標記所有文件。 此技能也支援某種程度的模糊比對，可加以套用以尋找類似但不完全精確的相符項目。 | 使用入口網站中的 [技能集編輯器] 或[建立技能集 (REST) ](https://docs.microsoft.com/rest/api/searchservice/create-skillset)的 api 版本 = 2020-06-30-preview 或 api 版本 = 2019-05 06-01.5.1-preview 來參考這項預覽技能。 |
| [**PII 偵測技能**](cognitive-search-skill-pii-detection.md) | AI 擴充 (技能集)  | 在編制索引期間使用的認知技能，可從輸入文字中解壓縮個人識別資訊，並可讓您選擇以各種方式從該文字遮罩它。 | 使用入口網站中的 [技能集編輯器] 或[建立技能集 (REST) ](https://docs.microsoft.com/rest/api/searchservice/create-skillset)的 api 版本 = 2020-06-30-preview 或 api 版本 = 2019-05 06-01.5.1-preview 來參考這項預覽技能。 |
| [**累加擴充**](cognitive-search-incremental-indexing-conceptual.md) | 索引子設定| 將快取新增至擴充管線，讓您在目標修改（例如技能集或另一個物件的更新）不會變更內容時重複使用現有的輸出。 快取只適用于技能集所產生的擴充檔。| 使用 [[建立索引子] (REST) ](https://docs.microsoft.com/rest/api/searchservice/create-indexer)的 api 版本 = 2020-06-30-preview 或 api 版本 = 2019-05 06-01.5.1-preview 來新增此設定。 |
| [**Cosmos DB 索引子： MongoDB API、Gremlin API、Cassandra API**](search-howto-index-cosmosdb.md) | 索引子資料來源 | 針對 Cosmos DB，SQL API 已正式推出，但 MongoDB、Gremlin 和 Cassandra Api 處於預覽狀態。 | 僅適用于 Gremlin 和 Cassandra，請[先註冊](https://aka.ms/azure-cognitive-search/indexer-preview)，才能在後端針對您的訂用帳戶啟用支援。 可以在入口網站中設定 MongoDB 資料來源。 否則，使用[建立資料來源 (REST) ](https://docs.microsoft.com/rest/api/searchservice/create-datasource)的 api 版本 = 2020-06-30-preview 或 api 版本 = 2019-05 06-01.5.1-preview，即可支援這三個 api 的資料來源設定。 |
|  [**Azure Data Lake Storage Gen2 索引子**](search-howto-index-azure-data-lake-storage.md) | 索引子資料來源 | 從 Data Lake Storage Gen2 編制內容和中繼資料的索引。| 需要[註冊](https://aka.ms/azure-cognitive-search/indexer-preview)，才能在後端針對您的訂用帳戶啟用支援。 使用[建立資料來源 (REST) ](https://docs.microsoft.com/rest/api/searchservice/create-datasource)的 api 版本 = 2020-06-30-preview 或 api 版本 = 2019-05 06-01.5.1-preview 來存取此資料來源。 |
| [**moreLikeThis**](search-more-like-this.md) | 查詢 | 尋找與特定檔相關的檔。 這個功能已存在舊版預覽中。 | 在搜尋檔中新增此查詢參數[ (REST) ](https://docs.microsoft.com/rest/api/searchservice/search-documents)呼叫與 api 版本 = 2020-06-30-preview、2019-05-06-preview、2016-09-01-preview 或 2017-11-11-preview。 |

## <a name="calling-preview-rest-apis"></a>呼叫預覽 REST Api

Azure 認知搜尋一律會先透過 REST API 預先發行實驗性功能，然後再透過 .NET SDK 的發行前版本進行。

預覽功能適用於測試和實驗，目標為收集有關功能設計和實作的意見反應。 基於這個原因，預覽功能可能會隨時間變更，很可能以中斷回溯相容性的方式進行。 這與 GA 版本中的功能形成鮮明對比，除了小型的回溯相容性修正和增強功能外，這些功能很穩定，不太可能變更。 此外，預覽功能不一定最終都會納入 GA 版本。

雖然某些預覽功能可能會在入口網站和 .NET SDK 中提供，但 REST API 一律會有預覽功能。

+ 針對搜尋作業， [**`2020-06-30-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-preview) 是目前的預覽版本。

+ 針對管理作業， [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) 是目前的預覽版本。

較舊的預覽仍可運作，但是經過一段時間後會變成過時。 如果您的程式碼呼叫 `api-version=2019-05-06-Preview` 或 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview` ，那些呼叫仍然有效。 但是，只有最新預覽版本會重新整理改進功能。 

下列的範例語法說明對預覽 API 版本的呼叫。

```HTTP
GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2020-06-30-Preview
```

Azure 認知搜尋服務提供多個版本。 如需詳細資訊，請參閱 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>後續步驟

請參閱搜尋 REST 預覽 API 參考檔。 如果您遇到問題，請向我們尋求[Stack Overflow](https://stackoverflow.com/)或[連絡人支援](https://azure.microsoft.com/support/community/?product=search)的協助。

> [!div class="nextstepaction"]
> [搜尋服務 REST API 參考 (預覽) ](https://docs.microsoft.com/rest/api/searchservice/index-preview)