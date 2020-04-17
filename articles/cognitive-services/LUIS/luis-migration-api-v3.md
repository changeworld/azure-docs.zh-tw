---
title: V3 API 的預測終結點變更
description: 查詢預測終結點 V3 API 已更改。 使用本指南瞭解如何遷移到版本 3 終結點 API。
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 4b6d28b24ffc6c0a848d1c7a34e863da0606d936
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530380"
---
# <a name="prediction-endpoint-changes-for-v3"></a>預測 V3 的終結點變更

查詢預測終結點 V3 API 已更改。 使用本指南瞭解如何遷移到版本 3 終結點 API。

**一般可用狀態**- 此 V3 API 包括重要的 JSON 請求和來自 V2 API 的回應更改。

V3 API 提供以下新功能:

* [外部實體](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [動態清單](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [預建構實體 JSON 變更](#prebuilt-entity-changes)

預測終結點[要求](#request-changes)與[回應](#response-changes)有重大變更來支援上面列出的新功能,包括:

* [回應物件變更](#top-level-json-changes)
* [實體角色名稱引用而不是實體名稱](#entity-role-name-instead-of-entity-name)
* [要在陳述中標記實體屬性](#marking-placement-of-entities-in-utterances)

[參考文件](https://aka.ms/luis-api-v3)可用於 V3。

## <a name="v3-changes-from-preview-to-ga"></a>V3 從預覽變更為 GA

V3 在移移到 GA 時進行了以下變更:

* 以下預建構實體具有不同的 JSON 回應:
    * [奧迪納爾V1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [日期時間V2](luis-reference-prebuilt-datetimev2.md)
    * 可測量單位金鑰名稱`units`,從到`unit`

* 要求正文 JSON 更改:
    * 從`preferExternalEntities`到`preferExternalEntities`
    * 外部`score`實體的選擇參數

* 回應正文 JSON 更改:
    * `normalizedQuery`刪除

## <a name="suggested-adoption-strategy"></a>建議的採用原則

如果使用機器人框架、必應拼寫檢查 V7,或者只想遷移 LUIS 應用創作,請繼續使用 V2 終結點。

如果您知道任何用戶端應用程式或集成(Bot Framework 和必應拼寫檢查 V7)都不會受到影響,並且您可以同時遷移 LUIS 應用創作和預測終結點,請開始使用 V3 預測終結點。 V2 預測終結點仍將可用,這是一個很好的回退策略。


## <a name="not-supported"></a>不支援

### <a name="bing-spell-check"></a>Bing 拼字檢查

V3 預測終結點不支援此 API - 繼續使用 V2 API 預測終結點進行拼寫更正。 如果您在使用 V3 API 時需要拼寫更正,請讓用戶端應用程式呼叫[必應拼寫檢查](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview)API,並在將文字發送到 LUIS API 之前將文字更改為正確的拼寫。

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>機器人架構與 Azure 機器人服務客戶端應用程式

繼續使用 V2 API 預測終結點,直到發佈 Bot 框架的 V4.7。

## <a name="v2-api-deprecation"></a>V2 API 棄用

2020 年 6 月 8 日 V3 預覽版後,V2 預測 API 至少 9 個月內不會棄用。

## <a name="endpoint-url-changes"></a>端點網址變更

### <a name="changes-by-slot-name-and-version-name"></a>按插槽名稱和版本名稱進行變更

V3 終結點 HTTP 調用的格式已更改。

如果要按版本查詢,首先需要[使用 透過 API 發表](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)。 `"directVersionPublish":true` 查詢引用版本 ID 而不是槽名稱的終結點。

|預測 API 版本|METHOD|URL|
|--|--|--|
|V3|GET|https://<b>[區域]</b>.api.cognitive.microsoft.com/luis/<b>預測</b>/<b>v3.0</b>/app_ID} /插槽/<b>[SLOT-NAME]</b>/預測查詢}<b>[查詢]</b> <b>{APP-ID}</b>|
|V3|POST|https://<b>[區域]</b>.api.cognitive.microsoft.com/luis/<b>預測</b>/<b>v3.0</b>/應用/<b>[APP-ID]</b>/插槽/<b>[SLOT-NAME]</b>/預測|
|V2|GET|https://<b>[區域]</b>.api.cognitive.microsoft.com/luis/<b>預測</b>/<b>v3.0</b>/app/ID} /版本<b>/[VERSION-ID]</b>/預測查詢}<b>[查詢]</b> <b>{APP-ID}</b>|
|V2|POST|https://<b>[區域]</b>.api.cognitive.microsoft.com/luis/<b>預測</b>/<b>v3.0</b>/應用/<b>[APP-ID]</b>/版本/<b>[VERSION-ID]</b>/預測|

|的有效值`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>要求變更

### <a name="query-string-changes"></a>查詢字串變更

V3 API 具有不同的查詢字串參數。

|參數名稱|類型|版本|預設|目的|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|將查詢儲存在紀錄檔中。 預設值為 false。|
|`query`|字串|僅限第 3 版|沒有預設值 ─GET 要求中需要預設|**在 V2**中,要預測的話語在`q`參數中 。 <br><br>**在 V3**中,該`query`功能在參數中傳遞。|
|`show-all-intents`|boolean|僅限第 3 版|false|返回**預測**中具有相應分數的所有意圖。 意圖將作為父`intents`物件中的物件返回。 這允許程式設計存取,而無需在陣列中找到意圖`prediction.intents.give`: 。 在 V2 中,這些在陣列中返回。 |
|`verbose`|boolean|V2 & V3|false|**在 V2**中,當設置為 true 時,將返回所有預測的意圖。 如果需要所有預測的意圖,請使用的`show-all-intents`V3 參數。<br><br>**在 V3**中,此參數僅提供實體預測的實體元數據詳細資訊。  |
|`timezoneOffset`|字串|V2|-|應用於日期時間V2實體的時區。|
|`datetimeReference`|字串|V3|-|套用日期時間 V2 實體的[時區](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 `timezoneOffset`從 V2 替換。|


### <a name="v3-post-body"></a>V3 POST 主體

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
|`dynamicLists`|array|僅限第 3 版|不需要。|[動態清單](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)允許您擴展現有的已訓練和已發佈的清單實體,已在 LUIS 應用中。|
|`externalEntities`|array|僅限第 3 版|不需要。|[外部實體](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)使 LUIS 應用能夠在運行時識別和標記實體,這些實體可用作現有實體的功能。 |
|`options.datetimeReference`|字串|僅限第 3 版|沒有預設值|用於確定[日期時間 V2 偏移](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)量。 日期參考的格式是[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)。|
|`options.preferExternalEntities`|boolean|僅限第 3 版|false|指定是否使用使用者[的外部實體(與現有實體同名)](schema-change-prediction-runtime.md#override-existing-model-predictions)或使用模型中的現有實體進行預測。 |
|`query`|字串|僅限第 3 版|必要。|**在 V2**中,要預測的話語在`q`參數中 。 <br><br>**在 V3**中,該`query`功能在參數中傳遞。|

## <a name="response-changes"></a>回應變更

查詢回應 JSON 已更改,以便對最常用的數據進行更大的程式設計訪問。

### <a name="top-level-json-changes"></a>頂級 JSON 更改



V2 的頂級 JSON`verbose`屬性是, 當設定為 true`intents`時,該屬性傳回 屬性中的所有意圖及其分數:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 的頂級 JSON 屬性是:

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

該`intents`對像是無序列表。 不要假定 對應`intents`於 中的第一個`topIntent`子級 。 相反,使用`topIntent`該值尋找分數:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

回應 JSON 架構變更允許:

* 明確區分原始陳述、`query`和返回的預測`prediction`。
* 更輕鬆地以程式設計方式訪問預測數據。 您可以按**名稱**訪問意圖和實體的值,而不是通過 V2 中的陣列枚舉。 對於預測的實體角色,將返回角色名稱,因為它在整個應用中是唯一的。
* 數據類型(如果確定)將受尊重。 數位不再作為字串返回。
* 在`$instance`物件中返回的第一優先順序預測資訊和附加元數據之間的區別。

### <a name="entity-response-changes"></a>實體回應變更

#### <a name="marking-placement-of-entities-in-utterances"></a>標記實體在陳述中放置

**在 V2**中,實體在 話語`startIndex`中`endIndex`標有 與 。

**在 V3**中,實體標`startIndex``entityLength`記為與 。

#### <a name="access-instance-for-entity-metadata"></a>實體`$instance`中繼資料

如果需要實體元數據,查詢字串需要使用`verbose=true`標誌,並且回應包含物件中的`$instance`元數據。 示例在以下各節中的 JSON 回應中顯示。

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>每個預測實體都表示為陣列

該`prediction.entities.<entity-name>`物件包含一個陣列,因為每個實體都可以在陳述中預測多個次。

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>預置實體變更

V3 回應物件包括對預建構實體的更改。 查看[特定的預建構實體](luis-reference-prebuilt-entities.md)以瞭解更多資訊。

#### <a name="list-entity-prediction-changes"></a>列出實體預測變更

清單實體預測的 JSON 已變更為陣列陣列:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
每個內部數組都對應於話語內的文本。 內部對像是陣列,因為相同的文本可以出現在清單實體的多個子清單中。

當`entities`物件映射`$instance`到 物件時,將保留列表實體預測的物件順序。

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>實體角色名稱而不是實體名稱

在 V2`entities`中,陣列返回所有預測實體,實體名稱為唯一標識符。 在 V3 中,如果實體使用角色,並且預測是實體角色,則主標識符是角色名稱。 這是可能的,因為實體角色名稱在整個應用中必須是唯一的,包括其他模型(意圖、實體)名稱。

在下面的示例中:請考慮包含文本的表述。 `Yellow Bird Lane` 此文字會預測為 自`Location`訂`Destination`實體的角色 。

|陳述文字|實體名稱|角色名稱|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

在 V2 中,實體由實體_名稱_識別,該實體名稱具有作為物件屬性的角色:

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

在 V3 中,_實體角色_引用該實體,如果預測針對該角色:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

在 V3 中`verbose`,與 標誌傳回實體中資料的結果相同:

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

## <a name="extend-the-app-at-prediction-time"></a>在預測時延伸應用

瞭解如何在預測執行時延伸應用[的概念](schema-change-prediction-runtime.md)。

## <a name="deprecation"></a>淘汰

V2 API 在 V3 預覽後至少 9 個月內不會棄用。

## <a name="next-steps"></a>後續步驟

使用 V3 API 文件將現有的 REST 呼叫更新到 LUIS[終結點](https://aka.ms/luis-api-v3)API。
