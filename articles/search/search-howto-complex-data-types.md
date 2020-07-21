---
title: 如何建立複雜資料類型模型
titleSuffix: Azure Cognitive Search
description: 您可以使用 ComplexType 和集合資料類型，在 Azure 認知搜尋索引中建立嵌套或階層式資料結構的模型。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 0fd7ba1723da77313407725ec676e69b0ef3bca1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496667"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中建立複雜資料類型的模型

用來填入 Azure 認知搜尋索引的外部資料集可能會有許多圖形。 有時候它們會包含階層式或嵌套子結構。 範例可能包括單一客戶的多個位址、單一 SKU 的多種色彩和大小、單一書籍的多位作者等等。 在模型化詞彙中，您可能會看到這些結構稱為「*複雜*」、「*複合*」、「*複合*」或「*匯總*」資料類型。 Azure 認知搜尋用於此概念的一詞是**複雜類型**。 在 Azure 認知搜尋中，複雜類型會使用**複雜欄位**進行模型化。 「複雜欄位」是一個欄位，其中包含可以是任何資料類型（包括其他複雜類型）的子系（子欄位）。 其運作方式類似于程式設計語言中的結構化資料類型。

複雜欄位代表檔中的單一物件，或物件的陣列，視資料類型而定。 類型的欄位 `Edm.ComplexType` 代表單一物件，而類型的欄位 `Collection(Edm.ComplexType)` 代表物件的陣列。

Azure 認知搜尋原本就支援複雜的類型和集合。 這些類型可讓您在 Azure 認知搜尋索引中建立幾乎任何 JSON 結構的模型。 在舊版的 Azure 認知搜尋 Api 中，只能匯入簡維資料列集。 在最新版本中，您的索引現在可以更緊密地對應至來源資料。 換句話說，如果您的來源資料有複雜的類型，您的索引也可以有複雜類型。

若要開始，我們建議使用[飯店資料集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)，您可以在 Azure 入口網站的 [匯**入資料**] 中載入。 Wizard 會偵測來源中的複雜類型，並根據偵測到的結構建議索引架構。

> [!Note]
> 從開始，複雜類型的支援已正式推出 `api-version=2019-05-06` 。 
>
> 如果您的搜尋解決方案是根據集合中壓平合併資料集的先前因應措施而建立，您應該將索引變更為包含最新 API 版本中支援的複雜類型。 如需有關升級 API 版本的詳細資訊，請參閱[升級至最新的 REST API 版本](search-api-migration.md)或[升級至最新的 .net SDK 版本](search-dotnet-sdk-migration-version-9.md)。

## <a name="example-of-a-complex-structure"></a>複雜結構的範例

下列 JSON 檔是由簡單欄位和複雜欄位所組成。 複雜欄位（例如 `Address` 和 `Rooms` ）有子欄位。 `Address`針對這些子欄位具有一組值，因為它是檔中的單一物件。 相反地， `Rooms` 有多組值用於其子欄位，集合中的每個物件各一個。

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>建立複雜欄位

就像任何索引定義一樣，您可以使用入口網站、 [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)或[.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet)來建立包含複雜類型的架構。 

下列範例顯示具有簡單欄位、集合和複雜類型的 JSON 索引架構。 請注意，在複雜型別中，每個子欄位都有一個型別，而且可能有屬性，就像最上層的欄位一樣。 架構對應于上述的範例資料。 `Address`是不是集合的複雜欄位（旅館有一個位址）。 `Rooms`是一個複雜的集合欄位（飯店有多個房間）。

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>更新複雜欄位

所有適用于一般欄位的重新[編制索引規則](search-howto-reindex.md)仍然會套用至複雜的欄位。 在這裡重新描述幾個主要規則，新增欄位不需要重建索引，但大部分的修改都是如此。

### <a name="structural-updates-to-the-definition"></a>定義的結構更新

您可以隨時在複雜欄位中加入新的子欄位，而不需要重建索引。 例如，您可以將 "ZipCode" 新增至或「可設施」 `Address` `Rooms` ，就像將最上層欄位加入至索引一樣。 針對新欄位，現有的檔會有 null 值，直到您藉由更新資料明確填入這些欄位為止。

請注意，在複雜型別中，每個子欄位都有一個型別，而且可能有屬性，就像最上層的欄位一樣

### <a name="data-updates"></a>資料更新

使用動作來更新索引中的現有檔，在 `upload` 複雜且簡單的欄位上的運作方式相同--所有欄位都會被取代。 不過， `merge` （或套用 `mergeOrUpload` 至現有檔時）在所有欄位中都不會有相同的作用。 具體而言， `merge` 不支援合併集合中的元素。 這項限制存在於基本類型和複雜集合的集合中。 若要更新集合，您必須取出完整的集合值、進行變更，然後在索引 API 要求中包含新的集合。

## <a name="searching-complex-fields"></a>搜尋複雜欄位

