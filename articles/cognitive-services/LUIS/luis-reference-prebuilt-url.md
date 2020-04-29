---
title: URL 預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 url 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270356"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的 URL 預先建置實體
URL 實體會擷取含有網域名稱或 IP 位址的 URL。 因為此實體已經定型，所以您不需要將包含 URL 的範例語句加入應用程式。 只有 `en-us` 文化特色才支援 URL 實體。

## <a name="types-of-urls"></a>URL 類型
Url 是從辨識器[文字](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)GitHub 存放庫進行管理

## <a name="resolution-for-prebuilt-url-entity"></a>解析預先建置的 URL 實體

系統會針對查詢傳回下列實體物件：

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3 回應](#tab/V3)

下列 JSON 是將`verbose`參數設定為： `false`

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 詳細回應](#tab/V3-verbose)

下列 JSON 是將`verbose`參數設定為： `true`

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
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

下列範例顯示的解決方式https://www.luis.ai是人工智慧的絕佳認知服務範例

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

了解 [ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md) 和 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。
