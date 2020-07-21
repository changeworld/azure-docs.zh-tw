---
title: 建立搜尋索引
titleSuffix: Azure Cognitive Search
description: 介紹 Azure 認知搜尋中的索引概念和工具，包括架構定義和實體資料結構。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 9e8d1c012ae07fc458a324315e2635f04c3dbd78
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496475"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>在 Azure 認知搜尋中建立基本搜尋索引

在 Azure 認知搜尋中，*搜尋索引*會儲存用於全文檢索和篩選查詢的可搜尋內容。 索引是由架構所定義並儲存至服務，並將資料匯入之後做為第二個步驟。 

索引包含*檔*。 就概念而言，文件是索引中可搜尋資料的單一單位。 零售商可能會有每個產品的檔，而新聞群組織可能會有每篇文章的檔，依此類推。 將這些概念對應到更熟悉的資料庫對等專案：*搜尋索引*等同于*資料表*，而*檔*大致上相當於資料表中的資料*列*。

索引的實體結構是由架構所決定，其中欄位標記為「可搜尋」，因而產生針對該欄位所建立的反向索引。 

您可以使用下列工具和 Api 來建立索引：

* 在 [Azure 入口網站中，使用 [**新增索引**] 或 [匯**入資料**] wizard
* 使用[建立索引（REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)
* 使用[.NET SDK](search-create-index-dotnet.md)

使用入口網站工具更容易學習。 入口網站會強制執行特定資料類型的需求和架構規則，例如不允許數值欄位的全文檢索搜尋功能。 當您擁有可運作的索引之後，就可以使用[Get index （REST API）](https://docs.microsoft.com/rest/api/searchservice/get-index) ，並將它新增至您的方案，藉此從服務中抓取 JSON 定義來轉換成程式碼。

## <a name="recommended-workflow"></a>建議的工作流程

到達最終的索引設計是一種反復的程式。 通常會從入口網站開始建立初始索引，然後切換至程式碼以將索引放在原始檔控制之下。

1. 判斷您是否可以使用 [匯[**入資料**](search-import-data-portal.md)]。 如果來源資料來自[Azure 中支援的資料來源類型](search-indexer-overview.md#supported-data-sources)，則 wizard 會執行所有以索引子為基礎的全文檢索索引。

1. 如果您無法使用匯**入資料**，請從**新增索引**開始，以定義架構。

   ![新增索引命令](media/search-what-is-an-index/add-index.png "新增索引命令")

1. 提供用來唯一識別索引中每個搜尋檔的名稱和金鑰。 索引鍵是必要的，而且必須是 Edm 類型。 在匯入期間，您應該規劃將來源資料中的唯一欄位對應到此欄位。 

   入口網站會提供您 `id` 金鑰的欄位。 若要覆寫預設值 `id` ，請建立新的欄位（例如，名為的新欄位定義 `HotelId` ），然後在 [**金鑰**] 中選取它。

   ![填入必要的屬性](media/search-what-is-an-index//field-attributes.png "填入必要的屬性")

1. 新增更多欄位。 入口網站會顯示哪些[欄位屬性](#index-attributes)適用于不同的資料類型。 如果您還不熟悉索引設計，這很有幫助。

   如果內送資料本質上為階層式，請指派[複雜型](search-howto-complex-data-types.md)別資料型別來代表嵌套的結構。 內建的範例資料集飯店會使用位址（包含多個子欄位）來說明複雜型別，這些型別與每個飯店具有一對一關聯性，還有一個會議室複雜的集合，其中有多個房間與每個飯店相關聯。 

1. 建立索引之前，請先將任何[分析器](#analyzers)指派給字串欄位。 如果您想要在特定欄位上啟用自動完成功能，請針對[建議工具](#suggesters)執行相同動作。

1. 按一下 [**建立**]，在您的搜尋服務中建立實體結構。

1. 建立索引之後，請使用其他命令來審查定義或加入更多元素。

   ![依資料類型顯示內容的新增索引頁面](media/search-what-is-an-index//field-definitions.png "依資料類型顯示內容的新增索引頁面")

1. 使用[Get index （REST API）](https://docs.microsoft.com/rest/api/searchservice/get-index)和 web 測試控管（例如[Postman](search-get-started-postman.md)）來下載索引架構。 您現在有可針對程式碼調整的索引 JSON 標記法。

1. [隨著資料載入您的索引](search-what-is-data-import.md)。 Azure 認知搜尋會接受 JSON 檔。 若要以程式設計方式載入資料，您可以使用 Postman 搭配要求承載中的 JSON 文件。 如果您的資料不會輕易地表示為 JSON，此步驟將最耗費人力。 

    一旦使用資料載入索引之後，對現有欄位進行大部分的編輯，就會要求您卸載並重建索引。

1. 在您開始查看您所預期的結果之前，查詢索引、檢查結果，以及進一步逐一查看索引結構描述。 您可以使用[**搜尋總管**](search-explorer.md)或 Postman 來查詢索引。

在開發期間，請經常規劃重建。 因為實體結構是在服務中建立，所以對現有欄位定義的大部分修改而言，都需要卸載和重新建立[索引](search-howto-reindex.md)。 您可以考慮處理部份的資料，讓重建更快速。 

> [!Tip]
> 建議使用程式碼，而不是入口網站方法，以同時處理索引設計和資料匯入。 另外，當開發專案仍在早期階段時，[Postman 和 REST API](search-get-started-postman.md) 之類的工具對於概念證明測試很有幫助。 您可以對要求主體中的索引定義進行累加變更，然後將要求傳送至您的服務，以使用更新後的結構描述來重建索引。

## <a name="index-schema"></a>索引架構

在 fields 集合中，需要有一個名稱和一個指定的索引鍵欄位（也就是 Edm. string）。 「[欄位集合](#fields-collection)」** 通常是索引中最大的一部分，其中每個欄位都會具有名稱、類型和屬性，以及可決定其使用方式的可允許行為。 

其他元素包括[建議工具](#suggesters)、[評分設定檔](#scoringprofiles)、用來根據語言規則或分析器支援的其他特性將字串處理成標記的[分析器](#analyzers)，以及[跨原始遠端腳本（CORS）](#corsoptions)設定。

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>欄位集合和欄位屬性

欄位具有名稱、用來分類儲存資料的類型，以及指定如何使用欄位的屬性。

### <a name="data-types"></a>資料類型

| 類型 | 描述 |
|------|-------------|
| Edm.String |可選擇性予以 Token 化以供進行全文檢索搜尋 (斷字、詞幹分析等) 的文字。 |
| Collection(Edm.String) |可選擇性予以 Token 化以供進行全文檢索搜尋的字串清單。 理論上，集合中的項目數沒有上限，但集合的承載大小有 16 MB 的上限。 |
| Edm.Boolean |包含 true/false 值。 |
| Edm.Int32 |32 位元整數值。 |
| Edm.Int64 |64 位元整數值。 |
| Edm.Double |雙精度數值資料。 |
| Edm.DateTimeOffset |以 OData V4 格式表示的日期時間值 (例如 `yyyy-MM-ddTHH:mm:ss.fffZ` 或 `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`)。 |
| Edm.GeographyPoint |代表地球上地理位置的一點。 |

如需詳細資訊，請參閱[支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)。

<a name="index-attributes"></a>

### <a name="attributes"></a>屬性

欄位屬性會決定欄位的使用方式，例如，是否將它用於全文檢索搜尋、多面向導覽、排序作業等。 

字串欄位通常會標示為「可搜尋」和「可抓取」。 用來縮小搜尋結果的欄位包括「可排序」、「可篩選」和「facetable」。

|屬性|描述|  
|---------------|-----------------|  
|線上 |可進行全文檢索搜尋，受限於語彙分析，例如，在編製索引期間執行斷字功能。 如果您為可搜尋的欄位設定像是「sunny day」的值，則系統會在內部將它分割為「sunny」和「day」這兩個個別的語彙基元。 如需詳細資訊，請參閱[全文檢索搜尋如何運作](search-lucene-query-architecture.md)。|  
|篩選 |在 $filter 查詢中加以參考。 類型 `Edm.String` 或 `Collection(Edm.String)` 的可篩選欄位不會執行斷字功能，因此，只會針對完全相符的項目進行比較。 例如，如果您將欄位 f 之類的欄位設為「sunny day」，`$filter=f eq 'sunny'` 就會找不到相符項目，但 `$filter=f eq 'sunny day'` 可以。 |  
|分類 |根據預設，系統會依分數來排序結果，但您可根據文件中的欄位設定排序。 類型的欄位 `Collection(Edm.String)` 不可以是「可排序」。 |  
|facetable |通常用來呈現搜尋結果，其中包含依類別的叫用次數 (例如，特定城市中的旅館)。 此選項無法與類型 `Edm.GeographyPoint`的欄位搭配使用。 `Edm.String`可篩選、「可排序」或「facetable」類型的欄位長度最多為 32 kb。 如需詳細資訊，請參閱[建立索引 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)。|  
|擊鍵 |索引內文件的唯一識別碼。 您必須只選擇一個欄位作為索引鍵欄位，而它的類型必須是 `Edm.String`。|  
|可擷取 |判斷搜尋結果中是否會傳回該欄位。 當您想要使用某個欄位 (例如 profit margin**) 作為篩選、排序或評分機制，但不想讓使用者看見該欄位時，這非常有用。 針對 `true` for `key` 。|  

雖然您可以隨時新增欄位，但現有的欄位定義會在索引的存留期間加以鎖定。 基於這個理由，開發人員通常會使用入口網站來建立簡單的索引、測試概念，或使用管理入口網站頁面來查詢設定。 如果您遵循以程式碼為基礎的方法，則整個索引設計中頻繁的反覆動作就會更有效率，如此便能讓您輕鬆地重建索引。

> [!NOTE]
> 您用來建立索引的 Api 有不同的預設行為。 對於[REST api](https://docs.microsoft.com/rest/api/searchservice/Create-Index)，大部分的屬性預設為啟用（例如，[可搜尋] 和 [可抓取] 對於字串欄位而言為 true），而且您通常只需要設定它們（如果您想要將它們關閉）。 針對 .NET SDK，相反的為 true。 在您未明確設定的任何屬性上，除非您特別啟用，否則預設會停用對應的搜尋行為。

## `analyzers`

分析器元素會設定要用於欄位之語言分析器的名稱。 如需可供您使用之分析器範圍的詳細資訊，請參閱[將分析器新增至 Azure 認知搜尋索引](search-analyzers.md)。 分析器只能搭配可搜尋的欄位使用。 將分析器指派給欄位後，除非重建索引，否則無法變更。

## `suggesters`

建議工具是結構描述的區段，其定義索引中有哪些欄位會被用來支援搜尋中的自動完成或預先輸入查詢。 一般而言，部分搜尋字串會在使用者輸入搜尋查詢時傳送至[建議（REST API）](https://docs.microsoft.com/rest/api/searchservice/suggestions) ，而 API 會傳回一組建議的檔或片語。 

新增至建議工具的欄位用來建置自動完成搜尋字詞。 所有的搜尋字詞都建立於編製索引期間，且分開儲存。 如需有關建立建議工具結構的詳細資訊，請參閱[新增建議工具](index-add-suggesters.md)。

## `corsOptions`

用戶端的 JavaScript 預設無法呼叫任何 API，因為瀏覽器會阻止所有跨原始來源的要求。 若要允許對索引進行跨原始來源查詢，請設定 **corsOptions** 屬性來啟用 CORS (跨原始來源資源共用)。 基於安全緣故，僅查詢 API 才能支援 CORS。 

您可以為 CORS 設定下列選項：

+ **allowedOrigins** （必要）：這是將授與您索引存取權的原始來源清單。 這表示將允許來自那些原始來源的所有 JavaScript 程式碼查詢您的索引 (假設它能提供正確的 API 金鑰)。 每個原始來源的形式通常是 `protocol://<fully-qualified-domain-name>:<port>` (儘管經常會忽略 `<port>`)。 請參閱[跨原始來源資源共用](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)以取得詳細資料。

  如果您想要允許所有原始來源進行存取，請在 **allowedOrigins** 陣列中包含 `*` 作為單一項目。 「此做法並不建議用於生產環境搜尋服務」**，但對於開發和偵錯通常很有幫助。

+ **maxAgeInSeconds** （選用）：瀏覽器會使用此值來判斷快取 CORS 預檢回應的持續時間（以秒為單位）。 這必須是非負數的整數。 這個值越大，效能就越好，但是讓 CORS 原則變更生效的時間也就越長。 若未設定，即會使用預設持續期間 5 分鐘。

## `scoringProfiles`

[評分設定檔](index-add-scoring-profiles.md)是結構描述的區段，能定義自訂評分行為，讓您能夠影響搜尋結果中哪些項目的出現機率會比較高。 評分設定檔是由欄位權數和函式所組成。 若要使用它們，您可以在查詢字串上以名稱指定設定檔。

預設的評分設定檔會在背景運行，以針對結果集中的每個項目計算搜尋分數。 您可以使用內部未命名的評分設定檔。 或者，設定 **defaultScoringProfile** 以使用自訂設定檔做為預設值，以在查詢字串中沒有指定自訂設定檔時叫用。

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>屬性和索引大小（儲存體含意）

索引的大小取決於您上傳的檔案大小，加上索引設定，例如您是否包含建議工具，以及如何在個別欄位上設定屬性。 

下列螢幕擷取畫面說明各種屬性組合所產生的索引儲存模式。 索引是以**房地產範例索引**為基礎，您可以使用 [匯入資料] wizard 輕鬆建立。 雖然未顯示索引結構描述，但您可以根據索引名稱推斷屬性。 例如， *realestate*可搜尋的索引已選取 [可搜尋] 屬性，而不是其他任何選項， *realestate*可抓取索引已選取 [可抓取] 屬性，而不是其他等等。

![以屬性選取專案為基礎的索引大小](./media/search-what-is-an-index/realestate-index-size.png "以屬性選取專案為基礎的索引大小")

雖然這些是人工的索引變體，但我們可以參考這些變體，以便廣泛比較屬性如何影響儲存體。 設定「已抓取」是否會增加索引大小？ 否。 將欄位新增至**建議工具**會增加索引大小嗎？ 是。

支援篩選和排序的索引會按比例大於僅支援全文檢索搜尋的索引。 這是因為篩選和排序作業會掃描完全相符的專案，而需要有逐字的文字字串。 相較之下，支援全文檢索查詢的可搜尋欄位會使用反向索引，其中填入的 token 化詞彙會耗用比整個檔少的空間。 

> [!Note]
> 儲存體架構會被視為 Azure 認知搜尋的執行詳細資料，而且可能會變更，恕不另行通知。 不保證未來會保存目前的行為。

## <a name="next-steps"></a>後續步驟

在了解索引的結構之後，您接著便可以在入口網站中建立第一個索引。 我們建議您從 [匯**入資料**] wizard 開始，選擇 [ *realestate-us-範例*] 或 [*飯店範例*] 裝載的資料來源。

> [!div class="nextstepaction"]
> [匯入資料 wizard （入口網站）](search-get-started-portal.md)

對於這兩個資料集，此 wizard 可以推斷索引架構、匯入資料，並輸出可搜尋的索引，您可以使用 Search Explorer 來進行查詢。 在 [匯**入資料**] wizard 的 [**連接到您的資料**] 頁面中，尋找這些資料來源。

   ![建立範例索引](media/search-what-is-an-index//import-wizard-sample-data.png "建立範例索引")