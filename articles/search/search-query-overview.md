---
title: 查詢類型和組合
titleSuffix: Azure Cognitive Search
description: 使用參數來篩選、選取和排序結果，在 Azure 認知搜尋中建立搜尋查詢的基本概念。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 018c3fb08c7fa0ad35fa567bffbeae48b6fbbce9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928831"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Azure 認知搜尋中的查詢類型和組合

在 Azure 認知搜尋中，查詢是一種來回行程作業的完整規格。 在要求中，有參數可提供引擎的執行指示，以及可塑造回應的參數。 未指定的 (`search=*`) ，沒有符合準則，而且使用 null 或預設參數，查詢會針對所有可搜尋的欄位執行，以做為全文檢索搜尋作業，並以任意順序傳回未計分結果集。

下列範例是 [REST API](/rest/api/searchservice/search-documents)中所建立的代表性查詢。 此範例以 [旅館示範索引](search-get-started-portal.md) 為目標，並包含一般參數，讓您可以瞭解查詢的外觀。

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** 設定剖析器，也就是 [預設的簡單查詢](search-query-simple-examples.md) 剖析器， (全文檢索搜尋) 的最佳方式，或是用於高階運算式、鄰近搜尋、模糊和萬用字元搜尋等先進查詢結構的 [完整 Lucene 查詢](search-query-lucene-examples.md) 剖析器。

+ **`search`** 提供比對準則，通常是完整的詞彙或片語，但通常伴隨著布林運算子。 單一的獨立字詞是「字詞」** 查詢。 以引號括住的多部分查詢是 *片語* 查詢。 搜尋可以是未定義的， **`search=*`** 但如果沒有符合的準則，則結果集是由任意選取的檔所組成。

+ **`searchFields`** 限制特定欄位的查詢執行。 *在索引架構中，* 任何屬性為可搜尋的欄位都是此參數的候選項。

回應也是由您在查詢中包含的參數所塑造：

+ **`select`** 指定要在回應中傳回的欄位。 只有在索引中 *標記為可* 抓取的欄位可以在 select 語句中使用。

+ **`top`** 傳回指定的最相符檔數目。 在此範例中，只會傳回10個點擊。 您可以使用 [上移] 和 [略過] (未顯示) 來將結果分頁。

+ **`count`** 告訴您整個索引中有多少份檔整體相符，這可能比傳回的檔多。 

+ **`orderby`** 如果您想要依值（例如評等或位置）來排序結果，則會使用。 否則，預設值是使用相關性分數來排名結果。

在 Azure 認知搜尋中，查詢執行一律會針對單一索引進行驗證，並使用要求中提供的 api 金鑰進行驗證。 在 REST 中，則會於要求標頭中同時提供這兩者。

### <a name="how-to-run-this-query"></a>如何執行這項查詢

撰寫任何程式碼之前，您可以使用查詢工具來學習語法，並使用不同的參數進行實驗。 最快的方法是內建入口網站工具 [ [搜尋瀏覽器](search-explorer.md)]。

