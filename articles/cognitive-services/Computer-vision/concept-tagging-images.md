---
title: 內容標籤 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 了解電腦視覺 API 的影像標記功能相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 887f6d9163f800ecbe8a5ab9a0715fd171f1d3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244506"
---
# <a name="applying-content-tags-to-images"></a>將內容標記套用到影像

「電腦視覺」會依據數千個可辨識的物件、生物、景象及動作傳回標記。 若標記不明確或不屬於常識，API 回應會提供「提示」來釐清標記在已知設定內容中的意涵。 標籤不會組織成分類，且不會有繼承階層存在。 內容標記的集合會形成影像「描述」的基礎，讓此描述顯示為完整句子中已格式化而可供閱讀的語言。 請注意，英文是影像描述目前唯一支援的語言。

在上傳影像或指定影像 URL 之後，電腦視覺的演算法會根據在影像中識別出的物件、生物和動作來輸出標記。 標記並未限定於主體 (例如前景中的人物)，而是包含周遭環境 (室內或室外)、家具、工具、植物、動物、配件和小工具等。

## <a name="image-tagging-example"></a>影像標記範例

下列 JSON 回應說明了在範例影像中偵測到標記視覺功能時，電腦視覺傳回的內容。

![藍色房屋和前院](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>使用 API

標記功能是[分析圖像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包括在`Tags`**視覺化功能**查詢參數中。 然後，當您獲得完整的 JSON 回應時，只需分析節的內容的`"tags"`字串。

* [快速入門：電腦視覺 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [快速入門：分析圖像（REST API）](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>後續步驟

瞭解對[圖像](concept-categorizing-images.md)進行分類和[描述圖像](concept-describing-images.md)的相關概念。
