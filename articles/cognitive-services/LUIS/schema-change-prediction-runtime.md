---
title: 在執行時間擴充應用程式-LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81538654"
---
# <a name="extend-app-at-prediction-runtime"></a>在預測執行時間擴充應用程式

應用程式的架構（模型和功能）會定型併發行至預測端點。 這個已發行的模型會用於預測執行時間。 您可以將新資訊連同使用者的語句傳遞至預測執行時間，以擴大預測。

兩個預測執行時間架構變更包括：
* [外部實體](#external-entities)
* [動態清單](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>外部實體

外部實體可讓您的 LUIS 應用程式在執行時間中識別實體並為其加上標籤，以做為現有實體的功能。 這可讓您在將查詢傳送至預測端點之前，先使用自己的個別和自訂實體擷取器。 因為這是在查詢預測端點上完成，所以您不需要重新定型和發行您的模型。

用戶端應用程式會藉由管理實體比對，以及判斷該相符實體的語句中的位置，然後透過要求傳送該資訊，來提供自己的實體解壓縮程式。

外部實體是擴充任何實體類型的機制，同時仍然用來作為其他模型的信號。

這適用于只有查詢預測執行時間可以使用資料的實體。 這類資料的範例包括經常變更的資料，或每位使用者的特定。 您可以使用使用者連絡人清單中的外部資訊來擴充 LUIS contact 實體。

外部實體是 V3 撰寫 API 的一部分。 深入瞭解如何[遷移](luis-migration-api-v3.md)至此版本。

### <a name="entity-already-exists-in-app"></a>實體已存在於應用程式中

外部實體（ `entityName`在端點要求張貼本文中傳遞）的值必須已存在於提出要求時，已定型和已發佈的應用程式中。 實體的類型並不重要，所有類型都受到支援。

### <a name="first-turn-in-conversation"></a>第一次開啟對話

請考慮在聊天機器人交談中的第一個語句，其中使用者會輸入下列未完成的資訊：

`Send Hazem a new message`

從聊天機器人到 LUIS 的要求可以傳入張貼本文中有關`Hazem`的資訊，因此它會直接與其中一個使用者連絡人進行比對。

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

預測回應會包含該外部實體，以及所有其他的預測實體，因為它是在要求中定義。

### <a name="second-turn-in-conversation"></a>第二回合交談

下一個在聊天機器人中語句的使用者會使用更不清楚的詞彙：

`Send him a calendar reminder for the party.`

在此交談回合中，語句會使用`him`做為的參考。 `Hazem` POST 主體中的交談式聊天機器人可以對應`him`到從第一個語句中解壓縮的實體值。 `Hazem`

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

預測回應會包含該外部實體，以及所有其他的預測實體，因為它是在要求中定義。

### <a name="override-existing-model-predictions"></a>覆寫現有的模型預測

`preferExternalEntities` Options 屬性指定如果使用者傳送的外部實體與具有相同名稱的預測實體重迭，LUIS 會選擇傳入的實體或模型中現有的實體。

例如，請考量 `today I'm free` 查詢。 LUIS 會`today`偵測為具有下列回應的 datetimeV2：

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

如果設定`preferExternalEntities`為`false`，則 LUIS 會傳迴響應，就像未傳送外部實體一樣。

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

如果設定`preferExternalEntities`為`true`，則 LUIS 會傳迴響應，包括：

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解決方案

_選擇性_ `resolution`屬性會在預測回應中傳回，讓您傳入與外部實體相關聯的中繼資料，然後在回應中接收它。

主要的目的是要擴充預先建立的實體，但不限於該實體類型。

`resolution`屬性可以是數位、字串、物件或陣列：

* 舉行
* {"text"： "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>動態清單

動態清單可讓您擴充現有的已定型和已發佈清單實體（已在 LUIS 應用程式中）。

當您的清單實體值需要定期變更時，請使用這項功能。 這項功能可讓您擴充已定型和已發佈的清單實體：

* 在查詢預測端點要求時。
* 適用于單一要求。

清單實體在 LUIS 應用程式中可以是空的，但它必須存在。 LUIS 應用程式中的清單實體不會變更，但在端點上的預測功能會擴充為包含最多2個具有大約1000專案的清單。

### <a name="dynamic-list-json-request-body"></a>動態清單 JSON 要求主體

傳送下列 JSON 主體，將含有同義字的新子清單新增至清單，並使用`LUIS` `POST`查詢預測要求來預測文字的清單實體：

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
