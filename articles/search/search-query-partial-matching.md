---
title: 匹配圖案和特殊字元
titleSuffix: Azure Cognitive Search
description: 使用萬用字元和首碼查詢在 Azure 認知搜索查詢請求中匹配整個或部分術語。 可以使用完整的查詢語法和自訂分析器解決包含特殊字元的難以匹配的模式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289306"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>匹配圖案和特殊字元（破折號）

對於包含特殊字元 （）`-, *, (, ), /, \, =`的查詢，或基於較大術語中的部分術語的查詢模式，通常需要執行其他配置步驟，以確保索引以正確的格式包含預期內容。 

預設情況下，類似`+1 (425) 703-6214``"1"`的電話號碼被標記為 ， `"425"`， `"703"` `"6214"`。 可以想像，在 包含破折`"3-62"`號的部分術語上搜索將失敗，因為索引中實際上不存在該內容。 

當您需要搜索部分字串或特殊字元時，可以使用自訂分析器覆蓋預設分析器，該分析器在更簡單的標記規則下運行，保留整個術語，當查詢字串包含術語或特殊字元的一部分時，需要保留整個術語字元。 退後一步，該方法如下所示：

+ 選擇預定義的分析儀或定義生成所需輸出的自訂分析器
+ 將分析器分配給欄位
+ 生成索引和測試

本文將引導您完成這些任務。 此處描述的方法在其他方案中很有用：萬用字元和正則運算式查詢也需要整個術語作為模式匹配的基礎。 

> [!TIP]
> 評估解譯器是一個反覆運算過程，需要頻繁的索引重建。 您可以使用 Postman、[用於創建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)的 REST API、[刪除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[載入文檔](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)和[搜索文檔](https://docs.microsoft.com/rest/api/searchservice/search-documents)，使此步驟更加簡單。 對於 Load 文檔，請求正文應包含要測試的小型代表性資料集（例如，包含電話號碼或產品代碼的欄位）。 使用同一 Postman 集合中的這些 API，您可以快速迴圈完成這些步驟。

## <a name="choosing-an-analyzer"></a>選擇分析儀

選擇生成整期權杖的分析器時，以下分析器是常見的選擇：

| 分析器 | 行為 |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整個欄位的內容被標記為單個術語。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 僅在空白上分隔。 包含破折號或其他字元的術語被視為單個權杖。 |
| [自訂分析器](index-add-custom-analyzers.md) | （推薦）通過創建自訂分析器，可以同時指定標記器和權杖篩選器。 以前的分析儀必須原樣使用。 自訂分析器允許您選擇要使用的標記器和權杖篩選器。 <br><br>建議的組合是具有[小寫標記篩選器的](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)[關鍵字標記器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)。 就其本身而言，預定義的[關鍵字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)不會小寫任何大寫文本，這可能導致查詢失敗。 自訂分析器為您提供了添加小寫權杖篩選器的機制。 |

如果使用像 Postman 這樣的 Web API 測試控管，則可以添加[測試分析器 REST 調用](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)以檢查權杖化輸出。 給定現有索引和包含破折號或部分術語的欄位，您可以嘗試針對特定術語的各種分析器以查看發出的權杖。  

1. 檢查標準分析器，瞭解預設情況下術語如何標記。

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
1. 修改使用`whitespace`或`keyword`分析器的請求：

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 現在，回應由單個權杖（大寫）組成，其中保留破折號作為字串的一部分。 如果需要搜索模式或部分術語，查詢引擎現在具有查找匹配項的基礎。


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
> 請注意，在生成查詢樹時，查詢解析器通常是搜索運算式中的小寫術語。 如果使用的分析器不小寫文本輸入，並且未獲得預期結果，這可能是原因。 解決方案是添加一個 lwower 大小寫權杖篩選器。

## <a name="analyzer-definitions"></a>分析器定義
 
無論您是在評估分析器還是推進特定配置，都需要在欄位定義上指定分析器，並且如果您不使用內置分析器，則可能需要配置分析器本身。 交換分析器時，通常需要重新生成索引（刪除、重新創建和重新載入）。 

### <a name="use-built-in-analyzers"></a>使用內置分析儀

內置或預定義的分析器可以在欄位定義的`analyzer`屬性上按名稱指定，索引中不需要其他配置。 下面的示例演示如何在`whitespace`欄位上設置分析器。

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
有關所有可用的內置分析器的詳細資訊，請參閱[預定義分析器清單](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)。 

### <a name="use-custom-analyzers"></a>使用自訂分析器

如果使用[自訂分析器](index-add-custom-analyzers.md)，請使用使用者定義的權杖器、權杖篩選器和可能的配置設置組合在索引中定義它。 接下來，在欄位定義上引用它，就像內置分析器一樣。

當目標是整個期限標記化時，建議使用由**關鍵字標記器**和**小寫權杖篩選器**組成的自訂分析器。

+ 關鍵字標記器為欄位的整個內容創建單個權杖。
+ 小寫權杖篩選器將大寫字母轉換為小寫文本。 查詢解析器通常小寫任何大寫文本輸入。 下部同質化使用標記化術語使輸入均勻化。

下面的示例演示了提供關鍵字標記器和小寫權杖篩選器的自訂分析器。

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
> `keyword_v2`系統知道權杖器`lowercase`和權杖篩選器，並使用它們的預設配置，因此無需首先定義它們即可按名稱引用它們。

## <a name="tips-and-best-practices"></a>祕訣和最佳作法

### <a name="tune-query-performance"></a>微調查詢效能

如果實現建議配置，包括keyword_v2權杖器和小寫權杖篩選器，您可能會注意到查詢性能下降，因為索引中現有權杖處理了額外的權杖篩選器。 

下面的示例添加[EdgeNGramTokenFilter，](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)以使首碼匹配得更快。 其他權杖以 2-25 個字元組合生成，包括字元：不僅 MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQ、MSFT/SQL）。 可以想像，額外的標記化會導致更大的索引。

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

分析器在索引期間和查詢執行期間調用。 兩者使用相同的分析器是很常見的，但您可以為每個工作負載配置自訂分析器。 分析器覆蓋在`analyzers`節中的[索引定義](https://docs.microsoft.com/rest/api/searchservice/create-index)中指定，然後引用到特定欄位。 

當僅在索引期間需要自訂分析時，您可以將自訂分析器應用於僅索引，並繼續使用標準 Lucene 分析器（或其他分析器）進行查詢。

要指定特定于角色的分析，可以為每個屬性設置欄位的屬性，設置`indexAnalyzer``searchAnalyzer`而不是預設`analyzer`屬性。

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>不同方案的重複欄位

另一個選項利用每場分析器分配來針對不同的方案進行優化。 具體而言，您可以定義"功能代碼"和"功能代碼Regex"，以支援在第一個搜索時的常規全文檢索搜尋，並在第二個搜索時支援高級模式匹配。

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
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>後續步驟

本文介紹了分析器如何同時導致查詢問題和解決查詢問題。 作為下一步，請仔細查看分析器對索引和查詢處理的影響。 特別是，請考慮流量分析文本 API 返回標記化輸出，以便您可以準確查看分析器為索引創建的內容。

+ [語言分析器](search-language-support.md)
+ [Azure 認知搜索中文本處理分析器](search-analyzers.md)
+ [分析文本 API （REST）](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [全文檢索搜尋的工作原理（查詢體系結構）](search-lucene-query-architecture.md)