自由形式搜尋運算式會如預期般使用複雜類型。 如果檔中任何位置的任何可搜尋欄位或子欄位相符，則檔本身會是相符專案。

當您有多個詞彙和運算子時，查詢會取得更多差別細微，而某些詞彙則會有指定的功能變數名稱，如同[Lucene 語法](query-lucene-syntax.md)。 例如，此查詢會嘗試針對 [位址] 欄位的兩個子欄位，比對兩個詞彙「上半」和「或」：

> `search=Address/City:Portland AND Address/State:OR`

這類查詢與篩選不同，這對全文檢索搜尋*而言是不相關的。* 在 [篩選準則] 中，使用[ `any` 或 `all` ](search-query-odata-collection-operators.md)中的範圍變數，將複雜集合的子欄位查詢相互關聯。 上述的 Lucene 查詢會傳回包含 "Maine" 和 "上海，俄勒岡" 的檔，以及其他俄勒岡中的城市。 之所以會發生這種情況，是因為每個子句都會套用到整個檔中其欄位的所有值，因此沒有「目前的子檔」的概念。 如需這項功能的詳細資訊，請參閱[瞭解 Azure 認知搜尋中的 OData 集合篩選](search-query-understand-collection-filters.md)。

## <a name="selecting-complex-fields"></a>選取複雜欄位

`$select`參數是用來選擇要在搜尋結果中傳回的欄位。 若要使用這個參數來選取複雜欄位的特定子欄位，請包含以斜線（）分隔的父欄位和子欄位 `/` 。

> `$select=HotelName, Address/City, Rooms/BaseRate`

如果您想要在搜尋結果中，欄位必須在索引中標記為可抓取。 只有標示為可抓取的欄位可以在語句中使用 `$select` 。

## <a name="filter-facet-and-sort-complex-fields"></a>篩選、facet 和排序複雜欄位

用於篩選和回復搜尋的相同[OData 路徑語法](query-odata-filter-orderby-syntax.md)也可以用於 facet、排序，以及選取搜尋要求中的欄位。 針對複雜型別，適用的規則會控制哪些子欄位可以標記為可排序或 facetable。 如需這些規則的詳細資訊，請參閱[建立索引 API 參考](/rest/api/searchservice/create-index)。

### <a name="faceting-sub-fields"></a>Facet 子欄位

任何子欄位都可以標記為 facetable，除非它是或類型 `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` 。

面向結果中傳回的檔計數是針對父檔（飯店）計算，而不是針對複雜集合（聊天室）中的子檔。 例如，假設飯店有20個 "suite" 類型的聊天室。 指定此 facet 參數 `facet=Rooms/Type` 時，facet 計數會是旅館的一個，而不是房間的20個。

### <a name="sorting-complex-fields"></a>排序複雜欄位

排序作業適用于檔（飯店），而不是子檔（聊天室）。 當您有複雜的類型集合（例如房間）時，請務必瞭解您無法在會議室上進行排序。 事實上，您無法對任何集合進行排序。

當欄位的每份檔都有單一值、欄位是簡單欄位或複雜型別中的子欄位時，排序作業就會運作。 例如， `Address/City` 允許可排序，因為每個飯店只有一個位址，因此 `$orderby=Address/City` 會依城市排序旅館。

### <a name="filtering-on-complex-fields"></a>篩選複雜欄位

您可以在篩選運算式中參考複雜欄位的子欄位。 只要使用用於 facet、排序和選取欄位的相同[OData 路徑語法](query-odata-filter-orderby-syntax.md)。 例如，下列篩選會傳回加拿大的所有旅館：

> `$filter=Address/Country eq 'Canada'`

若要篩選複雜集合欄位，您可以使用**lambda 運算式**搭配[ `any` 和 `all` 運算子](search-query-odata-collection-operators.md)。 在此情況下，lambda 運算式的**範圍變數**是具有子欄位的物件。 您可以使用標準的 OData 路徑語法來參考這些子欄位。 例如，下列篩選會傳回所有具有至少一個 deluxe 室和所有非吸煙室的飯店：

> `$filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)`

和最上層的簡單欄位一樣，只有在索引定義中有可**篩選**的屬性設為時，複雜欄位的簡單子欄位才會包含在篩選中 `true` 。 如需詳細資訊，請參閱[建立索引 API 參考](/rest/api/searchservice/create-index)。

## <a name="next-steps"></a>後續步驟

嘗試匯**入資料**嚮導中的[飯店資料集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)。 您將需要讀我檔案中所提供的 Cosmos DB 連接資訊，才能存取資料。

有了該資訊之後，您在嚮導中的第一個步驟就是建立新的 Azure Cosmos DB 資料來源。 此外，在 wizard 中，當您到達 [目標索引] 頁面時，您會看到具有複雜類型的索引。 建立並載入此索引，然後執行查詢以瞭解新的結構。

> [!div class="nextstepaction"]
> [快速入門：用於匯入、編制索引和查詢的入口網站 wizard](search-get-started-portal.md)
