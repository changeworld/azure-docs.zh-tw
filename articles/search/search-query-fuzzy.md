---
title: 模糊搜尋
titleSuffix: Azure Cognitive Search
description: 執行「您的意思是」搜尋體驗，以自動校正拼錯的詞彙或錯誤。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81262429"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>更正拼寫錯誤和錯誤的模糊搜尋

Azure 認知搜尋支援模糊搜尋，這種類型的查詢會補償輸入字串中的錯誤和拼錯詞彙。 它會藉由掃描具有類似組合的詞彙來完成這項工作。 展開搜尋以涵蓋近乎相符的效果，就是當差異只是幾個錯置的字元時，自動校正打字錯誤的影響。 

## <a name="what-is-fuzzy-search"></a>什麼是模糊搜尋？

這是一個展開練習，會產生與具有類似組合的字詞相符的結果。 當指定模糊搜尋時，引擎會針對查詢中的所有完整詞彙，針對類似的有限自動化理論建立一個圖形（根據[決定性的有限理論](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)）。 例如，如果您的查詢包含三個詞彙「華盛頓大學」，則會針對查詢中的每個詞彙建立圖表 `search=university~ of~ washington~` （模糊搜尋中不會有任何停用字組，因此 "of" 會取得圖形）。

此圖表包含每個詞彙最多50個擴充或相片順序，可在進程中同時捕捉正確和不正確的變數。 然後，引擎會傳迴響應中最重要的相關相符專案。 

對於「大學」之類的詞彙，圖形可能會有「unversty、universty、大學、universe、反向」。 結果中會包含任何符合圖形中的檔。 相較于分析文字來處理相同單字（「滑鼠」和「滑鼠」）不同形式的其他查詢，模糊查詢中的比較會以臉部值來進行，而不需要對文字進行任何語言分析。 「Universe」和「反向」（在語義上不同）會符合，因為語法差異很小。

如果不一致限制為兩個或更少的編輯，而其中的編輯是插入、刪除、取代或轉置的字元，則相符項就會成功。 指定差異的字串更正演算法是[damerau-levenshtein-Levenshtein 距離](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)計量，描述為將一個單字變更為另一個字組所需的最小作業數目（插入、刪除、替換或 transpositions 兩個相鄰字元）。 

在 Azure 認知搜尋中：

+ 模糊查詢適用于整個詞彙，但您可以透過和構造支援片語。 例如，"Unviersty ~ of" Wshington ~ "會符合「華盛頓大學」的結果。

+ 編輯的預設距離為2。 的值 `~0` 表示不展開（只會將確切的字詞視為相符），但是您可以指定 `~1` 一種差異程度，或進行一個編輯。 

+ 模糊查詢最多可以擴充到50個額外的排列。 這項限制是無法設定的，但您可以將編輯距離減少為1，藉此有效減少擴充的數目。

+ 回應是由包含相關相符項的檔（最多50）所組成。

這些圖形會共同提交，以符合索引中的權杖的比對準則。 您可以想像一下，模糊搜尋原本就比其他查詢表單慢。 索引的大小和複雜度，可以判斷其效益是否足以彌補回應的延遲。

> [!NOTE]
> 由於模糊搜尋的速度很慢，因此您可能可以使用較短的字元序列（bigram 和三個標記的兩個字元序列）的進展，來調查其他替代專案（例如 n 語法編制索引）。 根據您的語言和查詢介面，n 語法可能會提供更好的效能。 取捨的是，n 語法編制索引是非常密集的儲存體，而且會產生更大的索引。
>
> 另一個替代方法是，如果您只想要處理最缺陷還有嚴重的案例，則會是[同義字地圖](search-synonyms.md)。 例如，將「搜尋」對應至「搜尋、serch、sarch」或「抓取」以「取得」。

## <a name="indexing-for-fuzzy-search"></a>模糊搜尋的索引

在查詢處理期間，不會流量分析器來建立擴充圖形，但這並不表示在模糊搜尋案例中應該忽略分析器。 畢竟，在編制索引期間，會流量分析器來建立符合進行比對的權杖、查詢為自由形式、篩選搜尋，或是以圖表做為輸入的模糊搜尋。 

