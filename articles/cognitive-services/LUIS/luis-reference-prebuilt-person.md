---
title: PersonName 預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 此文章包含 Language Understanding (LUIS) 中預先建置的 PersonName 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273427"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的 PersonName 預先建置實體
預先建置的 personName 實體會偵測人員名稱。 由於此實體已經定型，因此您不需要將包含 PersonName 的範例語句加入至應用程式意圖。 personName 實體已支援英文與中文[文化特性](luis-reference-prebuilt-entities.md)。

## <a name="resolution-for-personname-entity"></a>personName 實體解析

系統會針對查詢傳回下列實體物件：

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[V3 回應](#tab/V3)


下列 JSON 會將 `verbose` 參數設定為 `false`：

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/V3-verbose)
下列 JSON 會將 `verbose` 參數設定為 `true`：

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[V2 回應](#tab/V2)

下列範例說明 **builtin.personName** 實體的解析。

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

了解 [email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md) 及 [ordinal](luis-reference-prebuilt-ordinal.md) 實體相關資訊。
