---
title: 升級 REST API 版本
titleSuffix: Azure Cognitive Search
description: 查看 API 版本中的差異，並瞭解將現有代碼遷移到最新的 Azure 認知搜索服務 REST API 版本所需的操作。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112163"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>升級到最新的 Azure 認知搜索服務 REST API 版本

如果您使用的是[搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)的早期版本，本文將説明您升級應用程式以使用最新的一般可用的 API 版本 2019-05-06。

REST API 的版本 2019-05-06 包含早期版本的一些更改。 這些是大部分具有回溯相容性，因此變更程式碼應該只需要最少的工作 (視您之前使用的版本而定)。 [升級步驟](#UpgradeSteps)概述了使用新功能所需的代碼更改。

> [!NOTE]
> Azure 認知搜索服務實例支援一系列 REST API 版本，包括以前的版本。 您可以繼續使用這些 API 版本，但我們建議您將代碼遷移到最新版本，以便可以訪問新功能。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>版本 2019-05-06 中的新增功能
版本 2019-05-06 是 REST API 的最新普遍版本。 已轉換到此 API 版本中的一般可用狀態的功能包括：

* [自動完成](index-add-suggesters.md)是完成部分指定術語輸入的排行功能。

* [複雜類型](search-howto-complex-data-types.md)為搜索索引中的結構化物件資料提供本機支援。

* [JsonLines 解析模式](search-howto-index-json-blobs.md)是 Azure Blob 索引的一部分，它每個 JSON 實體創建一個搜索文檔，該文檔由一條新線分隔。

* [AI 擴充](cognitive-search-concept-intro.md)提供索引，利用認知服務的 AI 擴充引擎。

多個預覽功能版本與此一般可用的更新一致。 要查看新的預覽功能清單，請參閱[搜索 REST api 版本 2019-05-06-預覽](search-api-preview.md)。

## <a name="breaking-changes"></a>重大變更

包含以下功能的現有代碼將在 api version_2019-05-06 上中斷。

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB 的索引子 - 資料來源現在是"類型"："cosmosdb"

如果使用[Cosmos DB 索引子](search-howto-index-cosmosdb.md )，則必須更改為`"type": "documentdb"``"type": "cosmosdb"`。

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>索引子執行結果錯誤不再具有狀態

索引子執行的錯誤結構以前具有元素`status`。 此元素被刪除，因為它沒有提供有用的資訊。

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>索引子資料來源 API 不再返回連接字串

從 API 版本 2019-05-06 和 2019-05-06-預覽開始，資料來源 API 不再在任何 REST 操作的回應中返回連接字串。 在以前的 API 版本中，對於使用 POST 創建的資料來源，Azure 認知搜索返回**201，** 後跟 OData 回應，該回應以純文字形式包含連接字串。

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>命名實體識別認知技能現已終止

如果在代碼中調用[名稱實體識別](cognitive-search-skill-named-entity-recognition.md)技能，則呼叫將失敗。 替換功能是[實體識別](cognitive-search-skill-entity-recognition.md)。 您應該能夠用沒有其他更改替換技能引用。 兩個版本的 API 簽名都相同。 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟
如果要從以前的 GA 版本 2017-11-11 或 2016-09-01 升級，則除了更改版本號之外，您可能不必對代碼進行任何更改。 您可能需要變更程式碼的唯一情況是︰

* 您的程式碼在 API 回應中傳回無法辨認的屬性時失敗。 您的應用程式預設應該會略過不了解的屬性。

* 您的程式碼仍然存在 API 要求，並嘗試將它們重新傳送給新的 API 版本。 例如，發生原因可能是您的應用程式持續保存搜尋服務 API 所傳回的接續 Token (如需詳細資訊，請在[搜尋服務 API 參考](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)) 中查找 `@search.nextPageParameters`。

如果下列其中一種情況適用您的情況，您可能需要據此變更程式碼。 否則，除非您要開始使用版本 2019-05-06 的[新功能](#WhatsNew)，否則不應進行任何更改。

如果要從預覽 API 版本升級，上述功能也適用，但您還必須注意，某些預覽功能在版本 2019-05-06 中不可用：

* ["More like this" 查詢](search-more-like-this.md)
* [CSV Blob 索引](search-howto-index-csv-blobs.md)
* [MongoDB API 支援 Cosmos DB 索引子](search-howto-index-cosmosdb.md)

如果代碼使用這些功能，則如果不刪除這些功能的使用，您將無法升級到 API 版本 2019-05-06。

> [!IMPORTANT]
> 預覽版 API 是針對測試與評估，不應該用於生產環境。
> 

### <a name="upgrading-complex-types"></a>升級複雜類型

如果代碼對較舊的預覽 API 版本 2017-11-11-預覽版或 2016-09-01 預覽版使用複雜類型，則版本 2019-05-06 中存在一些新的更改限制，您需要瞭解這些限制：

+ 子欄位深度的限制和每個索引的複雜集合數已降低。 如果使用預覽 api 版本創建的索引超過這些限制，則使用 API 版本 2019-05-06 更新或重新創建索引的任何嘗試都將失敗。 如果這適用于您，則需要重新設計架構以適應新的限制，然後重新生成索引。

+ api 版本 2019-05-06 中對於每個文檔的複雜集合元素數有一個新的限制。 如果使用預覽 api 版本創建的文檔超過這些限制的索引，則使用 api 版本 2019-05-06 重新索引資料的任何嘗試都將失敗。 如果這適用于您，則需要在重新索引資料之前，減少每個文檔的複雜集合元素數。

有關詳細資訊，請參閱[Azure 認知搜索的服務限制](search-limits-quotas-capacity.md)。

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>如何升級舊的複雜類型結構

如果代碼使用複雜類型與較舊的預覽 API 版本之一，則可能使用的索引定義格式如下所示：

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

API 版本 2017-11-11-預覽版引入了用於定義索引欄位的較新的樹狀格式。 在新格式中，每個複雜欄位都有一個欄位集合，其中定義了其子欄位。 在 API 版本 2019-05-06 中，此新格式被獨佔使用，嘗試使用舊格式創建或更新索引將失敗。 如果使用舊格式創建了索引，則需要使用 API 版本 2017-11-11-預覽將其更新為新格式，然後才能使用 API 版本 2019-05-06 對其進行管理。

您可以使用 API 版本 2017-11-11-預覽，使用以下步驟將"平面"索引更新為新格式：

1. 執行 GET 請求以檢索索引。 如果已經採用新格式，您就完成了。

2. 將索引從"平面"格式轉換為新格式。 您必須為此編寫代碼，因為編寫本文時沒有可用的示例代碼。

3. 執行 PUT 請求以將索引更新為新格式。 請確保不要更改索引的任何其他詳細資訊，例如欄位的可搜索性/可篩選性，因為更新索引 API 不允許這樣做。

> [!NOTE]
> 無法管理使用 Azure 門戶中舊的"平面"格式創建的索引。 請儘早將索引從"平面"表示形式升級到"樹"表示形式。

## <a name="next-steps"></a>後續步驟

查看搜索 REST API 參考文檔。 如果您遇到問題，請向我們請求[關於 StackOverflow 的説明](https://stackoverflow.com/)或[聯繫支援](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服務 REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)

