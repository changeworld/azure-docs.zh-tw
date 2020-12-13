---
title: 使用完整的 Lucene 查詢語法
titleSuffix: Azure Cognitive Search
description: 模糊搜尋、鄰近搜尋、詞彙提升、正則運算式搜尋，以及 Azure 認知搜尋服務中萬用字元搜尋的 Lucene 查詢語法。
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 406233fd93ca76a683cf9f9a9e857de9099705ef
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368540"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>使用 "full" Lucene 搜尋語法 (Azure 認知搜尋中的先進查詢) 

針對 Azure 認知搜尋建立查詢時，您可以將預設的 [簡單查詢](query-simple-syntax.md) 剖析器取代為 Azure 認知搜尋中更強大的 [Lucene 查詢](query-lucene-syntax.md) 剖析器，以制訂特殊和先進的查詢定義。 

Lucene 剖析器支援複雜的查詢結構，例如欄位範圍查詢、模糊搜尋、中置詞和後置詞萬用字元搜尋、鄰近搜尋、詞彙提升和正則運算式搜尋。 額外的處理需求需要更多能源，因此，您應該預期執行時間會略久。 在本文中，您可以逐步執行示範以完整語法為基礎之查詢作業的範例。

> [!Note]
> 許多透過完整 Lucene 查詢語法來啟用的特製化查詢建構都不是[文字分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)，因此如果您預期的是詞幹分析或詞形歸併還原，可能會感到意外。 只能對完整詞彙執行語彙分析 (詞彙查詢或片語查詢)。 不完整詞彙的查詢類型 (前置詞查詢、萬用字元查詢、Regex 查詢、模糊查詢) 會直接新增至查詢樹狀結構，並略過分析階段。 在部分查詢詞彙上唯一執行的轉換是小寫。 
>

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

1. 將 URL 設定為 **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + 索引上的檔集合包含所有可搜尋的內容。 要求標頭中提供的查詢 API 金鑰僅適用于以檔集合為目標的讀取作業。

   + **`$count=true`** 傳回符合搜尋準則的檔計數。 在空的搜尋字串中，此計數將會是索引中的所有檔 (大約是2558（NYC 作業) 的情況）。

   + **`search=*`** 這是未指定的查詢，相當於 null 或空的搜尋。 這並不是特別有用，但它是最簡單的搜尋，而且會顯示索引中所有可取出的欄位，以及所有值。

   + **`queryType=full`** 叫用完整的 Lucene 分析器。

1. 在驗證步驟中，將下列要求貼到 GET 中，然後按一下 [傳送]。 結果會以詳細 JSON 文件的形式傳回。

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>如何叫用完整 Lucene 剖析

加入 **`queryType=full`** 以叫用完整查詢語法，並覆寫預設的簡單查詢語法。 本文中的所有範例都會指定 **`queryType=full`** 搜尋參數，表示完整語法是由 Lucene 查詢剖析器所處理。 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>範例1：以欄位清單為範圍的查詢

第一個範例不是剖析器專屬的，但我們會介紹第一個基本查詢概念：內含項目。 此範例會將查詢執行和回應限制為只有幾個特定欄位。 當您的工具是 Postman 或搜尋總管時，了解如何建構可讀取的 JSON 回應很重要。 

此查詢僅以 *business_title* 在中 **`searchFields`** ，並透過 **`select`** 參數指定回應中的相同欄位。

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查詢的回應會如下列螢幕擷取畫面所示。

  ![使用分數的 Postman 範例回應](media/search-query-lucene-examples/postman-sample-results.png)

您可能已注意到在回應中的搜尋分數。 當沒有排名時，會發生 **1** 的統一分數，可能是因為搜尋不是全文檢索搜尋，或是未提供任何準則。 對於空白搜尋，資料列會以任意順序傳回。 當您包含實際準則時，您會發現搜尋分數逐漸具有其實質意義。

## <a name="example-2-fielded-search"></a>範例2：回復搜尋

Full Lucene 語法支援將個別搜尋運算式的範圍設定為特定欄位。 此範例會搜尋具有資深的職稱，而非初級的商務標題。 您可以使用和指定多個欄位。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

此查詢的回應看起來應該類似下列螢幕擷取畫面 (posting_type 不會顯示) 。

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Postman 範例回應搜尋運算式" border="false":::

搜尋運算式可以是單一單字或片語，或是以括弧括住的更複雜運算式（選擇性地使用布林運算子）。 部分範例如下：

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

