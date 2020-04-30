---
title: 將自訂分析器新增至字串欄位
titleSuffix: Azure Cognitive Search
description: 設定用於 Azure 認知搜尋全文檢索搜尋查詢中的文字 token 化工具和字元篩選器。
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
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
ms.openlocfilehash: f0d9576f5275bcfc061ce29740f8d85aff4ccfff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231079"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>將自訂分析器新增至 Azure 認知搜尋索引中的字串欄位

「自訂分析器」** 是特定類型的[文字分析器](search-analyzers.md)，其包含使用者定義的現有權杖化工具和選擇性篩選組合。 透過以新方式結合權杖化工具和篩選，您便可以自訂搜尋引擎中的文字處理來達成特定結果。 例如，您可以使用「字元篩選器 (char filter)」** 建立自訂分析器，讓文字輸入在變成語彙基元 (token) 之前能移除 HTML 標記。

 您可以定義多個自訂分析器來變更篩選器的組合，但是每個欄位只能針對索引分析使用一個分析器、針對搜尋分析使用一個分析器。 如需自訂分析器的相關說明，請參閱[自訂分析器範例](search-analyzers.md#Custom-analyzer-example)。

## <a name="overview"></a>概觀

 簡單地說，[全文檢索搜尋引擎](search-lucene-query-architecture.md)是處理和儲存文件的角色，而目的是要達到有效率的查詢和擷取。 概括而言，這一切包括從文件中擷取重要的字組、將其放在索引中，然後使用索引來尋找符合指定查詢中字組的文件。 從文件和搜尋查詢中擷取文字的程序稱為「語彙分析」**。 執行語彙分析的元件稱為「分析器」**。

 在 Azure 認知搜尋中，您可以從[分析器](#AnalyzerTable)資料表中選擇一組預先定義的非語言分析器，或在[語言分析器 &#40;Azure 認知搜尋服務 REST API&#41;](index-add-language-analyzers.md)中列出的特定語言分析器。 您也可以定義自己的自訂分析器。  

 自訂分析器可讓您充分控制將文字轉換成可檢索和可搜尋語彙基元的程序。 這是使用者定義的組態，其中包含預先定義的單一 Token 化工具、一個或多個語彙基元篩選器及一個或多個字元篩選器。 Token 化工具負責將文字分解成語彙基元，而語彙基元篩選器會修改 Token 化工具所發出的語彙基元。 字元篩選器會將輸入文字準備好，然後再交由 Token 化工具處理。 例如，字元篩選器可能會取代特定字元或符號。

 自訂分析器啟用的常見案例包括：  

- 語音搜尋。 新增語音篩選器，以根據文字聽起來如何 (而不是根據拼法) 來啟用搜尋。  

- 停用語彙分析。 使用關鍵字分析器以建立未分析的可搜尋欄位。  

- 快速前置詞/後置詞搜尋。 將邊緣 n-gram 語彙基元篩選器新增至文字的索引前置詞以啟用快速前置詞比對。 您可以將其與反向權杖篩選器結合以進行後置詞比對。  

- 自訂 Token 化。 例如，使用 Whitespace Token 化工具將句子分成使用空白字元作為分隔符號的語彙基元  

- ASCII 摺疊。 新增標準 ASCII 折疊篩選器，將搜尋字詞中的 ö 或 ê 等變音符號標準化。  

  此頁面提供支援的分析器、Token 化工具、語彙基元篩選器和字元篩選器清單。 您也可以找到索引定義的變更描述和相關使用範例。 如需有關 Azure 認知搜尋執行中所運用之基礎技術的詳細背景，請參閱[分析封裝摘要（Lucene）](https://lucene.apache.org/core/6_0_0/core/org/apache/lucene/codecs/lucene60/package-summary.html)。 如需分析器設定的範例，請參閱[在 Azure 認知搜尋中新增分析器](search-analyzers.md#examples)。

## <a name="validation-rules"></a>驗證規則  
 分析器、Token 化工具、語彙基元篩選器和字元篩選器的名稱必須是唯一的，而且不能與任何預先定義的分析器、Token 化工具、語彙基元篩選器或字元篩選器相同。 請參閱使用中名稱的[屬性參考](#PropertyReference)。

## <a name="create-custom-analyzers"></a>建立自訂分析器
 您可以在建立索引時定義自訂分析器。 本節會說明用來指定自訂分析器的語法。 您也可以查看在[Azure 認知搜尋中新增分析器](search-analyzers.md#examples)中的範例定義來熟悉語法。  

 分析器定義包括名稱、類型、一個或多個字元篩選器、最多一個 Token 化工具，以及一個或多個用於 Token 化後置作業的語彙基元篩選器。 字元篩選器會在 Token 化之前套用。 語彙基元篩選器和字元篩選器會以由左到右的順序套用。

 `tokenizer_name` 是 Token 化工具 的名稱，`token_filter_name_1` 和 `token_filter_name_2` 是語彙基元篩選器的名稱，而 `char_filter_name_1` 和 `char_filter_name_2` 是字元篩選器的名稱 (請參閱 [tokenizer](#Tokenizers)、[語彙基元篩選器](#TokenFilters)和字元篩選器表格以取得有效值)。

分析器定義屬於更大的索引。 如需有關其他索引的資訊，請參閱[建立索引 API](https://docs.microsoft.com/rest/api/searchservice/create-index)。

```
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

> [!NOTE]  
>  您所建立的自訂分析器不會顯示在 Azure 入口網站。 新增自訂分析器的唯一方法是透過程式碼，程式碼會在定義索引時呼叫 API。  

 在索引定義中，您可以將此區段放在建立索引要求本文中的任意位置，但通常會放在結尾：  

```
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

只有在您設定自訂選項時，才需要將字元篩選器、Token 化工具和語彙基元篩選器的定義新增至索引中。 若要使用現有的篩選器或 Token 化工具 (不做任何變更)，則以分析器定義中的名稱來加以指定。

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>測試自訂分析器

您可以使用 [REST API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) 中的**測試分析器作業**，查看分析器如何將指定文字分解成語彙基元。

**要求**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**回應**
```
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>上傳自訂分析器

定義分析器、Token 化工具、語彙基元篩選或字元篩選之後，即無法進行修改。 只有當索引更新要求中的 `allowIndexDowntime` 旗標設定為 true 時，才能將新分析器新增到現有的索引中：

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

這項作業至少會讓您的索引離線幾秒鐘，而會導致您的索引編製和查詢要求失敗。 在索引更新後，索引的效能和寫入可用性可能會降低數分鐘，如果是非常大的索引，則可能持續更久，但這些影響是暫時的，而且最終會自行解決。

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>分析器參考

下表會針對索引定義的分析器、Token 化工具、語彙基元篩選器和字元篩選器區段，列出組態屬性。 您索引中的分析器、Token 化工具或篩選器結構會由這些屬性組成。 如需有關指派值的資訊，請參閱[屬性參考](#PropertyReference)。

### <a name="analyzers"></a>分析器

對於分析器而言，索引屬性會因為使用預先定義的分析器或自訂分析器而有所不同。

#### <a name="predefined-analyzers"></a>預先定義的分析器

|||  
|-|-|  
|Name|名稱必須包含字母、數字、空格、虛線或底線，同時開頭必須是英數字元，而且不得超過 128 個字元。|  
|類型|可支援分析器清單中的分析器類型。 請參閱下方[分析器](#AnalyzerTable)表格中的 **analyzer_type** 資料行。|  
|選項|必須是下方[分析器](#AnalyzerTable)表格中有效的預先定義分析器選項。|  

#### <a name="custom-analyzers"></a>自訂分析器

|||  
|-|-|  
|Name|名稱必須包含字母、數字、空格、虛線或底線，同時開頭必須是英數字元，而且不得超過 128 個字元。|  
|類型|必須是 "#Microsoft.Azure.Search.CustomAnalyzer"。|  
|CharFilters|設定為[字元篩選器](#char-filters-reference)表格內其中一個預先定義的字元篩選器，或設定為索引定義中指定的自訂字元篩選器。|  
|權杖化工具|必要。 設定為下方 [Token 化工具](#Tokenizers) 表格內其中一個預先定義的 Token 化工具或設定為索引定義中指定的自訂 Token 化工具。|  
|語彙基元篩選器|設定為[語彙基元篩選器](#TokenFilters)表格內其中一個預先定義的語彙基元篩選器，或設定為索引定義中指定的自訂語彙基元篩選器。|  

> [!NOTE]
> 必須將您的自訂分析器設定為不會產生超過 300 個字元的語彙基元。 使用這類語彙基元的文件會使編製索引失敗。 若要縮短或略過這些語彙基元，請分別使用 **TruncateTokenFilter** 和 **LengthTokenFilter**。  檢查[**權杖篩選器**](#TokenFilters)以取得參考。

<a name="CharFilter"></a>

### <a name="char-filters"></a>字元篩選器

 字元篩選器的用途是將輸入文字準備好，然後再交由 Token 化工具處理。 例如，字元篩選器可能會取代特定字元或符號。 您可以在自訂分析器中具有多個字元篩選器。 字元篩選器會以所列的順序執行。  

|||  
|-|-|  
|Name|名稱必須包含字母、數字、空格、虛線或底線，同時開頭必須是英數字元，而且不得超過 128 個字元。|  
|類型|可支援字元篩選器清單中的字元篩選器類型。 請參閱下方[字元篩選器](#char-filters-reference)表格中的 **char_filter_type** 資料行。|  
|選項|必須是指定[字元篩選器](#char-filters-reference)類型的有效選項。|  

### <a name="tokenizers"></a>Tokenizers

 tokenizer 會將連續文字分割成權杖的序列，例如將句子分成多個文字。  

 您可以指定每個自訂分析器僅一個 tokenizer。 如果您需要一個以上的 tokenizer，您可以建立多個自訂分析器，並且在索引結構描述中逐欄位指派它們。  
自訂分析器可以使用預設或自訂選項來使用預先定義的 tokenizer。  

|||  
|-|-|  
|Name|名稱必須包含字母、數字、空格、虛線或底線，同時開頭必須是英數字元，而且不得超過 128 個字元。|  
|類型|可支援 Token 化工具清單中的 Token 化工具名稱。 請參閱下方 [Token 化工具](#Tokenizers) 表格中的 **tokenizer_type** 資料行。|  
|選項|必須是下方 [Token 化工具](#Tokenizers) 表格中指定 Token 化工具類型的有效選項。|  

### <a name="token-filters"></a>權杖篩選器

 權杖篩選器是用來篩選出或修改 tokenizer 所產生的權杖。 例如，您可以指定小寫篩選器，將所有字元轉換成小寫。   
您可以在自訂分析器中具有多個權杖篩選器。 權杖篩選器會以所列的順序執行。  

|||  
|-|-|  
|Name|名稱必須包含字母、數字、空格、虛線或底線，同時開頭必須是英數字元，而且不得超過 128 個字元。|  
|類型|可支援語彙基元篩選器清單中的語彙基元篩選器名稱。 請參閱下方[語彙基元篩選器](#TokenFilters)表格中的 **token_filter_type** 資料行。|  
|選項|必須是指定語彙基元篩選器類型的[語彙基元篩選器](#TokenFilters)。|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>屬性參考

本節會提供索引內自訂分析器、Token 化工具、字元篩選器或語彙基元篩選器定義中可指定的有效屬性值。 使用 Apache Lucene 實作的分析器、Token 化工具和篩選器會有 Lucene API 文件的連結。

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>預先定義的分析器參考

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**說明和選項**|  
|-|-|-|  
|[關鍵字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (有選項時才需套用類型) |將欄位的整個內容視為單一語彙基元。 這適合用於郵遞區號、識別碼和產品名稱等資料。|  
|[模式](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|透過規則運算式模式彈性地將文字分割成字詞。<br /><br /> **選項**<br /><br /> lowercase (類型：bool) - 判斷字詞是否為小寫。 預設值是 true。<br /><br /> [pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (類型：字串) - 用來比對語彙基元分隔符號的規則運算式模式。 預設值為`\W+`，符合非文字字元。<br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (類型：字串) - 規則運算式旗標。 預設值是空字串。 允許的值： CANON_EQ、CASE_INSENSITIVE、批註、DOTALL、LITERAL、多行、UNICODE_CASE、UNIX_LINES<br /><br /> stopwords (類型：字串陣列) - 停用字詞清單。 預設值是空白清單。|  
|[單個](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(有選項時才需套用類型) |在非字母的位置分割文字，並將其轉換成小寫。 |  
|[常用](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(也稱為 standard.lucene)|StandardAnalyzer|Standard Lucene 分析器，由標準 Token 化工具、小寫篩選器及停止篩選器所組成。<br /><br /> **選項**<br /><br /> maxTokenLength (類型：int) - 語彙基元長度的上限。 預設值為 255。 超過長度上限的權杖會進行分割。 可用的語彙基元長度上限是 300 個字元。<br /><br /> stopwords (類型：字串陣列) - 停用字詞清單。 預設值是空白清單。|  
|standardasciifolding.lucene|(有選項時才需套用類型) |使用 ASCII 折疊篩選器的標準分析器。 |  
|[stop](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|在非字母的位置上分割文字，套用 lowercase 和 stopword 語彙基元篩選器。<br /><br /> **選項**<br /><br /> stopwords (類型：字串陣列) - 停用字詞清單。 預設值為英文的預先定義清單。 |  
|[whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(有選項時才需套用類型) |使用 whitespace Token 化工具的分析器。 分割長度超過 255 個字元的語彙基元。|  

 <sup>1</sup> 分析器類型在程式碼中一律會以 "#Microsoft.Azure.Search" 作為前置詞，因此 "PatternAnalyzer" 實際上會指定為 "#Microsoft.Azure.Search.PatternAnalyzer"。 為求簡潔，我們已移除前置詞，但您的程式碼中仍須要有前置詞。 
 
analyzer_type 僅提供給可自訂的分析器使用。 如果沒有任何選項 (如同 keyword 分析器)，則也不會有相關聯的 #Microsoft.Azure.Search 類型。


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>字元篩選器參考

在下表中，使用 Apache Lucene 實作的字元篩選器會連結至 Lucene API 文件。

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**說明和選項**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(有選項時才需套用類型)  |嘗試去除 HTML 結構的字元篩選器。|  
|[繪製](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|套用比對 (由比對選項定義) 的字元篩選器。 比對採用貪婪演算法 (由指定點上最長的模式比對勝出) 取代項目可以是空字串。<br /><br /> **選項**<br /><br /> mappings (類型：字串陣列) - 下列格式的比對清單： "a=>b" (將所有出現 "a" 的地方取代為字元 "b")。 必要。|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|取代輸入字串中字元的字元篩選器。 其使用規則運算式來識別要保留的字元序列，並使用取代模式來識別要取代的字元。 例如，輸入文字 = "aa  bb aa bb"、模式 ="(aa)\\\s+(bb)" 取代 ="$1#$2"、結果 = "aa#bb aa#bb"。<br /><br /> **選項**<br /><br /> pattern (類型：字串) - 必要。<br /><br /> replacement (類型：字串) - 必要。|  

 <sup>1</sup> 字元篩選器類型在程式碼中一律會以 "#Microsoft.Azure.Search" 作為前置詞，因此 "MappingCharFilter" 實際上會指定為 "#Microsoft.Azure.Search.MappingCharFilter"。 為縮短表格寬度，我們已移除前置詞，但請務必將其包含在您的程式碼中。 請注意，只有可自訂的篩選才會提供 char_filter_type。 如果沒有任何選項 (如同 html_strip)，則也不會有相關聯的 #Microsoft.Azure.Search 類型。

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Token 化工具參考

在下表中，使用 Apache Lucene 實作的 Token 化工具會連結至 Lucene API 文件。

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**說明和選項**|  
|-|-|-|  
|[傳統](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|以文法為基礎且適合用來處理大部分歐洲語言文件的 Token 化工具。<br /><br /> **選項**<br /><br /> maxTokenLength (類型：int) - 語彙基元長度的上限。 預設值：255，最大值：300。 超過長度上限的權杖會進行分割。|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|透過從邊緣到指定 n-gram 大小的方式來 Token 化輸入。<br /><br /> **選項**<br /><br /> minGram （類型： int）-預設值：1，最大值：300。<br /><br /> maxGram （類型： int）-預設值：2，最大值：300。 必須大於 minGram。<br /><br /> tokenChars (類型：字串陣列) - 要在語彙基元中保留的字元類別。 允許的值： <br />"letter"、"digit"、"whitespace"、"punctuation"、"symbol"。 預設為空陣列 - 保留所有字元。 |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|以單一語彙基元的形式發出整個輸入。<br /><br /> **選項**<br /><br /> maxTokenLength (類型：int) - 語彙基元長度的上限。 預設值：256，最大值：300。 超過長度上限的權杖會進行分割。|  
|[letter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(有選項時才需套用類型)  |在非字母的位置上分割文字。 分割長度超過 255 個字元的語彙基元。|  
|[小寫](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(有選項時才需套用類型)  |在非字母的位置分割文字，並將其轉換成小寫。 分割長度超過 255 個字元的語彙基元。|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| 使用語言特有的規則分割文字。<br /><br /> **選項**<br /><br /> maxTokenLength （類型： int）-標記長度的上限，預設值：255，最大值：300。 超過長度上限的權杖會進行分割。 超過 300 個字元的語彙基元會先分割成長度為 300 個字元的語彙基元，然後再根據設定的 maxTokenLength 分割這每一個語彙基元。<br /><br />isSearchTokenizer (類型：bool) - 如果作為搜尋 Token 化工具使用，則設定為 true，如果作為索引 Token 化工具使用，則設為 false。 <br /><br /> language (類型：字串) - 要使用的語言，預設值是 "english"。 允許的值包括：<br />"bangla"、"bulgarian"、"catalan"、"chineseSimplified"、"chineseTraditional"、"croatian"、"czech"、"danish"、"dutch"、"english"、"french"、"german"、"greek"、"gujarati"、"hindi"、"icelandic"、"indonesian"、"italian"、"japanese"、"kannada"、"korean"、"malay"、"malayalam"、"marathi"、"norwegianBokmaal"、"polish"、"portuguese"、"portugueseBrazilian"、"punjabi"、"romanian"、"russian"、"serbianCyrillic"、"serbianLatin"、"slovenian"、"spanish"、"swedish"、"tamil"、"telugu"、"thai"、"ukrainian"、"urdu"、"vietnamese" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| 使用語言特有的規則來分割文字，並將字組縮減到其基本形式<br /><br /> **選項**<br /><br />maxTokenLength （類型： int）-標記長度的上限，預設值：255，最大值：300。 超過長度上限的權杖會進行分割。 超過 300 個字元的語彙基元會先分割成長度為 300 個字元的語彙基元，然後再根據設定的 maxTokenLength 分割這每一個語彙基元。<br /><br /> isSearchTokenizer (類型：bool) - 如果作為搜尋 Token 化工具使用，則設定為 true，如果作為索引 Token 化工具使用，則設為 false。<br /><br /> language (類型：字串) - 要使用的語言，預設值是 "english"。 允許的值包括：<br />"arabic"、"bangla"、"bulgarian"、"catalan"、"croatian"、"czech"、"danish"、"dutch"、"english"、"estonian"、finnish"、"french"、"german"、"greek"、"gujarati"、"hebrew"、"hindi"、"hungarian"、"icelandic"、"indonesian"、"italian"、"kannada"、"latvian"、"lithuanian"、"malay"、"malayalam"、"marathi"、"norwegianBokmaal"、"polish"、"portuguese"、"portugueseBrazilian"、"punjabi"、"romanian"、"russian"、"serbianCyrillic"、"serbianLatin"、"slovak"、"slovenian"、"spanish"、"swedish"、"tamil"、"telugu"、"turkish"、"ukrainian"、"urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|將輸入 Token 化到指定的 n-gram 大小。<br /><br /> **選項**<br /><br /> minGram （類型： int）-預設值：1，最大值：300。<br /><br /> maxGram （類型： int）-預設值：2，最大值：300。 必須大於 minGram。 <br /><br /> tokenChars (類型：字串陣列) - 要在語彙基元中保留的字元類別。 允許的值："letter"、"digit"、"whitespace"、"punctuation"、"symbol"。 預設為空陣列 - 保留所有字元。 |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|路徑類階層的 Token 化工具。<br /><br /> **選項**<br /><br /> delimiter (類型：字串) - 預設值：'/。<br /><br /> replacement (類型：字串) - 如有設定，則會取代分隔符號字元。 預設值與分隔符號的值相同。<br /><br /> maxTokenLength (類型：int) - 語彙基元長度的上限。 預設值：300，最大值：300。 長度超過 maxTokenLength 的路徑會遭到忽略。<br /><br /> reverse (類型：bool) - 如果為 true，則會以反向順序產生語彙基元。 預設：false。<br /><br /> skip (類型：bool) - 要略過的起始語彙基元數目。 預設值為 0。|  
|[模式](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|此 Token 化工具會使用 RegEx 模式比對來建構不同的語彙基元。<br /><br /> **選項**<br /><br /> [pattern](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) （類型：字串）-符合標記分隔符號的正則運算式模式。 預設值為`\W+`，符合非文字字元。 <br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (類型：字串) - 規則運算式旗標。 預設值是空字串。 允許的值： CANON_EQ、CASE_INSENSITIVE、批註、DOTALL、LITERAL、多行、UNICODE_CASE、UNIX_LINES<br /><br /> group (類型：int) - 要將哪個群組擷取至語彙基元。 預設值為 -1 (分割)。|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|遵循 [Unicode 文字分割規則](https://unicode.org/reports/tr29/)來分解文字。<br /><br /> **選項**<br /><br /> maxTokenLength (類型：int) - 語彙基元長度的上限。 預設值：255，最大值：300。 超過長度上限的權杖會進行分割。|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|將 URL 和電子郵件 Token 化為一個語彙基元。<br /><br /> **選項**<br /><br /> maxTokenLength (類型：int) - 語彙基元長度的上限。 預設值：255，最大值：300。 超過長度上限的權杖會進行分割。|  
|[whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(有選項時才需套用類型) |在空白字元處分割文字。 分割長度超過 255 個字元的語彙基元。|  

 <sup>1</sup> Token 化工具類型在程式碼中一律會以 "#Microsoft.Azure.Search" 作為前置詞，因此 "ClassicTokenizer" 實際上會指定為 "#Microsoft.Azure.Search.ClassicTokenizer"。 為縮短表格寬度，我們已移除前置詞，但請務必將其包含在您的程式碼中。 請注意，tokenizer_type 僅提供給可自訂的 token 化工具。 如果沒有任何選項 (如同字母 Token 化工具也不會有相關聯的 #Microsoft.Azure.Search 類型。

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>語彙基元篩選器參考

在下表中，使用 Apache Lucene 實作的語彙基元篩選器會連結至 Lucene API 文件。

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**說明和選項**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(有選項時才需套用類型)  |套用阿拉伯文標準化程式的語彙基元篩選器，用來標準化正字法。|  
|[apostrophe](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(有選項時才需套用類型)  |去除單引號 (包括單引號本身) 之後的所有字元。 |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|將不在前 127 個 ASCII 字元內的英文字母、數字和符號類 Unicode 字元 (「基本拉丁文」Unicode 區塊) 轉換為其對等的 ASCII 項目 (如果有的話)。<br /><br /> **選項**<br /><br /> preserveOriginal (類型：bool) - 如果為 true，則保留原始的語彙基元。 預設值為 false。|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|將從 StandardTokenizer 產生的 CJK 字詞塑造成雙字母組 (bigram)。<br /><br /> **選項**<br /><br /> ignoreScripts (類型：字串陣列) - 要略過的指令碼。 允許的值包括："han"、"hiragana"、"katakana"、"hangul"。 預設值是空白清單。<br /><br /> outputUnigrams (類型：bool) - 如果您一定要輸出單字母組 (unigram) 和雙字母組 (bigram)，則設為 true。 預設值為 false。|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(有選項時才需套用類型)  |標準化 CJK 寬度的差異。 將全形的 ASCII 變體摺疊成對等的基本拉丁文，並將半形的片假名變體摺疊成對等的假名。 |  
|[傳統](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(有選項時才需套用類型)  |移除英文所有格，以及移除縮寫中的點。 |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|在編製索引時，建構常用字詞的雙字母組。 仍會編製單個字詞的索引，並與雙字母組重疊。<br /><br /> **選項**<br /><br /> commonWords (類型：字串陣列) - 一組常見的字組。 預設值是空白清單。 必要。<br /><br /> ignoreCase (類型：bool) - 如果為 true，則比對不區分大小寫。 預設值為 false。<br /><br /> queryMode (類型：bool) - 產生雙字母組，然後移除常用字組與常用字組後的單一字詞。 預設值為 false。|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|分解在許多日耳曼語系中找到的複合字。<br /><br /> **選項**<br /><br /> wordList (類型：字串陣列) - 要作為比對依據的字組清單。 預設值是空白清單。 必要。<br /><br /> minWordSize (類型：int) - 只處理超過此長度的字組。 預設值為 5。<br /><br /> minSubwordSize (類型：int) - 只輸出超過此長度的部分字組 (subword)。 預設值為 2。<br /><br /> maxSubwordSize (類型：int) - 只輸出少於此長度的部分字組 (subword)。 預設值是 15。<br /><br /> onlyLongestMatch (類型：bool) - 只將最長的相符部分字組 (subword) 新增到輸出。 預設值為 false。|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|從輸入語彙基元的前端或後端作為起點，以產生指定大小的 n-gram。<br /><br /> **選項**<br /><br /> minGram （類型： int）-預設值：1，最大值：300。<br /><br /> maxGram （類型： int）-預設值：2，最大值300。 必須大於 minGram。<br /><br /> side (類型：字串) - 指定 n-gram 應從輸入的哪一側產生。 允許的值："front"、"back" |  
|[elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|移除元音省略。 例如，"l'avion" (the plane) 會轉換成 "avion" (plane)。<br /><br /> **選項**<br /><br /> articles (類型：字串陣列) - 要移除的一組冠詞。 預設值是空白清單。 如果沒有冠詞清單，預設會移除所有法文的冠詞。|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(有選項時才需套用類型)  |根據 [German2 雪球演算法](https://snowballstem.org/algorithms/german2/stemmer.html)的啟發式學習法標準化德文字元。|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(有選項時才需套用類型)  |標準化印度文字，以移除拼字變化的一些差異。 |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|標準化印度語文字的 Unicode 表示法。
|[步伐](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|此語彙基元篩選器只保留文字包含在指定字組清單的語彙基元。<br /><br /> **選項**<br /><br /> keepWords (類型：字串陣列) - 要保留的字組清單。 預設值是空白清單。 必要。<br /><br /> keepWordsCase (類型：bool) - 如果為 true，則會先將所有字組改為小寫。 預設值為 false。|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|將字詞標示為關鍵字。<br /><br /> **選項**<br /><br /> keywords (類型：字串陣列) - 要標示為關鍵字的字組清單。 預設值是空白清單。 必要。<br /><br /> ignoreCase (類型：bool) - 如果為 true，則會先將所有字組改為小寫。 預設值為 false。|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(有選項時才需套用類型)  |將每個傳入的語彙基元發出兩次，一次作為關鍵字，一次作為非關鍵字。 |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(有選項時才需套用類型)  |適用於英文的高效能 kstem 篩選。 |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|移除太長或太短的文字。<br /><br /> **選項**<br /><br /> min (類型：int) - 最小數目。 預設值：0，最大值：300。<br /><br /> max (類型：int) - 最大數目。 預設值：300，最大值：300。|  
|[只](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|限制編製索引時的語彙基元數目。<br /><br /> **選項**<br /><br /> maxTokenCount (類型：int) - 要產生的語彙基元數目上限。 預設值是 1。<br /><br /> consumeAllTokens (類型：bool) - 是否必須使用輸入中的所有語彙基元，即使已達到 maxTokenCount。 預設值為 false。|  
|[小寫](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(有選項時才需套用類型)  |將語彙基元文字標準化為小寫。 |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|產生指定大小的 n-gram。<br /><br /> **選項**<br /><br /> minGram （類型： int）-預設值：1，最大值：300。<br /><br /> maxGram （類型： int）-預設值：2，最大值300。 必須大於 minGram。|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|使用 Java RegEx 發出多個語彙基元，一個或多個模式中的每個擷取群組各會有一個。<br /><br /> **選項**<br /><br /> patterns (類型：字串陣列) - 要根據每個語彙基元比對的模式清單。 必要。<br /><br /> reserveOriginal (類型：bool) - 若設定為 true，則傳回原始的語彙基元，即使符合其中一個模式也是如此，預設值：true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|將模式套用到資料流中每個語彙基元的語彙基元篩選器，將以指定的取代字串取代相符項目。<br /><br /> **選項**<br /><br /> pattern (類型：字串) - 必要。<br /><br /> replacement (類型：字串) - 必要。|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(有選項時才需套用類型) |對波斯文套用標準化。 |  
|[phonetic](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|建立語音相符項目的語彙基元。<br /><br /> **選項**<br /><br /> encoder (型別：字串) - 要使用的語音編碼器。 允許的值包括："metaphone"、"doubleMetaphone"、"soundex"、"refinedSoundex"、"caverphone1"、"caverphone2"、"cologne"、"nysiis"、"koelnerPhonetik"、"haasePhonetik"、"beiderMorse"。 預設值："metaphone"。 預設值為 metaphone。<br /><br /> 請參閱 [encoder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) 以取得詳細資訊。<br /><br /> replace (類型：bool) - 如果編碼的語彙基元應該取代原始的語彙基元，則為 true，如果編碼的語彙基元應該新增為同義字，則為 false。 預設值是 true。|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(有選項時才需套用類型)  |根據 [Porter 詞幹演算法 (Porter stemming algorithm)](https://tartarus.org/~martin/PorterStemmer/) 來轉換語彙基元資料流。 |  
|[反向](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(有選項時才需套用類型)  |反轉語彙基元字串。 |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(有選項時才需套用類型)  |標準化可交換的斯堪的納維亞字元用法。 |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(有選項時才需套用類型)  |折疊斯堪的納維亞字元，åÅäæÄÆ->a 及 öÖøØ->o。 其也可以區分雙母音 aa、ae、ao、oe 和 oo 的使用，並且只保留第一個。 |  
|[shingle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|將語彙基元的組合建立為單一語彙基元。<br /><br /> **選項**<br /><br /> maxShingleSize (類型：int) - 預設值為 2。<br /><br /> minShingleSize (類型：int) - 預設值為 2。<br /><br /> outputUnigrams (類型：bool) - 如果為 true，輸出資料流會包含輸入語彙基元 (單字母組) 及 shingle。 預設值是 true。<br /><br /> outputUnigramsIfNoShingles (類型：bool) - 如果為 true，則覆寫 outputUnigrams 行為==而 false 用於沒有可用 shingle 的狀況。 預設值為 false。<br /><br /> tokenSeparator (類型：字串) - 加入相鄰語彙基元來形成 shingle 時要用的字串。 預設值是 " "。<br /><br /> filterToken (類型：字串) - 在每個沒有語彙基元的位置插入字串。 預設值為 "_"。|  
|[snowball](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Snowball 語彙基元篩選器。<br /><br /> **選項**<br /><br /> language (類型：字串) - 允許的值包括："armenian"、"basque"、"catalan"、"danish"、"dutch"、"english"、"finnish"、"french"、"german"、"german2"、"hungarian"、"italian"、"kp"、"lovins"、"norwegian"、"porter"、"portuguese"、"romanian"、"russian"、"spanish"、"swedish"、"turkish"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|標準化索拉尼 (Sorani) 文字的 Unicode 表示法。<br /><br /> **選項**<br /><br /> 無。|  
|詞幹分析器|StemmerTokenFilter|特定語言的詞幹分析篩選器。<br /><br /> **選項**<br /><br /> language (類型：字串) - 允許的值包括： <br /> -   [帶](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   [亞美尼亞](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   [巴斯克文](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brazilian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-「保加利亞文」<br />-   [加泰蘭文](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   [捷克文](https://portal.acm.org/citation.cfm?id=1598600)<br />-   [語](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   [荷蘭文](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   [/](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   [芬蘭文](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   [行楷](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-"加利西亞"<br />- "minimalGalician"<br />-   [德語](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   [條](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-「印度文」<br />-   [匈牙利](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   [印尼文](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   [語](https://snowballstem.org/otherapps/oregan/)<br />-   [義大利文](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   [拉脫維亞文](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norwegian"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   [葡萄牙文](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   [羅馬尼亞文](https://snowballstem.org/otherapps/romanian/)<br />-   [俄語](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   [西](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [瑞典文](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   [土耳其](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|任何可透過字典進行詞幹分析的字詞都會標示為關鍵字，這樣可避免鏈結底部的詞幹分析。 必須放在任何詞幹分析篩選器之前。<br /><br /> **選項**<br /><br /> rules (類型：字串陣列) - 使用下列格式的詞幹分析規則："字組 = > 詞幹"，例如 "ran => run"。 預設值是空白清單。  必要。|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|從語彙基元資料流移除停用字詞。 根據預設，篩選器會使用預先定義的停用字詞清單 (適用於英文)。<br /><br /> **選項**<br /><br /> stopwords (類型：字串陣列) - 停用字詞清單。 如果指定 stopwordsList，則無法指定此項目。<br /><br /> stopwordsList (類型：字串) - 停用字詞的預先定義清單。 如果指定 stopwords，則無法指定此項目。 允許的值包括："arabic"、"armenian"、"basque"、"brazilian", "bulgarian"、"catalan"、"czech"、"danish"、"dutch"、"english"、"finnish"、"french"、"galician"、"german"、"greek"、"hindi"、"hungarian"、"indonesian"、"irish"、"italian"、"latvian"、"norwegian"、"persian"、"portuguese"、"romanian"、"russian"、"sorani"、"spanish"、"swedish"、"thai"、"turkish"，預設值："english"。 如果指定 stopwords，則無法指定此項目。 <br /><br /> ignoreCase (類型：bool) - 如果為 true，則會先將所有字組改為小寫。 預設值為 false。<br /><br /> removeTrailing (類型：bool) - 如果為 true，若最後一個搜尋字詞是停用字詞，則會略過。 預設值是 true。
|[選定](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|比對語彙基元資料流中的單一或多個同義字。<br /><br /> **選項**<br /><br /> synonyms (類型：字串陣列) - 必要。 下列兩種格式之一的同義字清單：<br /><br /> -incredible, unbelievable, fabulous => amazing - 在 => 符號左邊的所有字詞都可由該符號右邊的所有字詞取代。<br /><br /> -incredible, unbelievable, fabulous, amazing - 以逗號分隔的對等字組清單。 設定 expand 選項來變更此清單的解譯方式。<br /><br /> ignoreCase (類型：bool) - 將輸入的大小寫摺疊以用於比對。 預設值為 false。<br /><br /> expand (類型：bool) - 如果為 true，則同義字清單中的所有字組 (如果不使用 => 標記) 都可互相對應。 <br />下列清單：incredible, unbelievable, fabulous, amazing 同等於：incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing<br /><br />如果為 false，下列清單：incredible, unbelievable, fabulous, amazing 同等於：incredible, unbelievable, fabulous, amazing => incredible。|  
|[物](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(有選項時才需套用類型)  |修剪語彙基元的開頭及結尾空白字元。 |  
|[各](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|將字詞截斷至特定長度。<br /><br /> **選項**<br /><br /> 長度（類型： int）-預設值：300，最大值：300。 必要。|  
|[唯一](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|篩選出與前一個語彙基元使用相同文字的語彙基元。<br /><br /> **選項**<br /><br /> onlyOnSamePosition (類型：bool) - 如果設定，則只會移除相同位置上的重複項目。 預設值是 true。|  
|[變為](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(有選項時才需套用類型)  |將語彙基元文字標準化為大寫。 |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|將字組分割成部分字組，並對部分字組群組執行選擇性的轉換。<br /><br /> **選項**<br /><br /> generateWordParts (類型：bool) - 產生部分字組，例如，"AzureSearch" 會變成 "Azure" "Search"。 預設值是 true。<br /><br /> generateNumberParts (類型：bool) - 產生數字的部分字組。 預設值是 true。<br /><br /> catenateWords (類型：bool) - 將所能連接的最多字組部分合併，例如，"Azure-Search" 會變成 "AzureSearch"。 預設值為 false。<br /><br /> catenateNumbers (類型：bool) - 將所能連接的最多數字部分合併，例如，"1-2" 會變成 "12"。 預設值為 false。<br /><br /> catenateAll (類型：bool) - 將所有部分字組合併，例如，"Azure-Search-1" 會變成 "AzureSearch1"。 預設值為 false。<br /><br /> splitOnCaseChange (類型：bool) - 如果為 true，則根據大小寫變更來分割字組，例如，"AzureSearch" 會變成 "Azure" "Search"。 預設值是 true。<br /><br /> preserveOriginal - 保留原始子組並新增至部分字組清單。 預設值為 false。<br /><br /> splitOnNumerics (類型：bool) - 如果為 true，則根據數字來分割，例如 "Azure1Search" 會變成 "Azure" "1" "Search"。 預設值是 true。<br /><br /> stemEnglishPossessive (類型：bool) - 移除每個字組部分尾端的 "s"。 預設值是 true。<br /><br /> protectedWords (類型：字串陣列) - 要防止分隔的語彙基元。 預設值是空白清單。|  

 <sup>1</sup> 語彙基元篩選器類型在程式碼中一律會以 "#Microsoft.Azure.Search" 作為前置詞，因此 "ArabicNormalizationTokenFilter" 實際上會指定為 "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter"。  為縮短表格寬度，我們已移除前置詞，但請務必將其包含在您的程式碼中。  


## <a name="see-also"></a>請參閱  
 [Azure 認知搜尋 REST Api](https://docs.microsoft.com/rest/api/searchservice/)   
 [Azure 認知搜尋中的分析器 > 範例](search-analyzers.md#examples)    
 [建立 Azure 認知搜尋 REST API&#41;的索引 &#40;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
