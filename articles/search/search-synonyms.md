---
title: 搜尋索引的查詢擴充同義字
titleSuffix: Azure Cognitive Search
description: 建立同義字地圖，以展開 Azure 認知搜尋索引上搜尋查詢的範圍。 範圍會擴大納入您清單所提供的對等詞彙。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: b62621a77f383b5c6413e7c187e7ba3d60beabad
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97732082"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure 認知搜尋中的同義字

使用同義字地圖時，您可以將對等詞彙建立關聯，而不需要使用者實際提供字詞，就能展開查詢的範圍。 例如，假設 "dog"、"canine" 和 "小狗" 是同義字，則對 "canine" 的查詢會比對包含 "dog" 的檔。

## <a name="create-synonyms"></a>建立同義字

同義字地圖是可建立一次並供許多索引使用的資產。 [服務層級](search-limits-quotas-capacity.md#synonym-limits)會決定您可以建立多少個同義字對應（範圍從3個同義字地圖適用于免費和基本層），最多20個適用于標準層。 

如果您的內容包含技術或隱匿的術語，您可能會針對不同的語言（例如英文和法文版本）或詞典建立多個同義字對應。 雖然您可以建立多個同義字地圖，但目前欄位只能使用其中一個。

同義字地圖包含可作為同義字對應專案的名稱、格式和規則。 唯一支援的格式為 `solr` ，而且 `solr` 格式會決定規則結構。

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

若要建立同義字地圖，請使用「 [建立同義字對應 (REST API) ](/rest/api/searchservice/create-synonym-map) 或 Azure SDK。 針對 c # 開發人員，我們建議您從 [使用 c # 的 Azure 認知搜尋中新增同義字](search-synonyms-tutorial-sdk.md)開始。

## <a name="define-rules"></a>定義規則

對應規則遵循此檔中所述之 Apache Solr 的開放原始碼同義字篩選準則規格： [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter)。此 `solr` 格式支援兩種類型的規則：

+ 相等 (，其中詞彙在查詢中是相等的替代項) 

+ 明確對應 (在查詢之前，詞彙會對應至一個明確詞彙 ) 

每個規則都必須以新的行字元分隔 (`\n`) 。 您最多可以為每個同義字地圖在免費服務中定義5000個規則，並在其他階層中針對每個地圖定義20000個規則。 每個規則在規則) 中最多可以有20個展開 (或專案。 如需詳細資訊，請參閱 [同義字限制](search-limits-quotas-capacity.md#synonym-limits)。

查詢剖析器將會降低任何大寫或混合的大小寫詞彙，但如果您想要保留字元串中的特殊字元（例如逗號或虛線），請在建立同義字對應時加入適當的 escape 字元。 

### <a name="equivalency-rules"></a>等的規則

相同規則中的對等詞彙規則會以逗號分隔。 在第一個範例中，的查詢 `USA` 會擴充至 `USA` 或 `"United States"` 或 `"United States of America"` 。 請注意，如果您想要讓片語相符，查詢本身必須是以引號括住的片語查詢。

在等價的情況下，的查詢 `dog` 會展開查詢，以同時包含 `puppy` 和 `canine` 。

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>明確對應

明確對應的規則是以箭號表示 `=>` 。 當指定時，符合左邊的搜尋查詢詞彙順序 `=>` 將會取代為在查詢時右邊的替代專案。

在明確的情況下，會將 `Washington` 或的查詢 `Wash.` `WA` 重寫為 `WA` ，而且查詢引擎只會尋找該詞彙的相符專案 `WA` 。 明確對應只適用于指定的方向，而且 `WA` `Washington` 在此情況下不會將查詢重寫為。

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>逸出特殊字元

如果您需要定義包含逗號或其他特殊字元的同義字，您可以使用反斜線來將其取消，如下列範例所示：

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

由於反斜線本身是另一種語言（例如 JSON 和 c #）中的特殊字元，因此您可能需要將它再次進行換用。 例如，針對上述同義字對應傳送至 REST API 的 JSON 如下所示：

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>上傳及管理同義字地圖

如先前所述，您可以建立或更新同義字地圖，而不會中斷查詢和編制索引工作負載。 同義字地圖是獨立物件 (例如索引或資料來源) ，而且只要沒有任何欄位使用，更新就不會導致索引編制或查詢失敗。 不過，一旦您將同義字地圖新增至欄位定義之後，如果您接著刪除同義字地圖，任何包含問題欄位的查詢都會失敗，並出現404錯誤。

建立、更新和刪除同義字地圖永遠是整份檔的作業，這表示您無法以累加方式更新或刪除同義字地圖的部分。 即使只更新單一規則，也需要重載。

## <a name="assign-synonyms-to-fields"></a>將同義字指派給欄位

上傳同義字對應之後，您可以在 `Edm.String` `Collection(Edm.String)` 具有的欄位上啟用同義字 `"searchable":true` 。 如所述，欄位定義只能使用一個同義字地圖。

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>對等或對應欄位進行查詢

加入同義字並不會對查詢結構強加新的要求。 您可以像在加入同義字之前一樣，發出詞彙和片語查詢。 唯一的差異在於，如果同義字對應中有查詢詞彙，則查詢引擎會根據規則來展開或重寫詞彙或片語。

## <a name="how-synonyms-interact-with-other-features"></a>同義字與其他功能的互動方式

同義字功能會在 OR 運算子中，使用同義字改寫原始的查詢。 基於這個原因，點閱數醒目提示與評分檔案，會將原始詞彙與同義字視為對等。

同義字僅適用于搜尋查詢，不支援篩選、facet、自動完成或建議。 自動完成和建議僅以原始詞彙為基礎;同義字相符專案不會出現在回應中。

同義字擴充不適用於萬用字元搜尋詞彙；也不會擴充前置詞、模糊與 Regex 詞彙。

如果您需要執行適用同義字展開和萬用字元、regex 或模糊搜尋的單一查詢，您可以使用 OR 語法結合查詢。 例如，若要針對簡單查詢語法結合同義字與萬用字元，詞彙可以是 `<query> | <query>*`。

如果您在開發 (非生產) 環境中有現有的索引，可以使用小型的字典進行試驗，看看增加同義字能如何改變搜尋體驗，包含對於評分檔案、點閱數醒目提示以及搜尋建議的影響。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立同義字地圖 (REST API) ](/rest/api/searchservice/create-synonym-map)