一般來說，在每個欄位上指派分析器時，微調分析鏈的決策是根據主要使用案例（篩選或全文檢索搜尋），而不是特定的查詢形式，例如模糊搜尋。 基於這個理由，模糊搜尋並沒有特定的分析器建議。 

不過，如果測試查詢不會產生您預期的相符專案，您可以嘗試將索引分析器設定為不同的[語言分析器](index-add-language-analyzers.md)，以查看是否得到更好的結果。 某些語言（特別是具有母音變化的語言）可受益于 Microsoft 自然語言處理器所產生的變化和不規則的文字形式。 在某些情況下，使用正確的語言分析器可能會使詞彙的標記方式與使用者所提供的值相容。

## <a name="how-to-use-fuzzy-search"></a>如何使用模糊搜尋

模糊查詢是使用完整的 Lucene 查詢語法來建立，叫用[lucene 查詢](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)剖析器。

1. 在查詢上設定完整的 Lucene 剖析器（ `queryType=full` ）。

1. （選擇性）使用此參數將要求的範圍限定于特定欄位（ `searchFields=<field1,field2>` ）。 

1. `~`在整個詞彙的結尾附加波狀符號（）運算子（ `search=<string>~` ）。

   如果您想要指定編輯距離（），請包含選擇性參數，也就是介於0和2（預設值）之間的數位 `~1` 。 例如，"blue~" 或 "blue~1" 會傳回 "blue"、"blues" 和 "glue"。

在 Azure 認知搜尋中，除了「詞彙」和「距離」（最大值為2）以外，不會在查詢上設定任何其他參數。

> [!NOTE]
> 在查詢處理期間，模糊查詢不會進行[詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 查詢輸入會直接新增至查詢樹狀結構，並展開以建立詞彙的圖表。 唯一執行的轉換是較低的大小寫。

## <a name="testing-fuzzy-search"></a>測試模糊搜尋

針對簡單的測試，建議您在查詢運算式中逐一查看[搜尋瀏覽器](search-explorer.md)或[Postman](search-get-started-postman.md) 。 這兩種工具都是互動式的，這表示您可以快速地逐步執行多個詞彙變體，並評估傳回的回應。

當結果不明確時，叫用反白[顯示](search-pagination-page-layout.md#hit-highlighting)可協助您識別回應中的相符項。 

> [!Note]
> 使用叫用反白顯示來識別模糊相符專案有一些限制，而且只適用于基本模糊搜尋。 如果您的索引有評分設定檔，或如果您使用額外的語法來將查詢分層，則叫用反白顯示可能無法識別相符項。 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>範例1：使用確切詞彙進行模糊搜尋

假設搜尋檔的欄位中有下列字串 `"Description"` ：`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

開始使用「特殊」的模糊搜尋，並在 [描述] 欄位中新增命中結果醒目提示：

    search=special~&highlight=Description

在回應中，因為您新增了命中結果醒目提示，所以格式會套用至「特殊」做為比對詞彙。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

再次嘗試此要求，並取出數個字母（"pe"），以拼錯「特殊」：

    search=scial~&highlight=Description

到目前為止，回應不會變更。 使用預設的2度距離，從「特殊」移除兩個字元 "pe" 仍允許成功符合該詞彙。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

再試一次，藉由取出一個最後一個字元（從「特殊」到「比例」），進一步修改搜尋詞彙：

    search=scal~&highlight=Description

請注意，會傳回相同的回應，但現在不會比對「特殊」，而是「SQL」的模糊比對。

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

此展開範例的重點是要說明出現反白顯示的清晰度，可能會帶到不明確的結果。 在所有情況下，都會傳回相同的檔。 如果您依賴檔識別碼來驗證相符的結果，您可能錯過了從「特殊」到「SQL」的轉換。

## <a name="see-also"></a>另請參閱

+ [全文檢索搜尋在 Azure 認知搜尋中的運作方式（查詢剖析架構）](search-lucene-query-architecture.md)
+ [搜尋總管](search-explorer.md)
+ [如何在 .NET 中進行查詢](search-query-dotnet.md)
+ [如何在 REST 中進行查詢](search-create-index-rest-api.md)
