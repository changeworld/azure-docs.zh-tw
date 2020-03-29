---
title: 預構建實體百分比 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的百分比實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270504"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的百分比預先建置實體
百分比數字可顯示為分數 `3 1/2` 或百分比 `2%`。 由於此實體已經定型，因此您不需要將包含百分比的範例語句加入至應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援百分比實體。

## <a name="types-of-percentage"></a>百分比類型
百分比從[識別器文本](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)GitHub 存儲庫進行管理

## <a name="resolution-for-prebuilt-percentage-entity"></a>解析預先建置的百分比實體

為查詢返回以下實體物件：

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

以下 JSON 的`verbose`參數設置為`false`：

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/V3-verbose)
以下 JSON 的`verbose`參數設置為`true`：

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

下列範例說明 **builtin.percentage** 實體的解析。

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

了解 [ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md) 和 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。