如果您想要將兩個字串評估為單一實體，請務必將多個字串放在引號中，如此一來，在此案例中，您會在欄位中搜尋兩個不同的位置 `state` 。 視工具而定，您可能需要將引號 (換 `\`) 。 

**FieldName： searchExpression** 中指定的欄位必須是可搜尋的欄位。 如需如何在欄位定義中使用索引屬性的詳細資訊，請參閱 [建立索引 (Azure 認知搜尋 REST API) ](/rest/api/searchservice/create-index) 。

> [!NOTE]
> 在上述範例中， **`searchFields`** 會省略參數，因為查詢的每個部分都有明確指定的功能變數名稱。 但是， **`searchFields`** 如果查詢有多個部分 (使用和語句) ，您仍然可以使用。 例如，查詢 `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` 只會比對 `senior NOT junior` `business_title` 欄位，而它會比對 "external" 與 `posting_type` 欄位。 在中提供的功能變數名稱會 `fieldName:searchExpression` 優先于 **`searchFields`** ，這也是在此範例中，我們可以省略的原因 `business_title` **`searchFields`** 。

## <a name="example-3-fuzzy-search"></a>範例 3：模糊搜尋

完整 Lucene 語法也支援模糊搜尋，會比對具有類似建構的字詞。 若要執行模糊搜尋，請在單一文字結尾附加波狀符號 `~`，加上選擇性參數 (介於 0 和 2 之間且會指定編輯距離的值)。 比方說，`blue~` 或 `blue~1` 會傳回 blue、blues 和 glue。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

不直接支援片語，但您可以在多部分片語（例如）的每個詞彙上指定模糊相符 `search=business_title:asosiate~ AND comm~` 。  在下方的螢幕擷取畫面中，回應包含與 *社區相關聯* 的對應。

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="模糊搜尋回應" border="false":::

> [!Note]
> 模糊查詢不會進行[分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 不完整詞彙的查詢類型 (前置詞查詢、萬用字元查詢、Regex 查詢、模糊查詢) 會直接新增至查詢樹狀結構，並略過分析階段。 在部分查詢詞彙上唯一執行的轉換是小寫。
>

## <a name="example-4-proximity-search"></a>範例 4：鄰近搜尋

鄰近搜尋可用來尋找文件中彼此相近的詞彙。 在片語的結尾插入波狀符號 "~"，後面加上建立鄰近界限的字數。 例如，"hotel airport"~5 會在文件中每 5 個字內尋找旅館和機場等詞彙。

此查詢會搜尋「資深」和「分析師」等詞彙，其中每個詞彙都不能以一個單字分隔，而且引號會被 (`\"`) 來保存片語：

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查詢的回應看起來應該類似下列螢幕擷取畫面 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="鄰近查詢" border="false":::

再試一次，以消除 (`~0` 在「資深分析師」兩個詞彙之間) 的距離。 請注意，相較於上一個查詢傳回 10 份文件，此查詢僅傳回 8 份文件。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>範例 5：詞彙提升

提升一詞指的是如果文件包含提升詞彙，則將其評等提高，高於不包含該詞彙的文件。 若要提升詞彙，請在您要 `^` 搜尋的字詞結尾使用插入號、、符號與提升因數 (數位) 。

在此 "before" 查詢中，搜尋含有 *computer analyst* 的工作時，我們會發現沒有同時包含 *computer* 和 *analyst* 兩個單字的結果，但是 *computer* 工作顯示於結果頂端。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

在 "after" 查詢中再次執行此搜尋，但這次在兩個單字未同時存在的情況下，提升含有 *analyst* 一詞的結果，使其高於 *computer* 一詞。 人們可讀取的查詢版本是 `search=business_title:computer analyst^2` 。 針對 Postman 中的可運作查詢， `^2` 會編碼為 `%5E2` 。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查詢的回應會如下列螢幕擷取畫面所示。

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="after 的詞彙提升" border="false":::

詞彙提升與評分設定檔的不同之處在於，評分設定檔會提升特定欄位，而不是特定字詞。 下列範例可協助說明差異。

請考慮使用評分設定檔，可提高特定欄位中的相符項目，例如 musicstoreindex 範例中的 **genre** 。 詞彙提升可用來進一步提升某些搜尋詞彙，使其高於其他詞彙。 比方說，"rock^2 electronic" 可提升包含搜尋詞彙的文件﹐使其在 [genre]  欄位中高於索引中的其他可搜尋欄位。 此外，包含搜尋詞彙 "rock" 的文件排名會比另一個搜尋詞彙 "electronic" 還高，此為詞彙提升值 (2) 的結果。

在設定係數層級時，提升係數越高，該字詞相對於其他搜尋字詞的關聯性也越高。 根據預設，提升係數為 1。 雖然提升係數必須是正數，但是它可能會小於 1 (例如，0.2)。

## <a name="example-6-regex"></a>範例 6：Regex

規則運算式搜尋會根據正斜線 "/" 之間的內容尋找相符項目，如 [RegExp 類別](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)中所記錄。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查詢的回應會如下列螢幕擷取畫面所示。

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="RegEx 查詢" border="false":::

> [!Note]
> RegEx 查詢不會進行[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)。 在部分查詢詞彙上唯一執行的轉換是小寫。
>

## <a name="example-7-wildcard-search"></a>範例 7：萬用字元搜尋

您可以使用一般辨識語法進行多個 (\*) 或單一 (?) 字元的萬用字元搜尋。 請注意，Lucene 查詢剖析器支援搭配使用這些符號與單一詞彙，而不是片語。

在此查詢中，搜尋包含前置詞 'prog' 的工作，其中包括內含 programming 與 programmer 的職稱。 您無法使用 `*` 或 `?` 符號做為搜尋的第一個字元。

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

此查詢的回應會如下列螢幕擷取畫面所示。

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="萬用字元查詢" border="false":::

> [!Note]
> 萬用字元查詢不會進行[分析](./search-lucene-query-architecture.md#stage-2-lexical-analysis)。 在部分查詢詞彙上唯一執行的轉換是小寫。
>

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