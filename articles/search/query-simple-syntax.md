---
title: 簡單查詢語法
titleSuffix: Azure Cognitive Search
description: Azure 認知搜索中用於全文搜索查詢的簡單查詢語法的參考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/03/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 3d5a4ddf863115747c27efbca1808d51444aac8c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656157"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋的簡單查詢語法

Azure 認知搜尋實現兩種基於 Lucene 的查詢語言:[簡單查詢解析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)與[Lucene 查詢解析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。 在 Azure 認知搜索中,簡單的查詢語法排除了模糊/斜面選項。  

> [!NOTE]
> 簡單的查詢語法用於在[搜尋文檔](https://docs.microsoft.com/rest/api/searchservice/search-documents)API**的搜尋**參數中傳遞的查詢運算式,不要與用於該 API [$filter](search-filters.md)參數的[OData 語法](query-odata-filter-orderby-syntax.md)混淆。 這些不同的語法具有用於建構查詢、轉義字串等自己的規則。
>
> Azure 認知搜尋為**搜尋**參數中的更複雜的查詢提供備用[的完整 Lucene 查詢文法](query-lucene-syntax.md)。 要瞭解有關查詢解析體系結構和每個語法的優點的更多資訊,請參閱[全文搜尋如何在 Azure 認知搜尋中工作](search-lucene-query-architecture.md)。

## <a name="how-to-invoke-simple-parsing"></a>如何叫用簡單剖析

簡單語法為預設語法。 從完整語法重設為簡單語法時，才需要引動過程。 若要明確地設定語法，請使用 `queryType` 搜尋參數。 有效值包括 `simple|full`，`simple` 為預設值，`full` 則用於 Lucene。 

## <a name="query-behavior-anomalies"></a>查詢行為異常

任何包含一或多個字詞的文字，均可視為執行查詢的有效起始點。 Azure 認知搜索將匹配包含任何或所有術語的文檔,包括在分析文本期間發現的任何變體。 

這聽起來很簡單,Azure 認知搜索中的查詢執行有一個方面*可能會*產生意外的結果,隨著輸入字串中添加更多術語和運算符,搜尋結果會增加而不是減少。 是否進行此擴充實際上取決於是否加入了 NOT 運算子，以及決定如何對 NOT 解譯其 AND 或 OR 行為的 `searchMode` 參數設定。 採用預設值 `searchMode=Any` 和 NOT 運算子時，將會以 OR 動作計算作業，而使 `"New York" NOT Seattle` 傳回所有不是 Seattle 的城市。  

一般而言，在內容搜尋應用程式的使用者互動模式中較有可能出現這類行為，因為使用者常會在查詢中加入運算子，而不是像電子商務網站具有較多內建導覽結構。 如需詳細資訊，請參閱 [NOT 運算子](#not-operator)。 

## <a name="boolean-operators-and-or-not"></a>布林運算符(AND、OR、不) 

可以在查詢字串中嵌入運算元,以生成一組豐富的條件,從中找到匹配的文檔。 

### <a name="and-operator-"></a>AND 運算子 `+`

AND 運算子是加號。 例如，`wifi+luxury` 會搜尋同時包含 `wifi` 和 `luxury` 的文件。

### <a name="or-operator-"></a>OR 運算子 `|`

OR 運算子是分隔號或直立線字元。 例如，`wifi | luxury` 會搜尋包含 `wifi` 或 `luxury` 的文件。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 運算子 `-`

NOT 運算子是負號。 例如，`wifi –luxury` 會搜尋含有 `wifi` 一詞且/或不含 `luxury` 的文件 (「且/或」由 `searchMode` 控制)。

> [!NOTE]  
>  在沒有 `+` 或 `|` 運算子的情況下，`searchMode` 選項會控制使用 NOT 運算子的字詞與查詢中的其他字詞之間應使用 AND 還是 OR 來處理。 先前提過，`searchMode` 可設為 `any` (預設值) 或 `all`。 如果使用 `any`，則會包含較多結果而提高查詢的召回率，且依預設 `-` 會解譯為 "OR NOT"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞的文件，或不含 `luxury` 一詞的文件。 如果使用 `all`，則會包含較少結果而提高查詢的精確度，且依預設 - 會解譯為 "AND NOT"。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞且不含 "luxury" 一詞的文件。 就 `-` 運算子而言，這算是較直覺化的行為。 因此，如果您想要最佳化搜尋的精確度而不是召回率，*且*您的使用者在搜尋中經常使用 `-` 運算子，您即應考慮使用 `searchMode=all` 而非 `searchMode=any`。

<a name="prefix-search"></a>

## <a name="suffix--operator-for-prefix-search"></a>前置字`*`串的後置字元

後綴運算子是星號`*`。 例如，`cap*` 會搜尋內有字詞以 `cap` 開頭 (忽略大小寫) 的文件。 

與篩選器類似,首碼查詢查找完全匹配。 因此,沒有相關性評分(所有結果都會獲得 1.0 的搜索分數)。 首碼查詢可能很慢,尤其是在索引較大且首碼由少量字元組成時。 

如果要執行後綴查詢(在字串的最後一部分匹配),請使用[通配符搜索](query-lucene-syntax.md#bkmk_wildcard)和完整的 Lucene 語法。

## <a name="phrase-search-operator"></a>片語搜尋運算子 

短語運算符以引號`" "`括括短語。 例如，`Roach Motel` (不含引號) 會搜尋在任一處包含 `Roach` 和/或 `Motel` (順序不拘) 的文件，而 `"Roach Motel"` (含引號) 則只會比對出依序包含這整個片語的文件 (文字分析仍適用)。

## <a name="precedence-operator"></a>優先順序運算子 

優先權運算符號將字串包在括弧中`( )`。 例如,`motel+(wifi | luxury)`將搜索包含汽車旅館術語和`wifi``luxury`或 (或兩者)的文檔。  

## <a name="escaping-search-operators"></a>逸出搜尋運算子  

 若要使用上述符號作為實際搜尋文字的一部分，則應在這些符號前面加上反斜線加以逸出。 例如，`luxury\+hotel` 會產生 `luxury+hotel` 一詞。 為了簡化一般案例下的作業，此規則有兩個不需使用逸出的例外情形：  

- NOT 運算子 `-` 只有在作為空白字元後面的第一個字元時，才需要逸出；若位於字詞的中間，則不需逸出。 例如，`wi-fi` 是單一字詞；而 GUID (例如 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) 會被視為單一語彙基元。
- 後置運算子 `*` 只有在作為空白字元前面的最後一個字元時，才需要逸出；若位於字詞的中間，則不需逸出。 例如，`wi*fi` 會被視為單一語彙基元。

> [!NOTE]  
>  雖然逸出可讓語彙基元結合為整體，但文字分析可能會根據分析模式加以分割。 有關詳細資訊[,請參閱 &#40;Azure 認知搜尋 REST API&#41;的语言支持](index-add-language-analyzers.md)。  

## <a name="see-also"></a>另請參閱  

+ [搜尋文件&#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene 查詢語法](query-lucene-syntax.md)
+ [OData 運算式語法](query-odata-filter-orderby-syntax.md) 
