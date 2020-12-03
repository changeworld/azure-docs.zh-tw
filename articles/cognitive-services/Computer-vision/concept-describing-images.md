---
title: 影像描述-電腦視覺
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
ms.openlocfilehash: 4fd328dad3544697ba1f4896f7383be857b097a5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530658"
---
# <a name="describe-images-with-human-readable-language"></a>描述映包含人類可讀語言的影像

電腦視覺可以分析影像，並產生人類看得懂的句子來描述其內容。 演算法實際上會根據不同的視覺功能傳回數個描述，而且每個描述都會獲得信賴分數。 最後的輸出是從最高到最低信賴分數排序的描述清單。

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

映射描述功能是「 [分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API」的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包含 `Description` 在 **visualFeatures** 查詢參數中。 然後，當您取得完整 JSON 回應時，只要剖析該區段內容的字串即可 `"description"` 。

* [快速入門：電腦視覺 REST API 或用戶端程式庫](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>後續步驟

瞭解 [標記影像](concept-tagging-images.md) 和 [分類影像](concept-categorizing-images.md)的相關概念。
