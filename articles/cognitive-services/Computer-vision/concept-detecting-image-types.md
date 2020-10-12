---
title: 影像類型偵測-電腦視覺
titleSuffix: Azure Cognitive Services
description: 電腦視覺 API 的影像類別偵測功能相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244557"
---
# <a name="detecting-image-types-with-computer-vision"></a>使用電腦視覺偵測影像類型

使用 [分析影像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API，電腦視覺可以分析影像的內容類型，指出影像為美工圖案或線條繪圖。

## <a name="detecting-clip-art"></a>偵測美工圖案

電腦視覺分析影像，並依照 0 到 3 的等級評估該影像是美工圖案的可能性，如下表所述。

| 值 | 意義 |
|-------|---------|
| 0 | 非美工圖案 |
| 1 | 不明確 |
| 2 | 一般美工圖案 |
| 3 | 良好美工圖案 |

### <a name="clip-art-detection-examples"></a>美工圖案偵測範例

下列 JSON 回應說明了當評估該範例影像是否為美工圖案的可能性時，電腦視覺傳回的內容。

![一片起司的美工圖案](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![藍色房屋和前院](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>偵測線條繪畫

電腦視覺分析影像並傳回布林值，指出影像是否為線條繪畫。

### <a name="line-drawing-detection-examples"></a>線條繪畫偵測範例

下列 JSON 回應說明了在指出該範例影像是否為線條繪圖時，電腦視覺傳回的內容。

![獅子的線圖影像](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![具有綠色背景的白色花卉](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>使用 API

影像類型偵測功能是「 [分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API」的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包含 `ImageType` 在 **visualFeatures** 查詢參數中。 然後，當您取得完整 JSON 回應時，只要剖析該區段內容的字串即可 `"imageType"` 。

* [快速入門：電腦視覺 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [快速入門：分析影像 (REST API) ](./quickstarts/csharp-analyze.md)
