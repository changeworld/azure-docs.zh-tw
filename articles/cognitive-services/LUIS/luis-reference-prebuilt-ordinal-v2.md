---
title: 序數 V2 預建實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding （LUIS）中的序數 V2 預建實體資訊。
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270491"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的序數 V2 預建實體
序數 V2 數位會展開[序數](luis-reference-prebuilt-ordinal.md)以提供相對參考，例如 `next`、`last`和 `previous`。 這些不會使用序數預建實體來解壓縮。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>預先建立的序數 V2 實體解析

系統會針對查詢傳回下列實體物件：

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

下列 JSON 會將 `verbose` 參數設定為 `false`：

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

下列 JSON 會將 `verbose` 參數設定為 `true`：

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

下列範例顯示**ordinalV2**實體的解決方式。

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
