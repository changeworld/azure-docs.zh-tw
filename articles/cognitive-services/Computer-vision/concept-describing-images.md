---
title: 圖像描述 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 電腦視覺 API 的影像描述功能相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244700"
---
# <a name="describe-images-with-human-readable-language"></a>描述映包含人類可讀語言的影像

電腦視覺可以分析影像，並產生人類看得懂的句子來描述其內容。 該演算法實際上根據不同的視覺特徵返回多個描述，並且每個描述都給出一個置信度分數。 最後的輸出是從最高到最低信賴分數排序的描述清單。

## <a name="image-description-example"></a>影像描述範例

下列 JSON 回應說明了在根據視覺功能描述範例影像時，電腦視覺傳回的內容。

![曼哈頓建築物的黑白照片](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>使用 API

圖像描述功能是[分析圖像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包括在`Description`**視覺化功能**查詢參數中。 然後，當您獲得完整的 JSON 回應時，只需分析節的內容的`"description"`字串。

* [快速入門：電腦視覺 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [快速入門：分析圖像（REST API）](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>後續步驟

瞭解[標記圖像](concept-tagging-images.md)和[分類圖像](concept-categorizing-images.md)的相關概念。
