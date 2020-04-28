---
title: 簡單查詢語法
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋中用於全文檢索搜尋查詢的簡單查詢語法參考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: dfd75ad2c6ae246bfe6ee8b983744b3db07a841f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194936"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的簡單查詢語法

Azure 認知搜尋會執行兩個以 Lucene 為基礎的查詢語言：[簡單查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)剖析器和[Lucene 查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)剖析器。

簡單剖析器比較有彈性，而且即使未完全撰寫，也會嘗試解讀要求。 基於此彈性，這是 Azure 認知搜尋中查詢的預設值。 

簡單語法用於在`search` [搜尋檔要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)的參數中傳遞的查詢運算式，而不會與相同搜尋檔 API 的[$filter 運算式](search-filters.md)參數所使用的[OData 語法](query-odata-filter-orderby-syntax.md)混淆。 `search`和`$filter`參數具有不同的語法，以及自己用來建立查詢、逸出字元串等等的規則。

雖然簡單剖析器是以[Apache Lucene 簡單查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)剖析器類別為基礎，但 Azure 認知搜尋中的實作為不包括模糊搜尋。 如果您需要[模糊搜尋](search-query-fuzzy.md)或其他先進的查詢表單，請考慮改用替代的[完整 Lucene 查詢語法](query-lucene-syntax.md)。

## <a name="invoke-simple-parsing"></a>叫用簡單剖析

簡單語法為預設語法。 從完整語法重設為簡單語法時，才需要引動過程。 若要明確地設定語法，請使用 `queryType` 搜尋參數。 有效值`queryType=simple`包括或`queryType=full`，其中`simple`是預設值，而且`full`會叫用[完整的 Lucene 查詢](query-lucene-syntax.md)剖析器來進行更先進的查詢。 

## <a name="syntax-fundamentals"></a>語法基礎

任何包含一或多個字詞的文字，均可視為執行查詢的有效起始點。 Azure 認知搜尋會比對包含任何或所有詞彙的檔，包括在文字分析期間找到的任何變化。

