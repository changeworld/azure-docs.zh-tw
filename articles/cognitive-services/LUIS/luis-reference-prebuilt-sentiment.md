---
title: 情感分析 - LUIS
titleSuffix: Azure Cognitive Services
description: 如果已設定情感分析，LUIS JSON 回應就會包含情感分析。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: dee07859da97f76d9a2db3ccbb71ae74d8ff4846
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270411"
---
# <a name="sentiment-analysis"></a>情感分析
如果已設定情感分析，LUIS JSON 回應就會包含情感分析。 若要深入了解情感分析，請參閱[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)文件。


## <a name="resolution-for-sentiment"></a>情感的解決方法

情感資料是一個介於 1 與 0 之間的分數，指出資料的正面 (較接近 1) 或負面 (較接近 0) 情感。

#### <a name="english-language"></a>[英文語言](#tab/english)

當文化特性為 `en-us` 時，回應為：

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[其他語言](#tab/other-languages)

針對所有其他文化特性，回應為：

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>後續步驟

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

