---
title: 查詢類型
titleSuffix: Azure Cognitive Search
description: 深入瞭解認知搜尋中支援的查詢類型，包括自由文字、篩選、自動完成和建議、地理搜尋、系統查詢和檔查閱。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 9ce0ab34aac1a3dda823c9270f4eacebfb99166f
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387661"
---
# <a name="querying-in-azure-cognitive-search"></a>在 Azure 認知搜尋中查詢

Azure 認知搜尋提供豐富的查詢語言，可支援各種不同的案例，從自由文字搜尋到高度指定的查詢模式。 本文將摘要說明您可以建立的查詢類型。

在認知搜尋中，查詢是一種反復存取作業的完整規格 **`search`** ，其中包含可通知查詢執行並塑造回應的參數。 參數和剖析器會決定查詢要求的類型。 下列查詢範例會使用 [ (REST API) 的搜尋檔 ](/rest/api/searchservice/search-documents)，以 [旅館示範索引](search-get-started-portal.md)為目標。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
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

在查詢執行期間使用的參數：

+ **`queryType`** 設定剖析器，也就是 [預設的簡單查詢](search-query-simple-examples.md) 剖析器， (全文檢索搜尋) 的最佳方式，或是用於高階運算式、鄰近搜尋、模糊和萬用字元搜尋等先進查詢結構的 [完整 Lucene 查詢](search-query-lucene-examples.md) 剖析器。

+ **`search`** 提供比對準則（通常是完整的詞彙或片語），不論是否有運算子都一樣。 *在索引架構中，* 任何屬性為可搜尋的欄位都是此參數的候選項。

+ **`searchFields`** 限制特定可搜尋欄位的查詢執行。

用來塑造回應的參數：

+ **`select`** 指定要在回應中傳回的欄位。 只有在索引中 *標記為可* 抓取的欄位可以在 select 語句中使用。

+ **`top`** 傳回指定的最相符檔數目。 在此範例中，只會傳回10個點擊。 您可以使用 [上移] 和 [略過] (未顯示) 來將結果分頁。

+ **`count`** 告訴您整個索引中有多少份檔整體相符，這可能比傳回的檔多。 

+ **`orderby`** 如果您想要依值（例如評等或位置）來排序結果，則會使用。 否則，預設值是使用相關性分數來排名結果。 欄位必須屬性化為可 *排序* ，才能成為此參數的候選項。

上述清單是代表性的，但不是完整的。 如需查詢要求的完整參數清單，請參閱 [REST API) 搜尋檔 (](/rest/api/searchservice/search-documents)。

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>查詢類型

有幾個值得注意的例外狀況，查詢要求會反復查看針對快速掃描結構化的反向索引，其中可在任意數目的搜尋檔中找到相符的任何欄位。 在認知搜尋中，尋找相符專案的主要方法是全文檢索搜尋或篩選，但您也可以執行其他知名的搜尋體驗，例如自動完成或地理位置搜尋。 本文的其餘部分將摘要說明認知搜尋中的查詢，並提供詳細資訊和範例的連結。

## <a name="full-text-search"></a>全文檢索搜尋

如果您的搜尋應用程式包含可收集詞彙輸入的搜尋方塊，則全文檢索搜尋可能是支援該體驗的查詢作業。 全文檢索搜尋可接受在索引的所有可搜尋欄位中，以參數傳遞的字詞或片語 **`search`** 。  查詢字串中的選擇性布林運算子可以指定包含或排除準則。 簡單剖析器和完整剖析器都支援全文檢索搜尋。

在認知搜尋中，全文檢索搜尋是以 Apache Lucene 查詢引擎為基礎。 全文檢索搜尋中的查詢字串會進行詞彙分析，讓掃描更有效率。 分析包括小寫的所有詞彙、移除 "" 這類的停用字組，以及將詞彙縮減為基本的根表單。 預設分析器是標準 Lucene。

