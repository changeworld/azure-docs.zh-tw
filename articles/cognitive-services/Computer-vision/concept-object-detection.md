---
title: 物件偵測 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 瞭解與電腦視覺 API 的物件偵測功能相關的概念-使用方式與限制。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80131664"
---
# <a name="detect-common-objects-in-images"></a>偵測影像中的一般物件

物件偵測與[標記功能](concept-tagging-images.md)類似，但 API 會傳回每個所找到物件的週框方塊座標 (以像素為單位)。 例如，如果影像包含狗、貓或人物，「偵測」作業就會列出這些物件及其在影像中的座標。 您可以使用此功能來處理影像中物件間的關聯性。 它也可讓您判斷影像中是否有多個相同標記的實例。

偵測 API 會根據在影像中識別到的物件或生物來套用標記。 標記分類法與物件偵測分類法之間目前沒有正式的關聯性。 在概念層級中，偵測 API 只會尋找物件和事物，而標記 API 也可以包含「室內」之類的內容詞彙，而這些詞彙無法使用周框方塊進行當地語系化。

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

請務必記下物件偵測的限制，如此您就可以避免或減輕錯誤否定 (遺漏物件的影響) 和有限的詳細資料。

* 如果物件很小 (小於影像) 的5%，通常就不會偵測到物件。
* 通常不會偵測到物件的相片順序， (一堆疊的盤子，例如) 。
* 無法依品牌或產品名稱區分物件 (例如貨架上有不同種類的汽水)。 不過，您可以使用[品牌偵測](concept-brand-detection.md)功能，從影像中取得品牌資訊。

## <a name="use-the-api"></a>使用 API

物件偵測功能是[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包含 `Objects` 在 **visualFeatures** 查詢參數中。 然後，當您取得完整 JSON 回應時，只要剖析該區段內容的字串即可 `"objects"` 。

* [快速入門：電腦視覺 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [快速入門：分析影像 (REST API) ](./quickstarts/csharp-analyze.md)