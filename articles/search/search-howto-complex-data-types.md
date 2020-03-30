---
title: 如何對複雜資料類型建模
titleSuffix: Azure Cognitive Search
description: 嵌套或分層資料結構可以使用複雜類型和集合資料類型在 Azure 認知搜索索引中建模。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283052"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中建模複雜資料類型

用於填充 Azure 認知搜索索引的外部資料集可以有多種形式。 有時，它們包括分層或嵌套子結構。 示例可能包括單個客戶的多個位址、單個 SKU 的多個顏色和大小、單個書籍的多個作者等。 在建模術語中，您可能會看到這些結構稱為*複雜*、*複合*、*複合*或*聚合*資料類型。 "Azure 認知搜索"一詞用於此概念是**複雜的類型**。 在 Azure 認知搜索中，使用**複雜欄位對複雜**類型進行建模。 複雜欄位是包含任何資料類型（包括其他複雜類型的子欄位）的欄位。 這與程式設計語言中的結構化資料類型的工作方式類似。

複雜欄位表示文檔中的單個物件或物件陣列，具體取決於資料類型。 類型的`Edm.ComplexType`欄位表示單個物件，而類型的`Collection(Edm.ComplexType)`欄位表示物件的陣列。

Azure 認知搜索本機支援複雜的類型和集合。 這些類型允許您對 Azure 認知搜索索引中幾乎任何 JSON 結構進行建模。 在早期版本的 Azure 認知搜索 API 中，只能導入拼合的行集。 在最新版本中，索引現在可以更緊密地對應于來源資料。 換句話說，如果來源資料具有複雜類型，則索引也可以具有複雜類型。

要開始，我們建議在 Azure 門戶中的 **"導入資料**嚮導"中載入"[酒店"資料集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)。 嚮導檢測源中的複雜類型，並建議基於檢測到的結構的索引架構。

> [!Note]
> 對複雜類型的支援通常在 中`api-version=2019-05-06`提供。 
>
> 如果搜索解決方案基於集合中拼合資料集的早期解決方法構建，則應更改索引以包括最新 API 版本中支援的複雜類型。 有關升級 API 版本的詳細資訊，請參閱[升級到最新的 REST API 版本](search-api-migration.md)或[升級到最新的 .NET SDK 版本](search-dotnet-sdk-migration-version-9.md)。

## <a name="example-of-a-complex-structure"></a>複雜結構的示例

以下 JSON 文檔由簡單欄位和複雜欄位組成。 複雜欄位（如`Address`和`Rooms`）具有子欄位。 `Address`這些子欄位具有一組值，因為它是文檔中的單個物件。 相反，`Rooms`其子欄位有多個值集，對於集合中的每個物件，一個值。

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

## <a name="creating-complex-fields"></a>創建複雜欄位

與任何索引定義一樣，您可以使用門戶[、REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)或[.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet)創建包含複雜類型的架構。 

下面的示例顯示了具有簡單欄位、集合和複雜類型的 JSON 索引架構。 請注意，在複雜類型中，每個子欄位都有一個類型，並且可能具有屬性，就像頂級欄位一樣。 架構對應于上面的示例資料。 `Address`是一個複雜的欄位，不是集合（酒店有一個位址）。 `Rooms`是一個複雜的收集領域（酒店有許多房間）。

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

通常應用於欄位的所有[重新索引規則](search-howto-reindex.md)仍應用於複雜欄位。 在此處重新聲明一些主要規則，添加欄位不需要重新生成索引，但大多數修改都不需要重新生成。

### <a name="structural-updates-to-the-definition"></a>對定義的結構更新

您可以隨時向複雜欄位添加新子欄位，而無需重新生成索引。 例如，允許向`Address`或"設施"添加"ZipCode"或"`Rooms`設施"，就像向索引添加頂級欄位一樣。 現有文檔為新欄位具有 null 值，直到通過更新資料顯式填充這些欄位。

請注意，在複雜類型中，每個子欄位都有一個類型，並且可能具有屬性，就像頂級欄位一樣

### <a name="data-updates"></a>資料更新

使用`upload`操作在索引中更新現有文檔的工作方式與複雜和簡單欄位的工作方式相同 - 所有欄位都將替換。 但是，（`merge`或`mergeOrUpload`應用於現有文檔時）在所有欄位中都不起作用。 具體而言`merge`，不支援合併集合中的元素。 此限制存在於基元類型和複雜集合的集合中。 若要更新集合，您需要檢索完整的集合值、進行更改，然後在 Index API 請求中包括新集合。

## <a name="searching-complex-fields"></a>搜索複雜欄位

自由格式搜索運算式在複雜類型中按預期工作。 如果文檔中任何任意位置的任何可搜索欄位或子欄位匹配，則文檔本身是匹配項。