找到相符的字詞時，查詢引擎會重組包含相符的搜尋檔、依相關性順序排列檔的順序，並在回應中預設) 傳回前 50 (。

如果您正在執行全文檢索搜尋，請瞭解您的內容如何標記化，以協助您進行任何查詢異常的偵錯工具。 以字元字串或特殊字元進行查詢，可能需要使用預設標準 Lucene 以外的分析器，以確定索引包含正確的標記。 您可以使用 [語言分析器](index-add-language-analyzers.md#language-analyzer-list) 或可修改詞法分析的 [特殊分析器](index-add-custom-analyzers.md#AnalyzerTable) 來覆寫預設值。 其中一個範例是將欄位的整個內容視為單一權杖的 [關鍵字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) 。 這適合用於郵遞區號、識別碼和產品名稱等資料。 如需詳細資訊，請參閱 [部分詞彙搜尋和具有特殊字元的模式](search-query-partial-matching.md)。

如果您預期有大量的布林運算子使用，這在包含大型文字區塊的索引中更有可能會 (內容欄位或詳細描述) ，請務必使用參數測試查詢， **`searchMode=Any|All`** 以評估該設定在布林值搜尋上的影響。

## <a name="autocomplete-and-suggested-queries"></a>自動完成和建議的查詢

[自動完成或建議的結果](search-autocomplete-tutorial.md) 是 **`search`** 根據部分字串輸入引發連續查詢要求的替代方案， (在搜尋即用類型的體驗中) 每個字元之後。 您可以 **`autocomplete`** 一起使用和 **`suggestions`** 參數，如 [本教學](tutorial-csharp-type-ahead-and-suggestions.md)課程中所述，但無法搭配使用 **`search`** 。 已完成的詞彙和建議的查詢都是衍生自索引內容。 引擎永遠不會傳回索引中不存在的字串或建議。 如需詳細資訊，請參閱 [自動完成 (REST API) ](/rest/api/searchservice/autocomplete) 和 [建議 (REST API) ](/rest/api/searchservice/suggestions)。

## <a name="filter-search"></a>篩選搜尋

篩選器廣泛用於包含認知搜尋的應用程式。 在應用程式頁面上，篩選通常會視覺化為連結導覽結構中的 facet，以進行使用者導向篩選。 篩選器也會在內部用來公開已編制索引之內容的磁區。 例如，如果索引包含英文和法文的欄位，您就可以篩選語言。 

您可能還需要篩選器來叫用特定的查詢表單，如下表所述。 您可以使用篩選器搭配未指定的搜尋 (**`search=*`**) ，或使用包含詞彙、片語、運算子和模式的查詢字串。

| 篩選準則案例 | 描述 |
|-----------------|-------------|
| 範圍篩選 | 在 Azure 認知搜尋中，系統會使用篩選參數來建立範圍查詢。 如需詳細資訊和範例，請參閱 [範圍篩選範例](search-query-simple-examples.md#example-4-range-filters)。 |
| 地理位置搜尋 | 如果可搜尋的欄位屬於 [GeographyPoint 型](/rest/api/searchservice/supported-data-types)別，您可以為「尋找近端分享」或以地圖為基礎的搜尋控制項建立篩選運算式。 推動地理搜尋的欄位包含座標。 如需詳細資訊和範例，請參閱 [地理搜尋範例](search-query-simple-examples.md#example-5-geo-search)。 |
| 多面向導覽 | 當您叫用篩選以回應 facet 上的事件時，facet 導覽結構會成為使用者導向導覽的功能 `onclick` 。 如此一來，facet 和篩選就會手上。 如果您加入 facet 導覽，將需要篩選來完成體驗。 如需詳細資訊，請參閱 [如何建立 facet 篩選](search-filters-facets.md)。 |

> [!NOTE]
> 在查詢處理期間，不會分析篩選運算式中使用的文字。 文字輸入會假設為逐字區分大小寫的字元模式，此模式會在相符時成功或失敗。 篩選條件運算式是使用 [OData 語法](query-odata-filter-orderby-syntax.md) 來建立，並在 **`filter`** 索引的所有可 *篩選* 欄位中傳入參數。 如需詳細資訊，請參閱 [Azure 認知搜尋中的篩選](search-filters.md)。

## <a name="document-look-up"></a>檔查閱

與先前所述的查詢表單相較之下，這個 [方法會依識別碼抓取單一搜尋檔](/rest/api/searchservice/lookup-document)，沒有對應的索引搜尋或掃描。 只會要求並傳回一份檔。 當使用者在搜尋結果中選取專案時，使用欄位來抓取檔和填入詳細資料頁面是一般回應，而檔查閱是支援它的作業。

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Advanced search：模糊、萬用字元、相近、RegEx

先進的查詢表單取決於觸發特定查詢行為的完整 Lucene 剖析器和運算子。

| 查詢類型 | 使用方式 | 範例和詳細資訊 |
|------------|--------|------------------------------|
| [回復搜尋](query-lucene-syntax.md#bkmk_fields) | **`search`**  參數 **`queryType=full`**  | 建置以單一欄位為目標的複合查詢運算式。 <br/>[回復搜尋範例](search-query-lucene-examples.md#example-2-fielded-search) |
| [模糊搜尋](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** 參數 **`queryType=full`** | 比對具有類似建構或拼法的字詞。 <br/>[模糊搜尋範例](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [鄰近搜尋](query-lucene-syntax.md#bkmk_proximity) | **`search`** 參數 **`queryType=full`** | 尋找文件中彼此相近的字詞。 <br/>[鄰近搜尋範例](search-query-lucene-examples.md#example-4-proximity-search) |
| [詞彙提升](query-lucene-syntax.md#bkmk_termboost) | **`search`** 參數 **`queryType=full`** | 如果文件包含已提升的字詞 (相較於其他未提升的字詞)，則將文件的順位提高。 <br/>[字詞提升範例](search-query-lucene-examples.md#example-5-term-boosting) |
| [正則運算式搜尋](query-lucene-syntax.md#bkmk_regex) | **`search`** 參數 **`queryType=full`** | 根據規則運算式的內容來比對。 <br/>[規則運算式範例](search-query-lucene-examples.md#example-6-regex) |
|  [萬用字元或首碼搜尋](query-lucene-syntax.md#bkmk_wildcard) | **`search`** 參數搭配 * *_`~`_* 或 **`?`** 、 **`queryType=full`**| 根據首碼和波狀符號 (`~`) 或單一字元 (`?`) 來比對。 <br/>[萬用字元搜尋範例](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>後續步驟

若要深入瞭解查詢的執行，請參閱每個語法的範例。 如果您不熟悉全文檢索搜尋，請仔細查看查詢引擎的功能，這可能是個不錯的選擇。

+ [簡單查詢範例](search-query-simple-examples.md)
+ [建置進階查詢的 Lucene 語法查詢範例](search-query-lucene-examples.md)
+ [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)