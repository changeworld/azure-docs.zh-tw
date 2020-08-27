---
title: 語言和文字處理的分析器
titleSuffix: Azure Cognitive Search
description: 將分析器指派給索引中的可搜尋文字欄位，可將預設的標準 Lucene 取代為自訂、預先定義或特定語言的替代項目。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/20/2020
ms.openlocfilehash: a05d58108561856f61a8c484882c996eee96e44d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918072"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Azure 認知搜尋中的文字處理分析器

「*分析器*」是[全文搜尋引擎](search-lucene-query-architecture.md)的元件，負責查詢字串和已編製索引文件中的文字處理。 文字處理 (也稱為「詞彙分析) 轉型，可透過下列動作修改字串：

+ 移除不必要的單字 (停用字詞) 和標點符號
+ 將片語和以字元分隔的單字分成元件部分
+ 小寫單字的大寫
+ 將文字縮減為基本的根表單，以節省儲存效率，讓您可以找到相符的專案，不論時態是否一致

分析會套用至標示為「可搜尋」的 `Edm.String` 欄位，這表示全文檢索搜尋。 若為具有此設定的欄位，分析會在建立權杖時于編制索引期間進行，然後在查詢進行時再次執行查詢，而引擎會掃描相符的權杖。 當同時使用相同的分析器來編制索引和查詢時，比對更有可能發生，但您可以根據您的需求，個別設定每個工作負載的分析器。

非全文檢索搜尋的查詢類型，例如正則運算式或模糊搜尋，不會經過查詢端的分析階段。 相反地，剖析器會使用您提供作為比對基礎的模式，將這些字串直接傳送至搜尋引擎。 一般而言，這些查詢表單需要整個字串標記，才能進行模式比對工作。 若要在編制索引期間取得整個詞彙權杖，您可能需要 [自訂分析器](index-add-custom-analyzers.md)。 如需查詢詞彙分析時機和原因的詳細資訊，請參閱 [Azure 認知搜尋中的全文檢索搜尋](search-lucene-query-architecture.md)。

如需更多有關「詞法分析」的背景資訊，請聆聽下列影片剪輯以取得簡短的說明。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>預設分析器  

在 Azure 認知搜尋查詢中，系統會自動在所有標記為可搜尋的字串欄位上叫用分析器。 

