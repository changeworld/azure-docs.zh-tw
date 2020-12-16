---
title: 簡單查詢語法
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中用於全文檢索搜尋查詢的簡單查詢語法參考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516586"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的簡單查詢語法

Azure 認知搜尋會執行兩個以 Lucene 為基礎的查詢語言： [簡單的查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 剖析器和 [lucene 查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)剖析器。 簡單剖析器更有彈性，而且即使未完全組合，也會嘗試解讀要求。 由於這種彈性，Azure 認知搜尋中的查詢預設為預設值。

簡單語法用於在搜尋檔的參數中傳遞的查詢運算式 **`search`** [ (REST API)](/rest/api/searchservice/search-documents) 要求，而不是與相同要求中用於和運算式的 [OData 語法](query-odata-filter-orderby-syntax.md) 混淆 [**`$filter`**](search-filters.md) [**`$orderby`**](search-query-odata-orderby.md) 。 OData 參數有不同的語法和規則，可用於建立查詢、逸出字元串等等。

雖然簡單剖析器是以 [Apache Lucene 簡單查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 剖析器類別為基礎，但認知搜尋中的實作為會排除模糊搜尋。 如果您需要 [模糊搜尋](search-query-fuzzy.md)，請改為考慮替代的 [完整 Lucene 查詢語法](query-lucene-syntax.md) 。

## <a name="example-simple-syntax"></a>範例 (簡單語法) 

雖然 **`queryType`** 是在下面設定，但它是預設值，除非您要從替代類型還原，否則可以省略。 下列範例是對獨立詞彙的搜尋，並要求所有相符的檔都必須包含「集區」。

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

**`searchMode`** 參數在此範例中是相關的。 當查詢上有布林運算子時，您通常應該設定， `searchMode=all` 以確保 *所有* 準則相符。 否則，您可以使用優先于精確度的預設值 `searchMode=any` 。

如需其他範例，請參閱 [簡單的查詢語法範例](search-query-simple-examples.md)。 如需查詢要求和參數的詳細資訊，請參閱 [REST API) 搜尋檔 (](/rest/api/searchservice/Search-Documents)。

## <a name="keyword-search-on-terms-and-phrases"></a>關鍵字搜尋詞彙和片語

傳遞給參數的字串 **`search`** 可以包含任何支援語言的詞彙或片語、布林運算子、優先順序運算子、"開頭為" 查詢、escape 字元和 URL 編碼字元的萬用字元或前置詞字元。 **`search`** 參數是選擇性的。 未指定、搜尋 (`search=*` 或 `search=" "`) 會以任意 (雜亂檔) 順序傳回前50份檔。

+ *詞彙搜尋* 是一項或多項詞彙的查詢，其中任何字詞都會被視為相符。

+ *片語搜尋* 是以引號括住的確切片語 `" "` 。 例如，雖然 ```Roach Motel``` (沒有引號的) 會 ```Roach``` 以任何順序搜尋包含及/或任何位置的檔 ```Motel``` ，但 ```"Roach Motel"``` 以引號) 括住的 (只會比對包含該整個片語的檔，而且以該順序表示 (詞法分析仍適用) 。 

  視您的搜尋用戶端而定，您可能需要在片語搜尋中將引號換用。 例如，在 POST 要求的 Postman 中，會 `"Roach Motel"` 將要求主體中的片語搜尋指定為 `"\"Roach Motel\""` 。

依預設，在參數中傳遞的所有字詞或片語都會進行「 **`search`** 詞法分析」。 請確定您瞭解您所使用之分析器的 token 化行為。 通常，當查詢結果非預期時，可能會追蹤原因，以說明如何在查詢時標記詞彙。

任何包含一或多個字詞的文字，均可視為執行查詢的有效起始點。 Azure 認知搜尋將符合包含任何或所有字詞的檔，包括在文字分析期間找到的任何變化。

就像這樣的音效一樣，Azure 認知搜尋中的查詢執行有一個層面 *可能會* 產生非預期的結果，因為在輸入字串中加入了更多詞彙和運算子，所以會增加而不是減少搜尋結果。 是否實際發生此擴充取決於是否包含 NOT 運算子， **`searchMode`** 並結合參數設定，以決定如何在和或行為中解讀。 如需詳細資訊，請參閱 [布林運算子](#boolean-operators)下的 NOT 運算子。

## <a name="boolean-operators"></a>布林運算子

您可以在查詢字串中內嵌布林運算子，以改善相符的精確度。 在簡單的語法中，布林運算子是以字元為基礎。 不支援文字運算子，例如單字和。

| 字元 | 範例 | 使用量 |
|----------- |--------|-------|
| `+` | `pool + ocean` | AND 運算。 例如， `pool + ocean` 規定檔必須同時包含這兩個詞彙。|
| `|` | `pool | ocean` | 找到任一詞彙時，或作業會尋找相符的。 在此範例中，查詢引擎會在包含 `pool` 或或兩者的檔上傳回相符的 `ocean` 。 OR 是預設的連接詞運算子，因此您也可以將其省略，而使 `pool ocean` 相當於 `pool | ocean`。|
| `-` | `pool – ocean` | NOT 作業會傳回包含字詞之檔的相符專案。 <br/><br/>若要取得 NOT 運算式的預期行為，請考慮在 **`searchMode=all`** 要求上進行設定。 否則，在預設的下 **`searchMode=any`** ，您將會取得相符專案 `pool` ，再加上不包含的所有檔的相符專案 `ocean` ，這可能是很多檔。 **`searchMode`** 查詢要求的參數會控制是否在查詢中使用 NOT 運算子的字詞以 and 連結或 or 運算， (假設 `+` `|`) 的其他詞彙沒有 or 運算子。 使用會 **`searchMode=all`** 包含較少的結果來提高查詢的精確度，而且根據預設，將會被解釋為 "AND NOT"。 <br/><br/>在決定設定時 **`searchMode`** ，請考慮各種應用程式中查詢的使用者互動模式。 搜尋資訊的使用者比較有可能在查詢中包含運算子，而不是具有更多內建導覽結構的電子商務網站。 |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>前置詞查詢

針對「開頭為」查詢，請將後置運算子 (`*`) 做為詞彙其餘部分的預留位置。 前置詞查詢必須以至少一個英數位元開頭，您才能加入尾碼運算子。

| 字元 | 範例 | 使用量 |
|----------- |--------|-------|
| `*` | `lingui*` 會比對 "語言" 或 "linguini" | 星號 (`*`) 代表任意長度的一或多個字元，忽略大小寫。  |

與篩選類似，前置詞查詢會尋找完全相符的。 因此，沒有任何相關性評分 (所有結果都會收到 1.0) 的搜尋分數。 請注意，前置詞查詢可能很慢，特別是當索引很大，且前置詞包含少量的字元時。 另一種方法（例如，邊緣 n g a n g）的執行速度可能更快。

簡單語法僅支援前置詞比對。 若為尾碼或中點比對，則使用 [萬用字元搜尋的完整 Lucene 語法](query-lucene-syntax.md#bkmk_wildcard)。

## <a name="escaping-search-operators"></a>逸出搜尋運算子  

在簡單的語法中，搜尋運算子包含下列字元： `+ | " ( ) ' \`  

如果其中有任何字元是索引中的權杖的一部分，請在查詢中使用單一反斜線 () ，以將其換用 `\` 。 例如，假設您使用自訂分析器來進行整個詞彙的 token 化，而且您的索引包含「豪華 + 飯店」字串。 若要取得此標記的完全相符項，請插入一個 escape 字元： `search=luxury\+hotel` 。

為了簡化一般案例，此規則有兩個例外狀況，不需要進行轉義：  

+ NOT 運算子 `-` 只需要在空格之後的第一個字元進行換用。 如果 `-` 出現在中間 (例如，在 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) 中，您可以略過轉義。

+ 如果尾碼運算子 `*` 是空白字元之前的最後一個字元，則只需要進行換用。 如果 `*` 出現在中間 (例如，在 `4*4=16`) 中，則不需要進行任何轉義。

> [!NOTE]  
> 根據預設，「標準分析器」會在進行 [詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)時刪除和中斷連字號、空白字元、符號和其他字元的單字。 如果您需要在查詢字串中保留特殊字元，您可能需要分析器將它們保留在索引中。 某些選擇包括 Microsoft 自然 [語言分析器](index-add-language-analyzers.md)（可保留斷字元字組）或自訂分析器，以取得更複雜的模式。 如需詳細資訊，請參閱 [部分詞彙、模式和特殊字元](search-query-partial-matching.md)。

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>為 URL 中的 Unsafe 字元和保留字元編碼

確定所有 unsafe 和 reserved 字元都是以 URL 編碼。 例如，' # ' 是不安全的字元，因為它是 URL 中的片段/錨點識別碼。 此字元在 URL 中使用時必須編碼為 `%23`。 ' & ' 和 ' = ' 是保留字元的範例，因為它們會將參數分隔，並在 Azure 認知搜尋中指定值。 如需詳細資訊，請參閱 [RFC1738：統一資源定位器 (URL) ](https://www.ietf.org/rfc/rfc1738.txt)。

Unsafe 字元包括 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字元包括 `; / ? : @ = + &`。

## <a name="special-characters"></a>特殊字元

在某些情況下，您可能會想要搜尋特殊字元，例如 ' ❤ ' 表情符號或 ' € ' 符號。 在這種情況下，請確定您使用的分析器不會將這些字元篩選掉。標準分析器會略過許多特殊字元，但不包括在您的索引中。

將 token 化特殊字元的分析器包括「空白字元」分析器，它會考慮任何字元序列（以空白字元分隔為標記） (因此「❤」字串會被視為權杖) 。 此外，類似 Microsoft 英文分析器 ( "en-us" ) 的語言分析器會採用 "€" 字串作為權杖。 您可以 [測試分析器](/rest/api/searchservice/test-analyzer) ，以查看它為指定查詢產生的權杖。

使用 Unicode 字元時，請確定已在查詢 url 中正確地將符號轉義 (例如，「❤」會使用) 的 escape 順序 `%E2%9D%A4+` 。 Postman 會自動進行這種轉譯。  

## <a name="precedence-grouping"></a>優先順序 (分組) 

您可以使用括號，在加上括號的陳述式內加入運算子，以建立子查詢。 例如，`motel+(wifi|luxury)` 會搜尋包含 "motel" 一詞和 "wifi" 或 "luxury" (或兩者) 的文件。

## <a name="query-size-limits"></a>查詢大小限制

如果您的應用程式以程式設計方式產生搜尋查詢，建議您依照此方式設計，以避免產生無大小限制的查詢。 

+ 若為 GET，URL 的長度不能超過 8 KB。

+ 對於 POST (以及任何其他要求) ，要求的本文包含 `search` 和其他參數（例如 `filter` 和 `orderby` ），大小上限為 16 MB，其中 (運算式中以 `search` and、) OR 等分隔的子句數目上限為1024。 此外，查詢中任何個別字詞的大小也有約 32 KB 的限制。 如需詳細資訊，請參閱 [API 要求限制](search-limits-quotas-capacity.md#api-request-limits)。

## <a name="next-steps"></a>後續步驟

如果您要以程式設計方式來建立查詢，請參閱 [Azure 認知搜尋中的全文檢索搜尋](search-lucene-query-architecture.md) ，以瞭解查詢處理的階段和文字分析的含意。

您也可以參閱下列文章，以深入瞭解查詢結構：

+ [簡單搜尋的查詢範例](search-query-simple-examples.md)
+ [完整 Lucene 搜尋的查詢範例](search-query-lucene-examples.md)
+ [搜尋檔 REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene 查詢語法](query-lucene-syntax.md)
+ [篩選和選取 (OData) 運算式語法](query-odata-filter-orderby-syntax.md)