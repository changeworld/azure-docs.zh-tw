---
title: 維度預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 dimension 實體資訊。
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3c7bebb38d0331c76eeaaa1d9d6464c568a61b83
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270641"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的維度預先建置實體
預先建置的 dimension 實體可偵測各種維度類型，無論 LUIS 應用程式文化特色為何。 因為此實體已經定型，所以您不需要將包含維度的範例語句加入應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援 dimension 實體。

## <a name="types-of-dimension"></a>維度類型

維度是從辨識器[文字](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)的 GitHub 存放庫進行管理。

## <a name="resolution-for-dimension-entity"></a>解析 dimension 實體

系統會針對查詢傳回下列實體物件：

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

下列 JSON 會將 `verbose` 參數設定為 `false`：

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/V3-verbose)
下列 JSON 會將 `verbose` 參數設定為 `true`：

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

下列範例顯示解析的 **builtin.dimension** 實體。

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

了解 [email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md) 及 [ordinal](luis-reference-prebuilt-ordinal.md) 實體相關資訊。
