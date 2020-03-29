---
title: 電話號碼 預建實體 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 phonenumber 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270477"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>LUIS 應用預先構建的序號
`phonenumber` 實體會擷取各種不同的電話號碼，包括國碼/區碼。 因為此實體已經定型，所以您不需要將範例語句加入應用程式。 只有 `en-us` 文化特色才支援 `phonenumber` 實體。

## <a name="types-of-a-phone-number"></a>電話號碼的類型
`Phonenumber`從[識別器文本](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)GitHub 存儲庫進行管理

## <a name="resolution-for-this-prebuilt-entity"></a>此預構建實體的解析度

為查詢返回以下實體物件：

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

以下 JSON 的`verbose`參數設置為`false`：

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/V3-verbose)
以下 JSON 的`verbose`參數設置為`true`：

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

下列範例顯示解析的 **builtin.phonenumber** 實體。

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

了解 [percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md) 及 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。
