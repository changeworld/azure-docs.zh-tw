---
title: 教學課程：建立自訂分析器
titleSuffix: Azure Cognitive Search
description: 了解如何在 Azure 認知搜尋中建置自訂分析器，以改善搜尋結果的品質。
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: ac7cee2c1d72b4102fb397aa8093c2d38686fc88
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397261"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>教學課程：建立電話號碼的自訂分析器

[分析器](search-analyzers.md)是任何搜尋解決方案中的重要元件。 若要改善搜尋結果的品質，就務必要了解分析器的運作方式，以及分析器會如何影響這些結果。

在某些情況下，像是使用任意格式的文字欄位時，只要選取正確的[語言分析器](index-add-language-analyzers.md)就能改善搜尋結果。 不過，某些案例 (例如，正確搜尋電話號碼、URL 或電子郵件) 則可能需要使用自訂分析器。

本教學課程使用 Postman 和 Azure 認知搜尋的 [REST API](/rest/api/searchservice/) 來執行下列作業：

> [!div class="checklist"]
> * 說明分析器的運作方式
> * 定義用來搜尋電話號碼的自訂分析器
> * 測試自訂分析器將文字化為權杖的方式
> * 建立用於編制索引和搜尋的個別分析器，以進一步改善結果

## <a name="prerequisites"></a>必要條件

本教學課程需要下列服務和工具。

