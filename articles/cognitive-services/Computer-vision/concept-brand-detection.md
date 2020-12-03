---
title: 品牌偵測 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 本文討論物件偵測的特殊模式;使用電腦視覺 API 的品牌及/或標誌偵測。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 40792585fbc52aaeec8a535b6a82decfce7618f2
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533684"
---
# <a name="detect-popular-brands-in-images"></a>偵測影像中的熱門品牌

品牌偵測是[物件偵測](concept-object-detection.md)的特製化模式，可使用全球數千個標誌的資料庫識別影像或影片中的商業品牌。 例如，您可以使用這項功能探索哪些品牌在社交媒體最受歡迎或在媒體產品位置中最常見。

電腦視覺服務可偵測特定影像中是否有品牌標誌；如果有，會傳回品牌名稱、信賴分數，以及標誌週框的座標。

內建的標誌資料庫涵蓋消費性電子產品、服飾等等的熱門品牌。 如果您發現電腦視覺服務偵測不到您尋找的品牌，您可以使用[自訂視覺](../custom-vision-service/index.yml)服務建立並定型您自己的標誌偵測器來獲得更好的服務。

## <a name="brand-detection-example"></a>品牌偵測範例

下列 JSON 回應說明了在範例影像中偵測到品牌時，電腦視覺傳回的內容。

![有 Microsoft 標籤和標誌的紅色 T 恤](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

在某些情況下，品牌偵測器會將標誌影像和樣式化品牌名稱視為兩個不同的標誌。

![有 Microsoft 標籤和標誌的灰色運動衫](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>使用 API

品牌偵測功能是[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API 的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包含 `Brands` 在 **visualFeatures** 查詢參數中。 然後，當您取得完整 JSON 回應時，只要剖析該區段內容的字串即可 `"brands"` 。

* [快速入門：電腦視覺 REST API 或用戶端程式庫](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)