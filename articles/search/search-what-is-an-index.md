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
ms.openlocfilehash: 3aa4a1917711f8997c282ba577c33e7a7f94472b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932877"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>在 Azure 認知搜尋中建立基本搜尋索引

在 Azure 認知搜尋中， *搜尋索引* 會儲存用於全文檢索和篩選查詢的可搜尋內容。 索引是由架構定義並儲存至服務，並在第二個步驟之後將資料匯入。 

索引包含 *檔*。 就概念而言，文件是索引中可搜尋資料的單一單位。 零售商可能會有每項產品的檔、新聞群組織可能會有每篇文章的檔，依此類推。 將這些概念對應到更熟悉的資料庫對等： *搜尋索引* 等同于 *資料表*， *檔* 大致相當於資料表中的資料 *列* 。

索引的實體結構取決於架構，而標示為「可搜尋」的欄位則會產生為該欄位建立的反向索引。 

您可以使用下列工具和 Api 來建立索引：

* 在 Azure 入口網站中，使用 [ **新增索引** ] 或 [匯 **入資料** ] wizard
* 使用 [Create Index (REST API) ](/rest/api/searchservice/create-index)
* 使用 [.NET SDK](./search-get-started-dotnet.md)

使用入口網站工具可以更輕鬆地學習。 入口網站會強制執行特定資料類型的需求和架構規則，例如在數值欄位上不允許全文檢索搜尋功能。 一旦有了可使用的索引之後，您就可以使用 [Get index (REST API) ](/rest/api/searchservice/get-index) ，並將其新增至您的方案，藉由從服務中取出 JSON 定義來轉換成程式碼。

## <a name="recommended-workflow"></a>建議的工作流程

到達最終的索引設計是一種反復的流程。 通常會從入口網站開始建立初始索引，然後切換至程式碼以將索引放在原始檔控制之下。

