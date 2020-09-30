---
title: LUIS 預建實體電子郵件參考
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 email 實體資訊。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 806357670bead54658f0b501ca20473293275d58
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533361"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的電子郵件預先建置實體
擷取的電子郵件包含語句中的整個電子郵件地址。 因為此實體已經定型，所以您不需要將包含電子郵件的範例語句加入應用程式意圖。 只有 `en-us` 文化特色才支援 email 實體。

## <a name="resolution-for-prebuilt-email"></a>解析預先建置的電子郵件

查詢會傳回下列實體物件：

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

以下是將 `verbose` 參數設定為的 JSON `false` ：

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細資訊回應](#tab/V3-verbose)

以下是將 `verbose` 參數設定為的 JSON `true` ：

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
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

下列範例顯示解析的 **builtin.email** 實體。

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

了解 [number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md) 及 [percentage](luis-reference-prebuilt-percentage.md)。
