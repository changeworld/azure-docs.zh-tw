---
title: 序數預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 ordinal 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273451"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的序數預先建置實體
序號是一組物件內的數值表示法：`first`、`second`、`third`。 因為此實體已經定型，所以您不需要將包含序數的範例語句加入應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援 ordinal 實體。

## <a name="types-of-ordinal"></a>序數類型
序數是從辨識器[文字](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)的 GitHub 存放庫進行管理

## <a name="resolution-for-prebuilt-ordinal-entity"></a>解析預先建置的 ordinal 實體

系統會針對查詢傳回下列實體物件：

`Order the second option`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

下列 JSON 是將`verbose`參數設定為： `false`

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/V3-verbose)
下列 JSON 是將`verbose`參數設定為： `true`

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

下列範例顯示解析的 **builtin.ordinal** 實體。

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

瞭解[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)、[電話號碼](luis-reference-prebuilt-phonenumber.md)和[溫度](luis-reference-prebuilt-temperature.md)實體。
