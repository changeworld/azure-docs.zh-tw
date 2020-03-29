---
title: 複合實體類型 - LUIS
titleSuffix: Azure Cognitive Services
description: 複合實體由其他實體組成，如預構建實體、簡單、正則運算式和清單實體。 個別實體會構成一個完整的提體。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "71695158"
---
# <a name="composite-entity"></a>複合實體 

複合實體由其他實體組成，如預構建實體、簡單、正則運算式和清單實體。 個別實體會構成一個完整的提體。 

**當資料有下列特性時，最適用此實體：**

* 彼此相關。 
* 在語句的內容中彼此相關。
* 使用各種實體類型。
* 需要由用戶端應用程式當作一個資訊單位進行分組和處理。
* 有各種使用者語句需要使用機器學習。

![複合實體](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>範例 JSON

考慮預構建`number`的複合實體，並`Location::ToLocation`具有以下話語：

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

如果在`verbose=false`查詢字串中設置，則這是 JSON：

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

如果在`verbose=true`查詢字串中設置，則這是 JSON：

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
|預構建實體 - 地理V2|"Location::ToLocation"|"開羅"|

## <a name="next-steps"></a>後續步驟

在[本教程中](luis-tutorial-composite-entity.md)，添加一個**複合實體**，將各種類型的提取資料捆綁到單個包含實體中。 用戶端應用程式可藉由組合資料，輕鬆地擷取不同資料類型的相關資料。
