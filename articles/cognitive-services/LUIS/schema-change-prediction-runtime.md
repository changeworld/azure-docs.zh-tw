---
title: 在執行時延伸應用 - LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538654"
---
# <a name="extend-app-at-prediction-runtime"></a>在預測執行時延伸應用程式

應用的架構(模型和功能)已訓練併發佈到預測終結點。 此已發佈的模型用於預測運行時。 您可以將新資訊以及用戶的話語傳遞給預測運行時,以增強預測。

兩個預測執行時架構變更包括:
* [外部實體](#external-entities)
* [動態清單](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>外部實體

外部實體使 LUIS 應用能夠在運行時標識和標記實體,這些實體可用作現有實體的功能。 這允許您在將查詢發送到預測終結點之前使用您自己的單獨實體提取器。 由於此操作是在查詢預測終結點上完成的,因此無需重新訓練併發佈模型。

用戶端應用程式通過提供自己的實體提取器,通過管理實體匹配和確定該匹配實體的話語中的位置,然後隨請求發送該資訊。

外部實體是擴展任何實體類型的機制,同時仍用作信號到其他模型。

這對於僅在查詢預測運行時具有可用數據的實體很有用。 此類數據的範例是不斷變化的數據或每個使用者的特定數據。 您可以擴展 LUIS 連絡人實體,該實體具有來自使用者聯繫人清單中的外部資訊。

外部實體是 V3 創作 API 的一部分。 瞭解有關[遷移到](luis-migration-api-v3.md)此版本的更多詳細資訊。

### <a name="entity-already-exists-in-app"></a>套用中已存在實體

在終結點請求`entityName`POST 正文中傳遞的外部實體的值必須在發出請求時已存在於已訓練和已發佈的應用中。 實體類型並不重要,所有類型都受支援。

### <a name="first-turn-in-conversation"></a>第一次轉談

考慮聊天機器人對話中的第一個陳述,其中使用者輸入以下不完整的資訊:

`Send Hazem a new message`

從聊天機器人到 LUIS 的請求可以在 POST 正`Hazem`文中傳遞有關 的資訊,以便直接匹配為使用者的聯繫人之一。

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

預測回應包括外部實體和所有其他預測實體,因為它在請求中定義。

### <a name="second-turn-in-conversation"></a>對話的第二回合

下一個使用者對聊天機器人的表述使用一個更模糊的術語:

`Send him a calendar reminder for the party.`

在對話的這一回合中,話語用作`him`對`Hazem`的引用。 對話聊天機器人,在POST正文中,可以映射到`him`從第一個話語中提取的實體值`Hazem`。

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

預測回應包括外部實體和所有其他預測實體,因為它在請求中定義。

### <a name="override-existing-model-predictions"></a>覆寫現有模型預測

選項`preferExternalEntities`屬性指定,如果使用者發送的外部實體與具有相同名稱的預測實體重疊,LUIS 將選擇傳入的實體或模型中存在的實體。

例如，請考量 `today I'm free` 查詢。 LUIS`today`檢測 為日期時間 V2,具有以下回應:

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

如果使用者傳送外部實體:

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

`preferExternalEntities`如果 設置`false`為 ,LUIS 將返回回應,就像未發送外部實體一樣。

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

`preferExternalEntities`如果 設定`true`為 ,LUIS 將傳回一個回應,包括:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解決方案

_可選_`resolution`屬性返回預測回應中,允許您傳遞與外部實體關聯的元數據,然後在回應中接收它。

主要目的是擴展預構建的實體,但不限於該實體類型。

屬性`resolution`可以是數字、字串、物件或陣列:

* "達拉斯"
* {"文字":"值"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>動態清單

動態清單允許您擴展現有的已訓練和已發佈的清單實體,已在 LUIS 應用中。

當清單實體值需要定期更改時,請使用此功能。 此功能允許您延伸已經過培訓並發布的清單實體:

* 在查詢預測終結點請求時。
* 對於單個請求。

清單實體在 LUIS 應用中可以為空,但它必須存在。 LUIS 應用中的清單實體未更改,但終結點的預測能力將擴展至最多包含 2 個包含約 1,000 個專案的清單。

### <a name="dynamic-list-json-request-body"></a>動態清單 JSON 要求正文

在以下 JSON 正文中傳送一個包含同義詞的新子清單,並預測文本的`LUIS`清單`POST`實體, 以及 查詢預測請求:

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

預測回應包括該列表實體和所有其他預測實體,因為它在請求中定義。

## <a name="next-steps"></a>後續步驟

* [預測分數](luis-concept-prediction-score.md)
* [編寫 API V3 變更](luis-migration-api-v3.md)