1. 判斷您是否可以使用匯 [**入資料**](search-import-data-portal.md)。 如果來源資料來自 [Azure 中支援的資料來源類型](search-indexer-overview.md#supported-data-sources)，則嚮導會執行多個以索引子為基礎的索引。

1. 如果您無法使用 [匯 **入資料**]，請從 [ **新增索引** ] 開始定義架構。

   ![新增索引命令](media/search-what-is-an-index/add-index.png "新增索引命令")

1. 提供用來唯一識別索引中每個搜尋檔的名稱和金鑰。 索引鍵是必要的，而且必須是 Edm 類型。 匯入期間，您應該規劃將來源資料中的唯一欄位對應到此欄位。 

   入口網站會為您提供索引 `id` 鍵的欄位。 若要覆寫預設值 `id` ，請建立新的欄位 (例如，新的欄位定義稱為 `HotelId`) ，然後在索引 **鍵**中選取它。

   ![填寫必要屬性](media/search-what-is-an-index//field-attributes.png "填寫必要屬性")

1. 加入更多欄位。 入口網站會顯示有哪些 [欄位屬性](#index-attributes) 可用於不同的資料類型。 如果您還不熟悉索引設計，這很有幫助。

   如果內送資料在本質上是階層式的，請指派 [複雜類型](search-howto-complex-data-types.md) 資料類型來表示嵌套結構。 內建的範例資料集（旅館）說明使用位址 (包含多個子欄位) 與每個飯店有一對一關聯性的子欄位，以及一個房間複雜的集合，其中有多個房間與每個飯店相關聯。 

1. 建立索引之前，請先將任何 [分析器](#analyzers) 指派給字串欄位。 如果您想要在特定欄位上啟用自動完成功能，請對 [建議工具](#suggesters) 進行相同的動作。

1. 按一下 [ **建立** ]，在您的搜尋服務中建立實體結構。

1. 建立索引之後，請使用其他命令來檢查定義或新增更多元素。

   ![依資料類型顯示內容的新增索引頁面](media/search-what-is-an-index//field-definitions.png "依資料類型顯示內容的新增索引頁面")

1. 使用 [Get index (REST API) ](/rest/api/searchservice/get-index) 和 web 測試控管（例如 [Postman](search-get-started-postman.md)）下載索引架構。 您現在有可針對程式碼調整之索引的 JSON 標記法。

1. [隨著資料載入您的索引](search-what-is-data-import.md)。 Azure 認知搜尋接受 JSON 檔。 若要以程式設計方式載入資料，您可以使用 Postman 搭配要求承載中的 JSON 文件。 如果您的資料不會輕易地表示為 JSON，此步驟將最耗費人力。 

    使用資料載入索引之後，對現有欄位的大部分編輯都需要您卸載並重建索引。

1. 在您開始查看您所預期的結果之前，查詢索引、檢查結果，以及進一步逐一查看索引結構描述。 您可以使用[**搜尋總管**](search-explorer.md)或 Postman 來查詢索引。

在開發期間，請規劃頻繁的重建。 因為實體結構是在服務中建立的，所以需要卸載和重新建立 [索引](search-howto-reindex.md) ，才能對現有的欄位定義進行大部分的修改。 您可以考慮處理部份的資料，讓重建更快速。 

> [!Tip]
> 建議使用程式碼（而不是入口網站方法）來同時處理索引設計和資料匯入。 另外，當開發專案仍在早期階段時，[Postman 和 REST API](search-get-started-postman.md) 之類的工具對於概念證明測試很有幫助。 您可以對要求主體中的索引定義進行累加變更，然後將要求傳送至您的服務，以使用更新後的結構描述來重建索引。

## <a name="index-schema"></a>索引架構

索引必須要有一個名稱和一個指定的索引鍵欄位 (在 fields 集合中) 的 Edm。 「[欄位集合](#fields-collection)」** 通常是索引中最大的一部分，其中每個欄位都會具有名稱、類型和屬性，以及可決定其使用方式的可允許行為。 

其他元素包括 [建議工具](#suggesters)、 [評分設定檔](#scoringprofiles)、用來根據分析器所支援的語言規則或其他特性，將字串處理至標記的 [分析器](#analyzers) ，以及 [跨原始的遠端腳本 (CORS) ](#corsoptions) 設定。

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

欄位具有名稱、分類儲存資料的類型，以及指定如何使用欄位的屬性。

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

如需詳細資訊，請參閱 [支援的資料類型](/rest/api/searchservice/Supported-data-types)。

<a name="index-attributes"></a>

### <a name="attributes"></a>屬性

欄位屬性會決定欄位的使用方式，例如，是否將它用於全文檢索搜尋、多面向導覽、排序作業等。 

字串欄位通常會標示為「可搜尋」和「可供取出」。 用來縮小搜尋結果的欄位包括「可排序」、「可篩選」和「可 facet」。

|屬性|說明|  
|---------------|-----------------|  
|供 |可進行全文檢索搜尋，受限於語彙分析，例如，在編製索引期間執行斷字功能。 如果您為可搜尋的欄位設定像是「sunny day」的值，則系統會在內部將它分割為「sunny」和「day」這兩個個別的語彙基元。 如需詳細資訊，請參閱[全文檢索搜尋如何運作](search-lucene-query-architecture.md)。|  
|篩選 |在 $filter 查詢中加以參考。 類型 `Edm.String` 或 `Collection(Edm.String)` 的可篩選欄位不會執行斷字功能，因此，只會針對完全相符的項目進行比較。 例如，如果您將欄位 f 之類的欄位設為「sunny day」，`$filter=f eq 'sunny'` 就會找不到相符項目，但 `$filter=f eq 'sunny day'` 可以。 |  
|8601 |根據預設，系統會依分數來排序結果，但您可根據文件中的欄位設定排序。 類型的欄位 `Collection(Edm.String)` 不可以是「可排序」。 |  
|可 facet |通常用來呈現搜尋結果，其中包含依類別的叫用次數 (例如，特定城市中的旅館)。 此選項無法與類型 `Edm.GeographyPoint`的欄位搭配使用。 `Edm.String`可以篩選的型別字段、「可排序」或「可 facet」的長度最多可達 32 kb。 如需詳細資訊，請參閱[建立索引 (REST API)](/rest/api/searchservice/create-index)。|  
|主金鑰 |索引內文件的唯一識別碼。 您必須只選擇一個欄位作為索引鍵欄位，而它的類型必須是 `Edm.String`。|  
|可擷取 |判斷搜尋結果中是否會傳回該欄位。 當您想要使用某個欄位 (例如 profit margin**) 作為篩選、排序或評分機制，但不想讓使用者看見該欄位時，這非常有用。 針對 `true` for `key` 。|  

雖然您可以隨時新增欄位，但現有的欄位定義會在索引的存留期間加以鎖定。 基於這個理由，開發人員通常會使用入口網站來建立簡單的索引、測試概念，或使用管理入口網站頁面來查詢設定。 如果您遵循以程式碼為基礎的方法，則整個索引設計中頻繁的反覆動作就會更有效率，如此便能讓您輕鬆地重建索引。

> [!NOTE]
> 您用來建立索引的 Api 具有不同的預設行為。 針對 [REST api](/rest/api/searchservice/Create-Index)，預設會啟用大部分的屬性 (例如，「可搜尋」和「可取得」適用于字串欄位) ，而且如果您想要關閉它們，通常只需要設定這些屬性。 針對 .NET SDK，相反的情況是如此。 在任何您未明確設定的屬性上，預設為停用對應的搜尋行為，除非您特別啟用它。

## `analyzers`

分析器元素會設定要用於欄位之語言分析器的名稱。 如需可供您使用之分析器範圍的詳細資訊，請參閱 [將分析器新增至 Azure 認知搜尋索引](search-analyzers.md)。 分析器只能搭配可搜尋的欄位使用。 將分析器指派給欄位後，除非重建索引，否則無法變更。

## `suggesters`

建議工具是結構描述的區段，其定義索引中有哪些欄位會被用來支援搜尋中的自動完成或預先輸入查詢。 一般而言，部分搜尋字串會傳送至 [建議 (REST API) ](/rest/api/searchservice/suggestions) 在使用者輸入搜尋查詢時，API 會傳回一組建議的檔或片語。 

新增至建議工具的欄位用來建置自動完成搜尋字詞。 所有的搜尋字詞都建立於編製索引期間，且分開儲存。 如需有關建立建議工具結構的詳細資訊，請參閱[新增建議工具](index-add-suggesters.md)。

## `corsOptions`

用戶端的 JavaScript 預設無法呼叫任何 API，因為瀏覽器會阻止所有跨原始來源的要求。 若要允許對索引進行跨原始來源查詢，請設定 **corsOptions** 屬性來啟用 CORS (跨原始來源資源共用)。 基於安全緣故，僅查詢 API 才能支援 CORS。 

您可以為 CORS 設定下列選項：

+ **allowedOrigins** (必要) ：這是將授與您索引存取權的來源清單。 這表示將允許來自那些原始來源的所有 JavaScript 程式碼查詢您的索引 (假設它能提供正確的 API 金鑰)。 每個原始來源的形式通常是 `protocol://<fully-qualified-domain-name>:<port>` (儘管經常會忽略 `<port>`)。 請參閱[跨原始來源資源共用](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)以取得詳細資料。

  如果您想要允許所有原始來源進行存取，請在 **allowedOrigins** 陣列中包含 `*` 作為單一項目。 「此做法並不建議用於生產環境搜尋服務」**，但對於開發和偵錯通常很有幫助。

+ **maxAgeInSeconds** (選擇性) ：瀏覽器會使用此值來判斷快取 CORS 預檢回應的持續時間 (秒數) 。 這必須是非負數的整數。 這個值越大，效能就越好，但是讓 CORS 原則變更生效的時間也就越長。 若未設定，即會使用預設持續期間 5 分鐘。

## `scoringProfiles`

[評分設定檔](index-add-scoring-profiles.md)是結構描述的區段，能定義自訂評分行為，讓您能夠影響搜尋結果中哪些項目的出現機率會比較高。 評分設定檔是由欄位權數和函式所組成。 若要使用它們，您可以在查詢字串上以名稱指定設定檔。

預設的評分設定檔會在背景運行，以針對結果集中的每個項目計算搜尋分數。 您可以使用內部未命名的評分設定檔。 或者，設定 **defaultScoringProfile** 以使用自訂設定檔做為預設值，以在查詢字串中沒有指定自訂設定檔時叫用。

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a> (儲存體影響的屬性和索引大小) 

索引的大小取決於您上傳的檔案大小，加上索引設定，例如您是否包含建議工具，以及如何在個別欄位上設定屬性。 

下列螢幕擷取畫面說明各種屬性組合所產生的索引儲存模式。 索引是以 **房地產範例索引**為基礎，您可以使用 [匯入資料] 嚮導輕鬆地建立此索引。 雖然未顯示索引結構描述，但您可以根據索引名稱推斷屬性。 例如， *realestate* 可搜尋的索引已選取 [可搜尋] 屬性，而沒有任何其他專案、 *realestate* 可取得的索引已選取 [可取得] 屬性，而沒有任何其他專案，依此類推。

![以屬性選取專案為基礎的索引大小](./media/search-what-is-an-index/realestate-index-size.png "以屬性選取專案為基礎的索引大小")

雖然這些是人工的索引變體，但我們可以參考這些變體，以便廣泛比較屬性如何影響儲存體。 設定「已抓取」是否增加索引大小？ 否。 將欄位新增至 **建議工具** 會增加索引大小嗎？ 是。

支援篩選和排序的索引會依比例大於僅支援全文檢索搜尋的索引。 這是因為篩選和排序作業會掃描是否有完全相符的專案，而需要存在逐字文字字串。 相較之下，支援全文檢索查詢的可搜尋欄位會使用反向索引，這些索引會以不含整份檔空間的 token 化詞彙來填入。 

> [!Note]
> 儲存體架構被視為 Azure 認知搜尋的執行詳細資料，而且可能會變更，恕不另行通知。 不保證未來會保存目前的行為。

## <a name="next-steps"></a>接下來的步驟

在了解索引的結構之後，您接著便可以在入口網站中建立第一個索引。 建議您從 [匯 **入資料** ] 嚮導開始，選擇 [ *realestate-範例* ] 或 [ *旅館-範例* 託管資料來源]。

> [!div class="nextstepaction"]
> [匯入資料 wizard (入口網站) ](search-get-started-portal.md)

針對這兩個資料集，wizard 可以推斷索引架構、匯入資料，以及輸出可搜尋的索引，您可以使用 [搜尋] Explorer 進行查詢。 在 [匯**入資料**] 嚮導的 [**連接到您的資料**] 頁面中，找到這些資料來源。

   ![建立範例索引](media/search-what-is-an-index//import-wizard-sample-data.png "建立範例索引")