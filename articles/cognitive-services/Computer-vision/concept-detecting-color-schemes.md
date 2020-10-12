---
title: 色彩配置偵測-電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 偵測影像中色彩配置的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af0c39ed8211ac2041d143112437ad5d6b384259
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244727"
---
# <a name="detect-color-schemes-in-images"></a>偵測影像中的色彩配置

電腦視覺可分析影像中的色彩，以提供三個不同的屬性：主要前景色彩、主要背景色彩，以及影像整體的主要色彩集合。 屬於該集合的傳回色彩：黑色、藍色、褐色、灰色、綠色、橙色、粉紅色、紫色、紅色、藍綠色、白色和黃色。 

電腦視覺也可根據主要色彩的組和飽和度來擷取輔色 (代表影像中最顯眼的色彩)。 輔色會以十六進位的 HTML 色彩代碼傳回。 

電腦視覺也可傳回布林值，指出影像是否為黑白。

## <a name="color-scheme-detection-examples"></a>色彩配置偵測範例

下列範例說明偵測範例影像的色彩配置時，電腦視覺所傳回的 JSON 回應。 在此情況下，本範例影像不是黑白影像，但主要前景和背景色彩為黑色，而且整體影像的主要色彩為黑色和白色。

![以人為側面的室外山](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>主要色彩範例

下表顯示傳回之每個範例影像的前景、背景及影像色彩。

| 映像 | 主要色彩 |
|-------|-----------------|
|![具有綠色背景的白色花卉](./Images/flower.png)| 前景：黑色<br/>背景：白色<br/>色彩：黑色、白色、綠色|
![通過車站的火車](./Images/train_station.png) | 前景：黑色<br/>背景：黑色<br/>色彩：黑色 |

### <a name="accent-color-examples"></a>輔色範例

 下表顯示傳回之每個範例影像的輔色 (十六進位 HTML 色彩值)。

| 映像 | 輔色 |
|-------|--------------|
|![日落時站在山岩上的人](./Images/mountain_vista.png) | #BB6D10 |
|![具有綠色背景的白色花卉](./Images/flower.png) | #C6A205 |
|![通過車站的火車](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>黑白偵測範例

下表顯示電腦視覺對範例影像的黑白評估。

| 映像 | 黑白？ |
|-------|----------------|
|![曼哈頓建築物的黑白照片](./Images/bw_buildings.png) | true |
|![藍色房屋和前院](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>使用 API

色彩配置偵測功能是「 [分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API」的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包含 `Color` 在 **visualFeatures** 查詢參數中。 然後，當您取得完整 JSON 回應時，只要剖析該區段內容的字串即可 `"color"` 。

* [快速入門：電腦視覺 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [快速入門：分析影像 (REST API) ](./quickstarts/csharp-analyze.md)
