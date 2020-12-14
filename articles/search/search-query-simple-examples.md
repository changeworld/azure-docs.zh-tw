---
title: 使用簡單的 Lucene 查詢語法
titleSuffix: Azure Cognitive Search
description: 依範例學習，方法是根據簡單的全文檢索搜尋、篩選搜尋、地理搜尋、多面向搜尋 Azure 認知搜尋索引的語法來執行查詢。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ff9495e37a499b5502d8f8ced79b69608fa9552a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401741"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>使用 Azure 認知搜尋中的「簡單」搜尋語法

在 Azure 認知搜尋中， [簡單的查詢語法](query-simple-syntax.md) 會叫用全文檢索搜尋的預設查詢剖析器。 剖析器會快速處理，並處理常見的案例，包括全文檢索搜尋、篩選及多面向搜尋，以及前置詞搜尋。 本文使用範例來說明搜尋檔中的簡單語法用法， [ (REST API) ](/rest/api/searchservice/search-documents) 要求。

> [!NOTE]
> 替代的查詢語法是 [Full Lucene](query-lucene-syntax.md)，支援更複雜的查詢結構，例如模糊和萬用字元搜尋。 如需詳細資訊和範例，請參閱 [使用完整的 Lucene 語法](search-query-lucene-examples.md)。

## <a name="nyc-jobs-examples"></a>NYC 作業範例