+ [Postman 桌面應用程式](https://www.getpostman.com/)
+ [建立](search-create-service-portal.md)或[尋找現有的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>下載檔案

本教學課程的原始程式碼位於 [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) GitHub 存放庫的 [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) 資料夾中。

## <a name="1---create-azure-cognitive-search-service"></a>1 - 建立 Azure 認知搜尋服務

若要完成本教學課程，您必須有 Azure 認知搜尋服務 (可[在入口網站中建立](search-create-service-portal.md))。 您可以使用免費層來完成此逐步解說。

在下一個步驟中，您必須知道搜尋服務的名稱及其 API 金鑰。 如果您不確定如何尋找這些項目，請參閱此[快速入門](search-create-service-portal.md#get-a-key-and-url-endpoint)。


## <a name="2---set-up-postman"></a>2 - 設定 Postman

接下來，啟動 Postman 並匯入您從 [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) 下載的集合。

若要匯入集合，請移至 [檔案] > [匯入]，然後選取您要匯入的集合檔案。

針對每個要求，您需要：

1. 使用您的搜尋服務名稱來取代 `<YOUR-SEARCH-SERVICE>`。

1. 將 `<YOUR-ADMIN-API-KEY>` 取代為搜尋服務的主要或次要金鑰。

  :::image type="content" source="media/search-get-started-postman/postman-url.png" alt-text="Postman 要求 URL 和標頭" border="false":::

如果您不熟悉 Postman，請參閱[使用 Postman 探索 Azure 認知搜尋 REST API](search-get-started-postman.md)。

## <a name="3---create-an-initial-index"></a>3 - 建立初始索引

在此步驟中，我們會建立初始索引、將文件載入至索引，然後查詢文件以了解初始搜尋的表現。

### <a name="create-index"></a>建立索引

首先，我們會建立一個名為 `tutorial-basic-index` 的簡單索引，其中包含兩個欄位：`id` 和 `phone_number`。 我們尚未定義分析器，因此預設會使用 `standard.lucene` 分析器。

為了建立索引，我們會傳送下列要求：

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>載入資料

接下來，我們會將資料載入至索引。 在某些情況下，您可能無法控制所擷取的電話號碼格式，因此我們會針對不同類型的格式進行測試。 在理想情況下，搜尋解決方案會傳回所有相符的電話號碼，而不論其格式為何。

資料會透過下列要求載入至索引： 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

索引有了資料後，我們就可以開始搜尋。

### <a name="search"></a>搜尋

為了獲得直覺式的搜尋，最好不要期望使用者會以特定方式來格式化查詢。 使用者可以使用上面所示的任何格式來搜尋 `(425) 555-0100`，但仍應該會傳回結果。 在此步驟中，我們將測試幾個範例查詢，以了解其表現。

我們一開始會先搜尋 `(425) 555-0100`：

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

此查詢傳回了**四個預期結果中的三個**，但也傳回了**兩個非預期的結果**：

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

接下來，讓我們搜尋沒有任何格式的數字 `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

此查詢的表現更差，只傳回**四個正確相符項目的其中一個**。

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

如果您發現這些結果令人困惑，那麼您並不孤單。 在下一節中，我們將深入探討為何會獲得這些結果。

## <a name="4---debug-search-results"></a>4 - 對搜尋結果進行偵錯

若要了解這些搜尋結果，請務必先了解分析器的運作方式。 從這裡，我們可以使用[分析文字 API](/rest/api/searchservice/test-analyzer) 來測試預設分析器，然後建立符合我們需求的分析器。

### <a name="how-analyzers-work"></a>分析器的運作方式

「[分析器](search-analyzers.md)」是[全文搜尋引擎](search-lucene-query-architecture.md)的元件，負責查詢字串和已編製索引文件中的文字處理。 視案例而定，不同的分析器會以不同的方式處理文字。 在此案例中，我們需要建置專門用於電話號碼的分析器。

分析器由三個元件組成︰

+ [**字元篩選器**](#CharFilters)，會從輸入文字中移除或取代個別字元。
+ **[權杖化工具](#Tokenizers)** ，會將輸入文字分拆為權杖，而成為搜尋索引中的索引鍵。
+ [**權杖篩選器**](#TokenFilters)，會操作權杖化工具所產生的權杖。

在下圖中，您可以看到這三個元件如何合作將句子化為權杖：

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Postman 要求 URL 和標頭":::

然後，這些權杖會儲存在反向索引中，以實現快速的全文檢索搜尋。  反向索引會將在語彙分析期間擷取的所有唯一詞彙對應到其發生所在的文件，以實現全文檢索搜尋。 您可以在下圖中看到範例：

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Postman 要求 URL 和標頭":::

所有搜尋都會往下一路搜尋儲存在反向索引中的詞彙。 當使用者發出查詢時：

1. 系統會剖析查詢並分析查詢詞彙。
1. 接著，會掃描反向索引，以尋找具有相符詞彙的文件。
1. 最後，所擷取的文件會依照[相似性演算法](index-ranking-similarity.md)排序。

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Postman 要求 URL 和標頭":::

如果查詢詞彙不符合反向索引中的詞彙，則不會傳回結果。 若要深入了解查詢的運作方式，請參閱關於[全文檢索搜尋](search-lucene-query-architecture.md)的這篇文章。

> [!Note]
> [部分詞彙查詢](search-query-partial-matching.md)是此規則的重要例外狀況。 與一般詞彙查詢不同，這些查詢 (前置詞查詢、萬用字元查詢、RegEx 查詢) 會略過語彙分析程序。 在與索引中的詞彙進行比對之前，部分詞彙只會是小寫狀態。 如果分析器未設定為支援這些類型的查詢，您往往會收到非預期的結果，因為索引中不存在相符的詞彙。

### <a name="test-analyzer-using-the-analyze-text-api"></a>使用分析文字 API 來測試分析器

Azure 認知搜尋會提供[分析文字 API](/rest/api/searchservice/test-analyzer)，以讓您測試分析器來了解其如何處理文字。

您可以使用下列要求來呼叫分析文字 API：

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
      "text": "(425) 555-0100",
      "analyzer": "standard.lucene"
  }
```

然後，API 會傳回從文字中擷取的權杖清單。 您可以看到，標準的 Lucene 分析器會將電話號碼分割成三個不同權杖：

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

相反地，格式化為沒有任何標點符號的電話號碼 `4255550100` 則會權杖化為單一權杖。

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

請記住，系統會同時分析查詢詞彙和已編制索引的文件。 回想一下上一個步驟中的搜尋結果，我們可以開始查看為何系統會傳回這些結果。

在第一個查詢中，傳回的電話號碼不正確，因為其中一個詞彙 `555` 符合我們所搜尋的其中一個詞彙。 在第二個查詢中，系統只傳回一個數字，因為這個數字是唯一有詞彙符合 `4255550100` 的記錄。

## <a name="5---build-a-custom-analyzer"></a>5 - 建置自訂分析器

我們已經了解所看到的結果，接下來讓我們建置自訂分析器來改善權杖化邏輯。

我們的目標是要針對電話號碼提供直覺式的搜尋，而不論查詢或索引字串的格式為何。 為了達成此結果，我們將指定[字元篩選器](#CharFilters)、[權杖化工具](#Tokenizers)和[權杖篩選器](#TokenFilters)。

<a name="CharFilters"></a>

### <a name="character-filters"></a>字元篩選器

可先使用字元篩選器處理文字，然後再將文字送到權杖化工具。 字元篩選器的常見用法包括篩選出 HTML 元素或取代特殊字元。

對於電話號碼，我們想要移除空白字元和特殊字元，因為並非所有的電話號碼格式都包含同樣的特殊字元和空格。

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

上述篩選器會從輸入中移除 `-` `(` `)` `+` `.` 和空格。

|輸入|輸出|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizers

權杖化工具會在過程中將文字分割成多個權杖，並捨棄一些字元，例如標點符號。 在許多情況下，權杖化的目標是要將句子分割成個別單字。

針對此案例，我們會使用關鍵字權杖化工具 `keyword_v2`，因為我們想要將電話號碼擷取為單一詞彙。 請注意，這不是解決此問題的唯一方法。 請參閱下面的[替代方法](#Alternate)一節。

在獲得相同文字時，關鍵字權杖化工具一律會將其輸出為單一詞彙。

|輸入|輸出|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>權杖篩選器

權杖篩選器會篩選出或修改權杖化工具所產生的權杖。 權杖篩選器的其中一個常見用法是使用小寫權杖篩選器將所有字元設為小寫。 另一個常見用法是篩選出 `the`、`and` 或 `is` 等停用字詞。

雖然我們不需要在此案例中使用任一篩選器，但我們會使用 nGram 權杖篩選器來實現電話號碼的部分搜尋。

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

[nGram_v2 權杖篩選器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)會根據 `minGram` 和 `maxGram` 參數，將權杖分割成指定大小的 n 元。

就電話分析器而言，我們會將 `minGram` 設定為 `3`，因為這是我們希望使用者搜尋的最短子字串。 `maxGram` 會設定為 `20` 以確保所有電話號碼 (即使有分機) 都能夠放入單一 n 元。

 n 元的副作用是會傳回一些誤判為真。 我們會在步驟 7 中修正此問題，方法是為不包含 n 元權杖篩選器的搜尋建置不同的分析器。

|輸入|輸出|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>分析器

準備好字元篩選器、權杖化工具和權杖篩選器之後，我們就可以定義分析器了。

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|輸入|輸出|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

請注意，我們現在可以搜尋輸出中的任何權杖。 如果我們的查詢包含這些權杖的任何一項，則會傳回電話號碼。

定義好自訂分析器之後，請重新建立索引，讓自訂分析器可用來在下一個步驟中進行測試。 為了簡單起見，Postman 集合會使用我們定義的分析器來建立名為 `tutorial-first-analyzer` 的新索引。

## <a name="6---test-the-custom-analyzer"></a>6 - 測試自訂分析器

在建立索引後，您現在可以使用下列要求來測試我們所建立的分析器：

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

然後，您就會看到電話號碼產生的權杖集合：

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 - 為查詢建置自訂分析器

使用自訂分析器對索引進行一些範例查詢之後，您會發現重新叫用已改善，而且現在會傳回所有相符的電話號碼。 不過，n 元權杖篩選器也會導致傳回一些誤判為真。 這是 n 元權杖篩選器的常見副作用。

為了避免誤判為真，我們會建立不同的查詢分析器。 此分析器會與我們已建立的分析器相同，但**沒有** `custom_ngram_filter`。

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

在索引定義中，我們接著會同時指定 `indexAnalyzer` 和 `searchAnalyzer`。

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

完成此變更後，您就已準備就緒。 重新建立索引、為資料編制索引，然後再次測試查詢，以確認搜尋如預期般運作。 如果您使用的是 Postman 集合，其會建立名為 `tutorial-second-analyzer` 的第三個索引。

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>替代方法

上述分析器的設計目的是要充分發揮搜尋的彈性。 不過，這樣做的代價是，索引中會儲存許多可能不重要的詞彙。

下列範例顯示也可用於這項工作的不同分析器。 

此分析器的功能良好，但 `14255550100` 之類的輸入資料除外，因為這類資料難以依據邏輯將電話號碼區塊化。 例如，此分析器無法將國碼 (地區碼) `1` 與區域碼 `425` 隔開。 如果使用者未在其搜尋中包含國碼 (地區碼)，這項差異會導致系統未傳回上述數字。

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

您可以在下列範例中看到，電話號碼會分割成您一般會希望使用者搜尋的區塊。

|輸入|輸出|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

根據您的需求，這可能是更有效率的問題解決方法。

## <a name="reset-and-rerun"></a>重設並重新執行

為了簡單起見，本教學課程已讓您建立三個新索引。 不過，在早期開發階段中，先刪除再重新建立索引的情形很常見。 您可以在 Azure 入口網站中刪除索引，也可以使用下列 API 呼叫來進行：

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>重要心得

本教學課程示範了用來建置和測試自訂分析器的程序。 您已建立索引、為資料編制索引，然後針對索引進行查詢，以查看傳回的搜尋結果。 您從該處使用了分析文字 API 來查看語彙分析程序的實際運作過程。

雖然本教學課程中定義的分析器提供了簡單的解決方案來針對電話號碼進行搜尋，但您也可以使用同樣的程序來為您所擁有的任何案例建立自訂的電話分析器。

## <a name="clean-up-resources"></a>清除資源

如果您使用自己的訂用帳戶，當專案結束時，建議您移除不再需要的資源。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

## <a name="next-steps"></a>後續步驟

您已經熟悉如何建立自訂分析器，接下來讓我們看看可供您建置豐富搜尋體驗的各種不同篩選器、權杖化工具和分析器。

> [!div class="nextstepaction"]
> [Azure 認知搜尋中的自訂分析器](index-add-custom-analyzers.md)