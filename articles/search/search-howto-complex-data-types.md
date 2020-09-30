---
title: 如何建立複雜資料類型模型
titleSuffix: Azure Cognitive Search
description: 您可以使用 ComplexType 和集合資料類型，將嵌套或階層式資料結構模型化在 Azure 認知搜尋索引中。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 5b430d5a8f0c2702617b7f6b3935e1b169753552
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530849"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中建立複雜資料類型的模型

用來填入 Azure 認知搜尋索引的外部資料集可能會有許多圖形。 有時會包含階層式或嵌套子結構。 範例可能包含單一客戶的多個位址、單一 SKU 的多種色彩和大小、單一書籍的多位作者等等。 在模型化方面，您可能會看到這些結構稱為 *複雜*、 *複合*、 *複合*或 *匯總* 資料類型。 此概念 Azure 認知搜尋使用的詞彙是 **複雜類型**。 在 Azure 認知搜尋中，複雜型別會使用 **複雜欄位**進行模型化。 複雜欄位是一個欄位，其中包含子欄位 (子欄位) 可以是任何資料類型，包括其他複雜類型。 其運作方式類似于程式設計語言中的結構化資料類型。

複雜欄位代表檔中的單一物件，或是物件的陣列（視資料類型而定）。 類型的欄位 `Edm.ComplexType` 代表單一物件，而類型的欄位則 `Collection(Edm.ComplexType)` 代表物件的陣列。

Azure 認知搜尋原本就支援複雜類型和集合。 這些類型可讓您在 Azure 認知搜尋索引中建立幾乎任何 JSON 結構的模型。 在舊版 Azure 認知搜尋 Api 中，只能匯入壓平合併的資料列集。 在最新版本中，您的索引現在可以更緊密對應至來源資料。 換句話說，如果您的來源資料有複雜型別，則您的索引也可以有複雜類型。

若要開始使用，我們建議使用 [飯店資料集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)，您可以在 Azure 入口網站的 [匯 **入資料** ] 中載入該資料集。 Wizard 會偵測來源中的複雜類型，並根據所偵測到的結構建議索引架構。

> [!Note]
> 從開始，已正式推出對複雜類型的支援 `api-version=2019-05-06` 。 
>
> 如果您的搜尋解決方案是以集合中壓平合併資料集的較早因應措施所建立，您應該變更索引以包含最新 API 版本所支援的複雜類型。 如需有關升級 API 版本的詳細資訊，請參閱 [升級至最新的 REST API 版本](search-api-migration.md) ，或 [升級至最新的 .net SDK 版本](search-dotnet-sdk-migration-version-9.md)。

## <a name="example-of-a-complex-structure"></a>複雜結構的範例

下列 JSON 檔是由簡單的欄位和複雜欄位所組成。 複雜欄位（例如 `Address` 和 `Rooms` ）有子欄位。 `Address` 針對這些子欄位有一組值，因為它是檔中的單一物件。 相反地， `Rooms` 有多組值的子欄位，集合中的每個物件各有一個值。

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

如同任何索引定義，您可以使用入口網站、 [REST API](/rest/api/searchservice/create-index)或 [.net SDK](/dotnet/api/microsoft.azure.search.models.index) 來建立包含複雜類型的架構。 

下列範例顯示具有簡單欄位、集合和複雜類型的 JSON 索引架構。 請注意，在複雜型別中，每個子欄位都有一個型別，而且可以有屬性，就像最上層欄位一樣。 架構對應于上述的範例資料。 `Address` 是一種複雜欄位，不是 (旅館有一個位址) 的集合。 `Rooms` 是一個複雜的集合欄位， (飯店有許多房間) 。

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

所有適用于一般欄位的重新 [編制索引規則](search-howto-reindex.md) 仍適用于複雜欄位。 在這裡重申幾個主要規則，加入欄位不需要重建索引，但大部分的修改都有。

### <a name="structural-updates-to-the-definition"></a>定義的結構更新

您可以隨時在複雜欄位中加入新的子欄位，而不需要重建索引。 例如，您可以將 "ZipCode" 新增至或「便利設施」 `Address` `Rooms` ，就像是將最上層欄位新增至索引一樣。 現有檔的新欄位必須有 null 值，直到您藉由更新資料明確填入這些欄位為止。

請注意，在複雜型別中，每個子欄位都有一個型別，而且可以有屬性，就像最上層欄位一樣

### <a name="data-updates"></a>資料更新

使用動作來更新索引中的現有檔，對 `upload` 複雜和簡單欄位的運作方式相同--會取代所有欄位。 不過， `merge` (或套用 `mergeOrUpload` 至現有檔時) 在所有欄位中的運作都不相同。 具體而言， `merge` 不支援合併集合內的元素。 這項限制存在於基本類型和複雜集合的集合中。 若要更新集合，您需要取出完整的集合值、進行變更，然後在索引 API 要求中包含新的集合。

## <a name="searching-complex-fields"></a>搜尋複雜欄位