如果您遵循本 [快速入門來建立旅館示範索引](search-get-started-portal.md)，您可以將此查詢字串貼入 explorer 的搜尋列中，以執行您的第一個查詢： `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>索引如何啟用查詢作業

索引設計和查詢設計在 Azure 認知搜尋中緊密結合。 事先要知道的重要事實是，「索引結構描述」** 與每個欄位上的屬性會決定您可以建置的查詢類型。 

欄位上的索引屬性會設定允許的作業 - 欄位在索引中是否「可搜尋」**、在結果中是否「可擷取」**、是否「可排序」**、是否「可篩選」**，不一而足。 在範例查詢字串中， `"$orderby": "Rating"` 僅適用于索引架構中的 [評等] 欄位標示為可 *排序* 。 

![旅館範例的索引定義](./media/search-query-overview/hotel-sample-index-definition.png "旅館範例的索引定義")

上述螢幕擷取畫面是旅館範例的索引屬性的部分清單。 您可以在入口網站中檢視整個索引結構描述。 如需索引屬性的詳細資訊，請參閱[建立索引 REST API](/rest/api/searchservice/create-index)。

> [!Note]
> 某些查詢功能會在整個索引 (而非個別欄位) 啟用。 這些功能包括： [同義字對應](search-synonyms.md)、 [自訂分析器](index-add-custom-analyzers.md)、 [建議工具結構 (用於自動完成和建議的查詢) ](index-add-suggesters.md)、 [評分邏輯以排名結果](index-add-scoring-profiles.md)。

## <a name="elements-of-a-query-request"></a>查詢要求的元素

查詢一律會導向單一索引。 您無法加入索引，或建立自訂或暫存資料結構作為查詢目標。 

查詢要求的必要元素包含下列元件：

+ 服務端點和索引檔集合，以包含固定和使用者定義元件的 URL 表示： **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (REST 僅) 是必要的，因為隨時都有一個以上的 API 版本可供使用。 
+ **`api-key`**（查詢或管理員 api 金鑰）會驗證對您服務的要求。
+ **`queryType`**（簡單或完整），如果您使用內建的預設簡單語法，則可以省略。
+ **`search`** 或 **`filter`** 提供比對準則，如果您想要執行空的搜尋，則可以不指定。 這兩種查詢類型會以簡單的剖析器來進行討論，但更進階的查詢則需要搜尋參數以便傳遞複雜的查詢運算式。

其他所有搜尋參數都是選用的。 如需屬性的完整清單，請參閱[建立索引 (REST)](/rest/api/searchservice/create-index)。 若要深入瞭解如何在處理期間使用參數，請參閱 [全文檢索搜尋在 Azure 認知搜尋中的運作方式](search-lucene-query-architecture.md)。

## <a name="choose-apis-and-tools"></a>選擇 Api 和工具

下表列出使用 API 和工具來提交查詢的方法。

| 方法 | 描述 |
|-------------|-------------|
| [搜尋 explorer (入口網站) ](search-explorer.md) | 提供搜尋列以及選取索引和 API 版本的選項。 結果會以 JSON 文件的形式傳回。 建議進行探索、測試和驗證。 <br/>[深入了解。](search-get-started-portal.md#query-index) | 
| [Postman 或其他 REST 工具](search-get-started-postman.md) | Web 測試工具是擬定 REST 呼叫的絕佳選擇。 REST API 支援 Azure 認知搜尋中的每個可能的作業。 在本文中，您將瞭解如何設定 HTTP 要求標頭和主體，以將要求傳送至 Azure 認知搜尋。  |
| [SearchIndexClient (.NET)](/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | 可以用來查詢 Azure 認知搜尋索引的用戶端。  <br/>[深入了解。](search-howto-dotnet-sdk.md#core-scenarios)  |
| [搜尋文件 (REST API)](/rest/api/searchservice/search-documents) | 索引的 GET 或 POST 方法，使用查詢參數作為額外輸入。  |

## <a name="choose-a-parser-simple--full"></a>選擇剖析器：簡單 | 完整

Azure 認知搜尋位於 Apache Lucene 之上，並可讓您選擇兩個查詢剖析器來處理一般和特殊的查詢。 使用簡單剖析器的要求會使用[簡單查詢語法](query-simple-syntax.md)來制訂，可選取作為預設值，以透過自由格式的文字查詢來獲得其速度和效能。 此語法支援許多常見的搜尋運算子，包括 AND、OR、NOT、片語、尾碼和優先順序運算子。

[完整的 Lucene 查詢語法](query-Lucene-syntax.md#bkmk_syntax)會在您將 `queryType=full` 新增至要求時啟用，可公開廣為採用、且開發作為 [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 一部分的表達式查詢語言。 完整語法可延伸簡單語法。 您為簡單語法所撰寫的查詢，均可在完整的 Lucene 剖析器下執行。 

下列範例可說明這點：相同的查詢，但有不同的 queryType 設定，會產生不同的結果。 在第一個查詢中， `^3` 會將 after `historic` 視為搜尋詞彙的一部分。 此查詢的最上層結果是「Marquis Plaza & 套件」，其描述中有*海洋*

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

使用完整 Lucene 剖析器的相同查詢會解讀 `^3` 為現場詞彙增強程式。 切換剖析器會變更排名，結果中會包含 *移至* 頂端的歷程記錄。

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>查詢類型

Azure 認知搜尋支援範圍廣泛的查詢類型。 

| 查詢類型 | 使用方式 | 範例和詳細資訊 |
|------------|--------|-------------------------------|
| 自由格式文字檢索 | 搜尋參數和任一剖析器| 全文檢索搜尋會掃描索引中所有「可搜尋」** 欄位內的一或多個字詞，而且運作方式就如同您對 Google 或 Bing 等搜尋引擎的期待。 簡介中的範例便是全文檢索搜尋。<br/><br/>全文檢索搜尋會使用標準 Lucene 分析器進行詞法分析 (預設) 為小寫的所有詞彙，移除 "a" 這類的停用字詞。 您可以使用 [非英文分析器](index-add-language-analyzers.md#language-analyzer-list) 或 [專門與語言無關的分析器](index-add-custom-analyzers.md#AnalyzerTable) （修改了「詞法分析」）來覆寫預設值。 其中一個範例是[關鍵字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)，其會將欄位的整個內容視為單一語彙基元。 這適合用於郵遞區號、識別碼和產品名稱等資料。 | 
| 經過篩選的搜尋 | [OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)和任一剖析器 | 篩選查詢會對索引中的所有「可篩選」** 欄位評估布林運算式。 不同於搜尋，篩選查詢會比對欄位的確切內容，包括字串欄位的大小寫。 另一個差異是篩選查詢會以 OData 語法來表示。 <br/>[篩選條件運算式範例](search-query-simple-examples.md#example-3-filter-queries) |
| 地區搜尋 | 欄位上的 [Edm.GeographyPoint 類型](/rest/api/searchservice/supported-data-types)、篩選條件運算式和任一剖析器 | 座標若儲存在具有 Edm.GeographyPoint 的欄位中，可用於 "find near me" 或以地圖為基礎的搜尋控制項。 <br/>[地理搜尋範例](search-query-simple-examples.md#example-5-geo-search)|
| 範圍搜尋 | 篩選條件運算式和簡單的剖析器 | 在 Azure 認知搜尋中，系統會使用篩選參數來建立範圍查詢。 <br/>[範圍篩選範例](search-query-simple-examples.md#example-4-range-filters) | 
| [回復搜尋](query-lucene-syntax.md#bkmk_fields) | 搜尋參數和完整剖析器 | 建置以單一欄位為目標的複合查詢運算式。 <br/>[回復搜尋範例](search-query-lucene-examples.md#example-2-fielded-search) |
| [模糊搜尋](query-lucene-syntax.md#bkmk_fuzzy) | 搜尋參數和完整剖析器 | 比對具有類似建構或拼法的字詞。 <br/>[模糊搜尋範例](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [鄰近搜尋](query-lucene-syntax.md#bkmk_proximity) | 搜尋參數和完整剖析器 | 尋找文件中彼此相近的字詞。 <br/>[鄰近搜尋範例](search-query-lucene-examples.md#example-4-proximity-search) |
| [詞彙提升](query-lucene-syntax.md#bkmk_termboost) | 搜尋參數和完整剖析器 | 如果文件包含已提升的字詞 (相較於其他未提升的字詞)，則將文件的順位提高。 <br/>[字詞提升範例](search-query-lucene-examples.md#example-5-term-boosting) |
| [正則運算式搜尋](query-lucene-syntax.md#bkmk_regex) | 搜尋參數和完整剖析器 | 根據規則運算式的內容來比對。 <br/>[規則運算式範例](search-query-lucene-examples.md#example-6-regex) |
|  [萬用字元或首碼搜尋](query-lucene-syntax.md#bkmk_wildcard) | 搜尋參數和完整剖析器 | 根據首碼和波狀符號 (`~`) 或單一字元 (`?`) 來比對。 <br/>[萬用字元搜尋範例](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>管理搜尋結果 

即使您使用 .NET API 而內建序列化，查詢結果仍會在 REST API 中以 JSON 文件的形式串流處理。 您可以設定查詢的參數，並為回應選取特定欄位，以形成結果。

查詢的參數可透過下列方式用來建構結果集：

+ 限制或批次處理結果中的文件數目 (預設為 50 個)
+ 選取要包含在結果中的欄位
+ 設定排序次序
+ 新增搜尋結果醒目提示，以凸顯搜尋結果本文中的相符字詞

### <a name="tips-for-unexpected-results"></a>未預期結果的提示

有時候，結果的本質和結構會不符預期的。 當查詢結果不符合您想看到的結果時，您可以嘗試進行下列查詢修改，看看結果是否有所改善：

+ 將 **`searchMode=any`** (預設) 變更為 **`searchMode=all`** ，以要求符合所有準則，而非任何準則。 此做法在查詢中包含布林運算子時特別有用。

+ 如果文字或語彙分析是必要的，但查詢類型無法進行語言處理，請變更查詢技術。 在全文檢索搜尋中，文字或詞彙分析更正拼寫錯誤、單複數文字形式，甚至是不規則的動詞或名詞。 針對某些查詢（例如模糊或萬用字元搜尋），詞法分析不是查詢剖析管線的一部分。 在某些情況下，規則運算式是既有的因應措施。 

### <a name="paging-results"></a>分頁結果
Azure 認知搜尋可讓您輕鬆地執行搜尋結果分頁。 藉由使用 **`top`** 和 **`skip`** 參數，您可以順暢地發出搜尋要求，讓您能夠以可管理、已排序的子集接收一組完整的搜尋結果，輕鬆地啟用良好的搜尋 UI 做法。 在收到這些分拆成較小子集的結果時，您也會收到整組搜尋結果中所含文件的計數。

您可以在 Azure 認知搜尋中，深入瞭解 [如何分頁搜尋結果](search-pagination-page-layout.md)中的頁面搜尋結果。

### <a name="ordering-results"></a>排序結果
收到搜尋查詢的結果時，您可以要求 Azure 認知搜尋提供依特定欄位中的值排序的結果。 根據預設，Azure 認知搜尋會根據每個檔的搜尋分數排名（衍生自 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)）來排序搜尋結果。

如果您想要 Azure 認知搜尋傳回以搜尋分數以外的值排序的結果，您可以使用 **`orderby`** 搜尋參數。 您可以指定參數的值 **`orderby`** 來包含功能變數名稱，以及地理空間值的[** `geo.distance()` 函數**](query-odata-filter-orderby-syntax.md)調用。 每個運算式後面都可以接著， `asc` 表示以遞增順序要求結果，以及 **`desc`** 表示以遞減順序要求結果。 預設排名為遞增順序。


### <a name="hit-highlighting"></a>搜尋結果醒目提示
在 Azure 認知搜尋中，使用 **`highlight`** 、和參數強調符合搜尋查詢的搜尋結果確切部分，會變得很容易 **`highlightPreTag`** **`highlightPostTag`** 。 您可以指定哪些可搜尋的欄位要強調其 *相符的文字* ，以及指定要附加至 Azure 認知搜尋傳回之相符文字的開頭和結尾的精確字串標記。

## <a name="see-also"></a>請參閱

+ [全文檢索搜尋在 Azure 認知搜尋 (查詢剖析架構中的運作方式) ](search-lucene-query-architecture.md)
+ [搜尋總管](search-explorer.md)
+ [如何在 .NET 中進行查詢](./search-get-started-dotnet.md)
+ [如何在 REST 中進行查詢](./search-get-started-powershell.md)