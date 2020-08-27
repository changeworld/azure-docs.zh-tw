---
title: 模糊搜尋
titleSuffix: Azure Cognitive Search
description: 實行「您的意思是」搜尋體驗，以自動校正拼錯的字詞或打字錯誤。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: faa98f1c52cfe2dd0e19f085f4d33dedb6f01851
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934883"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>模糊搜尋以更正拼寫錯誤和打字錯誤

Azure 認知搜尋支援模糊搜尋，這是一種查詢類型，可針對輸入字串中的錯誤和拼錯詞彙進行補償。 它會藉由掃描具有類似組合的詞彙來執行這項工作。 展開搜尋以涵蓋接近比對的效果，在差異只是幾個錯置的字元時，會自動校正打字錯誤。 

## <a name="what-is-fuzzy-search"></a>什麼是模糊搜尋？

它是一種展開練習，會根據具有類似組合的字詞產生相符的結果。 當您指定模糊搜尋時，引擎會根據具 [決定性的有限自動化理論](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) ，針對查詢中的所有完整詞彙建立圖形 (。 例如，如果您的查詢包含三個「華盛頓特區」的詞彙，則會為查詢中的每個詞彙建立一個圖形 `search=university~ of~ washington~` (模糊搜尋中不會有任何停用字組移除，所以「of」會取得圖形) 。

此圖表包含每個詞彙的最多50個擴充或相片順序，可在進程中同時取得正確和不正確的變體。 然後，引擎會傳迴響應中最頂端的相關符合專案。 

針對「大學」之類的詞彙，圖形可能會有 "unversty，universty，大學，universe，反向"。 結果中會包含任何符合圖形中的檔。 相較于分析文字以處理相同字組不同表單 ( 「老鼠」和「滑鼠」 ) 的其他查詢，模糊查詢中的比較會以臉部值取得，而不會對文字進行任何語言分析。 「Universe」和「反向」（語義不同）會相符，因為語法差異很小。

如果不一致限制為兩個或更少的編輯，則比對成功，其中編輯是插入、刪除、取代或轉換的字元。 指定差異的字串更正演算法是 [Damerau Levenshtein 距離](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) 計量，描述為將一個單字變更為另一個字組所需的「作業數目下限」 (插入、刪除、替換或 transpositions 兩個相鄰字元) 。 

在 Azure 認知搜尋：

+ 模糊查詢適用于整個詞彙，但您可以透過和結構支援片語。 例如，"Unviersty ~ ~" Wshington ~ "會符合「華盛頓大學」。

+ 編輯的預設距離為2。 值為 `~0` 表示無展開 (只有確切的詞彙會視為符合) ，但您可以指定 `~1` 一種程度的差異或一個編輯。 

+ 模糊查詢最多可將詞彙擴充至50個額外的排列。 這項限制無法設定，但您可以將編輯距離減少為1，以有效地減少擴充的數目。

+ 回應是由包含相關符合 (最多 50) 的檔所組成。

一般而言，會將圖形提交為索引中的權杖的相符準則。 您可以想像，模糊搜尋本質上比其他查詢表單慢。 索引的大小和複雜度可以判斷這些優點是否足以抵消回應的延遲。

> [!NOTE]
> 由於模糊搜尋的速度很慢，因此可能有可能需要調查像是 n-語法索引的替代專案，以及其在 bigram 和三個字元序列之間的 (兩個字元序列，) 的三個字元序列。 根據您的語言和查詢介面，n 語法可能會提供更好的效能。 要權衡的是，n----------------------
>
> 另一個替代方案是，如果您只想要處理最缺陷還有嚴重的案例，則可以考慮 [同義字對應](search-synonyms.md)。 例如，將 "search" 對應至 "搜尋，serch，sarch" 或 "抓取" 以「取得」。

## <a name="indexing-for-fuzzy-search"></a>模糊搜尋的索引

在查詢處理期間，不會流量分析器來建立擴充圖形，但這並不表示在模糊搜尋案例中應忽略分析器。 畢竟，在編制索引期間，分析器會在建立權杖時用來建立比對的標記、查詢是否為自由格式、篩選的搜尋，或以圖形做為輸入的模糊搜尋。 

一般而言，針對每個欄位指派分析器時，微調分析鏈的決策是根據主要使用案例 (篩選或全文檢索搜尋) ，而不是像模糊搜尋這樣的特殊查詢形式。 基於這個理由，不會有針對模糊搜尋的特定分析器建議。 

但是，如果測試查詢未產生您預期的相符專案，您可以嘗試將索引分析器設定為 [語言分析器](index-add-language-analyzers.md)，以查看是否得到更好的結果。 某些語言（尤其是具有母音突變的語言）可受益于 Microsoft 自然語言處理器所產生的變化和不規則單字形式。 在某些情況下，使用正確的語言分析器可以與使用者所提供的值相容的方式來標記詞彙，以產生差異。

## <a name="how-to-use-fuzzy-search"></a>如何使用模糊搜尋

模糊查詢是使用完整的 Lucene 查詢語法來建立，並叫用 [lucene 查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)剖析器。

1. 在查詢 () 上設定完整的 Lucene 剖析器 `queryType=full` 。

1. （選擇性）使用此參數 () 將要求的範圍限定為特定欄位 `searchFields=<field1,field2>` 。 

1. `~`在整個詞彙的結尾加上波狀符號 () 運算子 (`search=<string>~`) 。

   如果您想要指定編輯距離 () ，請包含選擇性參數，介於0和2之間的數位 (預設) `~1` 。 例如，"blue~" 或 "blue~1" 會傳回 "blue"、"blues" 和 "glue"。

在 Azure 認知搜尋中，除了 (最大值為 2) 的之外，還不需要在查詢上設定其他參數。

> [!NOTE]
> 在查詢處理期間，模糊查詢不會進行 [詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 查詢輸入會直接新增至查詢樹狀結構，並展開以建立詞彙圖形。 唯一執行的轉換是較低的大小寫。

## <a name="testing-fuzzy-search"></a>測試模糊搜尋

針對簡單的測試，建議使用 [Search explorer](search-explorer.md) 或 [Postman](search-get-started-postman.md) 來逐一查看查詢運算式。 這兩種工具都是互動式的，這表示您可以快速地逐步執行多個詞彙的變化，並評估傳回的回應。

當結果不明確時，搜尋醒目 [提示可協助](search-pagination-page-layout.md#hit-highlighting) 您識別回應中的相符項。 

> [!Note]
> 使用搜尋醒目提示來識別模糊相符專案具有限制，且僅適用于基本的模糊搜尋。 如果您的索引有計分設定檔，或如果您以其他語法將查詢分層，則可能無法識別相符的搜尋結果。 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>範例1：使用確切的字詞進行模糊搜尋

假設搜尋檔中的欄位有下列字串 `"Description"` ： `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

從模糊搜尋「特殊」開始，然後將搜尋結果醒目提示新增至描述欄位：

```console
search=special~&highlight=Description
```

在回應中，因為您新增了搜尋結果醒目提示，所以會將格式設定套用至「特殊」作為比對字詞。

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

再試一次此要求，並將數個字母 ( "pe" ) ，以拼錯「特殊」：

```console
search=scial~&highlight=Description
```

到目前為止，回應沒有變更。 使用預設值2度距離，從「特殊」移除兩個字元「pe」仍允許成功符合該詞彙。

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

再試一次要求，從 "特殊" 到 "比例" ) 取出最後一個字元，以進一步修改搜尋字詞 (：

```console
search=scal~&highlight=Description
```

請注意，會傳回相同的回應，但現在不會比對「特殊」，而是會在「SQL」上進行模糊比對。

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

這個展開範例的重點是要說明點擊醒目提示可能會帶來不明確結果的清楚之處。 在所有情況下，都會傳回相同的檔。 您是否依賴檔識別碼來驗證相符的情況，但您可能遺漏了從「特殊」到「SQL」的轉換。

## <a name="see-also"></a>請參閱

+ [全文檢索搜尋在 Azure 認知搜尋 (查詢剖析架構中的運作方式) ](search-lucene-query-architecture.md)
+ [搜尋總管](search-explorer.md)
+ [如何在 .NET 中進行查詢](./search-get-started-dotnet.md)
+ [如何在 REST 中進行查詢](./search-get-started-powershell.md)