---
title: 物件偵測 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 瞭解與電腦視覺 API 的物件檢測功能相關的概念 - 用法和限制。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131664"
---
# <a name="detect-common-objects-in-images"></a>檢測圖像中的常見物件

物件偵測與[標記功能](concept-tagging-images.md)類似，但 API 會傳回每個所找到物件的週框方塊座標 (以像素為單位)。 例如，如果影像包含狗、貓或人物，「偵測」作業就會列出這些物件及其在影像中的座標。 您可以使用此功能來處理影像中物件間的關聯性。 它還允許您確定映射中是否存在同一標記的多個實例。

偵測 API 會根據在影像中識別到的物件或生物來套用標記。 標記分類和物件檢測分類之間目前沒有正式關係。 在概念級別，檢測 API 僅查找物件和生物，而標記 API 還可以包含上下文術語，如"室內"，這些術語不能使用邊界框進行當地語系化。

## <a name="object-detection-example"></a>物件偵測範例

下列 JSON 回應說明了在範例影像中偵測到物件時，電腦視覺傳回的內容。

![在廚房使用 Microsoft Surface 裝置的女性](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>限制

請務必注意物件檢測的局限性，以便避免或減輕假負數（缺失物件）和有限細節的影響。

* 如果物件很小（小於圖像的 5%），則通常不會檢測到它們。
* 如果物件緊密排列在一起（例如一疊板），則通常不會檢測到它們。
* 無法依品牌或產品名稱區分物件 (例如貨架上有不同種類的汽水)。 不過，您可以使用[品牌偵測](concept-brand-detection.md)功能，從影像中取得品牌資訊。

## <a name="use-the-api"></a>使用 API

物件偵測功能是[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包括在`Objects`**視覺化功能**查詢參數中。 然後，當您獲得完整的 JSON 回應時，只需分析節的內容的`"objects"`字串。

* [快速入門：電腦視覺 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [快速入門：分析圖像（REST API）](./quickstarts/csharp-analyze.md)