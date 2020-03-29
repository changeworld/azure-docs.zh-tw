---
title: 資料提取 - LUIS
description: 從具有意圖和實體的話語文本中提取資料。 瞭解可以從語言理解 （LUIS） 中提取哪些類型的資料。
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 1c1a744c06e5347625fb96518bd809481ee797e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221527"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>從具有意圖和實體的話語文本中提取資料
LUIS 可讓您從使用者的自然語言語句取得資訊。 此資訊的擷取方式使得它可供程式、應用程式或 Chatbot 用來執行動作。 在下列各節中，您將透過 JSON 範例，了解從意圖和實體會傳回哪些資料。

最難擷取的資料是機器學習資料，因為它不是全文相符的資料。 機器學習[實體](luis-concept-entity-types.md)的資料擷取必須是[撰寫循環](luis-concept-app-iteration.md)的一部分，直到您確信收到預期的資料為止。

## <a name="data-location-and-key-usage"></a>資料位置和金鑰使用方式
LUIS 會從已發佈的[端點](luis-glossary.md#endpoint)提供資料。 **HTTPS 要求** (POST 或 GET) 除了包含一些額外的設定 (例如預備或生產環境) 之外，也包含語句。

#### <a name="v2-prediction-endpoint-request"></a>[V2 預測端點要求](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-request"></a>[V3 預測端點要求](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

當您編輯 LUIS 應用程式時，從該 LUIS 應用程式的 [設定]**** 頁面，以及從您 URL 的一部分 (在 `/apps/` 之後)，都可以取得 `appID`。 `subscription-key` 是用來查詢您應用程式的端點金鑰。 在學習 LUIS 期間，雖然您可以使用免費的撰寫/入門金鑰，但請務必將端點金鑰變更為支援[預期的 LUIS 使用方式](luis-boundaries.md#key-limits)的金鑰。 `timezoneOffset` 單位為分鐘。

**HTTPS 回應**包含 LUIS 可以根據目前已發佈之預備或生產環境端點模型來判斷的所有意圖和實體資訊。 端點 URL 是在 [LUIS](luis-reference-regions.md) 網站、[管理]**** 區段、[金鑰和端點]**** 頁面上找到的。

## <a name="data-from-intents"></a>來自意圖的資料
主要資料是最高分的**意圖名稱**。 端點回應為：

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

|資料物件|資料類型|資料位置|值|
|--|--|--|--|
|Intent|String|topScoringIntent.intent|"GetStoreInfo"|

如果您的聊天機器人或 LUIS 呼叫應用根據多個意圖分數做出決定，請返回所有意圖的分數。


#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

設置查詢字串參數`verbose=true`。 端點回應為：

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

設置查詢字串參數`show-all-intents=true`。 端點回應為：

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

意圖會依最高分到最低分排序。

|資料物件|資料類型|資料位置|值|Score|
|--|--|--|--|:--|
|Intent|String|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intent|String|intents[1].intent|"None"|0.0168218873|

如果您新增預先建置的定義域，則意圖名稱除了會指出意圖之外，也會指出該定義域，例如 `Utilties` 或 `Communication`：

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

|網域|資料物件|資料類型|資料位置|值|
|--|--|--|--|--|
|公用事業|Intent|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|通訊|Intent|String|intents[1].intent|<b>Communication</b>.StartOver"|
||Intent|String|intents[2].intent|"None"|


## <a name="data-from-entities"></a>來自實體的資料
大多數 Chatbot 和應用程式都不僅僅只是需要意圖名稱。 這個額外的選擇性資料來自在語句中探索到的實體。 每個類型的實體會傳回與比對相關的不同資訊。

語句中的單一單字或片語可能會與多個實體相符。 在該情況下，系統會傳回每個相符的實體及其分數。

所有實體都會在端點回應的**實體**陣列中傳回：

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

## <a name="tokenized-entity-returned"></a>傳回的 Token 化實體

查看 LUIS 中的[權杖支援](luis-language-support.md#tokenization)。

## <a name="simple-entity-data"></a>簡單實體資料

[簡單實體](reference-entity-simple.md)是一個機器學習值。 它可以是一個單字或片語。

## <a name="composite-entity-data"></a>複合實體資料

[複合實體](reference-entity-composite.md)由其他實體組成，如預構建實體、簡單、正則運算式和清單實體。 個別實體會構成一個完整的提體。

## <a name="list-entity-data"></a>清單實體資料

[清單實體](reference-entity-list.md)表示一組固定的、封閉的相關單詞及其同義字。 LUIS 並不會探索清單實體的額外值。 使用**建議**功能，以根據目前的清單查看適用於新字組的建議。 如果有多個清單實體具有相同的值，則在端點查詢中會傳回每個實體。

## <a name="prebuilt-entity-data"></a>預先建置的實體資料
探索[預先建置](luis-concept-entity-types.md)實體時，會使用開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案，根據規則運算式比對進行探索。 預先建置的實體會在實體陣列中傳回，並使用前面加上 `builtin::` 的類別名稱。 以下文字是一個範例語句，其中含有所傳回的預先建置實體：

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

如果沒有查詢字串參數， `verbose=true`

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

使用查詢字串參數： `verbose=true`

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *
## <a name="regular-expression-entity-data"></a>規則運算式實體資料

[正則運算式實體](reference-entity-regular-expression.md)基於您提供的正則運算式模式提取實體。

## <a name="extracting-names"></a>擷取名稱
從語句中取得名稱相當困難，因為名稱幾乎可以是字母與單字的任何組合。 視所要擷取的名稱類型而定，您會有數個選項。 下列建議不是規定，而是指導方針。

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>新增預建的 PersonName 和 GeographyV2 實體

[PersonName](luis-reference-prebuilt-person.md) 和 [GeographyV2](luis-reference-prebuilt-geographyV2.md) 實體可在某些[語言文化特性](luis-reference-prebuilt-entities.md)中使用。

### <a name="names-of-people"></a>人名

人名可依據語言和文化特性而有些微的格式。 使用預構建的**[personName](luis-reference-prebuilt-person.md)** 實體或具有名字和姓氏[角色](luis-concept-roles.md)**[的簡單實體](luis-concept-entity-types.md#simple-entity)**。

如果使用簡單實體，請確保提供在話語的不同部分使用名字和姓氏的示例、不同長度的話語以及包含"無"意圖的所有意圖的話語。 請定期[檢閱](luis-how-to-review-endoint-utt.md)端點語句，以標記任何未正確預測的名稱。

### <a name="names-of-places"></a>地名

位置名稱被設置和已知，如城市、縣、州、省和國家/地區。 使用預構建的實體**[地理 V2](luis-reference-prebuilt-geographyv2.md)** 提取位置資訊。

### <a name="new-and-emerging-names"></a>全新和新興的名稱

有些應用程式需要能夠尋找全新和新興的名稱，例如產品或公司。 這些類型的名稱是最難的資料提取類型。 從**[一個簡單的實體](luis-concept-entity-types.md#simple-entity)** 開始，然後添加[片語清單](luis-concept-feature.md)。 請定期[檢閱](luis-how-to-review-endoint-utt.md)端點語句，以標記任何未正確預測的名稱。

## <a name="pattern-roles-data"></a>模式角色資料
角色是實體的內容相關差異。


#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

機構名稱是`Location`，具有兩個角色`Origin``Destination`和 。

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

在 V3 中，**角色名稱**是物件的主名稱。

機構名稱是`Location`，具有兩個角色`Origin``Destination`和 。

如果沒有查詢字串參數， `verbose=true`

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

使用查詢字串參數： `verbose=true`

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

## <a name="patternany-entity-data"></a>Pattern.any 實體資料

[Pattern.any](reference-entity-pattern-any.md)是一個可變長度預留位置，僅用於模式的範本陳述中，用於標記實體的開始和結束位置。

## <a name="sentiment-analysis"></a>情感分析
如果已設定情感分析，LUIS JSON 回應就會包含情感分析。 若要深入了解情感分析，請參閱[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)文件。

### <a name="sentiment-data"></a>情感資料
情感資料是一個介於 1 與 0 之間的分數，指出資料的正面 (較接近 1) 或負面 (較接近 0) 情感。

當文化特性為 `en-us` 時，回應為：

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

針對所有其他文化特性，回應為：

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>關鍵片語擷取實體資料
關鍵片語擷取實體會傳回語句中[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)所提供的關鍵片語。


#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

如果沒有查詢字串參數， `verbose=true`

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

使用查詢字串參數： `verbose=true`

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *


## <a name="data-matching-multiple-entities"></a>與多個實體相符的資料

LUIS 會傳回在語句中探索到的所有實體。 因此，您的 Chatbot 可能需要根據結果進行決策。 一個語句可以包含許多實體：

`book me 2 adult business tickets to paris tomorrow on air france`

LUIS 終結點可以在不同的實體中發現相同的資料。

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

不`verbose=true`作為查詢字串參數。

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

用作`verbose=true`查詢字串參數。


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

## <a name="data-matching-multiple-list-entities"></a>與多個清單實體相符的資料

如果單字或片語與多個清單實體相符，端點查詢會傳回每個清單實體。

如果查詢為 `when is the best time to go to red rock?`，且應用程式在多個清單中有 `red` 一字，LUIS 就會辨識所有實體，並在 JSON 端點回應中傳回實體陣列：

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

查詢`verbose=true`字串中無：

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


在`verbose=true`查詢字串中：

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

## <a name="next-steps"></a>後續步驟

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
