---
title: 模糊搜尋
titleSuffix: Azure Cognitive Search
description: 實現「您的意思是」搜尋體驗,以自動更正拼寫錯誤的術語或拼寫錯誤。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262429"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>模糊搜尋以更正拼字錯誤與拼字錯誤

Azure 認知搜尋支援模糊搜索,這是一種查詢類型,用於補償輸入字串中的拼寫錯誤和拼寫錯誤的術語。 它通過掃描具有類似組合的術語來進行此用。 如果差異只是幾個錯位的字元,則擴展搜索以覆蓋近匹配具有自動更正拼寫錯誤的效果。 

## <a name="what-is-fuzzy-search"></a>什麼是模糊搜索?

這是一個擴展練習,在具有類似組成的術語上產生匹配。 指定模糊搜尋時,引擎會為查詢中的所有術語生成一個圖形(基於[確定性有限自動機理論](https://en.wikipedia.org/wiki/Deterministic_finite_automaton))。 例如,如果查詢包含三個術語"Washington 大學",則為`search=university~ of~ washington~`查詢 中的每個術語創建一個圖形(模糊搜索中沒有止步字刪除,因此"of"獲取一個圖形)。

該圖由每個術語的最多 50 個擴展或排列組成,捕獲流程中的正確和不正確的變體。 然後,引擎返回回應中最相關的匹配項。 

對於像"大學"這樣的術語,圖表可能有"不合性、大學性、大學性、宇宙性、反數"。 與圖形中文件匹配的任何文檔都包含在結果中。 與其他分析文本以處理相同單詞的不同形式("mouse"和"Mouse)的查詢不同"不同"的查詢不同,模糊查詢中的比較以面值進行,而不對文本進行任何語言分析。 "宇宙"和"反"在語義上是不同的,將匹配,因為句法差異很小。

如果差異限制為兩個或更少的編輯,其中編輯是插入、刪除、替換或轉換的字元,則匹配將成功。 指定差分的字串校正演演演算法是[Damerau-Levenshtein 距離](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)指標,描述為"將單字更改為另一個單詞所需的最小操作數(插入、刪除、替換或轉位)。 

在 Azure 認知搜尋中:

+ 模糊查詢適用於整個術語,但您可以通過 AND 建構支援短語。 例如,「Wshington」的「不溫不雅」與「華盛頓大學」相媲美。

+ 編輯的預設距離為 2。 值`~0`表示沒有擴展(僅表示確切術語被視為匹配項),但您可以`~1`指定一個差值或一個編輯。 

+ 模糊查詢可以擴展一個術語,最多只能擴展 50 個附加排列。 此限制不可配置,但您可以通過將編輯距離減小到 1 來有效地減少擴展數。

+ 答覆包括包含相關匹配的檔(最多 50 份)。

總體而言,圖形將作為匹配條件與索引中的令牌提交。 可以想像,模糊搜索本質上比其他查詢表單慢。 索引的大小和複雜性可以確定好處是否足以抵消回應的延遲。

> [!NOTE]
> 由於模糊搜索往往很慢,因此可能需要研究替代項,如 n-gram 索引,其短字元序列(bigram 和三字標記的兩個和三個字元序列)的進展。 根據您的語言和查詢面,n-gram 可能會為您提供更好的性能。 權衡的是,n-gram 索引非常存儲密集型,並生成更大的索引。
>
> 另一個替代方案,如果只想處理最惡劣的情況,你可以考慮另一個替代方法,那就是同[義詞映射](search-synonyms.md)。 例如,將「搜索」映射到「serach、serch、sarch」或「檢索」到「回」。。

## <a name="indexing-for-fuzzy-search"></a>模糊搜尋的索引

分析器在查詢處理期間不用於創建擴展圖,但這並不意味著在模糊搜索方案中應忽略分析器。 畢竟,在索引期間,分析器用於創建完成匹配的權杖,查詢是自由形式、篩選搜尋還是以圖形作為輸入的模糊搜索。 

通常,當按欄位分配分析器時,微調分析鏈的決定基於主要用例(篩選器或全文搜索),而不是模糊搜索等專用查詢表單。 因此,沒有針對模糊搜索的特定分析器建議。 

但是,如果測試查詢未生成預期的匹配項,則可以嘗試更改索引分析器,將其設置為[語言分析器](index-add-language-analyzers.md),以查看是否獲得更好的結果。 某些語言,尤其是元音突變的語言,可以從 Microsoft 自然語言處理器生成的拐點和不規則單詞形式中受益。 在某些情況下,使用正確的語言分析器可以改變術語是否以與使用者提供的值相容的方式標記化。

## <a name="how-to-use-fuzzy-search"></a>如何使用模糊搜尋

模糊查詢使用完整的 Lucene 查詢文法建構,呼叫[Lucene 查詢解析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

1. 在查詢()`queryType=full`上設置完整的 Lucene 解析器。

1. 或者,使用此參數 ()`searchFields=<field1,field2>`將請求範圍限定為特定欄位。 

1. 在整個學期結束時附加波浪線`~`( ) 運算`search=<string>~`子 ( ) 。

   包括一個可選參數,如果要指定編輯距離 (),`~1`則數位介於 0 和 2 之間(預設值)。 例如，"blue~" 或 "blue~1" 會傳回 "blue"、"blues" 和 "glue"。

在 Azure 認知搜索中,除了術語和距離(最多 2),在查詢上沒有要設置的其他參數。

> [!NOTE]
> 在查詢處理過程中,模糊查詢不經過[詞法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 查詢輸入直接添加到查詢樹中並展開以創建術語圖。 執行的唯一轉換是較低的套管。

## <a name="testing-fuzzy-search"></a>測試模糊搜尋

對於簡單的測試,我們建議[搜索資源管理器](search-explorer.md)或[Postman](search-get-started-postman.md)在查詢運算式上反覆運算。 這兩種工具都是互動式的,這意味著您可以快速單步執行術語的多個變體,並評估返回的回應。

當結果不明確時,[點擊突出顯示](search-pagination-page-layout.md#hit-highlighting)可以説明您在回應中識別匹配項。 

> [!Note]
> 使用命中突出顯示來識別模糊匹配有局限性,僅適用於基本模糊搜索。 如果索引具有評分配置檔,或者使用其他語法對查詢進行分層,則點擊突出顯示可能無法標識匹配項。 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>範例 1:具有確切術語的模糊搜尋

假設搜尋文件中的`"Description"`欄位中存在以下字串:`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

從「特殊」的模糊搜尋開始,並將點擊突出顯示添加到「描述」欄位:

    search=special~&highlight=Description

在回應中,由於添加了命中突出顯示,因此格式設置將應用於"特殊"作為匹配項。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

請重試請求,通過取出幾個字母(「pe」):拼寫錯誤「特殊」:

    search=scial~&highlight=Description

到目前為止,反應沒有變化。 使用預設的 2 度距離,從「特殊」中刪除兩個字元「pe」仍然允許該術語成功匹配。

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

嘗試一個請求,通過刪除最後一個字元進行總共三個刪除(從"特殊"到"scal")來進一步修改搜索詞:

    search=scal~&highlight=Description

請注意,返回相同的回應,但現在而不是在"特殊"上匹配,模糊匹配位於"SQL"。

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

此擴展示例的要點是說明命中突出顯示可能帶來的不明確結果的清晰度。 在所有情況下,將返回同一文檔。 如果您依賴文件 ID 來驗證匹配,則可能錯過了從"特殊"到"SQL"的轉變。

## <a name="see-also"></a>另請參閱

+ [全文搜索在 Azure 認知搜尋中的工作原理(查詢解析體系結構)](search-lucene-query-architecture.md)
+ [搜尋資源管理員](search-explorer.md)
+ [如何在 .NET 中進行查詢](search-query-dotnet.md)
+ [如何在 REST 中進行查詢](search-create-index-rest-api.md)
