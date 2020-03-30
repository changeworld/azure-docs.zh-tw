---
title: 搜尋結果的篩選
titleSuffix: Azure Cognitive Search
description: 按使用者安全標識、語言、地理位置或數值進行篩選，以減少 Azure 認知搜索（Microsoft Azure 上的託管雲搜索服務）中查詢上的搜尋結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03333e853a2ab7606ebe60cc3f68bcb5facfbdb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191009"
---
# <a name="filters-in-azure-cognitive-search"></a>Azure 認知搜索中的篩選器 

*篩選器*提供選擇 Azure 認知搜索查詢中使用的文檔的條件。 未篩選的搜尋會包含索引中的所有文件。 篩選條件會將搜尋查詢的範圍設定為文件子集。 例如，篩選條件可以限制只對具有特定品牌或色彩且超過特定閾值之價格點的產品進行全文檢索搜尋。

某些搜尋體驗會強制實行篩選條件需求以作為實作的一部分，但是，當您想要使用「以值為基礎」** 的準則 (針對 "Simon & Schuster" 所發行的類別 "non-fiction"，將搜尋範圍設定為產品類型 "books") 來限制搜尋時，隨時都可使用篩選條件。

反之，如果您的目標是在特定資料「結構」** 上進行搜尋 (將搜尋範圍設定為客戶評論欄位)，則有替代方法，如下所述。

## <a name="when-to-use-a-filter"></a>使用篩選條件的時機

篩選條件是數個搜尋體驗的基礎，包括 "find near me"、多面向導覽和安全性篩選，以便只顯示允許使用者查看的文件。 如果您實作這其中任一個體驗，則篩選條件是必要的。 它是搜尋查詢附加的篩選條件，這類查詢可提供地理位置座標、使用者所選取的 Facet 類別或要求者的安全性識別碼。

範例案例包含下列項目：

1. 使用篩選條件，根據索引中的資料值來為索引配量。 假設有一個具有城市、房屋類型及便利設施的結構描述，您可能會建立一個篩選條件，明確選取滿足您準則 (位於西雅圖、公寓、濱水區) 的文件。 

   具備相同輸入的全文檢索搜尋通常會產生類似的結果，但篩選條件更為精確，因為它需要針對索引中的內容取得與篩選條件完全相符的項目。 

2. 如果搜尋體驗伴隨著篩選條件需求，請使用篩選條件：

   * [多面向導覽](search-faceted-navigation.md)會使用篩選條件，傳回使用者所選取的 Facet 類別。
   * 地理搜尋會使用篩選條件，在 "find near me" 應用程式中傳遞目前位置的座標。 
   * 安全性篩選條件會傳遞安全性識別碼作為篩選條件準則，索引中的相符項目則會作為用以存取文件權限的 Proxy。

3. 如果您想要數值欄位上的搜尋準則，請使用篩選條件。 

   數值欄位在文件中是可擷取且可出現在搜尋結果中，但不可單獨搜尋它們 (受限於全文檢索搜尋)。 如果您需要以數值資料為基礎的選取準則，請使用篩選條件。

### <a name="alternative-methods-for-reducing-scope"></a>縮減範圍的替代方法

如果您希望在搜尋結果中產生縮減效果，則篩選條件並非唯一的選擇。 視您的目標而定，這些替代選項可能更適合：

 + `searchFields` 查詢參數會將搜尋限制為特定欄位。 例如，如果您的索引分別提供適用於英文和西班牙文描述的欄位，您可以使用 searchFields，將目標設定為要用來進行全文檢索搜尋的欄位。 

+ `$select` 參數可用來指定要包含在結果集中的欄位，先有效地調整回應，然後將它傳送至呼叫應用程式。 此參數不會優化查詢或減少文件組合，但如果目標是較小的回應，則此參數是需要考慮的選項。 

