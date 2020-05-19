---
title: 複合實體類型-LUIS
titleSuffix: Azure Cognitive Services
description: 複合實體是由其他實體所組成，例如預建實體、簡單、正則運算式和列出實體。 個別實體會構成一個完整的提體。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: bac37e91933d16f36f2d8917760968122a4f5619
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588899"
---
# <a name="composite-entity"></a>複合實體

複合實體是由其他實體所組成，例如預建實體、簡單、正則運算式和列出實體。 個別實體會構成一個完整的提體。

> [!CAUTION]
> 這個實體已被**取代**。 請遷移至[機器學習的實體](reference-entity-machine-learned-entity.md)。

**當資料有下列特性時，最適用此實體：**

* 彼此相關。
* 在語句的內容中彼此相關。
* 使用各種實體類型。
* 需要由用戶端應用程式當作一個資訊單位進行分組和處理。
* 有各種使用者語句需要使用機器學習。

![複合實體](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>範例 JSON

請考慮使用預建 `number` 和下列語句的複合實體 `Location::ToLocation` ：

`book 2 tickets to cairo`

請注意，數字 `2` 與 ToLocation `cairo` 之間有不屬於任何實體的單字。 [LUIS](luis-reference-regions.md) 網站中用於所標記語句的綠色底線表示複合實體。

![複合實體](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

複合實體會在 `compositeEntities` 陣列中傳回，而複合項目內的所有實體則會一併在 `entities` 陣列中傳回：

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

如果 `verbose=false` 是在查詢字串中設定，則這是 JSON：

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

如果 `verbose=true` 是在查詢字串中設定，則這是 JSON：

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *


|資料物件|實體名稱|值|
|--|--|--|
|預先建置的實體 - number|"builtin.number"|"2"|
|預先建立的實體-GeographyV2|"Location::ToLocation"|cairo|

## <a name="next-steps"></a>後續步驟

深入瞭解實體：

* [概念](luis-concept-entity-types.md)
* [如何建立](luis-how-to-add-entities.md)
