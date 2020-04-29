---
title: 建立索引定義和概念
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋中的索引詞彙和概念簡介，包括元件部分和實體結構。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282779"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>在 Azure 認知搜尋中建立基本索引

在「Azure 認知搜尋」中，「*索引*」是一種持續性的*檔*和其他結構的存放區，用於 Azure 認知搜尋服務上的篩選和全文檢索搜尋。 就概念而言，文件是索引中可搜尋資料的單一單位。 例如，電子商務零售商可能會有儲存了每個銷售項目的文件、新聞組織可能會有儲存了每篇文章的文件，類似情況不一而足。 對應這些概念到更熟悉的資料庫同等項目：索引** 在概念上類似於資料表**，而文件** 大致上相當於資料表中的資料列**。

當您新增或上傳索引時，Azure 認知搜尋會根據您提供的架構來建立實體結構。 例如，如果您索引中的某個欄位被標記為可搜尋，系統便會針對該欄位建立反向的索引。 之後，當您新增或上傳檔，或提交搜尋查詢到 Azure 認知搜尋時，您會將要求傳送到搜尋服務中的特定索引。 載入具有文件值的欄位，被稱為「編製索引」** 或資料擷取。

您可以在入口網站、[REST API](search-create-index-rest-api.md) 或 [.NET SDK](search-create-index-dotnet.md)中建立索引。

## <a name="recommended-workflow"></a>建議的工作流程

若要達到正確的索引設計，通常可透過多次反覆運算達成。 搭配使用各項工具和 API，可協助您快速完成設計。

