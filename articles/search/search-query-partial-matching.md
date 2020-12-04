---
title: 部分詞彙、模式和特殊字元
titleSuffix: Azure Cognitive Search
description: 您可以使用萬用字元、RegEx 和前置詞查詢來比對 Azure 認知搜尋查詢要求中的整個或部分字詞。 您可以使用完整查詢語法和自訂分析器來解析包含特殊字元的硬式比對模式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 79ba186351cc145e012658abc30572e99b123dbb
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573981"
---
# <a name="partial-term-search-and-patterns-with-special-characters-hyphens-wildcard-regex-patterns"></a>具有特殊字元的部分詞彙搜尋和模式 (連字號、萬用字元、RegEx、模式) 

*部分詞彙搜尋* 是指由詞彙片段所組成的查詢，而不是整個詞彙，您可能只會有開始、中間或結束詞彙 (有時也稱為前置詞、中置或後置詞查詢) 。 部分詞彙搜尋可能包含片段的組合，通常會有特殊字元，例如連字號、連字號或斜線（屬於查詢字串的一部分）。 常見的使用案例包括電話號碼、URL、代碼或以字元分隔的複合字組的部分。

如果索引沒有預期格式的權杖，則部分詞彙搜尋和包含特殊字元的查詢字串可能會造成問題。 在編制索引的 [詞法分析階段](search-lucene-query-architecture.md#stage-2-lexical-analysis) 期間 (假設預設標準分析器) ，會捨棄特殊字元、將複合字分割，以及刪除空白字元;在找不到相符的情況時，所有的都會導致查詢失敗。 例如，像 `+1 (425) 703-6214` (標記為、、) 的電話號碼將 `"1"` `"425"` `"703"` `"6214"` 不會顯示在查詢中， `"3-62"` 因為該內容實際上並不存在於索引中。 

解決方法是在編制索引期間叫用分析器，以保留完整字串（如有需要，包含空格和特殊字元），以便您可以在查詢字串中包含空格和字元。 同樣地，具有未標記為較小部分的完整字串，會啟用「開頭為」或「結尾為」查詢的模式比對，而您提供的模式可以針對不是透過詞法分析轉換的詞彙進行評估。 為不完整的字串建立額外的欄位，再加上使用內容保留的分析器來發出整個詞彙的權杖，就是模式比對的方案，並且比對包含特殊字元的查詢字串。

> [!TIP]
> 如果您熟悉 Postman 和 REST Api，請 [下載查詢範例集合](https://github.com/Azure-Samples/azure-search-postman-samples/) ，以查詢本文中所述的部分詞彙和特殊字元。

## <a name="about-partial-term-search"></a>關於部分詞彙搜尋

Azure 認知搜尋會掃描索引中的整個 token 化詞彙，除非您將萬用字元預留位置運算子 (`*` 和 `?`) ，或將查詢格式化為正則運算式，否則不會找到部分詞彙的相符項。 部分詞彙會使用這些技術來指定：

+ [正則運算式查詢](query-lucene-syntax.md#bkmk_regex) 可以是任何在 Apache Lucene 下有效的正則運算式。 

+ [具有前置](query-simple-syntax.md#prefix-search) 詞比對的萬用字元運算子會參考一般辨識的模式，其中包含詞彙開頭，後面接著或後置 `*` `?` 運算子，例如 `search=cap*` 在 "Cap'n" 的濱水區 Inn "或" Gacc 資本 "上進行比對。 簡單和完整的 Lucene 查詢語法都支援前置比對。

+ [具有](query-lucene-syntax.md#bkmk_wildcard) 中置字元和後置字元的萬用字元，會將 `*` and 運算子放在詞彙的 `?` 內部或開頭，且需要正則運算式語法 (其中運算式以正斜線) 括住。 例如，查詢字串 () 會傳回 `search=/.*numeric*./` "英數位元" 和 "英數" 的結果做為尾碼和中置比對。

針對部分詞彙或模式搜尋，以及一些其他查詢形式（例如模糊搜尋），不會在查詢時流量分析器。 針對這些查詢表單，剖析器會藉由運算子和分隔符號來偵測，而不使用詞法分析將查詢字串傳遞給引擎。 針對這些查詢表單，會忽略欄位上指定的分析器。

> [!NOTE]
> 當部分查詢字串包含字元（例如 URL 片段中的斜線）時，您可能需要加入 escape 字元。 在 JSON 中，正斜線 `/` 會以反斜線來進行轉義 `\` 。 因此， `search=/.*microsoft.com\/azure\/.*/` 是 URL 片段 "microsoft.com/azure/" 的語法。

## <a name="solving-partialpattern-search-problems"></a>解決部分/模式搜尋問題

當您需要對片段或模式或特殊字元進行搜尋時，您可以使用以較簡單的 token 化規則運作的自訂分析器來覆寫預設分析器，以保留索引中的整個字串。 回到一步，方法看起來像這樣：

1. 定義欄位來儲存字串的完整版本， (假設您想要在查詢時分析和未分析的文字) 
1. 評估並選擇在適當的資料細微性層級發出權杖的各種分析器
1. 將分析器指派給欄位
1. 建立並測試索引

> [!TIP]
> 評估分析器是需要經常重建索引的反復程式。 您可以使用 Postman、REST Api 來 [建立索引](/rest/api/searchservice/create-index)、 [刪除索引](/rest/api/searchservice/delete-index)、[載入檔](/rest/api/searchservice/addupdate-or-delete-documents)和 [搜尋檔](/rest/api/searchservice/search-documents)，以簡化此步驟。 針對載入檔，要求本文應該包含您想要測試的小型代表性資料集 (例如，具有電話號碼或產品代碼的欄位) 。 使用這些 Api 在相同的 Postman 集合中，您可以快速地迴圈執行這些步驟。

## <a name="1---create-a-dedicated-field"></a>1-建立專用欄位

分析器會決定如何在索引中 token 化詞彙。 因為分析器是以每個欄位為基礎來指派，所以您可以在索引中建立欄位，以針對不同的案例進行優化。 例如，您可以定義 "featureCode" 和 "featureCodeRegex"，以支援第一個的一般全文檢索搜尋，以及第二個的先進模式比對。 指派給每個欄位的分析器會決定如何在索引中標記每個欄位的內容。  

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

<a name="set-an-analyzer"></a>

## <a name="2---set-an-analyzer"></a>2-設定分析器

選擇會產生整個詞彙標記的分析器時，下列分析器是常見的選擇：

| 分析器 | 行為 |
|----------|-----------|
| [語言分析器](index-add-language-analyzers.md) | 以複合單字或字串、母音突變和動詞形式保存連字號。 如果查詢模式包含連字號，使用語言分析器可能就已足夠。 |
| [關鍵 字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整個欄位的內容會 token 化為單一詞彙。 |
| [空白](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 只將空格分隔。 包含連字號或其他字元的詞彙會被視為單一標記。 |
| [自訂分析器](index-add-custom-analyzers.md) |  (建議) 建立自訂分析器可讓您同時指定 tokenizer 和權杖篩選器。 先前的分析器必須依原樣使用。 自訂分析器可讓您挑選要使用的 token 化工具和權杖篩選器。 <br><br>建議的組合是使用[小寫標記篩選準則](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)的[關鍵字 tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) 。 預先定義的 [關鍵字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) 本身不會區分大小寫的任何大寫文字，這可能會導致查詢失敗。 自訂分析器會提供您新增小寫標記篩選器的機制。 |

如果您使用 web API 測試控管（例如 Postman），您可以加入 [測試分析器 REST 呼叫](/rest/api/searchservice/test-analyzer) ，以檢查 token 化的輸出。

您必須要有已填入的索引才能使用。 指定現有的索引，以及包含虛線或部分詞彙的欄位時，您可以嘗試各種不同的分析器來查看發出的權杖。  

1. 首先，請檢查標準分析器，以查看詞彙如何預設為 token 化。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 評估回應以查看如何在索引中標記文字。 請注意每個詞彙的小寫、移除的連字號，以及子字串分成個別的權杖。 只有符合這些權杖的查詢才會在結果中傳回這份檔。 包含 "10-或" 的查詢將會失敗。

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
1. 現在修改要求以使用 `whitespace` 或 `keyword` 分析器：

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 現在，回應是由單一權杖（大寫）所組成，並保留虛線作為字串的一部分。 如果您需要搜尋模式或部分詞彙（例如 "10-or"），查詢引擎現在會有尋找相符項的基礎。


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
> 請注意，在建立查詢樹狀結構時，查詢剖析器通常會在搜尋運算式中使用較低案例的詞彙。 如果您使用的分析器在編制索引期間不會以較低案例的文字輸入，而且您沒有收到預期的結果，這可能是原因。 解決方法是新增較低案例的權杖篩選準則，如下面的「使用自訂分析器」一節所述。

## <a name="3---configure-an-analyzer"></a>3-設定分析器
 
無論您正在評估分析器或使用特定設定繼續進行，您都必須在欄位定義上指定分析器，如果您不是使用內建的分析器，也可能設定分析器本身。 交換分析器時，您通常需要重建索引， (卸載、重新建立和重載) 。 

### <a name="use-built-in-analyzers"></a>使用內建分析器

內建或預先定義的分析器可以在欄位定義的屬性上依名稱指定 `analyzer` ，不需要在索引中進行其他設定。 下列範例示範如何 `whitespace` 在欄位上設定分析器。 

針對其他案例，若要深入瞭解其他內建分析器，請參閱 [預先定義的分析器清單](./index-add-custom-analyzers.md#predefined-analyzers-reference)。 

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

如果您使用的是 [自訂分析器](index-add-custom-analyzers.md)，請在索引中使用使用者定義的 tokenizer （token 篩選）組合來定義它，並提供可能的設定。 接下來，在欄位定義上參考它，就和內建分析器一樣。

當目標為整個詞彙 token 化時，建議使用由 **關鍵字 tokenizer** 和 **小寫標記篩選準則** 所組成的自訂分析器。

+ 關鍵字 tokenizer 會為欄位的整個內容建立單一標記。
+ 小寫標記篩選會將大寫字母轉換成小寫的文字。 查詢剖析器通常會將任何大寫文字輸入變成小寫。 較低的大小寫會 homogenizes 具有 token 化詞彙的輸入。

下列範例說明可提供關鍵字 tokenizer 和小寫標記篩選準則的自訂分析器。

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
> `keyword_v2` `lowercase` 系統會知道 tokenizer 和 token 篩選器，並使用其預設設定，這就是為什麼您可以依名稱參考它們，而不需要先定義它們。

## <a name="4---build-and-test"></a>4-組建和測試

當您使用支援您案例的分析器和欄位定義來定義索引之後，請載入具有代表性字串的檔，讓您可以測試部分字串查詢。 

上述各節說明邏輯。 本節會逐步解說您在測試解決方案時應呼叫的每個 API。 如先前所述，如果您使用互動式 web 測試控管（例如 Postman），您可以快速地逐步執行這些工作。

+ [Delete index](/rest/api/searchservice/delete-index) 會移除相同名稱的現有索引，讓您可以重新建立。

+ [Create index](/rest/api/searchservice/create-index) 會在您的搜尋服務上建立索引結構，包括分析器定義和含分析器規格的欄位。

+ [載入檔](/rest/api/searchservice/addupdate-or-delete-documents) 會匯入具有和您的索引相同結構的檔，以及可搜尋的內容。 完成此步驟之後，您的索引即可進行查詢或測試。

+ [測試分析器](/rest/api/searchservice/test-analyzer) 已在 [設定分析器](#set-an-analyzer)中推出。 使用各種分析器測試索引中的一些字串，以瞭解如何 token 化詞彙。

+ [搜尋檔](/rest/api/searchservice/search-documents) 說明如何使用 [簡單語法](query-simple-syntax.md) 或萬用字元和正則運算式的 [完整 Lucene 語法](query-lucene-syntax.md) 來建立查詢要求。

  針對部分詞彙查詢（例如查詢 "3-6214"）以找出符合 "+ 1 (425) 703-6214" 的結果，您可以使用簡單的語法： `search=3-6214&queryType=simple` 。

  若為中置和後置詞查詢（例如查詢 "num" 或 "numeric" 以尋找符合 "英數位元"），請使用完整的 Lucene 語法和正則運算式： `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>微調查詢效能

如果您執行的建議設定包含 keyword_v2 tokenizer 和小寫標記篩選器，您可能會注意到因為對索引中現有的權杖進行額外的權杖篩選器處理，所以查詢效能會降低。 

下列範例會新增 [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) ，以加快前置詞比對的速度。 在包含字元的2-25 字元組合中，會產生額外的權杖：不只 (MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQL) 。 

您可以想像，額外的 token 化會導致較大的索引。 如果您有足夠的容量來容納較大的索引，則此方法的回應時間更快，可能是較佳的解決方案。

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

## <a name="next-steps"></a>後續步驟

本文說明分析器如何協助查詢問題和解決查詢問題。 在下一個步驟中，請進一步瞭解分析器對編制索引和查詢處理的影響。 尤其是，請考慮使用「分析文字 API」來傳回 token 化輸出，如此您就可以看到分析器為索引建立的確切內容。

+ [語言分析器](search-language-support.md)
+ [Azure 認知搜尋中的文字處理分析器](search-analyzers.md)
+ [分析文字 API (REST) ](/rest/api/searchservice/test-analyzer)
+ [全文檢索搜尋的運作方式 (查詢架構) ](search-lucene-query-architecture.md)