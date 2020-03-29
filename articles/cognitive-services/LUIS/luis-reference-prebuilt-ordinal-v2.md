---
title: 預構建實體 V2 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文在語言理解 （LUIS） 中包含預構建的實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270491"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>用於 LUIS 應用的預構建實體
Ordinal V2 編號[擴展了 Ordinal](luis-reference-prebuilt-ordinal.md)以提供`next`相對`last`引用，`previous`如 、和 。 這些不會使用預構建的實體提取。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>預構建的 Ddinal V2 實體的解析度

為查詢返回以下實體物件：

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

以下 JSON 的`verbose`參數設置為`false`：

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/V3-verbose)

以下 JSON 的`verbose`參數設置為`true`：

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
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
#### <a name="v2-response"></a>[V2 回應](#tab/V2)

下面的示例顯示了**內置.ordinalV2**實體的解析度。

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

瞭解[百分比](luis-reference-prebuilt-percentage.md)、[電話號碼](luis-reference-prebuilt-phonenumber.md)和[溫度](luis-reference-prebuilt-temperature.md)實體。