自由格式的搜尋運算式會如預期般使用複雜類型。 如果檔中任何位置的任何可搜尋欄位或子欄位相符，則檔本身會是相符專案。

當您有多個詞彙和運算子時，查詢會獲得更多差別細微，而某些詞彙則具有指定的功能變數名稱，因此可以使用 [Lucene 語法](query-lucene-syntax.md)。 例如，此查詢會根據 [位址] 欄位的兩個子欄位，嘗試比對兩個詞彙：「先出」和「或」：

> `search=Address/City:Portland AND Address/State:OR`

這類 *查詢與全文檢索搜尋不相關* ，與篩選不同。 在篩選中，會使用[ `any` 或 `all` ](search-query-odata-collection-operators.md)中的範圍變數，將複雜集合的子欄位查詢相互關聯。 上述的 Lucene 查詢會傳回包含 "上 Maine" 和 "上海，俄勒岡" 的檔，以及在俄勒岡中的其他城市。 發生這種情況是因為每個子句都會套用至整個檔中其欄位的所有值，因此沒有「目前的子檔」的概念。 如需有關這個的詳細資訊，請參閱 [Azure 認知搜尋中的瞭解 OData 集合篩選](search-query-understand-collection-filters.md)。

## <a name="selecting-complex-fields"></a>選取複雜欄位

`$select`參數是用來選擇要在搜尋結果中傳回哪些欄位。 若要使用此參數來選取複雜欄位的特定子欄位，請包含以斜線分隔的父欄位和子欄位 (`/`) 。

> `$select=HotelName, Address/City, Rooms/BaseRate`

如果您想要在搜尋結果中，欄位必須在索引中標記為可抓取。 只有標記為可抓取的欄位可以在語句中使用 `$select` 。

## <a name="filter-facet-and-sort-complex-fields"></a>篩選、facet 和排序複雜欄位

用於篩選和回復搜尋的相同 [OData 路徑語法](query-odata-filter-orderby-syntax.md) 也可以用於 facet、排序和選取搜尋要求中的欄位。 針對複雜類型，會套用規則，以控制哪些子欄位可標示為可排序或可 facet。 如需這些規則的詳細資訊，請參閱 [建立索引 API 參考](/rest/api/searchservice/create-index)。

### <a name="faceting-sub-fields"></a>Facet 子欄位

任何子欄位都可以標示為可 facet，除非它是或類型 `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` 。

在面向的結果中傳回的檔計數，會針對 (飯店) 的父檔計算，而不是 (房間) 的複雜集合中的子檔。 例如，假設旅館具有20個 "suite" 類型的房間。 指定此 facet 參數 `facet=Rooms/Type` 時，facet 計數將會是旅館的一個，而不是房間的20個。

### <a name="sorting-complex-fields"></a>排序複雜欄位

排序作業適用于檔 (飯店) ，而不是 (室) 的子檔。 當您有複雜的型別集合（例如會議室）時，請務必瞭解您根本無法在房間上進行排序。 事實上，您無法對任何集合進行排序。

當欄位具有每份檔的單一值、欄位是否為簡單欄位，或是複雜類型中的子欄位時，排序作業就會運作。 例如， `Address/City` 允許可排序，因為每個旅館只有一個位址，因此 `$orderby=Address/City` 會依城市排序旅館。

### <a name="filtering-on-complex-fields"></a>篩選複雜欄位

您可以參考篩選運算式中複雜欄位的子欄位。 您只需使用用於 facet、排序和選取欄位的相同 [OData 路徑語法](query-odata-filter-orderby-syntax.md) 。 例如，下列篩選準則會傳回加拿大的所有旅館：

> `$filter=Address/Country eq 'Canada'`

若要篩選複雜集合欄位，您可以使用**lambda 運算式**搭配[ `any` and `all` 運算子](search-query-odata-collection-operators.md)。 在此情況下，lambda 運算式的 **範圍變數** 是具有子欄位的物件。 您可以使用標準 OData 路徑語法來參考這些子欄位。 例如，下列篩選器會傳回至少有一個 deluxe 房間和所有非吸煙房間的所有旅館：

> `$filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)`

如同最上層的簡單欄位，如果複雜欄位的簡單子欄位在索引定義中的可 **篩選** 屬性設定為，則只能包含在篩選中 `true` 。 如需詳細資訊，請參閱 [建立索引 API 參考](/rest/api/searchservice/create-index)。

## <a name="next-steps"></a>後續步驟

在 [匯**入資料**] 嚮導中，嘗試[旅館資料集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)。 您將需要讀我檔案中提供的 Cosmos DB 連接資訊，才能存取資料。

有了該資訊之後，您在嚮導中的第一個步驟就是建立新的 Azure Cosmos DB 資料來源。 此外，在嚮導中，當您進入 [目標索引] 頁面時，您會看到具有複雜類型的索引。 建立並載入此索引，然後執行查詢以瞭解新的結構。

> [!div class="nextstepaction"]
> [快速入門：匯入、編制索引和查詢的入口網站 wizard](search-get-started-portal.md)