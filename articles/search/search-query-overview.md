---
title: 查詢類型和組合
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜索中構建搜索查詢的基礎知識，使用參數篩選、選擇和排序結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282818"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Azure 認知搜索中的查詢類型和組合

在 Azure 認知搜索中，查詢是往返操作的完整規範。 請求上的參數為在索引中查找文檔提供了匹配條件，其中包含或排除哪些欄位、傳遞給引擎的執行指令以及用於形成回應的指令。 未指定`search=*`（ ），查詢將對所有可搜索欄位作為全文檢索搜尋操作運行，以任意順序返回未評分的結果集。

下面的示例是在[REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)中構造的代表性查詢。 此示例針對[酒店演示索引](search-get-started-portal.md)，並包含通用參數。

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

+ **`queryType`** 設置解析器，該解析器是[預設的簡單查詢解析器](search-query-simple-examples.md)（最適合全文檢索搜尋），或用於高級查詢構造（如正則運算式、鄰近搜尋、模糊搜索和萬用字元搜尋等）[的完整 Lucene 查詢解析器](search-query-lucene-examples.md)，僅舉幾例。

+ **`search`** 提供匹配條件，通常是文本，但通常伴隨著布林運算子。 單一的獨立字詞是「字詞」** 查詢。 用引號括住、有多個部分的查詢則是「關鍵片語」** 查詢。 搜索可以未定義，如 在**`search=*`** 中所示，但更可能包括與示例中顯示的術語、短語和運算子類似的術語、短語和運算子。

+ **`searchFields`** 將查詢執行約束到特定欄位。 在索引架構中歸為*可搜索*的任何欄位都是此參數的候選欄位。

回應也會由查詢中所包含的參數來形成。 在此示例中，結果集由**`select`** 語句中列出的欄位組成。 只有標記為*可檢索*的欄位才能在$select語句中使用。 此外，此查詢**`top`** 中僅返回 10 個命中，**`count`** 同時告訴您總體匹配的文檔數，這比返回的文檔數還多。 在此查詢中，行按降冪按評級排序。

在 Azure 認知搜索中，查詢執行始終針對一個索引，使用請求中提供的 api 鍵進行身份驗證。 在 REST 中，則會於要求標頭中同時提供這兩者。

### <a name="how-to-run-this-query"></a>如何執行這項查詢

要執行此查詢，請使用[搜索資源管理器和酒店演示索引](search-get-started-portal.md)。 

