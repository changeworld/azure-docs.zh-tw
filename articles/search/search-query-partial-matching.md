---
title: 部份字語、圖案與特殊字元
titleSuffix: Azure Cognitive Search
description: 使用通配符、正則表達式和首碼查詢在 Azure 認知搜尋查詢請求中匹配整個或部分術語。 可以使用完整的查詢語法和自定義分析器解決包含特殊字元的難以匹配的模式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: db60a864ff29ff9eccdcfbdc0bd63587375d4bbd
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114971"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>具有特殊字元的部分術語搜尋和模式(通配元、正規表示式、模式)

*部分術語搜索*是指由術語片段組成的查詢,其中您可能只有術語的開頭、中間或結尾(有時稱為首碼、內綴或後綴查詢),而不是整個術語片段。 *模式*可能是片段的組合,通常具有特殊字元,如作為查詢字串的一部分的斜杠或斜杠。 常見用例包括查詢電話號碼、URL、人員或產品代碼或複合詞的某些部分。

如果索引沒有預期格式的術語,則部分和模式搜索可能會有問題。 在索引的[詞法分析階段](search-lucene-query-architecture.md#stage-2-lexical-analysis)(假設默認標準分析器),將丟棄特殊字元,拆分複合字串和複合字串,刪除空白;當找不到匹配項時,所有這些都可能導致模式查詢失敗。 例如,電話號碼`+1 (425) 703-6214`(令牌化`"1"`為`"425"``"703"``"6214"`,、、)`"3-62"`不會顯示在查詢中,因為索引中實際上不存在該內容。 

解決方案是調用保留完整字串(如有必要)包括空格和特殊字元的分析器,以便可以匹配部分術語和模式。 為完整字串創建附加欄位,以及使用內容保留分析器,是解決方案的基礎。

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Azure 認知搜尋中的部分搜尋是什麼

在 Azure 認知搜尋中,部分搜尋和模式以以下形式提供:

+ [首碼搜尋](query-simple-syntax.md#prefix-search),`search=cap*`如 ,匹配"Cap'n Jack 的海濱旅館"或"Gacc 資本"。 您可以使用簡單的查詢語法或完整的 Lucene 查詢語法進行前綴搜索。

+ [連接式元件](query-lucene-syntax.md#bkmk_wildcard)或用於搜尋嵌入字串的模式或部份的[正規表示式](query-lucene-syntax.md#bkmk_regex)。 通配符和正則表達式需要完整的 Lucene 語法。 後綴和索引查詢被表述為正則表達式。

  部分術語搜索的一些示例包括以下內容。 對於後綴查詢,給定術語"字母數位",您將使用通配符搜索 ()`search=/.*numeric.*/`來尋找匹配項。 對於包含內部字元(如 URL 片段)的部分術語,可能需要添加轉義字元。 在 JSON`/`中, 向前斜杠用向`\`後斜杠 轉出。 因此,URL`search=/.*microsoft.com\/azure\/.*/`片段的語法「microsoft.com/azure/」。

如前所述,上述所有要求索引包含有利於模式匹配的格式的字串,而標準分析器未提供這種格式。 通過按照本文中的步驟,可以確保存在支持這些方案所需的內容。

## <a name="solving-partialpattern-search-problems"></a>解決部分/模式搜尋問題

當您需要搜尋片段、模式或特殊字元時,可以使用在更簡單的標記規則下操作的自定義分析器覆蓋預設分析器,保留整個字串。 退後一步,該方法如下所示:

+ 定義一個字段以儲存字串的完整版本(假定您需要分析和非分析文字)
+ 選擇預定義的分析器或定義自訂分析器以輸出未分析的完整字串
+ 將自訂分析器分配給欄位
+ 組建及測試索引

> [!TIP]
> 評估分析器是一個反覆運算過程,需要頻繁的索引重建。 您可以使用 Postman、[用於建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)的 REST API、[刪除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[載入文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)和[搜尋文件](https://docs.microsoft.com/rest/api/searchservice/search-documents),使此步驟更加簡單。 對於 Load 文件,請求正文應包含要測試的小型代表性數據集(例如,包含電話號碼或產品代碼的欄位)。 使用同一 Postman 集合中的這些 API,您可以快速迴圈完成這些步驟。

## <a name="duplicate-fields-for-different-scenarios"></a>不同機制的重複欄位

分析器按欄位分配,這意味著您可以在索引中創建欄位以針對不同方案進行優化。 具體而言,您可以定義"功能代碼"和"功能代碼Regex",以支援在第一個搜索時的常規全文搜索,並在第二個搜索時支援高級模式匹配。

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>選擇分析器

選擇產生整期權杖的分析器時,以下分析器是常見的選擇:

| 分析器 | 行為 |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整個欄位的內容被標記為單個術語。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 僅在空白上分隔。 包含破折號或其他字元的術語被視為單個令牌。 |
| [自訂分析器](index-add-custom-analyzers.md) | (推薦)通過創建自定義分析器,可以同時指定標記器和令牌篩選器。 以前的分析儀必須原樣使用。 自訂分析器允許您選擇要使用的標記器和權杖篩選器。 <br><br>建議的組合是具有[小寫標記篩選器的](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)[關鍵字標記器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)。 就其本身而言,預定義的[關鍵字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)不會小寫任何大寫文本,這可能導致查詢失敗。 自訂分析器為您提供了添加小寫權杖篩選器的機制。 |

如果使用像 Postman 這樣的 Web API 測試工具,則可以新增[測試分析器 REST 呼叫](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)以檢查權杖化輸出。

您必須具有要處理的現有索引。 給定現有索引和包含破折號或部分術語的欄位,您可以嘗試針對特定術語的各種分析器以查看發出的權杖。  

1. 檢查標準分析器,瞭解默認情況下術語如何標記。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 評估回應以查看文本在索引中的標記方式。 請注意每個術語如何小寫和分解。

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. 變更使用`whitespace``keyword`或 分析器的請求:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 現在,回應由單個令牌(大寫)組成,其中保留破折號作為字串的一部分。 如果需要搜索模式或部分術語,查詢引擎現在具有查找匹配項的基礎。


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> 請注意,在生成查詢樹時,查詢解析器通常是搜索表達式中的小寫術語。 如果使用的分析器不小寫文本輸入,並且未獲得預期結果,這可能是原因。 解決方案是添加小寫權杖篩選器,如下所述「使用自訂分析器」部分。

## <a name="configure-an-analyzer"></a>設定分析器
 
無論您是在評估分析器還是推進特定配置,都需要在欄位定義上指定分析器,並且如果您不使用內建分析器,則可能需要配置分析器本身。 交換分析器時,通常需要重新生成索引(刪除、重新創建和重新載入)。 

### <a name="use-built-in-analyzers"></a>使用內建分析器

內置或預定義的分析器可以在欄位定義的`analyzer`屬性上按名稱指定,索引中不需要其他配置。 下面的範例展示如何在`whitespace`欄位上設定分析器。 有關可用內建分析器的詳細資訊,請參閱[預先定義分析器清單](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)。 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>使用自訂分析器

如果使用[自定義分析器](index-add-custom-analyzers.md),請使用使用者定義的權杖器、權杖篩選器和可能的設定設定的組合在索引中定義它。 接下來,在欄位定義上引用它,就像內置分析器一樣。

當目標是整個期限標記化時,建議使用由**關鍵字標記器**和**小寫權杖篩選器**組成的自定義分析器。

+ 關鍵字標記器為欄位的整個內容創建單個權杖。
+ 小寫權杖篩選器將大寫字母轉換為小寫文字。 查詢解析器通常小寫任何大寫文本輸入。 低封裝使用標記化術語使輸入均質化。

下面的範例展示提供關鍵字標記器和小寫權杖篩選器的自訂分析器。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2`系統知道權杖器`lowercase`和權杖篩選器,並使用它們的預設配置,因此無需首先定義它們即可按名稱引用它們。

## <a name="build-and-test"></a>建置和測試

使用支援方案的分析器和欄位定義定義索引後,載入具有代表性字串的文檔,以便可以測試部分字串查詢。 

前面的各節解釋了邏輯。 本節將介紹測試解決方案時應調用的每個 API。 如前所述,如果您使用互動式 Web 測試工具(如 Postman),則可以快速完成這些任務。

+ [刪除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)將刪除同名的現有索引,以便可以重新創建它。

+ [建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)在搜索服務上創建索引結構,包括分析器定義和具有分析器規範的欄位。

+ [載入文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)匯入具有與索引具有相同結構的文檔以及可搜尋的內容。 執行此步驟后,索引即可查詢或測試。

+ [測試分析器](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)是在[選擇分析器](#choose-an-analyzer)中引入的。 使用各種分析器測試索引中的某些字串,以瞭解術語如何標記化。

+ [搜索文件](https://docs.microsoft.com/rest/api/searchservice/search-documents)解釋了如何使用[簡單的語法](query-simple-syntax.md)或通配符和正則表達式[使用完整的 Lucene 語法](query-lucene-syntax.md)構造查詢請求。

  對於部分術語查詢(如查詢"3-6214"以在"+1 (425) 703-6214"上尋找匹配項,可以使用簡單的語法`search=3-6214&queryType=simple`: 。

  對於內綴和後綴查詢(如查詢"num"或"數位以尋找「字母數位」上的匹配項),請使用完整的 Lucene 語法和正則運算式:`search=/.*num.*/&queryType=full`

## <a name="tips-and-best-practices"></a>祕訣和最佳作法

### <a name="tune-query-performance"></a>微調查詢效能

如果實現建議配置,包括keyword_v2權杖器和小寫權杖篩選器,您可能會注意到查詢性能下降,因為索引中現有權杖處理了額外的權杖篩選器。 

下面的範例添加[EdgeNGramTokenFilter,](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)以使前綴匹配得更快。 其他權杖以 2-25 個字元組合生成,包括字元:不僅 MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQ、MSFT/SQL)。 可以想像,額外的標記化會導致更大的索引。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>使用不同的分析器進行索引和查詢處理

分析器在索引期間和查詢執行期間調用。 兩者使用相同的分析器是很常見的,但您可以為每個工作負載配置自定義分析器。 分析器覆蓋在`analyzers`節中的[索引定義](https://docs.microsoft.com/rest/api/searchservice/create-index)中指定,然後引用到特定欄位。 

當僅在索引期間需要自定義分析時,您可以將自定義分析器應用於僅索引,並繼續使用標準 Lucene 分析器(或其他分析器)進行查詢。

要指定特定於角色的分析,可以為每個屬性設置欄位的屬性,設置`indexAnalyzer``searchAnalyzer`而不是`analyzer`預設 屬性。

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>後續步驟

本文介紹了分析器如何同時導致查詢問題和解決查詢問題。 作為下一步,請仔細查看分析器對索引和查詢處理的影響。 特別是,請考慮使用分析文本 API 返回標記化輸出,以便您可以準確查看分析器為索引創建的內容。

+ [語言分析器](search-language-support.md)
+ [Azure 認知搜尋中文字處理分析器](search-analyzers.md)
+ [分析文字 API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [全文搜尋的工作原理(查詢體系結構)](search-lucene-query-architecture.md)