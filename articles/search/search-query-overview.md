---
title: 查詢類型
titleSuffix: Azure Cognitive Search
description: 深入瞭解認知搜尋中支援的查詢類型，包括自由文字、篩選、自動完成和建議、地理搜尋、系統查詢和檔查閱。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007852"
---
# <a name="query-types-in-azure-cognitive-search"></a>Azure 認知搜尋中的查詢類型

在 Azure 認知搜尋中，查詢是一種往返作業的完整規格，其中包含管理查詢執行的參數，以及可塑造回應的參數。

## <a name="elements-of-a-request"></a>要求的元素

下列範例是使用 [搜尋檔 REST API](/rest/api/searchservice/search-documents)所建立的代表性查詢。 此範例以 [旅館示範索引](search-get-started-portal.md) 為目標，並包含一般參數，讓您可以瞭解查詢的外觀。

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
{
    "queryType": "simple" 
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

查詢一律會導向單一索引的檔集合。 您無法加入索引，或建立自訂或暫存資料結構作為查詢目標。

+ **`queryType`** 設定剖析器，也就是 [預設的簡單查詢](search-query-simple-examples.md) 剖析器， (全文檢索搜尋) 的最佳方式，或是用於高階運算式、鄰近搜尋、模糊和萬用字元搜尋等先進查詢結構的 [完整 Lucene 查詢](search-query-lucene-examples.md) 剖析器。

+ **`search`** 提供比對準則，通常是完整的詞彙或片語，但通常伴隨著布林運算子。 單一的獨立字詞是「字詞」查詢。 以引號括住的多部分查詢是 *片語* 查詢。 搜尋可以是未定義的， **`search=*`** 但如果沒有符合的準則，則結果集是由任意選取的檔所組成。

+ **`searchFields`** 限制特定欄位的查詢執行。 *在索引架構中，* 任何屬性為可搜尋的欄位都是此參數的候選項。

回應也是由您在查詢中包含的參數所塑造：

+ **`select`** 指定要在回應中傳回的欄位。 只有在索引中 *標記為可* 抓取的欄位可以在 select 語句中使用。

+ **`top`** 傳回指定的最相符檔數目。 在此範例中，只會傳回10個點擊。 您可以使用 [上移] 和 [略過] (未顯示) 來將結果分頁。

+ **`count`** 告訴您整個索引中有多少份檔整體相符，這可能比傳回的檔多。 

+ **`orderby`** 如果您想要依值（例如評等或位置）來排序結果，則會使用。 否則，預設值是使用相關性分數來排名結果。

> [!Tip]
> 撰寫任何程式碼之前，您可以使用查詢工具來學習語法，並使用不同的參數進行實驗。 最快的方法是內建入口網站工具 [ [搜尋瀏覽器](search-explorer.md)]。
>
> 如果您遵循本 [快速入門來建立旅館示範索引](search-get-started-portal.md)，您可以將此查詢字串貼入 explorer 的搜尋列中，以執行您的第一個查詢： `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>索引中的欄位屬性如何決定查詢行為

索引設計和查詢設計在 Azure 認知搜尋中緊密結合。 事先要知道的重要事實是，「索引結構描述」與每個欄位上的屬性會決定您可以建置的查詢類型。 

欄位上的索引屬性會設定允許的作業 - 欄位在索引中是否「可搜尋」、在結果中是否「可擷取」、是否「可排序」、是否「可篩選」，不一而足。 在範例查詢字串中， `"$orderby": "Rating"` 僅適用于索引架構中的 [評等] 欄位標示為可 *排序* 。 

![旅館範例的索引定義](./media/search-query-overview/hotel-sample-index-definition.png "旅館範例的索引定義")

上述螢幕擷取畫面是旅館範例的索引屬性的部分清單。 您可以在入口網站中檢視整個索引結構描述。 如需索引屬性的詳細資訊，請參閱[建立索引 REST API](/rest/api/searchservice/create-index)。

> [!Note]
> 某些查詢功能會在整個索引 (而非個別欄位) 啟用。 這些功能包括： [同義字對應](search-synonyms.md)、 [自訂分析器](index-add-custom-analyzers.md)、 [建議工具結構 (用於自動完成和建議的查詢) ](index-add-suggesters.md)、 [評分邏輯以排名結果](index-add-scoring-profiles.md)。

## <a name="choose-a-parser-simple--full"></a>選擇剖析器：簡單 | 完整

Azure 認知搜尋可讓您在兩個查詢剖析器之間進行選擇，以處理一般和特定的查詢。 使用簡單剖析器的要求會使用[簡單查詢語法](query-simple-syntax.md)來制訂，可選取作為預設值，以透過自由格式的文字查詢來獲得其速度和效能。 此語法支援許多常見的搜尋運算子，包括 AND、OR、NOT、片語、尾碼和優先順序運算子。

[完整的 Lucene 查詢語法](query-Lucene-syntax.md#bkmk_syntax)會在您將 `queryType=full` 新增至要求時啟用，可公開廣為採用、且開發作為 [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 一部分的表達式查詢語言。 完整語法可延伸簡單語法。 您為簡單語法所撰寫的查詢，均可在完整的 Lucene 剖析器下執行。 

下列範例可說明這點：相同的查詢，但有不同的 queryType 設定，會產生不同的結果。 在第一個查詢中， `^3` 會將 after `historic` 視為搜尋詞彙的一部分。 此查詢的最高排名結果是「Marquis Plaza & 套件」，其描述中有 *海洋* 。

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

使用完整 Lucene 剖析器的相同查詢會解讀 `^3` 為現場詞彙增強程式。 切換剖析器會變更排名，結果中會包含 *移至* 頂端的歷程記錄。

```http
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>查詢類型

Azure 認知搜尋支援範圍廣泛的查詢類型。 

| 查詢類型 | 使用方式 | 範例和詳細資訊 |
|------------|--------|-------------------------------|
| 自由格式文字檢索 | 搜尋參數和任一剖析器| 全文檢索搜尋會掃描索引中所有「可搜尋」欄位內的一或多個字詞，而且運作方式就如同您對 Google 或 Bing 等搜尋引擎的期待。 簡介中的範例便是全文檢索搜尋。<br/><br/>全文檢索搜尋會使用標準 Lucene 分析器進行詞法分析 (預設) 為小寫的所有詞彙，移除 "a" 這類的停用字詞。 您可以使用 [非英文分析器](index-add-language-analyzers.md#language-analyzer-list) 或 [專門與語言無關的分析器](index-add-custom-analyzers.md#AnalyzerTable) （修改了「詞法分析」）來覆寫預設值。 其中一個範例是[關鍵字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)，其會將欄位的整個內容視為單一語彙基元。 這適合用於郵遞區號、識別碼和產品名稱等資料。 | 
| 經過篩選的搜尋 | [OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)和任一剖析器 | 篩選查詢會對索引中的所有「可篩選」欄位評估布林運算式。 不同於搜尋，篩選查詢會比對欄位的確切內容，包括字串欄位的大小寫。 另一個差異是篩選查詢會以 OData 語法來表示。 <br/>[篩選條件運算式範例](search-query-simple-examples.md#example-3-filter-queries) |
| 地區搜尋 | 欄位上的 [Edm.GeographyPoint 類型](/rest/api/searchservice/supported-data-types)、篩選條件運算式和任一剖析器 | 座標若儲存在具有 Edm.GeographyPoint 的欄位中，可用於 "find near me" 或以地圖為基礎的搜尋控制項。 <br/>[地理搜尋範例](search-query-simple-examples.md#example-5-geo-search)|
| 範圍搜尋 | 篩選條件運算式和簡單的剖析器 | 在 Azure 認知搜尋中，系統會使用篩選參數來建立範圍查詢。 <br/>[範圍篩選範例](search-query-simple-examples.md#example-4-range-filters) | 
| [回復搜尋](query-lucene-syntax.md#bkmk_fields) | 搜尋參數和完整剖析器 | 建置以單一欄位為目標的複合查詢運算式。 <br/>[回復搜尋範例](search-query-lucene-examples.md#example-2-fielded-search) |
| [自動完成或建議的結果](search-autocomplete-tutorial.md) | 自動完成或建議參數 | 以搜尋即服務體驗中的部分字串為基礎執行的替代查詢表單。 您可以一起或個別使用自動完成和建議。 |
| [模糊搜尋](query-lucene-syntax.md#bkmk_fuzzy) | 搜尋參數和完整剖析器 | 比對具有類似建構或拼法的字詞。 <br/>[模糊搜尋範例](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [鄰近搜尋](query-lucene-syntax.md#bkmk_proximity) | 搜尋參數和完整剖析器 | 尋找文件中彼此相近的字詞。 <br/>[鄰近搜尋範例](search-query-lucene-examples.md#example-4-proximity-search) |
| [詞彙提升](query-lucene-syntax.md#bkmk_termboost) | 搜尋參數和完整剖析器 | 如果文件包含已提升的字詞 (相較於其他未提升的字詞)，則將文件的順位提高。 <br/>[字詞提升範例](search-query-lucene-examples.md#example-5-term-boosting) |
| [正則運算式搜尋](query-lucene-syntax.md#bkmk_regex) | 搜尋參數和完整剖析器 | 根據規則運算式的內容來比對。 <br/>[規則運算式範例](search-query-lucene-examples.md#example-6-regex) |
|  [萬用字元或首碼搜尋](query-lucene-syntax.md#bkmk_wildcard) | 搜尋參數和完整剖析器 | 根據首碼和波狀符號 (`~`) 或單一字元 (`?`) 來比對。 <br/>[萬用字元搜尋範例](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>後續步驟

使用入口網站或其他工具（例如 Postman 或 Visual Studio Code）或其中一個 Sdk 來深入探索查詢。 下列連結可讓您開始使用。

+ [搜尋總管](search-explorer.md)
+ [如何在 .NET 中進行查詢](./search-get-started-dotnet.md)
+ [如何在 REST 中進行查詢](./search-get-started-powershell.md)