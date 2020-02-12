---
title: 簡單查詢語法
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋中用於全文檢索搜尋查詢的簡單查詢語法參考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: fc1eb1836badc3ced688750bbc7c7a164773d022
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152664"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的簡單查詢語法

Azure 認知搜尋會執行兩個以 Lucene 為基礎的查詢語言：[簡單查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)剖析器和[Lucene 查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)剖析器。 在 Azure 認知搜尋中，簡單查詢語法會排除模糊/slop 選項。  

> [!NOTE]
> 簡單查詢語法會用於在[搜尋檔](https://docs.microsoft.com/rest/api/searchservice/search-documents)API 的**搜尋**參數中傳遞的查詢運算式，而不會與用於該 api 之[$filter](search-filters.md)參數的[OData 語法](query-odata-filter-orderby-syntax.md)混淆。 這些不同的語法有自己的規則，可用於建立查詢、逸出字元串等等。
>
> Azure 認知搜尋會針對**搜尋**參數中更複雜的查詢，提供替代的[完整 Lucene 查詢語法](query-lucene-syntax.md)。 若要深入瞭解查詢剖析架構和每個語法的優點，請參閱[全文檢索搜尋在 Azure 認知搜尋中的運作方式](search-lucene-query-architecture.md)。

## <a name="how-to-invoke-simple-parsing"></a>如何叫用簡單剖析

簡單語法為預設語法。 從完整語法重設為簡單語法時，才需要引動過程。 若要明確地設定語法，請使用 `queryType` 搜尋參數。 有效值包括 `simple|full`，`simple` 為預設值，`full` 則用於 Lucene。 

## <a name="query-behavior-anomalies"></a>查詢行為異常

任何包含一或多個字詞的文字，均可視為執行查詢的有效起始點。 Azure 認知搜尋會比對包含任何或所有詞彙的檔，包括在文字分析期間找到的任何變化。 

簡單地說，在 Azure 認知搜尋中，查詢執行的其中一個層面*可能會*產生非預期的結果、增加，而不是在輸入字串中新增更多詞彙和運算子時減少搜尋結果。 是否進行此擴充實際上取決於是否加入了 NOT 運算子，以及決定如何對 NOT 解譯其 AND 或 OR 行為的 `searchMode` 參數設定。 採用預設值 `searchMode=Any` 和 NOT 運算子時，將會以 OR 動作計算作業，而使 `"New York" NOT Seattle` 傳回所有不是 Seattle 的城市。  

一般而言，在內容搜尋應用程式的使用者互動模式中較有可能出現這類行為，因為使用者常會在查詢中加入運算子，而不是像電子商務網站具有較多內建導覽結構。 如需詳細資訊，請參閱 [NOT 運算子](#not-operator)。 

## <a name="boolean-operators-and-or-not"></a>布林運算子（AND、OR、NOT） 

您可以在查詢字串中內嵌運算子，以建立一組豐富的準則，以尋找相符的檔。 

### <a name="and-operator-"></a>AND 運算子 `+`

AND 運算子是加號。 例如，`wifi+luxury` 會搜尋同時包含 `wifi` 和 `luxury` 的文件。

### <a name="or-operator-"></a>OR 運算子 `|`

OR 運算子是分隔號或直立線字元。 例如，`wifi | luxury` 會搜尋包含 `wifi` 或 `luxury` 的文件。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 運算子 `-`

NOT 運算子是負號。 例如，`wifi –luxury` 會搜尋含有 `wifi` 一詞且/或不含 `luxury` 的文件 (「且/或」由 `searchMode` 控制)。

> [!NOTE]  
>  `searchMode` 選項控制具有 NOT 運算子的字詞是否與查詢中沒有 `+` 或 `|` 運算子的其他字詞進行 AND 或 OR 運算。 請回想，`searchMode` 可設定為 `any` (預設值) 或 `all`。 如果您使用 `any`，會包括更多結果，且依預設 `-` 會被解譯為 "OR NOT"，所以會增加查詢的選出。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞的文件，或不含 `luxury` 一詞的文件。 如果您使用 `all`，會包括較少結果，且依預設 - 會被解譯為 "AND NOT"，所以會增加查詢的精確度。 例如，`wifi -luxury` 會比對出包含 `wifi` 一詞且不含 "luxury" 一詞的文件。 這可說是 `-` 運算子更直覺化的行為。 因此，如果您想要最佳化搜尋的精確度而不是召回率，`searchMode=all`且`searchMode=any`您的使用者在搜尋中經常使用 *運算子，您即應考慮使用* 而非 `-`。

## <a name="suffix-operator"></a>尾碼運算子

尾碼運算子是星號 `*`。 例如，`lux*` 會搜尋內有字詞以 `lux` 開頭 (忽略大小寫) 的文件。  

## <a name="phrase-search-operator"></a>片語搜尋運算子

片語運算子會將片語括在引號中 `" "`。 例如，`Roach Motel` (不含引號) 會搜尋在任一處包含 `Roach` 和/或 `Motel` (順序不拘) 的文件，而 `"Roach Motel"` (含引號) 則只會比對出依序包含這整個片語的文件 (文字分析仍適用)。

## <a name="precedence-operator"></a>優先順序運算子

優先順序運算子會將字串以括弧括住 `( )`。 例如，`motel+(wifi | luxury)` 會搜尋包含 motel 詞彙的檔，並 `wifi` 或 `luxury` （或兩者）。  

## <a name="escaping-search-operators"></a>逸出搜尋運算子  

 若要使用上述的符號做為實際的搜尋文字部分，則應在這些符號前加上反斜線予以逸出。 例如，`luxury\+hotel` 會產生 `luxury+hotel` 一詞。 為了讓更多典型案例的情況變簡單，此規則有兩個例外狀況不需要逸出：  

- NOT 運算子 `-` 只有在作為空白字元後面的第一個字元時，才需要逸出；若位於字詞的中間，則不需逸出。 例如，`wi-fi` 是單一字詞；而 GUID (例如 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) 會被視為單一語彙基元。
- 後置運算子 `*` 只有在作為空白字元前面的最後一個字元時，才需要逸出；若位於字詞的中間，則不需逸出。 例如，`wi*fi` 會被視為單一語彙基元。

> [!NOTE]  
>  雖然逸出可讓語彙基元結合為整體，但文字分析可能會根據分析模式加以分割。 如需詳細資訊，請參閱[語言支援&#40;Azure 認知搜尋 REST API&#41; ](index-add-language-analyzers.md) 。  

## <a name="see-also"></a>另請參閱  

+ [搜尋檔&#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene 查詢語法](query-lucene-syntax.md)
+ [OData 運算式語法](query-odata-filter-orderby-syntax.md) 