根據預設，Azure 認知搜尋會使用 [Apache Lucene 標準分析器 (標準 Lucene) ](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html)，它會依照「 [Unicode 文字分割](https://unicode.org/reports/tr29/) 」規則，將文字分成幾個元素。 此外，標準分析器會將所有字元轉換為它們的小寫形式。 已編製索引的文件和搜尋字詞在編製索引和查詢處理期間都會執行分析。  

您可以逐欄覆寫預設值。 替代分析器可以是適用于語言處理的 [語言分析器](index-add-language-analyzers.md) 、 [自訂分析器](index-add-custom-analyzers.md)，或是 [可用分析器清單](index-add-custom-analyzers.md#AnalyzerTable)中預先定義的分析器。

## <a name="types-of-analyzers"></a>分析器類型

下列清單描述 Azure 認知搜尋中可用的分析器。

| 類別 | 描述 |
|----------|-------------|
| [標準 Lucene 分析器](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | 預設值。 不需要任何規格或設定。 這種一般用途的分析器適用于許多語言和案例。|
| 預先定義的分析器 | 作為預計要依現狀使用的成品提供。 <br/>共有兩種類型：特製化和語言。 使它們成為「預先定義」的條件是依名稱參考，無須設定或自訂。 <br/><br/>[特製化 (語言無從驗證) 分析器](index-add-custom-analyzers.md#AnalyzerTable)適用於文字輸入需要特殊處理或最少處理時。 非語言預先定義的分析器包含 **Asciifolding**、**金鑰**、**模式**、**簡單**、**停止**、**空白**。<br/><br/>[語言分析器](index-add-language-analyzers.md)適用於當您需要為個別語言提供豐富的語言支援時。 Azure 認知搜尋支援 35 Lucene 語言分析器和 50 Microsoft 自然語言處理分析器。 |
|[自訂分析器](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 意指結合現有元素的使用者定義組態，包括一個權杖化工具 (必要) 和選擇性篩選條件 (Char 或權杖)。|

一些預先定義的分析器 (例如 **Pattern** 或 **Stop**) 可支援一組有限的設定選項。 若要設定這些選項，請實際上建立自訂分析器，其中包含預先定義的分析器和其中一個記載於[預先定義的分析器參考](index-add-custom-analyzers.md#AnalyzerTable)中的替代選項。 如同任何自訂組態，為您的新組態提供名稱，例如 myPatternAnalyzer**，以便與 Lucene 分析器有所區別。

## <a name="how-to-specify-analyzers"></a>如何指定分析器

設定分析器是選擇性的。 一般規則是先嘗試使用預設標準 Lucene 分析器來查看其執行方式。 如果查詢無法傳回預期的結果，切換至不同的分析器通常是正確的解決方案。

1. 在 [索引](/rest/api/searchservice/create-index)中建立欄位定義時，請將 [  **分析器** ] 屬性設定為下列其中一項： [預先定義的分析器](index-add-custom-analyzers.md#AnalyzerTable) （例如）、 `keyword` [語言分析器](index-add-language-analyzers.md) （例如 `en.microsoft` ）或自訂分析器 (在相同的索引架構) 中定義。  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   如果您使用的是 [語言分析器](index-add-language-analyzers.md)，則必須使用 **分析器** 屬性來指定它。 **>searchanalyzer**和 **>indexanalyzer**屬性不支援語言分析器。

1. 或者，設定 **>indexanalyzer** 和 **>searchanalyzer** ，以改變每個工作負載的分析器。 這些屬性會一起設定，並取代 **分析器** 屬性，該屬性必須是 null。 您可以使用不同的分析器來準備和抓取資料，如果其中一個活動需要另一個不需要的特定轉換。

   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "whitespace"
    },
   ```

1. 僅限自訂分析器，請在索引的 **[分析器]** 區段中建立專案，然後將自訂分析器指派給每一個前兩個步驟中的欄位定義。 如需詳細資訊，請參閱[建立索引](/rest/api/searchservice/create-index)以及[新增自訂分析器](index-add-custom-analyzers.md)。

## <a name="when-to-add-analyzers"></a>新增分析器的時機

新增並指派分析器的最佳時機是在有效開發期間內，當卸除並重建索引為例行工作時。

因為分析器會用來 token 化詞彙，所以您應該在建立欄位時指派分析器。 事實上，不允許將 **分析器** 或 **>indexanalyzer** 指派給已實際建立的欄位， (雖然您可以隨時變更 **>searchanalyzer** 屬性，而不會影響到索引) 。

若要變更現有欄位的分析器，您必須 [完全重建索引](search-howto-reindex.md) (您無法) 重建個別的欄位。 針對生產環境中的索引，您可以使用新的分析器指派來建立新的欄位來延遲重建，然後開始使用它來取代舊的。 使用[更新索引](/rest/api/searchservice/update-index)來合併新欄位，以及使用 [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) 來填入資料。 稍後，您可以清除索引以移除過時的欄位，當作規劃索引服務的一部分。

若要將新欄位加入至現有的索引，請呼叫 [Update index](/rest/api/searchservice/update-index) 來新增欄位，並 [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) 以填入該欄位。

如果您想要避免這個錯誤，請在[Update index](/rest/api/searchservice/update-index)中傳遞**allowIndexDowntime**旗標，以將自訂分析器新增至現有的索引中：

*「不允許索引更新，因為這會導致停機。若要將新的分析器、token 化工具、token 篩選器或字元篩選器新增至現有的索引，請在索引更新要求中將 ' allowIndexDowntime ' 查詢參數設定為 ' true '。請注意，這種作業會讓您的索引離線至少幾秒鐘，導致您的索引編制和查詢要求失敗。在索引更新後，索引的效能和寫入可用性可能會降低數分鐘，如果是非常大的索引，則會變得較長。」*

## <a name="recommendations-for-working-with-analyzers"></a>使用分析器的建議

本節提供如何使用分析器的建議。

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>一個用於讀寫的分析器，除非您有特定的需求

Azure 認知搜尋可讓您指定不同的分析器來編制索引，並透過其他 **>indexanalyzer** 和 **>searchanalyzer** 欄位屬性來搜尋。 如果未指定，以 **analyzer** 屬性設定的分析器可用於編製索引和搜尋。 如果未指定 **分析器** ，則會使用預設的標準 Lucene 分析器。

一般規則是使用索引和查詢的相同分析器，除非特定需求另有指示。 請務必徹底測試。 當搜尋和索引時的文字處理不同，會有查詢字詞和索引字詞不符的風險，因為搜尋和索引分析器的設定不一致。

### <a name="test-during-active-development"></a>在開發期間進行測試

覆寫標準分析器需要重建索引。 可能的話，請先決定要在開發期間使用的作用中分析器，然後才將索引實際執行。

### <a name="inspect-tokenized-terms"></a>檢查權杖化字詞

若搜尋作業無法傳回預期中的結果，則查詢的字詞輸入以及索引中的權杖化字詞之間，極有可能發生權杖不相符的狀況。 若權杖不相同，則比對作業無法實行。 若要檢查權杖化工具輸出，建議使用[分析 API](/rest/api/searchservice/test-analyzer) 做為調查工具。 回應由權杖組成，一如特定分析器所產生的權杖。

<a name="examples"></a>

## <a name="rest-examples"></a>REST 範例

下列範例會顯示幾個重要情節的分析器定義。

+ [自訂分析器範例](#Custom-analyzer-example)
+ [將分析器指派給欄位範例](#Per-field-analyzer-assignment-example)
+ [混合編製索引和搜尋的分析器](#Mixing-analyzers-for-indexing-and-search-operations)
+ [語言分析器範例](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>自訂分析器範例

此範例會使用自訂選項來說明分析器定義。 Char 篩選、安全性權杖和權杖篩選條件會個別指定為具名的建構，然後在分析器定義中加以參考。 預先定義的元素會依現狀使用，而且只依名稱加以參考。

逐步解說這個範例：

* 分析器是可搜尋欄位的欄位類別屬性。
* 自訂分析器是索引定義的一部分。 它可能是小幅自訂 (例如，在一個篩選條件中自訂單一選項) 或在多個位置中加以自訂。
* 在此情況下，自訂分析器是 "my_analyzer"，會依次使用自訂的標準權杖化工具 "my_standard_tokenizer" 和兩個權杖篩選條件：小寫和自訂的 asciifolding 篩選條件 "my_asciifolding"。
* 此外，其中也會定義 2 個自訂 char 篩選條件「map_dash」和「remove_whitespace」。 第一個會以底線取代所有的連字號，而第二個則會移除所有空格。 空間必須在對應規則中以 UTF-8 編碼。 權杖化之前會套用 Char 篩選條件，而且Char 篩選條件會影響所產生的權杖 (標準權杖化工具會在虛線和空格中斷，而不會在底線中斷)。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>每個欄位的分析器指派範例

預設值為標準分析器。 假設您需要將預設值取代為不同的預先定義分析器，例如模式分析器。 如果您未設定自訂選項，就只需要在欄位定義中依名稱加以指定。

「分析器」元素會以各欄位方式來覆寫標準分析器。 沒有通用的覆寫。 在此範例中，`text1` 會使用模式分析器和 `text2` (其中並未指定分析器) 來使用預設值。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>混合編製索引和搜尋作業的分析器

API 包含其他的索引屬性，可針對索引和搜尋指定不同的分析器。 必須將 **searchAnalyzer** 和 **indexAnalyzer** 屬性指定為一組，從而取代單一 **analyzer** 屬性。


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>語言分析器範例

包含不同語言之字串的欄位可以使用語言分析器，而其他欄位則是保留預設值 (或使用一些其他的預先定義或自訂分析器)。 如果您是使用語言分析器，就必須將它用於索引和搜尋作業。 使用語言分析器的欄位不能具有索引和搜尋的不同分析器。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>C # 範例

如果您使用 .NET SDK 程式碼範例，您可以附加這些範例以使用或設定分析器。

+ [指派內建分析器](#Assign-a-language-analyzer)
+ [設定分析器](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>指派語言分析器

依原樣使用的任何分析器（沒有設定）都會在欄位定義上指定。 不需要在索引的 **[分析器]** 區段中建立專案。 

此範例會將 Microsoft 英文和法文分析器指派給描述欄位。 它是取自旅館索引較大定義的程式碼片段，在 [>dotnethowto](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 範例的 hotels.cs 檔中使用飯店類別建立。

呼叫 [分析器](/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)，指定 [AnalyzerName](/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) 類型，提供 Azure 認知搜尋中支援的文字分析器。

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>定義自訂分析器

需要自訂或設定時，您必須將分析器結構新增至索引。 一旦定義之後，您就可以將其加入至先前範例中所示範的欄位定義。

建立 [CustomAnalyzer](/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) 物件。 如需更多範例，請參閱 [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs)。

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>後續步驟

+ 請參閱 [完整的全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)的完整說明。 本文使用範例來說明表面上看似違反直覺的行為。

+ 請從[搜尋文件](/rest/api/searchservice/search-documents#bkmk_examples)範例章節，或從入口網站的搜尋總管中[簡單查詢語法](query-simple-syntax.md)，嘗試其他查詢語法。

+ 了解如何套用[特定語言的語彙分析器](index-add-language-analyzers.md)。

+ [設定自訂分析器](index-add-custom-analyzers.md)以進行最少的處理，或是在個別欄位上進行特殊的處理。

## <a name="see-also"></a>請參閱

 [搜尋檔 REST API](/rest/api/searchservice/search-documents) 

 [簡單查詢語法](query-simple-syntax.md) 

 [完整的 Lucene 查詢語法](query-lucene-syntax.md) 
 
 [處理搜尋結果](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png