1. 判斷您是否可以使用[索引子](search-indexer-overview.md#supported-data-sources)。 如果您的外部資料是其中一個支援的資料來源，您可以使用[**匯入資料**](search-import-data-portal.md)精靈來建立原型並載入索引。

2. 如果您無法使用 [匯入資料]****，仍然可以[在入口網站中建立初始索引](search-create-index-portal.md)，以及使用 [新增索引]**** 頁面上的控制項來新增欄位、資料類型和指派屬性。 入口網站會顯示不同的資料類型適用哪些屬性。 如果您還不熟悉索引設計，這很有幫助。

   ![依資料類型顯示內容的新增索引頁面](media/search-create-index-portal/field-attributes.png "依資料類型顯示內容的新增索引頁面")
  
   當您按一下 [建立]**** 時，所有支援您索引的實體結構都會建立於您的搜尋服務中。

3. 使用[取得索引 REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) 和 [Postman](search-get-started-postman.md) 之類的 Web 測試工具，下載索引結構描述。 對於您在入口網站中建立的索引，您現在有其 JSON 表示法。 

   您會在此時切換到程式碼式方法。 入口網站不適合用于反復專案，因為您無法編輯已建立的索引。 但您可以使用 Postman 和 REST 進行剩餘的工作。

4. [隨著資料載入您的索引](search-what-is-data-import.md)。 Azure 認知搜尋會接受 JSON 檔。 若要以程式設計方式載入資料，您可以使用 Postman 搭配要求承載中的 JSON 文件。 如果您的資料不會輕易地表示為 JSON，此步驟將最耗費人力。

5. 在您開始查看您所預期的結果之前，查詢索引、檢查結果，以及進一步逐一查看索引結構描述。 您可以使用[**搜尋總管**](search-explorer.md)或 Postman 來查詢索引。

6. 繼續使用程式碼來逐一查看您的設計。  

因為實體結構是在服務中建立，所以每當您對現有欄位定義進行材質變更時，都必須卸載和重新建立[索引](search-howto-reindex.md)。 這表示在開發期間，您應該規劃經常重建。 您可以考慮處理部份的資料，讓重建更快速。 

建議將程式碼 (而非入口網站方法) 用於反覆式設計。 如果您依賴入口網站進行索引定義，則必須在每次重建時填妥索引定義。 另外，當開發專案仍在早期階段時，[Postman 和 REST API](search-get-started-postman.md) 之類的工具對於概念證明測試很有幫助。 您可以對要求主體中的索引定義進行累加變更，然後將要求傳送至您的服務，以使用更新後的結構描述來重建索引。

## <a name="components-of-an-index"></a>索引的元件

方式，Azure 認知搜尋索引是由下列元素所組成。 

「[欄位集合](#fields-collection)」** 通常是索引中最大的一部分，其中每個欄位都會具有名稱、類型和屬性，以及可決定其使用方式的可允許行為。 其他元素包括[建議工具](#suggesters)、[評分設定檔](#scoring-profiles)、具有元件部分的[分析器](#analyzers)，以支援自訂、 [CORS](#cors)和[加密金鑰](#encryption-key)選項。

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

您在定義結構描述時必須指定索引中每個欄位的名稱、類型和屬性。 欄位類型可分類該欄位中儲存的資料。 個別欄位上設定的屬性可指定使用欄位的方式。 下列幾個資料表列舉您可以指定的類型和屬性。

### <a name="data-types"></a>資料類型
| 類型 | 描述 |
| --- | --- |
| *Edm.String* |可選擇性予以 Token 化以供進行全文檢索搜尋 (斷字、詞幹分析等) 的文字。 |
| *Collection （Edm 字串）* |可選擇性予以 Token 化以供進行全文檢索搜尋的字串清單。 理論上，集合中的項目數沒有上限，但集合的承載大小有 16 MB 的上限。 |
| *Edm.Boolean* |包含 true/false 值。 |
| *Edm.Int32* |32 位元整數值。 |
| *Edm.Int64* |64 位元整數值。 |
| *Edm.Double* |雙精度數值資料。 |
| *Edm.DateTimeOffset* |以 OData V4 格式表示的日期時間值 (例如 `yyyy-MM-ddTHH:mm:ss.fffZ` 或 `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`)。 |
| *Edm.GeographyPoint* |代表地球上地理位置的一點。 |

您可以在這裡找到更多有關 Azure 認知搜尋[支援之資料類型](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)的詳細資訊。

### <a name="index-attributes"></a>索引屬性

您的索引中只有一個欄位必須是指定為唯一識別每份檔的索引**鍵**欄位。

其他屬性會決定欄位在應用程式中的使用方式。 例如，可搜尋**的屬性會**指派給應該包含在全文檢索搜尋中的每個欄位。 

您用來建立索引的 Api 有不同的預設行為。 對於[REST api](https://docs.microsoft.com/rest/api/searchservice/Create-Index)，大部分的屬性預設為啟用（例如，字串欄位**的可搜尋和可供**搜尋 **），而且**您通常只需要設定它們（如果您想要將它們關閉）。 針對 .NET SDK，相反的為 true。 在您未明確設定的任何屬性上，除非您特別啟用，否則預設會停用對應的搜尋行為。

| 屬性 | 描述 |
| --- | --- |
| `key` |字串，提供每一份文件的唯一識別碼，用於查閱文件。 每個索引必須有一個索引鍵。 只有一個欄位可以做為索引鍵，而且其類型必須設定為 Edm.String。 |
| `retrievable` |指定搜尋結果中是否可傳回某欄位。 |
| `filterable` |允許欄位用於篩選查詢。 |
| `Sortable` |允許查詢使用此欄位排序搜尋結果。 |
| `facetable` |允許欄位用於使用者自我引導篩選的 [多面向導覽](search-faceted-navigation.md) 結構中。 通常，欄位若包含您可以用來將多份文件群組在一起的重複值 (例如，落在單一品牌或服務類別目錄下的多份文件)，最適合做為 Facet。 |
| `searchable` |將欄位標記為可供全文檢索。 |

## <a name="index-size"></a>索引大小

索引的大小取決於您上傳的檔案大小，加上索引設定，例如您是否包含建議工具，以及如何在個別欄位上設定屬性。 下列螢幕擷取畫面說明各種屬性組合所產生的索引儲存模式。

索引是以內建的[房地產範例](search-get-started-portal.md)資料來源為基礎，您可以在入口網站中編制索引和查詢。 雖然未顯示索引結構描述，但您可以根據索引名稱推斷屬性。 例如，*realestate 可搜尋*索引僅只選取 **searchable** 屬性，*realestate 可擷取*索引僅只選取 **retrievable** 屬性。

![以屬性選取專案為基礎的索引大小](./media/search-what-is-an-index/realestate-index-size.png "以屬性選取專案為基礎的索引大小")

雖然這些是人工的索引變體，但我們可以參考這些變體，以便廣泛比較屬性如何影響儲存體。 設定 **retrievable** 是否會增加索引大小？ 否。 將欄位新增至**建議工具**是否會增加索引大小？ 是。

支援篩選和排序的索引會按比例大於僅支援全文檢索搜尋的索引。 篩選和排序作業會掃描是否有完全相符的專案，需要有不完整的檔。 相較之下，支援全文檢索和模糊搜尋的可搜尋欄位會使用倒置索引，其中會填入比完整文件取用較少空間的權杖化字詞。 

> [!Note]
> 儲存體架構會被視為 Azure 認知搜尋的執行詳細資料，而且可能會變更，恕不另行通知。 不保證未來會保存目前的行為。

## <a name="suggesters"></a>建議工具
建議工具是結構描述的區段，其定義索引中有哪些欄位會被用來支援搜尋中的自動完成或預先輸入查詢。 一般而言，部分搜尋字串會在使用者輸入搜尋查詢時傳送至[建議（REST API）](https://docs.microsoft.com/rest/api/searchservice/suggestions) ，而 API 會傳回一組建議的檔或片語。 

新增至建議工具的欄位用來建置自動完成搜尋字詞。 所有的搜尋字詞都建立於編製索引期間，且分開儲存。 如需有關建立建議工具結構的詳細資訊，請參閱[新增建議工具](index-add-suggesters.md)。

## <a name="scoring-profiles"></a>評分設定檔

[評分設定檔](index-add-scoring-profiles.md)是結構描述的區段，能定義自訂評分行為，讓您能夠影響搜尋結果中哪些項目的出現機率會比較高。 評分設定檔是由欄位權數和函式所組成。 若要使用它們，您可以在查詢字串上以名稱指定設定檔。

預設的評分設定檔會在背景運行，以針對結果集中的每個項目計算搜尋分數。 您可以使用內部未命名的評分設定檔。 或者，設定 **defaultScoringProfile** 以使用自訂設定檔做為預設值，以在查詢字串中沒有指定自訂設定檔時叫用。

## <a name="analyzers"></a>分析器

分析器元素會設定要用於欄位之語言分析器的名稱。 如需可供您使用之分析器範圍的詳細資訊，請參閱[將分析器新增至 Azure 認知搜尋索引](search-analyzers.md)。 分析器只能搭配可搜尋的欄位使用。 將分析器指派給欄位後，除非重建索引，否則無法變更。

## <a name="cors"></a>CORS

用戶端的 JavaScript 預設無法呼叫任何 API，因為瀏覽器會阻止所有跨原始來源的要求。 若要允許對索引進行跨原始來源查詢，請設定 **corsOptions** 屬性來啟用 CORS (跨原始來源資源共用)。 基於安全緣故，僅查詢 API 才能支援 CORS。 

您可以為 CORS 設定下列選項：

+ **allowedOrigins** （必要）：這是將授與您索引存取權的原始來源清單。 這表示將允許來自那些原始來源的所有 JavaScript 程式碼查詢您的索引 (假設它能提供正確的 API 金鑰)。 每個原始來源的形式通常是 `protocol://<fully-qualified-domain-name>:<port>` (儘管經常會忽略 `<port>`)。 請參閱[跨原始來源資源共用](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)以取得詳細資料。

  如果您想要允許所有原始來源進行存取，請在 **allowedOrigins** 陣列中包含 `*` 作為單一項目。 「此做法並不建議用於生產環境搜尋服務」**，但對於開發和偵錯通常很有幫助。

+ **maxAgeInSeconds** （選用）：瀏覽器會使用此值來判斷快取 CORS 預檢回應的持續時間（以秒為單位）。 這必須是非負數的整數。 這個值越大，效能就越好，但是讓 CORS 原則變更生效的時間也就越長。 若未設定，即會使用預設持續期間 5 分鐘。

## <a name="encryption-key"></a>加密金鑰

雖然所有 Azure 認知搜尋索引預設都會使用 Microsoft 管理的金鑰進行加密，但您可以將索引設定為使用 Key Vault 中**客戶管理的金鑰**進行加密。 若要深入瞭解，請參閱[在 Azure 認知搜尋中管理加密金鑰](search-security-manage-encryption-keys.md)。

## <a name="next-steps"></a>後續步驟

在了解索引的結構之後，您接著便可以在入口網站中建立第一個索引。

> [!div class="nextstepaction"]
> [新增索引 (入口網站)](search-create-index-portal.md)