簡單地說，在 Azure 認知搜尋中，查詢執行的其中一個層面*可能會*產生非預期的結果、增加，而不是在輸入字串中新增更多詞彙和運算子時減少搜尋結果。 這項擴充實際上是否發生，取決於是否包含 NOT 運算子，並結合**searchMode**參數設定來判斷如何根據和或或行為來解讀 not。 如需詳細資訊，請參閱 [NOT 運算子](#not-operator)。

### <a name="precedence-operators-grouping"></a>優先順序運算子（群組）

您可以使用括號，在加上括號的陳述式內加入運算子，以建立子查詢。 例如，`motel+(wifi|luxury)` 會搜尋包含 "motel" 一詞和 "wifi" 或 "luxury" (或兩者) 的文件。

欄位分組也相類似，但分組的範圍會限定於單一欄位。 例如，`hotelAmenities:(gym+(wifi|pool))` 會在 "hotelAmenities" 欄位中搜尋 "gym" 和 "wifi"，或搜尋 "gym" 和 "pool"。  

### <a name="escaping-search-operators"></a>逸出搜尋運算子  

在簡單的語法中，搜尋運算子包含下列字元：`+ | " ( ) ' \`  

如果其中任何一個字元是索引中的標記的一部分，請在查詢中的前面加上一個反斜線`\`（），以將其轉義。 例如，假設您使用自訂分析器來進行整個詞彙 token 化，且您的索引包含「大量 + 飯店」字串。 若要取得與此標記完全相符的內容，請插入一個 `search=luxury\+hotel`escape 字元：。 

為了簡化更常見的情況，這項規則有兩個例外，不需要進行的轉義：  

+ NOT 運算子`-`只有在是空白字元後面的第一個字元時，才需要進行轉義。 如果`-`出現在中間（例如，在中`3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`），您可以略過轉義。

+ 如果尾碼運算子`*`是空白字元前面的最後一個字元，則只需要進行轉義。 如果`*`出現在中間（例如，在中`4*4=16`），則不需要進行任何轉義。

> [!NOTE]  
> 根據預設，標準分析器會在[詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)期間刪除和中斷連字號、空白字元、符號和其他字元的單字。 如果您需要在查詢字串中保留特殊字元，您可能需要分析器，將它們保留在索引中。 有些選擇包括 Microsoft 自然[語言分析器](index-add-language-analyzers.md)，它會保留斷字元字組，或自訂分析器以進行更複雜的模式。 如需詳細資訊，請參閱[部分詞彙、模式和特殊字元](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>為 URL 中的 Unsafe 字元和保留字元編碼

請確定所有 Unsafe 字元和保留字元在 URL 中都已編碼。 例如，' # ' 是不安全的字元，因為它是 URL 中的片段/錨點識別碼。 此字元在 URL 中使用時必須編碼為 `%23`。 ' & ' 和 ' = ' 是保留字元的範例，因為它們會將參數分隔，並在 Azure 認知搜尋中指定值。 如需詳細資訊，請參閱[RFC1738：統一資源定位器（URL）](https://www.ietf.org/rfc/rfc1738.txt) 。

Unsafe 字元包括 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字元包括 `; / ? : @ = + &`。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>查詢大小限制

 您可以傳送至 Azure 認知搜尋的查詢大小有限制。 具體來說，您最多可以有 1024 個子句 (以 AND、OR 等運算子分隔的運算式)。 此外，查詢中任何個別字詞的大小也有約 32 KB 的限制。 如果您的應用程式以程式設計方式產生搜尋查詢，建議您依照此方式設計，以避免產生無大小限制的查詢。  

## <a name="boolean-search"></a>布林值搜尋

您可以在查詢字串中內嵌布林運算子（AND、OR、NOT）來建立一組豐富的準則，以符合找到的相符檔。 

### <a name="and-operator-"></a>AND 運算子 `+`

AND 運算子是加號。 例如，`wifi + luxury` 會搜尋同時包含 `wifi` 和 `luxury` 的文件。

### <a name="or-operator-"></a>OR 運算子 `|`

OR 運算子是分隔號或直立線字元。 例如，`wifi | luxury` 會搜尋包含 `wifi` 或 `luxury` 的文件。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 運算子 `-`

NOT 運算子是負號。 例如， `wifi –luxury`會搜尋具有和/或不具有`wifi` `luxury`該詞彙的檔。

查詢要求上的**searchMode**參數會控制是否要在查詢中使用 NOT 運算子來以 and 連結或 ORed 詞彙（假設其他詞彙沒有`+`或`|`運算子）。 有效值包括 `any` 或 `all`。

`searchMode=any`藉由包含更多結果來增加查詢的召回率， `-`且依預設會將其視為「或不」。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞的文件，或不含 `luxury` 一詞的文件。

`searchMode=all`藉由包含較少的結果來增加查詢的精確度，而且根據預設，會轉譯為 "AND NOT"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞且不含 "luxury" 一詞的文件。 就 `-` 運算子而言，這算是較直覺化的行為。 因此， `searchMode=any`如果您想要`searchMode=all`優化搜尋精確度而不是回收，而您的使用者經常在搜尋中使用`-` *運算子，您*應該考慮使用而不是。

在決定**searchMode**設定時，請考慮各種應用程式中查詢的使用者互動模式。 搜尋資訊的使用者較可能在查詢中包含運算子，而不是具有更多內建導覽結構的電子商務網站。

<a name="prefix-search"></a>

## <a name="prefix-search"></a>前置詞搜尋

尾碼運算子是星號`*`。 例如， `lingui*`會尋找 "語言" 或 "linguini"，忽略大小寫。 

與篩選類似，前置詞查詢會尋找完全相符的。 因此，沒有任何相關性計分（所有結果都會收到搜尋分數1.0）。 前置詞查詢可能會變慢，特別是當索引很大且前置詞包含少量的字元時。 

如果您想要執行尾碼查詢，比對字串的最後一個部分，請使用[萬用字元搜尋](query-lucene-syntax.md#bkmk_wildcard)和完整的 Lucene 語法。

## <a name="phrase-search-"></a>片語搜尋`"`

詞彙搜尋是一個或多個詞彙的查詢，其中任何字詞都會視為相符。 片語搜尋是以引號括住的確切片語`" "`。 例如，`Roach Motel` (不含引號) 會搜尋在任一處包含 `Roach` 和/或 `Motel` (順序不拘) 的文件，而 `"Roach Motel"` (含引號) 則只會比對出依序包含這整個片語的文件 (文字分析仍適用)。

## <a name="see-also"></a>另請參閱  

+ [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)
+ [簡單搜尋的查詢範例](search-query-simple-examples.md)
+ [完整 Lucene 搜尋的查詢範例](search-query-lucene-examples.md)
+ [搜尋檔 REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Lucene 查詢語法](query-lucene-syntax.md)
+ [OData 運算式語法](query-odata-filter-orderby-syntax.md) 
