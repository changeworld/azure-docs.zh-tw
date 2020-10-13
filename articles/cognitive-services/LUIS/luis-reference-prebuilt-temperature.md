---
title: 溫度預建實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 temperature 實體資訊。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 46161a83d261ae23ca45b7293e48ff15e435f42d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535333"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的溫度預先建置實體
Temperature 實體會擷取各種溫度類型。 因為此實體已經定型，所以您不需要將包含溫度的範例語句加入應用程式。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援 temperature 實體。

## <a name="types-of-temperature"></a>溫度類型
溫度可從辨識器 [-文字](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) GitHub 存放庫進行管理

## <a name="resolution-for-prebuilt-temperature-entity"></a>解析預先建置的 temperature 實體

查詢會傳回下列實體物件：

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[V3 回應](#tab/V3)

以下是將 `verbose` 參數設定為的 JSON `false` ：

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細資訊回應](#tab/V3-verbose)
以下是將 `verbose` 參數設定為的 JSON `true` ：

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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

下列範例顯示解析的 **builtin.temperature** 實體。

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

了解 [percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md) 及 [age](luis-reference-prebuilt-age.md) 實體相關資訊。
