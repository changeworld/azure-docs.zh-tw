---
title: 簡單查詢語法
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中用於全文檢索搜尋查詢的簡單查詢語法參考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923376"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的簡單查詢語法

Azure 認知搜尋會執行兩個以 Lucene 為基礎的查詢語言： [簡單的查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 剖析器和 [lucene 查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)剖析器。

簡單剖析器更有彈性，而且即使未完全組合，也會嘗試解讀要求。 由於這種彈性，Azure 認知搜尋中的查詢預設為預設值。 

簡單語法用於在 `search` [搜尋檔要求](/rest/api/searchservice/search-documents)的參數中傳遞的查詢運算式，而不是與用於相同搜尋檔 API 的[$Filter](search-filters.md) expression 參數的[OData 語法](query-odata-filter-orderby-syntax.md)混淆。 `search`和 `$filter` 參數具有不同的語法，以及用來建立查詢、逸出字元串等的規則。

雖然簡單剖析器是以 [Apache Lucene Simple Query](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 剖析器類別為基礎，但 Azure 認知搜尋中的執行不會排除模糊搜尋。 如果您需要 [模糊搜尋](search-query-fuzzy.md) 或其他先進的查詢表單，請考慮改用替代的 [完整 Lucene 查詢語法](query-lucene-syntax.md) 。

## <a name="invoke-simple-parsing"></a>叫用簡單剖析

簡單語法為預設語法。 從完整語法重設為簡單語法時，才需要引動過程。 若要明確地設定語法，請使用 `queryType` 搜尋參數。 有效值包括 `queryType=simple` 或 `queryType=full` ，其中 `simple` 是預設值，並叫用完整的 `full` [Lucene 查詢](query-lucene-syntax.md) 剖析器進行更高階的查詢。 

## <a name="syntax-fundamentals"></a>語法基礎

任何包含一或多個字詞的文字，均可視為執行查詢的有效起始點。 Azure 認知搜尋將符合包含任何或所有字詞的檔，包括在文字分析期間找到的任何變化。

就像這樣的音效一樣，Azure 認知搜尋中的查詢執行有一個層面 *可能會* 產生非預期的結果，因為在輸入字串中加入了更多詞彙和運算子，所以會增加而不是減少搜尋結果。 是否實際發生此擴充取決於是否包含 NOT 運算子，並結合了 **searchMode** 參數設定，以決定如何在和或行為中解讀。 如需詳細資訊，請參閱 [NOT 運算子](#not-operator)。

### <a name="precedence-operators-grouping"></a>優先順序運算子 (分組) 

您可以使用括號，在加上括號的陳述式內加入運算子，以建立子查詢。 例如，`motel+(wifi|luxury)` 會搜尋包含 "motel" 一詞和 "wifi" 或 "luxury" (或兩者) 的文件。

欄位分組也相類似，但分組的範圍會限定於單一欄位。 例如，`hotelAmenities:(gym+(wifi|pool))` 會在 "hotelAmenities" 欄位中搜尋 "gym" 和 "wifi"，或搜尋 "gym" 和 "pool"。  

### <a name="escaping-search-operators"></a>逸出搜尋運算子  

在簡單的語法中，搜尋運算子包含下列字元： `+ | " ( ) ' \`  

如果其中有任何字元是索引中的權杖的一部分，請在查詢中使用單一反斜線 () ，以將其換用 `\` 。 例如，假設您使用自訂分析器來進行整個詞彙的 token 化，而且您的索引包含「豪華 + 飯店」字串。 若要取得此標記的完全相符項，請插入一個 escape 字元：  `search=luxury\+hotel` 。 

為了簡化一般案例，此規則有兩個例外狀況，不需要進行轉義：  

+ NOT 運算子 `-` 只需要在空格之後的第一個字元進行換用。 如果 `-` 出現在中間 (例如，在 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) 中，您可以略過轉義。

+ 如果尾碼運算子 `*` 是空白字元之前的最後一個字元，則只需要進行換用。 如果 `*` 出現在中間 (例如，在 `4*4=16`) 中，則不需要進行任何轉義。

> [!NOTE]  
> 根據預設，「標準分析器」會在進行 [詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)時刪除和中斷連字號、空白字元、符號和其他字元的單字。 如果您需要在查詢字串中保留特殊字元，您可能需要分析器將它們保留在索引中。 某些選擇包括 Microsoft 自然 [語言分析器](index-add-language-analyzers.md)（可保留斷字元字組）或自訂分析器，以取得更複雜的模式。 如需詳細資訊，請參閱 [部分詞彙、模式和特殊字元](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>為 URL 中的 Unsafe 字元和保留字元編碼

請確定所有 Unsafe 字元和保留字元在 URL 中都已編碼。 例如，' # ' 是不安全的字元，因為它是 URL 中的片段/錨點識別碼。 此字元在 URL 中使用時必須編碼為 `%23`。 ' & ' 和 ' = ' 是保留字元的範例，因為它們會將參數分隔，並在 Azure 認知搜尋中指定值。 如需詳細資訊，請參閱 [RFC1738：統一資源定位器 (URL) ](https://www.ietf.org/rfc/rfc1738.txt) 。

Unsafe 字元包括 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字元包括 `; / ? : @ = + &`。

### <a name="querying-for-special-characters"></a>查詢特殊字元

在某些情況下，您可能會想要搜尋特殊字元，例如 ' ❤ ' 表情符號或 ' € ' 符號。 在這些情況下，請確定您使用的分析器不會將這些字元篩選掉。 標準分析器會忽略許多特殊字元，因此它們不會成為索引中的權杖。

因此，第一個步驟是確定您使用的分析器會考慮這些元素標記。 比方說，「空白字元」分析器會考慮以空白字元分隔的任何字元序列做為標記，因此 "❤" 字串會被視為標記。 此外，像是 Microsoft 英文分析器 ( "en-us" ) 的分析器會將 "€" 字串視為標記。 您可以 [測試分析器](/rest/api/searchservice/test-analyzer) ，以查看它為指定查詢產生的權杖。

使用 Unicode 字元時，請確定已在查詢 url 中正確地將符號轉義 (例如，「❤」會使用) 的 escape 順序 `%E2%9D%A4+` 。 Postman 會自動進行這種轉譯。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> 查詢大小限制

 您可以傳送給 Azure 認知搜尋的查詢大小有限制。 具體來說，您最多可以有 1024 個子句 (以 AND、OR 等運算子分隔的運算式)。 此外，查詢中任何個別字詞的大小也有約 32 KB 的限制。 如果您的應用程式以程式設計方式產生搜尋查詢，建議您依照此方式設計，以避免產生無大小限制的查詢。  

## <a name="boolean-search"></a>布林值搜尋

您可以在查詢字串中內嵌布林運算子 (和（或），而不是在查詢字串中) ，以建立一組豐富的準則來尋找相符的檔。 

### <a name="and-operator-"></a>AND 運算子 `+`

AND 運算子是加號。 例如，`wifi + luxury` 會搜尋同時包含 `wifi` 和 `luxury` 的文件。

### <a name="or-operator-"></a>OR 運算子 `|`

OR 運算子是分隔號或直立線字元。 例如，`wifi | luxury` 會搜尋包含 `wifi` 或 `luxury` 的文件。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 運算子 `-`

NOT 運算子是負號。 例如， `wifi –luxury` 會搜尋具有 `wifi` 和/或不具有該詞彙的檔 `luxury` 。

查詢要求的 **searchMode** 參數會控制使用 NOT 運算子的詞彙是否為以 and 連結或 or 運算，以及查詢中的其他詞彙 (假設 `+` `|`) 的其他詞彙沒有 or 運算子。 有效值包括 `any` 或 `all`。

`searchMode=any` 藉由包含更多結果來提高查詢的召回率，且依預設 `-` 會將其解釋為 "OR NOT"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞的文件，或不含 `luxury` 一詞的文件。

`searchMode=all` 藉由包含較少的結果來提高查詢的精確度，而且根據預設，將會被解釋為 "AND NOT"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞且不含 "luxury" 一詞的文件。 就 `-` 運算子而言，這算是較直覺化的行為。 因此， `searchMode=all` `searchMode=any` 如果您想要優化搜尋的精確度而不是召回率， *且* 您的使用者經常 `-` 在搜尋中使用運算子，您應該考慮使用而不是。

決定 **searchMode** 設定時，請考慮各種應用程式中查詢的使用者互動模式。 搜尋資訊的使用者比較有可能在查詢中包含運算子，而不是具有更多內建導覽結構的電子商務網站。

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>萬用字元前置詞符合 ( *，？ ) 

針對「開頭為」查詢，請將後置運算子新增為詞彙其餘部分的預留位置。 `*`針對多個字元或 `?` 單一字元使用星號。 例如， `lingui*` 將會比對 "語言" 或 "linguini"，並忽略大小寫。 

與篩選類似，前置詞查詢會尋找完全相符的。 因此，沒有任何相關性評分 (所有結果都會收到 1.0) 的搜尋分數。 請注意，前置詞查詢可能很慢，特別是當索引很大，且前置詞包含少量的字元時。 另一種方法（例如，邊緣 n g a n g）的執行速度可能更快。

針對其他萬用字元查詢變異數（例如，在字尾或中間的尾碼或中置比對），請使用 [萬用字元搜尋的完整 Lucene 語法](query-lucene-syntax.md#bkmk_wildcard)。

## <a name="phrase-search-"></a>片語搜尋 `"`

詞彙搜尋是一項或多項詞彙的查詢，其中任何字詞都會被視為相符。 片語搜尋是以引號括住的確切片語 `" "` 。 例如，雖然 `Roach Motel` (沒有引號的) 會 `Roach` 以任何順序搜尋包含及/或任何位置的檔 `Motel` ，但 `"Roach Motel"` 以引號) 括住的 (只會比對包含該整個片語的檔，而且以該順序表示 (詞法分析仍適用) 。

## <a name="see-also"></a>另請參閱  

+ [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)
+ [簡單搜尋的查詢範例](search-query-simple-examples.md)
+ [完整 Lucene 搜尋的查詢範例](search-query-lucene-examples.md)
+ [搜尋檔 REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene 查詢語法](query-lucene-syntax.md)
+ [OData 運算式語法](query-odata-filter-orderby-syntax.md)