如需其中任一個參數的詳細資訊，請參閱[搜尋文件 > 要求 > 查詢參數](/rest/api/searchservice/search-documents#query-parameters) \(英文\)。


## <a name="how-filters-are-executed"></a>篩選器的執行方式

在查詢時，篩選器解析器接受條件作為輸入，將運算式轉換為表示為樹的原子布林運算式，然後在索引中的可篩選欄位上計算篩選器樹。

篩選與搜索同步進行，從而限定在下游處理中要包括哪些文檔以進行文檔檢索和相關性評分。 當與搜索字串配對時，篩選器可有效地減少後續搜索操作的撤回集。 單獨使用時 (例如，當查詢字串空白時，其中 `search=*`)，篩選條件準則會是唯一的輸入。 

## <a name="defining-filters"></a>定義篩選器
篩選器是 OData 運算式，使用[Azure 認知搜索 中支援的 OData V4 語法子集](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)進行表達。 

您可以為每個**搜索**操作指定一個篩選器，但篩選器本身可以包含多個欄位、多個條件，如果使用**ismatch**函數、多個全文檢索搜尋運算式。 在多部件篩選器運算式中，可以按任意順序指定謂詞（但須遵守運算子優先順序的規則）。 如果您嘗試以特定順序重新整理述詞，則不會有任何顯著的效能改善。

篩選器運算式的限制之一是請求的最大大小限制。 整個要求 (包含篩選條件) 針對 POST 最多可有 16 MB，針對 GET 則是 8 KB。 篩選器運算式中子句的數量也有限制。 最佳經驗法則是，如果您有數百個子句，則必須承擔達到限制的風險。 我們建議以這類不會產生無大小限制之篩選條件的方式來設計您的應用程式。

下列範例會呈現數個 API 中的典型篩選條件定義。

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>篩選使用模式

以下示例說明了篩選器方案的幾個使用模式。 如需更多概念，請參閱 [OData 運算式語法 > 範例](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) \(英文\) 。

+ 獨立的 **$filter** (不含查詢字串)，在篩選條件運算式能夠完全限定為感興趣的文件時很有用。 沒有查詢字串，就沒有語彙或語言分析、沒有計分且沒有排名。 請注意，搜索字串只是一個星號，這意味著"匹配所有文檔"。

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ 查詢字串和 **$filter** 的組合，其中的篩選條件會建立子集，而查詢字串會提供字詞輸入，以便在篩選的子集上進行全文檢索搜尋。 添加術語（步行距離影院）在結果中引入了搜索分數，其中最匹配術語的文檔的排名較高。 將篩選器與查詢字串一起使用是最常見的使用模式。

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ 複合式查詢 (以 "or" 分隔)，每個均有自己的篩選條件準則 (例如，'dog' 中的 'beagles' 或 'cat' 中的 'siamese')。 與 運算式`or`結合計算單獨計算，與回應中發送的每個運算式匹配的文檔的合併。 此使用模式是通過 函數`search.ismatchscoring`實現的。 您還可以使用非評分版本`search.ismatch`。

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  `search.ismatchscoring`也可以將全文檢索搜尋與使用`and`而不是 的`or`篩選器合併，但這在功能上等效于在搜索請求中使用 和`search``$filter`參數。 例如，以下兩個查詢會產生相同的結果：

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

繼續閱讀下列文章，以取得特定使用案例的完整指導方針：

+ [Facet 篩選條件](search-filters-facets.md)
+ [語言篩選條件](search-filters-language.md)
+ [安全性調整](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>進行篩選的欄位需求

在 REST API 中，預設情況下，對於簡單欄位，可篩選為*打開*。 可篩選的欄位會增加索引大小；請務必針對您不打算在篩選條件中實際使用的欄位設定 `"filterable": false`。 如需適用於欄位定義之設定的詳細資訊，請參閱[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\)。

在 .NET SDK 中，可篩選預設為 *off*。 通過將相應[欄位](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet)物件的`true`["可篩選"屬性](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet)設置為 ，可以使欄位可篩選。 您還可以使用[Is 可篩選屬性](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute)以聲明性方式執行此操作。 在下面的示例中，屬性設置在`BaseRate`映射到索引定義的模型類的屬性上。

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>使現有欄位可過濾

不能修改現有欄位以使它們可過濾。 相反，您需要添加新欄位或重建索引。 有關重建索引或重新填充欄位的詳細資訊，請參閱[如何重建 Azure 認知搜索索引](search-howto-reindex.md)。

## <a name="text-filter-fundamentals"></a>文字篩選條件基本概念

文本篩選器將字串欄位與篩選器中提供的文本字串匹配。 與全文檢索搜尋不同，文本篩選器沒有詞法分析或斷字，因此僅對匹配進行比較。 例如，假設欄位*f*包含"陽光日"，`$filter=f eq 'Sunny'`不匹配，但`$filter=f eq 'sunny day'`將。 

文字字串會區分大小寫。 大寫字沒有小寫字：`$filter=f eq 'Sunny day'`找不到"晴天"。

### <a name="approaches-for-filtering-on-text"></a>文本篩選方法

| 方法 | 描述 | 使用時機 |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | 將欄位與分隔的字串清單匹配的函數。 | 建議用於[安全篩選器](search-security-trimming-for-azure-search.md)和需要與字串欄位匹配許多原始文本值的任何篩選器。 **search.in**函數是專為速度設計的，比顯式比較欄位與使用`eq`和`or`的每個字串要快得多。 | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | 此函式可讓您在相同的篩選條件運算式中，混用全文檢索搜尋作業以及純布林值篩選作業。 | 當您想要在一個請求中有多個搜索篩選器組合時，請使用**搜索.ismatch（** 或其計分等效項，**搜索.ismatch計分**）。 您也可以將它用於 *contains* 篩選條件，以篩選較大字串內的部分字串。 |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | 使用者定義的運算式，由欄位、運算子和值所組成。 | 如果要查找字串欄位和字串值之間的精確匹配項，請使用此選項。 |

## <a name="numeric-filter-fundamentals"></a>數值篩選條件基本概念

數值欄位在全文檢索搜尋的內容中不是 `searchable`。 只有字串才能進行全文檢索搜尋。 例如，如果您輸入 99.99 作為搜尋字詞，則將不會得到價格為 $ 99.99 美元的項目。 相反地，您會看到在文件的字串欄位中有數字 99 的項目。 因此，如果您有數值資料，則很可能會用於篩選條件，包括範圍、Facet、群組等。 

包含數值欄位 (價格、大小、SKU、識別碼) 的文件會在搜尋結果中提供那些值，但前提是已將該欄位標記為 `retrievable`。 此處的重點是全文檢索搜尋本身不適用於數值欄位類型。

## <a name="next-steps"></a>後續步驟

首先，嘗試使用入口網站中的 [搜尋總管]****，提交含有 **$filter** 參數的查詢。 [不動產範例索引](search-get-started-portal.md)會在您將下列篩選查詢貼至搜尋列時提供它們的有趣結果：

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

若要使用更多範例，請參閱 [OData 篩選條件運算式語法 > 範例](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) \(英文\)。

## <a name="see-also"></a>另請參閱

+ [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)
+ [搜索文檔 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [簡單查詢語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 查詢語法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
