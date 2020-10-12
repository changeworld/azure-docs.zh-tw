---
title: 序數 V2 預建實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中的序數 V2 預建實體資訊。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 3fd80389dfa54cb8386c13e0e05a1e71ac4bd956
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541929"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的序數 V2 預建實體
序數 V2 數位會展開 [序數](luis-reference-prebuilt-ordinal.md) 以提供相對參考 `next` ，例如、 `last` 和 `previous` 。 這些都不會使用序數預建實體進行解壓縮。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>預建序數 V2 實體的解析

查詢會傳回下列實體物件：

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

以下是將 `verbose` 參數設定為的 JSON `false` ：

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

#### <a name="v3-verbose-response"></a>[V3 詳細資訊回應](#tab/V3-verbose)

以下是將 `verbose` 參數設定為的 JSON `true` ：

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

下列範例顯示 **ordinalV2** 實體的解析。

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

瞭解 [百分比](luis-reference-prebuilt-percentage.md)、 [電話號碼](luis-reference-prebuilt-phonenumber.md)和 [溫度](luis-reference-prebuilt-temperature.md) 實體。
