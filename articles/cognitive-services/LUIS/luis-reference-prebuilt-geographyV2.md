---
title: 地理 V2 預建實體-LUIS
titleSuffix: Azure Cognitive Services
description: 此文章包含 Language Understanding (LUIS) 中的 geographyV2 預建實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: b2b2b0781abce59628660b669f43110bf91b15e6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273500"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的 GeographyV2 預先建置實體
預建 geographyV2 實體可偵測位置。 因為此實體已經定型，所以您不需要將包含 GeographyV2 的範例語句加入應用程式意圖。 英文[文化特性](luis-reference-prebuilt-entities.md)支援 GeographyV2 實體。

## <a name="subtypes"></a>子類型
地理位置都有子類型：

|Subtype|目的|
|--|--|
|`poi`|感興趣的點|
|`city`|城市名稱|
|`countryRegion`|國家或地區名稱|
|`continent`|洲別名稱|
|`state`|州或省名稱|


## <a name="resolution-for-geographyv2-entity"></a>GeographyV2 實體解析

系統會針對查詢傳回下列實體物件：

`Carol is visiting the sphinx in gizah egypt in africa before heading to texas.`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

下列 JSON 是將`verbose`參數設定為： `false`

```json
"entities": {
    "geographyV2": [
        {
            "value": "the sphinx",
            "type": "poi"
        },
        {
            "value": "gizah",
            "type": "city"
        },
        {
            "value": "egypt",
            "type": "countryRegion"
        },
        {
            "value": "africa",
            "type": "continent"
        },
        {
            "value": "texas",
            "type": "state"
        }
    ]
}
```

在前面的 JSON 中`poi` ，是一**種感興趣點**的縮寫。

#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/V3-verbose)

下列 JSON 是將`verbose`參數設定為： `true`

```json
"entities": {
    "geographyV2": [
        {
            "value": "the sphinx",
            "type": "poi"
        },
        {
            "value": "gizah",
            "type": "city"
        },
        {
            "value": "egypt",
            "type": "countryRegion"
        },
        {
            "value": "africa",
            "type": "continent"
        },
        {
            "value": "texas",
            "type": "state"
        }
    ],
    "$instance": {
        "geographyV2": [
            {
                "type": "builtin.geographyV2.poi",
                "text": "the sphinx",
                "startIndex": 18,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.city",
                "text": "gizah",
                "startIndex": 32,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.countryRegion",
                "text": "egypt",
                "startIndex": 38,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.continent",
                "text": "africa",
                "startIndex": 47,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.state",
                "text": "texas",
                "startIndex": 72,
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
```
#### <a name="v2-response"></a>[V2 回應](#tab/V2)

下列範例示範如何解析 **builtin.geographyV2** 實體。

```json
"entities": [
    {
        "entity": "the sphinx",
        "type": "builtin.geographyV2.poi",
        "startIndex": 18,
        "endIndex": 27
    },
    {
        "entity": "gizah",
        "type": "builtin.geographyV2.city",
        "startIndex": 32,
        "endIndex": 36
    },
    {
        "entity": "egypt",
        "type": "builtin.geographyV2.countryRegion",
        "startIndex": 38,
        "endIndex": 42
    },
    {
        "entity": "africa",
        "type": "builtin.geographyV2.continent",
        "startIndex": 47,
        "endIndex": 52
    },
    {
        "entity": "texas",
        "type": "builtin.geographyV2.state",
        "startIndex": 72,
        "endIndex": 76
    },
    {
        "entity": "carol",
        "type": "builtin.personName",
        "startIndex": 0,
        "endIndex": 4
    }
]
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

了解 [email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md) 及 [ordinal](luis-reference-prebuilt-ordinal.md) 實體相關資訊。