下列範例會根據紐約[OpenData 方案的城市](https://nycopendata.socrata.com/)所提供的資料集，使用[NYC 工作搜尋索引](https://azjobsdemo.azurewebsites.net/)，其中包含可用的工作。 這項資料不應視為目前的或已完成。 索引位於 Microsoft 提供的沙箱服務上，這表示您不需要 Azure 訂用帳戶或 Azure 認知搜尋來嘗試這些查詢。

您需要的是 Postman 或對等的工具，以便在 GET 或 POST 上發出 HTTP 要求。 如果您不熟悉這些工具，請參閱 [快速入門：探索 Azure 認知搜尋 REST API](search-get-started-rest.md)。

## <a name="set-up-the-request"></a>設定要求

1. 要求標頭必須具有下列值：

   | 機碼 | 值 |
   |-----|-------|
   | Content-Type | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br>  (這是裝載 NYC 作業索引的沙箱搜尋服務實際的查詢 API 金鑰)  |

1. 將動詞設定為 **`GET`** 。

1. 將 URL 設定為 **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** 。 

   + 索引上的檔集合包含所有可搜尋的內容。 要求標頭中提供的查詢 api 金鑰僅適用于以檔集合為目標的讀取作業。

   + **`$count=true`** 傳回符合搜尋準則的檔計數。 在空的搜尋字串中，此計數將會是索引中的所有檔 (大約是2558（NYC 作業) 的情況）。

   + **`search=*`** 這是未指定的查詢，相當於 null 或空的搜尋。 這並不是特別有用，但它是最簡單的搜尋，而且會顯示索引中所有可取出的欄位，以及所有值。

1. 在驗證步驟中，將下列要求貼到 GET 中，然後按一下 [傳送]。 結果會以詳細 JSON 文件的形式傳回。

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>如何叫用簡單查詢剖析

針對互動式查詢，您不需要指定任何項目：簡單是預設值。 在程式碼中，如果您先前叫 **`queryType=full`** 用，您可以使用來重設預設值 **`queryType=simple`** 。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>範例1：在特定欄位上進行全文檢索搜尋

第一個範例不是剖析器專屬的，但我們會介紹第一個基本查詢概念：內含項目。 此範例會將查詢執行和回應限制為只有幾個特定欄位。 當您的工具是 Postman 或搜尋總管時，了解如何建構可讀取的 JSON 回應很重要。 

此查詢僅以 *business_title* 在中 **`searchFields`** ，並透過 **`select`** 參數指定回應中的相同欄位。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查詢的回應會如下列螢幕擷取畫面所示。

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Postman 範例回應" border="false":::

您可能已注意到在回應中的搜尋分數。 當沒有排名時，會發生 **1** 的統一分數，可能是因為搜尋不是全文檢索搜尋，或是未提供任何準則。 對於空白搜尋，資料列會以任意順序傳回。 當您包含實際準則時，您會發現搜尋分數逐漸具有其實質意義。

## <a name="example-2-look-up-by-id"></a>範例 2︰依識別碼查閱

當您在查詢中傳回搜尋結果時，邏輯上的後續步驟是提供詳細資料頁面，其中包含檔中的多個欄位。 此範例示範如何使用 [查閱](/rest/api/searchservice/lookup-document) 作業傳回單一檔，以傳遞檔識別碼。

所有文件都有唯一識別碼。 若要嘗試使用查閱查詢的語法，請先傳回文件識別碼清單，以尋找您要使用的文件。 NYC 工作的識別碼會儲存在 `id` 欄位中。

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

接下來，根據 "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" 從集合取出檔 `id` ，其會先出現在先前的回應中。 下列查詢會傳回整份檔的所有可取得的欄位。

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>範例 3：篩選查詢

[篩選語法](./search-query-odata-filter.md) 是一種 OData 運算式，您可以單獨使用或搭配使用 **`search`** 。 獨立的篩選條件 (不含搜尋參數) 在篩選運算式能夠完全限定相關文件時，將有其效用。 沒有查詢字串，就沒有語彙或語言分析、沒有計分 (所有分數均為 1)，也沒有排名。 請注意，搜尋字串是空的。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

搭配使用時，會先將篩選套用到整個索引，再對篩選結果執行搜尋。 由於篩選能夠減少搜尋查詢需要處理的資料集合，因此對於提升查詢效能方面是很實用的技術。

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="篩選查詢回應" border="false":::

結合篩選和搜尋的另一個強大方式是 **`search.ismatch*()`** 在篩選條件運算式中，您可以在篩選準則中使用搜尋查詢。 這個篩選運算式在 *plan* 上使用萬用字元來選取包含 term plan、planner、planning 等等的 business_title。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

如需此函式的詳細資訊，請參閱[「篩選範例」中的 search.ismatch](./search-query-odata-full-text-search-functions.md#examples)。

## <a name="example-4-range-filters"></a>範例 4︰範圍篩選條件

您可以透過 **`$filter`** 任何資料類型的運算式來支援範圍篩選。 下列範例會搜尋數值和字串欄位。 

資料類型在範圍篩選條件中很重要，而當數值資料位於數值欄位且字串資料位於字串欄位時效果最好。 字串欄位中的數值資料不適合範圍，因為數值字串無法在 Azure 認知搜尋中比較。

下列查詢是數值範圍：

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
此查詢的回應會如下列螢幕擷取畫面所示。

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="數值範圍的範圍篩選條件" border="false":::

在此查詢中，範圍會超過字串欄位， (business_title) ：

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

此查詢的回應會如下列螢幕擷取畫面所示。

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="文字範圍的範圍篩選條件" border="false":::

> [!NOTE]
> 透過值範圍進行面向化是常見的搜尋應用程式需求。 如需詳細資訊和範例，請參閱 [如何建立 facet 篩選](search-filters-facets.md)。

## <a name="example-5-geo-search"></a>範例 5：異地搜尋

此範例索引包含具有經度和緯度座標的 geo_location 欄位。 這個範例會使用 [geo.distance 函式](search-query-odata-geo-spatial-functions.md#examples)，以篩選起始點周圍以至您提供的任意距離 (以公里為單位) 內的文件。 您可以調整查詢 (4) 中的最後一個值，以縮小或放大查詢的介面區。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

針對更容易閱讀的結果，會修剪搜尋結果以包含職稱和工作位置。 起始座標是從索引中的隨機文件取得 (在此例中為史泰登島上的工作地點)。

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Staten 島的地圖" border="false":::

## <a name="example-6-search-precision"></a>範例 6：搜尋精準度

字詞查詢是個別評估的單一字詞 (可能有許多個)。 片語查詢會以引號括住，並以逐字字串的形式評估。 比對的精確度由運算子和 searchMode 所控制。

範例1： `search=fire`  符合140結果，其中所有相符專案都包含檔中某處的文字。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

範例 2︰`search=fire department` 傳回 2002 個結果。 系統會針對包含 fire 或 department 的文件傳回相符項目。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

範例3：傳回 `search="fire department"` 77 結果。 以引號括住字串會建立包含兩個詞彙的片語搜尋，而在包含合併詞彙的索引中，則會找到相符專案的標記化詞彙。 這解釋了為何 `search=+fire +department` 這樣的搜尋並不相同。 這兩個字詞都必須存在，但兩者的掃描會個別執行。 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> 因為片語查詢是透過引號指定，所以此範例會新增一個 escape 字元 (`\`) 來保留語法。

## <a name="example-7-booleans-with-searchmode"></a>範例 7︰使用 searchMode 的布林值

簡單語法支援字元格式 (`+, -, |`) 的布林運算子。 SearchMode 參數會在精確度和召回之間告知取捨， **`searchMode=any`** favoring 回想 (符合任何準則的檔會符合結果集) 的檔，而且 **`searchMode=all`** favoring 精確度 (所有準則都必須符合) 。 

預設值是 **`searchMode=any`** ，如果您使用多個運算子堆疊查詢並取得更廣泛的結果，而不是較小的結果，這可能會造成混淆。 在使用 NOT 更是如此，因為其結果會包含所有「不含」特定字詞的文件。

使用預設 searchMode (任何) ，會傳回2800個檔：包含「消防部門」一詞的所有檔，以及所有沒有「Metrotech 中心」片語的檔。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

此查詢的回應會如下列螢幕擷取畫面所示。

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="搜尋模式：任何" border="false":::

變更以對 **`searchMode=all`** 準則強制執行累計效果，並傳回較小的結果集-21 份檔，包括包含整個片語「消防部門」的檔，減去 Metrotech Center 位址的那些作業。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="搜尋模式：全部" border="false":::

## <a name="example-8-structuring-results"></a>範例 8︰建構結果

有數個參數會控制要在搜尋結果中包含的欄位、每個批次中傳回的文件數目，以及排序次序。 此範例會 resurfaces 幾個先前的範例，使用語句和逐字搜尋準則將結果限制為特定欄位 **`$select`** ，傳回82相符專案。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

接續先前的範例，您可以依職稱排序。 之所以可進行此排序，是因為 civil_service_title 在索引中是 *可排序的*。

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

使用參數來執行分頁結果 **`$top`** ，在此案例中會傳回前5份檔：

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

若要取得後續的 5 份文件，請略過第一個批次：

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

## <a name="next-steps"></a>後續步驟

請嘗試在程式碼中指定查詢。 下列連結說明如何使用 Azure Sdk 設定搜尋查詢。

+ [使用 .NET SDK 查詢您的索引](search-get-started-dotnet.md)
+ [使用 Python SDK 查詢您的索引](search-get-started-python.md)
+ [使用 JavaScript SDK 查詢您的索引](search-get-started-javascript.md)

您可以在下列連結中找到其他語法參考、查詢架構和範例：

+ [建置進階查詢的 Lucene 語法查詢範例](search-query-lucene-examples.md)
+ [全文檢索搜尋如何在 Azure 認知搜尋中運作](search-lucene-query-architecture.md)
+ [簡單查詢語法](query-simple-syntax.md)
+ [完整的 Lucene 查詢語法](query-lucene-syntax.md)
+ [篩選語法](search-query-odata-filter.md)