您可以將這個查詢字串貼到總管中的搜尋列：`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>索引如何啟用查詢作業

索引設計和查詢設計在 Azure 認知搜索中緊密耦合。 事先要知道的重要事實是，「索引結構描述」** 與每個欄位上的屬性會決定您可以建置的查詢類型。 

欄位上的索引屬性會設定允許的作業 - 欄位在索引中是否「可搜尋」**、在結果中是否「可擷取」**、是否「可排序」**、是否「可篩選」**，不一而足。 在依例查詢字串中，`"$orderby": "Rating"`只有"評級"欄位在索引架構中標記為*可排序*，才有效。 

![酒店樣本的索引定義](./media/search-query-overview/hotel-sample-index-definition.png "酒店樣本的索引定義")

上述螢幕截圖是酒店示例的索引屬性的部分清單。 您可以在入口網站中檢視整個索引結構描述。 如需索引屬性的詳細資訊，請參閱[建立索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)。

> [!Note]
> 某些查詢功能會在整個索引 (而非個別欄位) 啟用。 這些功能包括：[同義字映射](search-synonyms.md)，[自訂分析器](index-add-custom-analyzers.md)，[建議器構造（用於自動完成和建議的查詢），](index-add-suggesters.md)[評分邏輯的排名結果](index-add-scoring-profiles.md)。

## <a name="elements-of-a-query-request"></a>查詢要求的元素

查詢一律會導向單一索引。 您無法加入索引，或建立自訂或暫存資料結構作為查詢目標。 

查詢要求的必要元素包含下列元件：

+ 服務終結點和索引文件組合，表示為包含固定和使用者定義的元件的 URL：**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**（僅 REST） 是必需的，因為 API 的多個版本在任何時候都可用。 
+ **`api-key`**，查詢或管理員 api 鍵，對服務的請求進行身份驗證。
+ **`queryType`**，如果使用內置的預設簡單語法，可以省略簡單語法。
+ **`search`** 或**`filter`** 提供匹配條件，如果要執行空搜索，可以未指定匹配條件。 這兩種查詢類型會以簡單的剖析器來進行討論，但更進階的查詢則需要搜尋參數以便傳遞複雜的查詢運算式。

其他所有搜尋參數都是選用的。 如需屬性的完整清單，請參閱[建立索引 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index)。 有關在處理過程中如何使用參數，請參閱[全文檢索搜尋在 Azure 認知搜索中的工作原理](search-lucene-query-architecture.md)。

## <a name="choose-apis-and-tools"></a>選擇 API 和工具

下表列出使用 API 和工具來提交查詢的方法。

| 方法 | 描述 |
|-------------|-------------|
| [搜索資源管理器（門戶）](search-explorer.md) | 提供搜尋列以及選取索引和 API 版本的選項。 結果會以 JSON 文件的形式傳回。 建議用於探索、測試和驗證。 <br/>[深入了解。](search-get-started-portal.md#query-index) | 
| [郵遞員或其他 REST 工具](search-get-started-postman.md) | Web 測試工具是擬定 REST 呼叫的絕佳選擇。 REST API 支援 Azure 認知搜索中的每個可能操作。 在本文中，瞭解如何設置用於向 Azure 認知搜索發送請求的 HTTP 要求標頭和正文。  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | 可用於查詢 Azure 認知搜索索引的用戶端。  <br/>[深入了解。](search-howto-dotnet-sdk.md#core-scenarios)  |
| [搜尋文件 (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | 索引的 GET 或 POST 方法，使用查詢參數作為額外輸入。  |

## <a name="choose-a-parser-simple--full"></a>選擇剖析器：簡單 | 完整

Azure 認知搜索位於 Apache Lucene 的頂部，為您提供了兩個查詢解析器之間的選擇，用於處理典型查詢和專用查詢。 使用簡單剖析器的要求會使用[簡單查詢語法](query-simple-syntax.md)來制訂，可選取作為預設值，以透過自由格式的文字查詢來獲得其速度和效能。 此語法支援許多常見的搜尋運算子，包括 AND、OR、NOT、片語、尾碼和優先順序運算子。

[完整的 Lucene 查詢語法](query-Lucene-syntax.md#bkmk_syntax)會在您將 `queryType=full` 新增至要求時啟用，可公開廣為採用、且開發作為 [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 一部分的表達式查詢語言。 完整語法可延伸簡單語法。 您為簡單語法所撰寫的查詢，均可在完整的 Lucene 剖析器下執行。 

下列範例可說明這點：相同的查詢，但有不同的 queryType 設定，會產生不同的結果。 在第一個查詢中，`^3`後`historic`項被視為搜索詞的一部分。 此查詢排名靠前的結果是"馬奎斯廣場&套房"，其描述中具有*海洋*

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

使用完整 Lucene 解析器的相同查詢`^3`將解釋為現場內術語助推器。 切換解析器更改排名，包含術語 *"歷史*"的結果將移動到頂部。

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>查詢類型

Azure 認知搜索支援廣泛的查詢類型。 

| 查詢類型 | 使用量 | 範例和詳細資訊 |
|------------|--------|-------------------------------|
| 自由格式文字檢索 | 搜尋參數和任一剖析器| 全文檢索搜尋會掃描索引中所有「可搜尋」** 欄位內的一或多個字詞，而且運作方式就如同您對 Google 或 Bing 等搜尋引擎的期待。 簡介中的範例便是全文檢索搜尋。<br/><br/>全文檢索搜尋會使用標準 Lucene 分析器 (預設值) 將所有字詞轉換為小寫，並移除停用字詞 (例如 "the")，以進行文字分析。 若要覆寫預設值，您可以使用[非英文分析器](index-add-language-analyzers.md#language-analyzer-list)或[特製化語言無關分析器](index-add-custom-analyzers.md#AnalyzerTable) (以修改文字分析)。 其中一個範例是[關鍵字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)，其會將欄位的整個內容視為單一語彙基元。 這適合用於郵遞區號、識別碼和產品名稱等資料。 | 
| 經過篩選的搜尋 | [OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)和任一剖析器 | 篩選查詢會對索引中的所有「可篩選」** 欄位評估布林運算式。 不同於搜尋，篩選查詢會比對欄位的確切內容，包括字串欄位的大小寫。 另一個差異是篩選查詢會以 OData 語法來表示。 <br/>[篩選條件運算式範例](search-query-simple-examples.md#example-3-filter-queries) |
| 地區搜尋 | 欄位上的 [Edm.GeographyPoint 類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)、篩選條件運算式和任一剖析器 | 座標若儲存在具有 Edm.GeographyPoint 的欄位中，可用於 "find near me" 或以地圖為基礎的搜尋控制項。 <br/>[地理搜尋範例](search-query-simple-examples.md#example-5-geo-search)|
| 範圍搜尋 | 篩選條件運算式和簡單的剖析器 | 在 Azure 認知搜索中，使用篩選器參數生成範圍查詢。 <br/>[範圍篩選範例](search-query-simple-examples.md#example-4-range-filters) | 
| [欄位搜索](query-lucene-syntax.md#bkmk_fields) | 搜尋參數和完整剖析器 | 建置以單一欄位為目標的複合查詢運算式。 <br/>[欄位搜索示例](search-query-lucene-examples.md#example-2-fielded-search) |
| [模糊搜索](query-lucene-syntax.md#bkmk_fuzzy) | 搜尋參數和完整剖析器 | 比對具有類似建構或拼法的字詞。 <br/>[模糊搜尋範例](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [鄰近搜尋](query-lucene-syntax.md#bkmk_proximity) | 搜尋參數和完整剖析器 | 尋找文件中彼此相近的字詞。 <br/>[鄰近搜尋範例](search-query-lucene-examples.md#example-4-proximity-search) |
| [術語提升](query-lucene-syntax.md#bkmk_termboost) | 搜尋參數和完整剖析器 | 如果文件包含已提升的字詞 (相較於其他未提升的字詞)，則將文件的順位提高。 <br/>[字詞提升範例](search-query-lucene-examples.md#example-5-term-boosting) |
| [正則運算式搜索](query-lucene-syntax.md#bkmk_regex) | 搜尋參數和完整剖析器 | 根據規則運算式的內容來比對。 <br/>[規則運算式範例](search-query-lucene-examples.md#example-6-regex) |
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

+ 更改**`searchMode=any`**（預設）以**`searchMode=all`** 要求匹配所有條件（而不是任何條件）。 此做法在查詢中包含布林運算子時特別有用。

+ 如果文字或語彙分析是必要的，但查詢類型無法進行語言處理，請變更查詢技術。 在全文檢索搜尋中，文本或詞法分析自動校正拼寫錯誤、單數複數單詞形式，甚至不規則動詞或名詞。 在像是模糊或萬用字元搜尋等某些查詢中，文字分析並不是查詢剖析管線的一部分。 在某些情況下，規則運算式是既有的因應措施。 

### <a name="paging-results"></a>分頁結果
Azure 認知搜索使實現搜尋結果的分頁變得容易。 通過使用**`top`** 和**`skip`** 參數，您可以順利發出搜索請求，以便以可管理的有序子集接收搜尋結果總數，從而輕鬆啟用良好的搜索 UI 實踐。 在收到這些分拆成較小子集的結果時，您也會收到整組搜尋結果中所含文件的計數。

您可以在"[如何在 Azure 認知搜索"中分頁](search-pagination-page-layout.md)搜尋結果一文中瞭解有關搜索搜尋結果的更多資訊。

### <a name="ordering-results"></a>排序結果
接收搜索查詢的結果時，可以請求 Azure 認知搜索為特定欄位中的值排序的結果提供服務。 預設情況下，Azure 認知搜索根據從[TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)派生的每個文檔的搜索分數的排名對搜尋結果進行排序。

如果希望 Azure 認知搜索返回按搜索分數以外的值排序的結果，則可以使用**`orderby`** 搜索參數。 您可以指定**`orderby`** 參數的值，以包括欄位名稱和對地理空間值[**`geo.distance()`函數**](query-odata-filter-orderby-syntax.md)的調用。 每個運算式可以跟後`asc`，以指示按昇冪請求結果，並**`desc`** 指示按降冪請求結果。 預設排名為遞增順序。


### <a name="hit-highlighting"></a>搜尋結果醒目提示
在 Azure 認知搜索中，通過使用**`highlight`** 和**`highlightPreTag`****`highlightPostTag`** 參數，可以輕鬆強調與搜索查詢匹配的搜尋結果的確切部分。 您可以指定哪些*可搜索*欄位應強調其匹配文本，以及指定確切的字串標記以追加到 Azure 認知搜索返回的匹配文本的開頭和結尾。

## <a name="see-also"></a>另請參閱

+ [全文檢索搜尋在 Azure 認知搜索中的工作原理（查詢解析體系結構）](search-lucene-query-architecture.md)
+ [搜索資源管理器](search-explorer.md)
+ [如何在 .NET 中進行查詢](search-query-dotnet.md)
+ [如何在 REST 中進行查詢](search-create-index-rest-api.md)
