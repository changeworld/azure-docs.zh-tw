---
title: 升級 REST API 版本
titleSuffix: Azure Cognitive Search
description: 查看 API 版本的差異，並瞭解將現有程式碼遷移至最新的 Azure 認知搜尋服務 REST API 版本所需的動作。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 0f1050bf58e0cd8d9a601d60a4c5dc22a5420483
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949026"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>升級至 Azure 認知搜尋中的最新 REST API

如果您使用的是舊版的 [**搜尋 REST API**](/rest/api/searchservice/)，本文將協助您將應用程式升級至最新正式運作的 API 版本 **2020-06-30**。

2020-06-30 版包含 ([知識存放區](knowledge-store-concept-intro.md)) 的重要新功能，並引進數個次要行為變更。 因此，這個版本大多是回溯相容性，因此，如果您要從舊版 (2019-05-06) 升級，程式碼的變更應該會很短。

> [!NOTE]
> 搜尋服務支援一系列的 REST API 版本，包括較舊的版本。 您可以繼續使用這些 API 版本，但建議您將程式碼遷移至最新版本，以便您可以存取新功能。 經過一段時間後，最舊的 REST API 版本將會被取代，而且 [不再受到支援](search-api-versions.md#unsupported-versions)。

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>如何升級

升級至新版本時，您可能不需要對程式碼進行許多變更，只要變更版本號碼即可。 您可能需要變更程式碼的唯一情況是︰

* 您的程式碼在 API 回應中傳回無法辨認的屬性時失敗。 您的應用程式預設應該會略過不了解的屬性。

* 您的程式碼仍然存在 API 要求，並嘗試將它們重新傳送給新的 API 版本。 例如，發生原因可能是您的應用程式持續保存搜尋服務 API 所傳回的接續 Token (如需詳細資訊，請在[搜尋服務 API 參考](/rest/api/searchservice/Search-Documents)) 中查找 `@search.nextPageParameters`。

* 您的程式碼會參考比2019-05-06 的 API 版本，並且受限於該版本中的一或多個重大變更。 [升級至 2019-05-06](#upgrade-to-2019-05-06)一節會提供更多詳細資料。 

如果其中任何一種情況適用于您，您可能需要據此變更程式碼。 否則，就不需要進行任何變更，不過您可能會想要開始使用新版本中新增的功能。

## <a name="upgrade-to-2020-06-30"></a>升級至2020-06-30

2020-06-30 版是 REST API 的新正式推出版本。 沒有任何中斷性變更，但有一些行為差異。 

此 API 版本中的功能現已正式推出，包括：

* [知識存放區](knowledge-store-concept-intro.md)，透過技能集建立的擴充內容的持續性儲存體，為透過其他應用程式進行下游分析和處理所建立。 有了這項功能，索引子導向的 AI 擴充管線除了搜尋索引之外，還可以填入知識存放區。 如果您使用此功能的預覽版本，它就相當於正式推出的版本。 唯一需要的程式碼變更是修改 api 版本。

行為變更包括下列各項：

* [BM25 排名演算法](index-ranking-similarity.md) 會以較新的技術取代先前的排名演算法。 新服務會自動使用此演算法。 針對現有的服務，您必須將參數設定為使用新的演算法。

* Null 值的排序結果已在此版本中變更，如果排序是，則會先出現 null 值，如果 `asc` 排序是，則為最後一個 `desc` 。 如果您撰寫程式碼來處理 null 值的排序方式，請注意這項變更。

## <a name="upgrade-to-2019-05-06"></a>升級至2019-05-06

2019-05-06 版是舊版 REST API 的正式推出版本。 此 API 版本正式推出的功能包括：

* [自動完成](index-add-suggesters.md) 是一項自動提示功能，會完成部分指定的字詞輸入。
* [複雜](search-howto-complex-data-types.md) 型別提供搜尋索引中結構化物件資料的原生支援。
* [JsonLines 剖析模式](search-howto-index-json-blobs.md)（Azure Blob 索引編制的一部分）會為每個以新行分隔的 JSON 實體建立一個搜尋檔。
* [Ai 擴充](cognitive-search-concept-intro.md) 提供可利用認知服務之 AI 擴充引擎的索引。

### <a name="breaking-changes"></a>重大變更

如果程式碼包含下列功能，則針對舊版 API 所撰寫的現有程式碼將會在 API 版本 = 2019-05-06 和更新版本中斷：

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB 資料來源的索引子現在是 "type"： "cosmosdb"

如果您使用 Cosmos DB 的 [索引子](search-howto-index-cosmosdb.md )，則必須將變更 `"type": "documentdb"` 為 `"type": "cosmosdb"` 。

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>索引子執行結果錯誤不再具有狀態

索引子執行的錯誤結構之前有 `status` 元素。 已移除此元素，因為它並未提供有用的資訊。

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>索引子資料來源 API 不再傳回連接字串

從 API 版本2019-05-06 和 2019-05-06-Preview 開始，資料來源 API 在任何 REST 作業的回應中都不會再傳回連接字串。 在先前的 API 版本中，針對使用 POST 建立的資料來源，Azure 認知搜尋傳回 **201** ，後面接著 OData 回應，其中包含純文字格式的連接字串。

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>現在已停止命名實體辨識認知技能

如果您在程式碼中呼叫 [名稱實體](cognitive-search-skill-named-entity-recognition.md) 辨識技能，則呼叫會失敗。 取代功能是 [實體](cognitive-search-skill-entity-recognition.md)辨識。 您應該能夠取代技能參考，而不需要進行其他變更。 這兩個版本的 API 簽章都相同。 

### <a name="upgrading-complex-types"></a>升級複雜類型

API 版本2019-05-06 新增了對複雜類型的正式支援。 如果您的程式碼在 2017-11-11-Preview 或 2016-09-01-Preview 中針對複雜類型相等的建議實行了先前的建議，則在2019-05-06 版中，您必須注意一些新的和變更的限制：

+ 子欄位深度的限制和每個索引的複雜集合數目已減少。 如果您使用預覽 api 版本建立了超出這些限制的索引，則任何嘗試使用 API 版本2019-05-06 來更新或重新建立的索引都將會失敗。 如果這適用于您，您將需要重新設計您的架構以符合新的限制，然後重建您的索引。

+ 從 api 版本2019-05-06 開始，每份檔的複雜集合元素數目都有新的限制。 如果您使用預覽 api 版本，以超過這些限制的檔建立索引，則任何使用 api 版本2019-05-06 重新索引該資料的嘗試都會失敗。 如果這適用于您，您將需要減少每份檔的複雜集合元素數目，然後再重新索引資料。

如需詳細資訊，請參閱 [Azure 認知搜尋的服務限制](search-limits-quotas-capacity.md)。

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>如何升級舊的複雜型別結構

如果您的程式碼使用複雜型別搭配其中一個較舊的預覽 API 版本，您可能會使用如下所示的索引定義格式：

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

API 版本 2017-11-11-Preview 中引進了定義索引欄位的較新樹狀結構格式。 在新的格式中，每個複雜欄位都有一個欄位集合，其中定義了其子欄位。 在 API 版本2019-05-06 中，這種新格式是專門用的，而且嘗試使用舊格式建立或更新索引將會失敗。 如果您有使用舊格式建立的索引，您必須使用 API 版本 2017-11-11-Preview，將其更新為新格式，才能使用 API 版本2019-05-06 來管理它們。

使用 API 版本 2017-11-11-Preview，您可以使用下列步驟，將「一般」索引更新為新格式：

1. 執行 GET 要求以取得您的索引。 如果已經採用新的格式，就大功告成了。

2. 將索引從 "平坦" 格式轉譯為新格式。 您必須撰寫程式碼，因為在撰寫本文時，沒有可用的範例程式碼。

3. 執行 PUT 要求，將索引更新為新的格式。 請務必不要變更索引的任何其他詳細資料，例如欄位的搜尋能力/filterability，因為更新索引 API 不允許這種情況。

> [!NOTE]
> 您無法從 Azure 入口網站管理以舊的 "單層" 格式建立的索引。 請在最早的便利性中，將您的索引從「一般」標記法升級為「樹狀結構」標記法。

## <a name="next-steps"></a>接下來的步驟

請參閱搜尋 REST API 參考檔。 如果您遇到問題，請向我們尋求 [Stack Overflow](https://stackoverflow.com/) 或 [連絡人支援](https://azure.microsoft.com/support/community/?product=search)的協助。

> [!div class="nextstepaction"]
> [搜尋服務 REST API 參考](/rest/api/searchservice/)