當您有多個術語和運算子時，查詢會變得更加微妙，並且某些術語具有指定的欄位名稱，這與[Lucene 語法](query-lucene-syntax.md)一樣。 例如，此查詢嘗試將兩個術語"波特蘭"和"OR"與"位址"欄位的兩個子欄位匹配：

    search=Address/City:Portland AND Address/State:OR

與篩選器不同，像這樣的查詢對於全文檢索搜尋*是不相關的*。 在篩選器中，對複雜集合子欄位的查詢使用[`any`或`all`](search-query-odata-collection-operators.md)中的範圍變數進行關聯。 上面的 Lucene 查詢返回包含"緬因州波特蘭"和"俄勒岡州波特蘭"以及俄勒岡州其他城市的文檔。 這是因為每個子句都適用于整個文檔中其欄位的所有值，因此沒有"當前子文檔"的概念。 有關此的詳細資訊，請參閱[Azure 認知搜索 中瞭解 OData 集合篩選器](search-query-understand-collection-filters.md)。

## <a name="selecting-complex-fields"></a>選擇複雜欄位

該`$select`參數用於選擇在搜尋結果中返回哪些欄位。 要使用此參數選擇複雜欄位的特定子欄位，請包括父欄位和子欄位，由斜杠 （）`/`分隔。

    $select=HotelName, Address/City, Rooms/BaseRate

如果需要在搜尋結果中標記欄位，則必須在索引中標記為"可檢索"。 語句中只能使用標記為"可檢索"的`$select`欄位。

## <a name="filter-facet-and-sort-complex-fields"></a>篩選、分面和排序複雜欄位

用於篩選和欄位搜索的相同[OData 路徑語法](query-odata-filter-orderby-syntax.md)也可用於在搜索請求中對欄位進行分面、排序和選擇。 對於複雜類型，應用規則來控制哪些子欄位可以標記為可排序或可面性。 有關這些規則的詳細資訊，請參閱[創建索引 API 引用](/rest/api/searchservice/create-index)。

### <a name="faceting-sub-fields"></a>對子欄位進行面

任何子欄位都可以標記為可面欄位，除非其類型`Edm.GeographyPoint`為 或`Collection(Edm.GeographyPoint)`。

在分面結果中返回的文檔計數是為父文檔（酒店）計算的，而不是複雜集合（房間）中的子文檔。 例如，假設一家酒店有 20 間客房的類型"套房"。 給定這個面參數`facet=Rooms/Type`，面數將是一個酒店，而不是20的房間。

### <a name="sorting-complex-fields"></a>對複雜欄位進行排序

排序操作適用于文檔（酒店），不適用於子文檔（房間）。 當您具有複雜的類型集合（如"房間"）時，請務必意識到您根本不不能對"房間"進行排序。 事實上，您無法對任何集合進行排序。

當欄位具有每個文檔的單個值（無論欄位是簡單欄位還是複雜類型的子欄位）時，排序操作都起作用。 例如，`Address/City`允許排序，因為每個酒店只有一個位址，因此`$orderby=Address/City`將按城市對酒店進行排序。

### <a name="filtering-on-complex-fields"></a>在複雜欄位上篩選

可以引用篩選器運算式中複雜欄位的子欄位。 只需使用用於對面、排序和選擇欄位的相同[OData 路徑語法](query-odata-filter-orderby-syntax.md)。 例如，以下篩選器將返回加拿大的所有酒店：

    $filter=Address/Country eq 'Canada'

要在複雜的集合欄位上進行篩選，可以使用[`any`和`all`](search-query-odata-collection-operators.md)的**lambda 運算式**。 在這種情況下，lambda 運算式**的範圍變數**是具有子欄位的物件。 您可以使用標準 OData 路徑語法引用這些子欄位。 例如，以下篩檢程式將返回至少一間豪華客房和所有無煙客房的所有酒店：

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

與頂級簡單欄位一樣，複雜欄位的簡單子欄位只有在篩選器中具有索引定義`true`中的**可篩選**屬性時才能包含在篩選器中。 有關詳細資訊，請參閱[創建索引 API 引用](/rest/api/searchservice/create-index)。

## <a name="next-steps"></a>後續步驟

嘗試 **"導入資料**"嚮導中的["酒店"資料集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)。 您需要在讀取器中提供的 Cosmos DB 連接資訊才能訪問資料。

掌握這些資訊後，嚮導中的第一步是創建新的 Azure Cosmos DB 資料來源。 在嚮導中，當您到達目標索引頁時，您將看到具有複雜類型的索引。 創建並載入此索引，然後執行查詢以瞭解新結構。

> [!div class="nextstepaction"]
> [快速入門：用於導入、索引和查詢的門戶嚮導](search-get-started-portal.md)
