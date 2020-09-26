---
title: 在執行時間擴充應用程式-LUIS
description: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 69e2608fb01ece81f555aae2f3d4a2e4a05cfc90
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322796"
---
# <a name="extend-app-at-prediction-runtime"></a>在預測執行時間擴充應用程式

應用程式的架構 (模型和功能) 已定型併發行至預測端點。 這個已發行的模型用於預測執行時間。 您可以將新資訊以及使用者的語句傳遞至預測執行時間，以擴大預測。

兩個預測執行時間架構變更包括：
* [外部實體](#external-entities)
* [動態清單](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>外部實體

外部實體可讓您的 LUIS 應用程式在執行時間識別和標記實體的能力，而這些實體可作為現有實體的功能使用。 這可讓您使用自己的個別和自訂實體擷取器，然後再將查詢傳送至您的預測端點。 由於這是在查詢預測端點上完成，因此您不需要重新定型和發佈您的模型。

用戶端應用程式會藉由管理實體比對來提供自己的實體解壓縮程式，並決定該相符實體的語句內的位置，然後將該資訊與要求一起傳送。

外部實體是一種擴充任何實體類型的機制，但仍可作為其他模型的信號。

這適用于只有在查詢預測執行時間才有可用資料的實體。 這類資料的範例包括不斷變化的資料或每位使用者的特定資料。 您可以從使用者的連絡人清單中，使用外部資訊來擴充 LUIS contact 實體。

外部實體是 V3 撰寫 API 的一部分。 深入瞭解如何 [遷移](luis-migration-api-v3.md) 至此版本。

### <a name="entity-already-exists-in-app"></a>實體已存在於應用程式中

當提出要求時，在 `entityName` 已定型和已發佈的應用程式中，傳遞給端點要求 POST 主體的外部實體，其值必須已經存在。 實體類型並不重要，支援所有類型。

### <a name="first-turn-in-conversation"></a>首次開啟對話

請考慮聊天機器人交談中的第一個語句，其中使用者會輸入下列未完成的資訊：

`Send Hazem a new message`

從聊天機器人 LUIS 至的要求可以傳入 POST 主體中的資訊 `Hazem` ，因此它會直接與使用者的其中一個連絡人相符。

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

預測回應包含該外部實體，以及所有其他預測的實體，因為它是在要求中定義。

### <a name="second-turn-in-conversation"></a>第二回合對話

下一次語句聊天機器人的使用者會使用更清楚的詞彙：

`Send him a calendar reminder for the party.`

在此交談回合中，語句會使用 `him` 做為的參考 `Hazem` 。 POST 主體中的交談式聊天機器人可以對應 `him` 到從第一個語句中解壓縮的實體值 `Hazem` 。

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

預測回應包含該外部實體，以及所有其他預測的實體，因為它是在要求中定義。

### <a name="override-existing-model-predictions"></a>覆寫現有的模型預測

`preferExternalEntities`Options 屬性指定如果使用者傳送的外部實體與具有相同名稱的預測實體重迭，LUIS 會選擇傳入的實體或模型中現有的實體。

例如，請考量 `today I'm free` 查詢。 LUIS 會以 `today` 下列回應偵測為 datetimeV2：

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

如果使用者傳送外部實體：

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

如果 `preferExternalEntities` 設定為，LUIS 就會傳回 `false` 回應，如同未傳送外部實體。

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

如果 `preferExternalEntities` 設定為 `true` ，LUIS 會傳迴響應，包括：

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解決方案

_選擇性_ `resolution` 屬性會在預測回應中傳回，可讓您傳入與外部實體相關聯的中繼資料，然後在回應中傳回。

主要用途是擴充預建實體，但不限於該實體類型。

`resolution`屬性可以是數位、字串、物件或陣列：

* 舉行
* {"text"： "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>動態清單

動態清單可讓您擴充現有已定型和已發佈的清單實體，其已在 LUIS 應用程式中。

當您的清單實體值需要定期變更時，請使用此功能。 這項功能可讓您擴充已定型和已發佈的清單實體：

* 在查詢預測端點要求時。
* 針對單一要求。

LUIS 應用程式中的清單實體可以是空的，但必須存在。 LUIS 應用程式中的清單實體不會變更，但端點的預測能力會擴充到最多包含大約1000個專案的2個清單。

### <a name="dynamic-list-json-request-body"></a>動態清單 JSON 要求主體

使用下列 JSON 主體來傳送新的子清單，以將同義字新增至清單，並 `LUIS` 使用查詢預測要求來預測文字的清單實體 `POST` ：

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

預測回應會包含該清單實體，以及所有其他預測的實體，因為它是在要求中定義。

## <a name="next-steps"></a>後續步驟

* [預測分數](luis-concept-prediction-score.md)
* [撰寫 API V3 變更](luis-migration-api-v3.md)
