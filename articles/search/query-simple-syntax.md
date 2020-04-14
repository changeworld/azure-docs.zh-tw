---
title: 簡單查詢語法
titleSuffix: Azure Cognitive Search
description: Azure 認知搜索中用於全文搜索查詢的簡單查詢語法的參考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258859"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋的簡單查詢語法

Azure 認知搜尋實現兩種基於 Lucene 的查詢語言:[簡單查詢解析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)與[Lucene 查詢解析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。 

在 Azure 認知搜索中,簡單的查詢語法排除了模糊搜索操作。 相反,使用完整的 Lucene 語法進行[模糊搜尋](search-query-fuzzy.md)。

> [!NOTE]
> 簡單的查詢語法用於在[搜尋文檔](https://docs.microsoft.com/rest/api/searchservice/search-documents)API**的搜尋**參數中傳遞的查詢運算式,不要與用於該 API [$filter](search-filters.md)參數的[OData 語法](query-odata-filter-orderby-syntax.md)混淆。 這些不同的語法具有用於建構查詢、轉義字串等自己的規則。
>
> Azure 認知搜尋為**搜尋**參數中的更複雜的查詢提供備用[的完整 Lucene 查詢文法](query-lucene-syntax.md)。 要瞭解有關查詢解析體系結構和每個語法的優點的更多資訊,請參閱[全文搜尋如何在 Azure 認知搜尋中工作](search-lucene-query-architecture.md)。

## <a name="invoke-simple-parsing"></a>呼叫簡單解析

簡單語法為預設語法。 從完整語法重設為簡單語法時，才需要引動過程。 若要明確地設定語法，請使用 `queryType` 搜尋參數。 有效值包括`queryType=simple``queryType=full``simple`或 ,預設位置`full`,並調用完整的[Lucene 查詢解析器](query-lucene-syntax.md)以進行更進階的查詢。 

## <a name="syntax-fundamentals"></a>語法基礎

任何包含一或多個字詞的文字，均可視為執行查詢的有效起始點。 Azure 認知搜索將匹配包含任何或所有術語的文檔,包括在分析文本期間發現的任何變體。

這聽起來很簡單,Azure 認知搜索中的查詢執行有一個方面*可能會*產生意外的結果,隨著輸入字串中添加更多術語和運算符,搜尋結果會增加而不是減少。 此擴展是否實際發生取決於是否包含 NOT 運算符,以及**一個搜尋模式**參數設置,該參數設置確定如何根據 AND 或 OR 行為來解釋"不"。 如需詳細資訊，請參閱 [NOT 運算子](#not-operator)。

### <a name="precedence-operators-grouping"></a>優先權運算符(群組)

您可以使用括號，在加上括號的陳述式內加入運算子，以建立子查詢。 例如，`motel+(wifi||luxury)` 會搜尋包含 "motel" 一詞和 "wifi" 或 "luxury" (或兩者) 的文件。

欄位分組也相類似，但分組的範圍會限定於單一欄位。 例如，`hotelAmenities:(gym+(wifi||pool))` 會在 "hotelAmenities" 欄位中搜尋 "gym" 和 "wifi"，或搜尋 "gym" 和 "pool"。  

### <a name="escaping-search-operators"></a>逸出搜尋運算子  

為了將任何搜索運算符用作搜索文本的一部分,請使用單個反斜杠 ()`\`的首碼來轉義該字元。 例如,對於在的`https://`通配符搜尋`://`,其中是查詢字串的一部分,可以`search=https\:\/\/*`指定 。 同樣,轉義電話號碼模式可能如下所示`\+1 \(800\) 642\-7676`。

需要轉義的特殊字元包括以下內容:`- * ? \ /`  

為了簡化一般案例下的作業，此規則有兩個不需使用逸出的例外情形：  

+ NOT 運算子 `-` 只有在作為空白字元後面的第一個字元時，才需要逸出；若位於字詞的中間，則不需逸出。 例如,以下 GUID 在沒有轉義字元的`3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`狀況: 。

+ 後置運算子 `*` 只有在作為空白字元前面的最後一個字元時，才需要逸出；若位於字詞的中間，則不需逸出。 例如,`4*4=16`不需要反斜杠。

> [!NOTE]  
> 儘管轉義會將令牌放在一起,但索引期間的[詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)可能會將它們剝離出來。例如,標準 Lucene 分析器將刪除和中斷連字元、空格和其他字元上的單詞。 如果查詢字串中需要特殊字元,則可能需要一個分析器來在索引中保留這些字元。 一些選擇包括微軟自然[語言分析器](index-add-language-analyzers.md),它保留連字元詞,或一個自定義分析器更複雜的模式。 有關詳細資訊,請參閱[部分術語、模式和特殊字元](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>為 URL 中的 Unsafe 字元和保留字元編碼

請確定所有 Unsafe 字元和保留字元在 URL 中都已編碼。 例如,"*"是一個不安全的字元,因為它是 URL 中的片段/錨點識別符。 此字元在 URL 中使用時必須編碼為 `%23`。 "&"和"*"是保留字元的範例,因為它們在Azure認知搜索中分隔參數並指定值。 有關詳細資訊,請參閱[RFC1738:統一資源定位器 (URL)。](https://www.ietf.org/rfc/rfc1738.txt)

Unsafe 字元包括 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字元包括 `; / ? : @ = + &`。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>查詢大小限制

 可以發送到 Azure 認知搜尋的查詢大小有限制。 具體來說，您最多可以有 1024 個子句 (以 AND、OR 等運算子分隔的運算式)。 此外，查詢中任何個別字詞的大小也有約 32 KB 的限制。 如果您的應用程式以程式設計方式產生搜尋查詢，建議您依照此方式設計，以避免產生無大小限制的查詢。  

## <a name="boolean-search"></a>布林搜尋

您可以將布林運算子 (AND、 OR、 NOT) 嵌入到查詢字串中,以產生一組豐富的條件,並基於這些條件可以找到匹配的文件。 

### <a name="and-operator-"></a>AND 運算子 `+`

AND 運算子是加號。 例如，`wifi+luxury` 會搜尋同時包含 `wifi` 和 `luxury` 的文件。

### <a name="or-operator-"></a>OR 運算子 `|`

OR 運算子是分隔號或直立線字元。 例如，`wifi | luxury` 會搜尋包含 `wifi` 或 `luxury` 的文件。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 運算子 `-`

NOT 運算子是負號。 例如,`wifi –luxury`將搜尋`wifi`具有術語和/或`luxury`沒有的文檔。

查詢請求上的**searchMode**參數控制帶有 NOT 運算符的術語是「已使用」的,還是帶有查詢中其他術語的`+`ORed(假設 其他`|`術語上沒有或運算符)。 有效值包括 `any` 或 `all`。

`searchMode=any`通過包含更多結果來增加查詢的撤回,默認情況下`-`將解釋為"OR NOT"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞的文件，或不含 `luxury` 一詞的文件。

`searchMode=all`通過包含較少的結果來提高查詢的精度,默認情況下, 將解釋為"不"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞且不含 "luxury" 一詞的文件。 就 `-` 運算子而言，這算是較直覺化的行為。 因此,您應該考慮`searchMode=all`使用`searchMode=any`而不是 ,如果您想要優化搜索的精度,而不是撤回,*並且*您的用戶經常在`-`搜索中使用 運算符號。

在決定**搜尋模式**設置時,請考慮各種應用程式中查詢的使用者交互模式。 搜索資訊的使用者更有可能在查詢中包括運算符,而不是具有更多內置導航結構的電子商務網站。

<a name="prefix-search"></a>

## <a name="prefix-search"></a>前置字串搜尋

後綴運算子是星號`*`。 例如,`lingui*`將查找"語言"或"語言",忽略案例。 

與篩選器類似,首碼查詢查找完全匹配。 因此,沒有相關性評分(所有結果都會獲得 1.0 的搜索分數)。 首碼查詢可能很慢,尤其是在索引較大且首碼由少量字元組成時。 

如果要執行後綴查詢(在字串的最後一部分匹配),請使用[通配符搜索](query-lucene-syntax.md#bkmk_wildcard)和完整的 Lucene 語法。

## <a name="phrase-search-"></a>短語搜尋`"`

術語搜索是一個或多個術語的查詢,其中任何術語都被視為匹配項。 短語搜索是包含在引號`" "`中的準確短語。 例如，`Roach Motel` (不含引號) 會搜尋在任一處包含 `Roach` 和/或 `Motel` (順序不拘) 的文件，而 `"Roach Motel"` (含引號) 則只會比對出依序包含這整個片語的文件 (文字分析仍適用)。

## <a name="see-also"></a>另請參閱  

+ [簡單搜尋查詢範例](search-query-simple-examples.md)
+ [完整 Lucene 搜尋查詢範例](search-query-lucene-examples.md)
+ [搜尋文件&#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Lucene 查詢語法](query-lucene-syntax.md)
+ [OData 運算式語法](query-odata-filter-orderby-syntax.md) 
