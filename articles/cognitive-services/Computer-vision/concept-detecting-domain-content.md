---
title: 特定于域的內容 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 了解如何指定影像分類網域，以傳回影像的詳細資訊。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68946331"
---
# <a name="detect-domain-specific-content"></a>偵測特定領域內容

除了標記與高層級分類之外，電腦視覺也支援使用透過特製化資料定型的模型進一步進行特定領域的分析。

特定領域模型有兩種使用方式：一是透過本身 (已設定範圍的分析)，一是做為分類功能的增強功能。

### <a name="scoped-analysis"></a>已設定範圍的分析

您可以只使用所選的特定領域模型呼叫 [Models/\<model\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API 來分析影像。

以下是針對指定的影像由 **models/celebrities/analyze** API 傳回的 JSON 回應範例：

![薩蒂亞·納德拉站著，面帶微笑](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>強化分類分析

也可以使用特定領域模型，來補足一般的影像分析。 您可以在 [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 呼叫的 details ** 參數中指定特定領域模型，做為[高階分類](concept-categorizing-images.md)的一環來進行。

在此情況下，必須先呼叫 86 類別分類器。 如果任何偵測到的類別符合特定領域模型，即會透過該模型傳遞影像，並新增結果。

下列 JSON 回應顯示了如何納入網域特定分析，使之成為更廣泛的分類分析中的 `detail` 節點。

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>列出特定領域模型

目前，電腦視覺支援下列特定領域模型：

| 名稱 | 描述 |
|------|-------------|
| 名人 | 支援 `people_` 類別中分類影像的名人辨識 |
| 地標 | 支援 `outdoor_` 或 `building_` 類別中分類影像的地標辨識 |

呼叫 [Models](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API 會傳回此資訊，以及每個模型可以套用的類別：

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

了解[分類影像](concept-categorizing-images.md)的相關概念。
