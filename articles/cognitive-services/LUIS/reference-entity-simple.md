---
title: 簡單實體類型-LUIS
titleSuffix: Azure Cognitive Services
description: 簡單實體描述來自機器學習服務內容的單一概念。 使用簡單實體來改善結果時，請新增片語清單。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1b5754be3c9941101a53f332841ace93caf9acdd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684565"
---
# <a name="simple-entity"></a>簡單實體

簡單實體是描述單一概念並從機器學習內容學習的一般實體。 由於簡單實體通常是名稱，例如公司名稱、產品名稱或其他類別的名稱，因此，使用簡單實體時可加入[片語清單](luis-concept-feature.md)，以提升所用名稱所代表的信號。

**以下時機適用此實體：**

* 格式不一致但表示相同內容的資料。

![簡單實體](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>範例 JSON

`Bob Jones wants 3 meatball pho`

在先前的語句中，`Bob Jones` 被標記為簡單的 `Customer` 實體。

從端點傳回的資料會包含實體名稱、從語句探索到的文字，所探索文字的位置，以及分數：

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

如果 `verbose=false` 是在查詢字串中設定，則這是 JSON：

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
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

* * *

|資料物件|實體名稱|值|
|--|--|--|
|簡單實體|`Customer`|`bob jones`|

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [學習模式語法](reference-pattern-syntax.md)