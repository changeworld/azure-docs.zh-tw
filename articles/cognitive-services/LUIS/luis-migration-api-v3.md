---
title: V3 API 中的預測端點變更
description: 查詢預測端點 V3 Api 已變更。 使用本指南來瞭解如何遷移至第3版端點 Api。
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: diberry
ms.openlocfilehash: 84afcbcd348c3fd91014096877de2315722b53a0
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849326"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3 的預測端點變更

查詢預測端點 V3 Api 已變更。 使用本指南來瞭解如何遷移至第3版端點 Api。

**正式運作狀態**-此 V3 api 包含來自 V2 api 的重大 JSON 要求和回應變更。

V3 API 提供下列新功能：

* [外部實體](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [動態清單](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [預先建立的實體 JSON 變更](#prebuilt-entity-changes)

預測端點[要求](#request-changes)和[回應](#response-changes)有重大變更，可支援上列的新功能，包括下列各項：

* [回應物件變更](#top-level-json-changes)
* [實體角色名稱參考，而不是機構名稱](#entity-role-name-instead-of-entity-name)
* [要在語句中標記實體的屬性](#marking-placement-of-entities-in-utterances)

[參考檔](https://aka.ms/luis-api-v3)適用于 V3。

## <a name="v3-changes-from-preview-to-ga"></a>V3 從預覽變更為 GA

V3 進行下列變更，做為移至 GA 的一部分：

* 下列預建實體具有不同的 JSON 回應：
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * 可測量的單位索引鍵名稱 `units` ，從到`unit`

* 要求主體 JSON 變更：
    * 從 `preferExternalEntities` 到`preferExternalEntities`
    * `score`外部實體的選擇性參數

* 回應主體 JSON 變更：
    * `normalizedQuery`拆卸

## <a name="suggested-adoption-strategy"></a>建議採用策略

如果您使用 Bot Framework、Bing 拼寫檢查 V7，或只想要遷移 LUIS 應用程式撰寫，請繼續使用 V2 端點。

如果您不知道您的用戶端應用程式或整合（Bot Framework 和 Bing 拼寫檢查 V7）都受到影響，而且您很樂意同時遷移 LUIS 應用程式撰寫和預測端點，請開始使用 V3 預測端點。 V2 預測端點仍然可供使用，而且是不錯的回溯策略。


## <a name="not-supported"></a>不支援

### <a name="bing-spell-check"></a>Bing 拼字檢查

V3 預測端點中不支援此 API-繼續使用 V2 API 預測端點進行拼寫更正。 如果您在使用 V3 API 時需要拼寫更正，請讓用戶端應用程式呼叫[Bing 拼寫檢查](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview)API，並將文字變更為正確的拼寫，再將文字傳送至 LUIS API。

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework 和 Azure Bot 服務用戶端應用程式

繼續使用 V2 API 預測端點，直到已釋放 Bot Framework 的4.7。

## <a name="v2-api-deprecation"></a>V2 API 淘汰

2020年6月8日之後，V2 預測 API 將不會被淘汰至少9個月。

## <a name="endpoint-url-changes"></a>端點 URL 變更

### <a name="changes-by-slot-name-and-version-name"></a>依位置名稱和版本名稱的變更

[V3 端點 HTTP](developer-reference-resource.md#rest-endpoints)呼叫的格式已變更。

如果您想要依版本進行查詢，您必須先透過[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)與發行 `"directVersionPublish":true` 。 查詢參考版本識別碼的端點，而不是位置名稱。

|有效的值`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>要求變更

### <a name="query-string-changes"></a>查詢字串變更

V3 API 有不同的查詢字串參數。

|參數名稱|類型|版本|預設|目的|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|將查詢儲存在記錄檔中。 預設值為 false。|
|`query`|字串|僅限第 3 版|無預設值-GET 要求中需要它|**在 V2 中**，要預測的語句是在 `q` 參數中。 <br><br>**在 V3**中，此功能會在參數中傳遞 `query` 。|
|`show-all-intents`|boolean|僅限第 3 版|false|傳回在**預測. 意圖**物件中具有對應分數的所有意圖。 意圖會以物件的形式傳回父 `intents` 物件。 這可讓您以程式設計方式存取，而不需要在陣列中尋找意圖： `prediction.intents.give` 。 在 V2 中，這些會在陣列中傳回。 |
|`verbose`|boolean|V2 & V3|false|**在第2版中**，當設定為 true 時，會傳回所有預測意圖。 如果您需要所有預測的意圖，請使用的 V3 參數 `show-all-intents` 。<br><br>**在 V3 中**，這個參數只提供實體預測的實體中繼資料詳細資料。  |
|`timezoneOffset`|字串|V2|-|適用于 datetimeV2 實體的時區。|
|`datetimeReference`|字串|V3|-|適用于 datetimeV2 實體的[時區](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 取代為 `timezoneOffset` V2。|


### <a name="v3-post-body"></a>V3 張貼內容

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|屬性|類型|版本|預設|目的|
|--|--|--|--|--|
|`dynamicLists`|array|僅限第 3 版|不需要。|[動態清單](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)可讓您擴充現有的已定型和已發佈清單實體（已在 LUIS 應用程式中）。|
|`externalEntities`|array|僅限第 3 版|不需要。|[外部實體](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)可讓您的 LUIS 應用程式在執行時間中識別實體並為其加上標籤，以做為現有實體的功能。 |
|`options.datetimeReference`|字串|僅限第 3 版|無預設值|用來判斷[datetimeV2 位移](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 DatetimeReference 的格式為[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)。|
|`options.preferExternalEntities`|boolean|僅限第 3 版|false|指定是否使用使用者的[外部實體（具有與現有實體相同的名稱）](schema-change-prediction-runtime.md#override-existing-model-predictions) ，或模型中的現有實體用於預測。 |
|`query`|字串|僅限第 3 版|必要。|**在 V2 中**，要預測的語句是在 `q` 參數中。 <br><br>**在 V3**中，此功能會在參數中傳遞 `query` 。|

## <a name="response-changes"></a>回應變更

查詢回應 JSON 已變更為允許以程式設計方式存取最常使用的資料。

### <a name="top-level-json-changes"></a>最上層 JSON 變更



當設定為 true 時，V2 的最上層 JSON 屬性是，它會傳回 `verbose` 屬性中的所有意圖和其分數 `intents` ：

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 的最上層 JSON 屬性包括：

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

`intents`物件是未排序的清單。 請勿假設中的第一個子系 `intents` 對應至 `topIntent` 。 相反地，請使用 `topIntent` 值來尋找分數：

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

回應 JSON 架構變更允許：

* 清除原始語句、 `query` 和傳回的預測之間的區別 `prediction` 。
* 以程式設計方式輕鬆存取預測的資料。 您可以透過**名稱**來存取意圖和實體的值，而不是列舉 V2 中的陣列。 對於預測實體角色，會傳回角色名稱，因為它在整個應用程式中都是唯一的。
* 會遵守資料類型（如果已決定）。 數值不會再以字串傳回。
* 在物件中傳回的第一個優先順序預測資訊和其他中繼資料之間的區別 `$instance` 。

### <a name="entity-response-changes"></a>實體回應變更

#### <a name="marking-placement-of-entities-in-utterances"></a>標記實體在語句中的位置

**在 V2 中**，實體已在具有和的語句中 `startIndex` 標記 `endIndex` 。

**在 V3 中**，實體會以 `startIndex` 和標記 `entityLength` 。

#### <a name="access-instance-for-entity-metadata"></a>`$instance`實體中繼資料的存取

如果您需要實體中繼資料，則查詢字串必須使用 `verbose=true` 旗標，而回應會包含物件中的中繼資料 `$instance` 。 下列各節的 JSON 回應中會顯示範例。

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>每個預測實體都會以陣列表示

`prediction.entities.<entity-name>`物件包含陣列，因為每個實體可以在語句中預測一次以上。

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>預先建立的實體變更

V3 回應物件包含預建實體的變更。 若要深入瞭解，請參閱[特定的預建實體](luis-reference-prebuilt-entities.md)。

#### <a name="list-entity-prediction-changes"></a>列出實體預測變更

清單實體預測的 JSON 已變更為陣列陣列：

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
每個內部陣列都會對應至語句內的文字。 內建物件是一個陣列，因為相同的文字可能會出現在清單實體的一個以上子清單中。

在 `entities` 物件與物件之間進行對應時 `$instance` ，會保留物件的順序供清單實體預測之用。

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>實體角色名稱，而不是機構名稱

在 V2 中， `entities` 陣列傳回所有具有機構名稱的預測實體為唯一識別碼。 在 V3 中，如果實體使用角色，而預測是針對實體角色，則主要識別碼是角色名稱。 這是可行的，因為在整個應用程式中，實體角色名稱必須是唯一的，包括其他模型（意圖、實體）名稱。

在下列範例中：請考慮包含文字的語句 `Yellow Bird Lane` 。 此文字會預測為的自訂 `Location` 實體角色 `Destination` 。

|語句文字|實體名稱|角色名稱|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

在 V2 中，實體是以_機構名稱_識別，並以角色作為物件的屬性：

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

在 V3 中，實體是由_實體角色_參考，如果是針對角色的預測：

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

在 V3 中，具有傳回實體中繼資料之旗標的相同結果 `verbose` ：

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>在預測時間擴充應用程式

瞭解如何在預測執行時間擴充應用程式的相關[概念](schema-change-prediction-runtime.md)。

## <a name="deprecation"></a>淘汰

在 V3 preview 之後，V2 API 將不會被淘汰至少9個月。

## <a name="next-steps"></a>後續步驟

使用 V3 API 檔，將現有的 REST 呼叫更新為 LUIS[端點](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8)api。
