---
title: V3 API 中的預測終結點更改
description: 查詢預測終結點 V3 API 已更改。 使用本指南瞭解如何遷移到版本 3 終結點 API。
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117379"
---
# <a name="prediction-endpoint-changes-for-v3"></a>預測 V3 的終結點更改

查詢預測終結點 V3 API 已更改。 使用本指南瞭解如何遷移到版本 3 終結點 API。

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**一般可用狀態**- 此 V3 API 包括重要的 JSON 請求和來自 V2 API 的回應更改。

V3 API 提供以下新功能：

* [外部實體](#external-entities-passed-in-at-prediction-time)
* [動態清單](#dynamic-lists-passed-in-at-prediction-time)
* [預構建實體 JSON 更改](#prebuilt-entity-changes)

預測終結點[請求](#request-changes)和[回應](#response-changes)有重大更改來支援上面列出的新功能，包括：

* [回應物件更改](#top-level-json-changes)
* [實體角色名稱引用而不是機構名稱](#entity-role-name-instead-of-entity-name)
* [要在陳述中標記實體的屬性](#marking-placement-of-entities-in-utterances)

[參考文檔](https://aka.ms/luis-api-v3)可用於 V3。

## <a name="v3-changes-from-preview-to-ga"></a>V3 從預覽更改為 GA

V3 在遷移到 GA 時進行了以下更改：

* 以下預構建實體具有不同的 JSON 回應：
    * [奧迪納爾V1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [日期時間V2](luis-reference-prebuilt-datetimev2.md)
    * 可測量單位金鑰名稱`units`，從 到`unit`

* 請求正文 JSON 更改：
    * 從`preferExternalEntities`到`preferExternalEntities`
    * 外部`score`實體的可選參數

* 回應正文 JSON 更改：
    * `normalizedQuery`刪除

## <a name="suggested-adoption-strategy"></a>建議的採用策略

如果使用機器人框架、必應拼寫檢查 V7，或者只想遷移 LUIS 應用創作，請繼續使用 V2 終結點。

如果您知道任何用戶端應用程式或集成（Bot Framework 和必應拼寫檢查 V7）都不會受到影響，並且您可以同時遷移 LUIS 應用創作和預測終結點，請開始使用 V3 預測終結點。 V2 預測終結點仍將可用，這是一個很好的回退策略。


## <a name="not-supported"></a>不支援

### <a name="bing-spell-check"></a>Bing 拼字檢查

V3 預測終結點不支援此 API - 繼續使用 V2 API 預測終結點進行拼寫更正。 如果您在使用 V3 API 時需要拼寫更正，請讓用戶端應用程式調用[必應拼寫檢查](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview)API，並在將文本發送到 LUIS API 之前將文本更改為正確的拼寫。

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>機器人框架和 Azure 機器人服務用戶端應用程式

繼續使用 V2 API 預測終結點，直到發佈 Bot 框架的 V4.7。

## <a name="v2-api-deprecation"></a>V2 API 棄用

2020 年 6 月 8 日 V3 預覽版後，V2 預測 API 至少 9 個月內不會棄用。

## <a name="endpoint-url-changes"></a>端點 URL 更改

### <a name="changes-by-slot-name-and-version-name"></a>按插槽名稱和版本名稱進行更改

V3 終結點 HTTP 調用的格式已更改。

如果要按版本查詢，首先需要[使用 通過 API 發佈](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)。 `"directVersionPublish":true` 查詢引用版本 ID 而不是槽名稱的終結點。

|預測 API 版本|METHOD|URL|
|--|--|--|
|V3|GET|HTTPs://<b>[區域]</b>.api.cognitive.microsoft.com/luis/<b>預測</b>/<b>v3.0</b>/app_ID} /插槽/<b>[SLOT-NAME]</b>/預測查詢}<b>[查詢]</b> <b>{APP-ID}</b>|
|V3|POST|HTTPs://<b>[區域]</b>.api.cognitive.microsoft.com/luis/<b>預測</b>/<b>v3.0</b>/應用/<b>[APP-ID]</b>/插槽/<b>[SLOT-NAME]</b>/預測|
|V2|GET|HTTPs://<b>[區域]</b>.api.cognitive.microsoft.com/luis/<b>預測</b>/<b>v3.0</b>/app/ID} /版本<b>/[VERSION-ID]</b>/預測查詢}<b>[查詢]</b> <b>{APP-ID}</b>|
|V2|POST|HTTPs://<b>[區域]</b>.api.cognitive.microsoft.com/luis/<b>預測</b>/<b>v3.0</b>/應用/<b>[APP-ID]</b>/版本/<b>[VERSION-ID]</b>/預測|

|的有效值`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>要求變更

### <a name="query-string-changes"></a>查詢字串更改

V3 API 具有不同的查詢字串參數。

|參數名稱|類型|版本|預設|目的|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|將查詢存儲在日誌檔中。 預設值為 false。|
|`query`|字串|僅限第 3 版|無預設值 - GET 請求中需要預設|**在 V2**中，要預測的話語在參數中`q`。 <br><br>**在 V3**中，該功能在`query`參數中傳遞。|
|`show-all-intents`|boolean|僅限第 3 版|false|返回**預測**中具有相應分數的所有意圖。 意圖將作為父`intents`物件中的物件返回。 這允許程式設計訪問，而無需在陣列中找到意圖： `prediction.intents.give`。 在 V2 中，這些在陣列中返回。 |
|`verbose`|boolean|V2 & V3|false|**在 V2**中，當設置為 true 時，將返回所有預測的意圖。 如果需要所有預測的意圖，請使用 的`show-all-intents`V3 參數。<br><br>**在 V3**中，此參數僅提供實體預測的實體中繼資料詳細資訊。  |
|`timezoneOffset`|字串|V2|-|應用於日期時間V2實體的時區。|
|`datetimeReference`|字串|V3|-|應用於日期時間V2實體的[時區](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 `timezoneOffset`從 V2 替換。|


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
|`dynamicLists`|array|僅限第 3 版|不需要。|[動態清單](#dynamic-lists-passed-in-at-prediction-time)允許您擴展現有的已訓練和已發佈的清單實體，已在 LUIS 應用中。|
|`externalEntities`|array|僅限第 3 版|不需要。|[外部實體](#external-entities-passed-in-at-prediction-time)使 LUIS 應用能夠在運行時標識和標記實體，這些實體可用作現有實體的功能。 |
|`options.datetimeReference`|字串|僅限第 3 版|無預設值|用於確定[日期時間V2偏移](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)量。 日期參考的格式是[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)。|
|`options.preferExternalEntities`|boolean|僅限第 3 版|false|指定是否使用使用者[的外部實體（與現有實體同名）](#override-existing-model-predictions)或使用模型中的現有實體進行預測。 |
|`query`|字串|僅限第 3 版|必要。|**在 V2**中，要預測的話語在參數中`q`。 <br><br>**在 V3**中，該功能在`query`參數中傳遞。|



## <a name="response-changes"></a>回應更改

查詢回應 JSON 已更改，以便對最常用的資料進行更大的程式設計訪問。

### <a name="top-level-json-changes"></a>頂級 JSON 更改



V2 的頂級 JSON 屬性是，`verbose`當設置為 true 時，該屬性返回`intents`屬性中的所有意圖及其分數：

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 的頂級 JSON 屬性是：

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

該`intents`物件是無序清單。 不要假定 對應于`intents`中的第一個子級`topIntent`。 相反，使用`topIntent`該值查找分數：

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

回應 JSON 架構更改允許：

* 明確區分原始陳述、`query`和返回的預測。 `prediction`
* 更輕鬆地以程式設計方式訪問預測資料。 您可以按**名稱**訪問意圖和實體的值，而不是通過 V2 中的陣列枚舉。 對於預測的實體角色，將返回角色名稱，因為它在整個應用中是唯一的。
* 資料類型（如果確定）將受尊重。 數位不再作為字串返回。
* 在`$instance`物件中返回的第一優先順序預測資訊和附加中繼資料之間的區別。

### <a name="entity-response-changes"></a>實體回應更改

#### <a name="marking-placement-of-entities-in-utterances"></a>標記實體在陳述中的放置

**在 V2**中，實體在 話語中標有`startIndex`和`endIndex`。

**在 V3**中，實體標記為`startIndex``entityLength`和 。

#### <a name="access-instance-for-entity-metadata"></a>實體`$instance`中繼資料的訪問

如果需要實體中繼資料，查詢字串需要使用`verbose=true`標誌，並且回應包含物件中的`$instance`中繼資料。 示例在以下各節中的 JSON 回應中顯示。

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>每個預測實體都表示為數組

該`prediction.entities.<entity-name>`物件包含一個陣列，因為每個實體都可以在陳述中預測多個次。

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>預構建實體更改

V3 回應物件包括對預構建實體的更改。 查看[特定的預構建實體](luis-reference-prebuilt-entities.md)以瞭解更多資訊。

#### <a name="list-entity-prediction-changes"></a>列出實體預測更改

清單實體預測的 JSON 已更改為數組陣列：

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
每個內部陣列都對應于話語內的文本。 內建物件是陣列，因為相同的文本可以出現在清單實體的多個子清單中。

當`entities`物件映射到`$instance`物件時，將保留清單實體預測的物件順序。

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>實體角色名稱而不是機構名稱

在 V2`entities`中，陣列返回所有預測實體，機構名稱為唯一識別碼。 在 V3 中，如果實體使用角色，並且預測是實體角色，則主識別碼是角色名稱。 這是可能的，因為實體角色名稱在整個應用中必須是唯一的，包括其他模型（意圖、實體）名稱。

在下面的示例中：請考慮包含文本的表述。 `Yellow Bird Lane` 此文本被預測為 自訂`Location`實體的角色`Destination`。

|陳述文本|實體名稱|角色名稱|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

在 V2 中，實體由實體_名稱_標識，該機構名稱具有作為物件屬性的角色：

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

在 V3 中，_實體角色_引用該實體，如果預測針對該角色：

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

在 V3 中，與`verbose`標誌返回實體中繼資料的結果相同：

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

## <a name="external-entities-passed-in-at-prediction-time"></a>預測時傳入的外部實體

外部實體使 LUIS 應用能夠在運行時標識和標記實體，這些實體可用作現有實體的功能。 這允許您在將查詢發送到預測終結點之前使用您自己的單獨實體提取器。 由於此操作是在查詢預測終結點上完成的，因此無需重新訓練併發布模型。

用戶端應用程式通過提供自己的實體提取器，通過管理實體匹配和確定該匹配實體的話語中的位置，然後隨要求傳送該資訊。

外部實體是擴展任何實體類型的機制，同時仍用作其他模型（如角色、複合和其他模型）的信號。

這對於僅在查詢預測運行時具有可用資料的實體很有用。 此類資料的示例是不斷變化的資料或每個使用者的特定資料。 您可以擴展 LUIS 連絡人實體，該實體具有來自使用者連絡人清單中的外部資訊。

### <a name="entity-already-exists-in-app"></a>應用中已存在實體

在終結點請求`entityName`POST 正文中傳遞的外部實體的值必須在發出請求時已存在於已訓練和已發佈的應用中。 實體類型並不重要，所有類型都受支援。

### <a name="first-turn-in-conversation"></a>第一次轉談

考慮聊天機器人對話中的第一個陳述，其中使用者輸入以下不完整的資訊：

`Send Hazem a new message`

從聊天機器人到 LUIS 的請求可以在 POST 正文中傳遞有關`Hazem`的資訊，以便直接匹配為使用者的連絡人之一。

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

預測回應包括外部實體和所有其他預測實體，因為它在請求中定義。

### <a name="second-turn-in-conversation"></a>對話的第二回合

下一個使用者對聊天機器人的表述使用一個更模糊的術語：

`Send him a calendar reminder for the party.`

在前面的話語中，話語用作`him`對 的`Hazem`引用。 對話聊天機器人，在POST正文中，可以映射到`him`從第一個話語中提取的實體值。 `Hazem`

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

預測回應包括外部實體和所有其他預測實體，因為它在請求中定義。

### <a name="override-existing-model-predictions"></a>覆蓋現有模型預測

選項`preferExternalEntities`屬性指定，如果使用者發送的外部實體與具有相同名稱的預測實體重疊，LUIS 將選擇傳入的實體或模型中存在的實體。

例如，請考量 `today I'm free` 查詢。 LUIS 檢測`today`為日期時間 V2，具有以下回應：

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

如果使用者發送外部實體：

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

`preferExternalEntities`如果 設置為`false`，LUIS 將返回回應，就像未發送外部實體一樣。

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

`preferExternalEntities`如果 設置為`true`，LUIS 將返回一個回應，包括：

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解決方案

_可選_`resolution`屬性返回預測回應中，允許您傳遞與外部實體關聯的中繼資料，然後在回應中接收它。

主要目的是擴展預構建的實體，但不限於該實體類型。

屬性`resolution`可以是數位、字串、物件或陣列：

* "達拉斯"
* {"文本"："值"}
* 12345
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>預測時傳入的動態清單

動態清單允許您擴展現有的已訓練和已發佈的清單實體，已在 LUIS 應用中。

當清單實體值需要定期更改時，請使用此功能。 此功能允許您擴展已經過培訓併發布的清單實體：

* 在查詢預測終結點請求時。
* 對於單個請求。

清單實體在 LUIS 應用中可以為空，但它必須存在。 LUIS 應用中的清單實體未更改，但終結點的預測能力將擴展至最多包含 2 個包含約 1，000 個專案的清單。

### <a name="dynamic-list-json-request-body"></a>動態清單 JSON 請求正文

在以下 JSON 正文中發送一個包含同義字的新子清單，並預測文本的清單實體，`LUIS`以及`POST`查詢預測請求：

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

預測回應包括該清單實體和所有其他預測實體，因為它在請求中定義。

## <a name="deprecation"></a>淘汰

V2 API 在 V3 預覽後至少 9 個月內不會棄用。

## <a name="next-steps"></a>後續步驟

使用 V3 API 文檔將現有的 REST 調用更新到 LUIS[終結點](https://aka.ms/luis-api-v3)API。
