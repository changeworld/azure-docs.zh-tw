---
title: 影像分類-電腦視覺
titleSuffix: Azure Cognitive Services
description: 了解電腦視覺 API 的影像分類功能相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 477349f1addf71a30e8ecb179266d8eac5510887
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80244745"
---
# <a name="categorize-images-by-subject-matter"></a>依主題分類影像

除了標記和描述以外，電腦視覺也會傳回在影像中偵測到的分類型類別。 與標記不同，類別會在父/子階層結構中組織，並且更少 (86 個，而不是數以千計的標記)。 所有類別名稱皆採用英文。 類別可以單獨完成，也可以與較新的標記模型一起完成。

## <a name="the-86-category-concept"></a>86 類別概念

電腦視覺可以使用下圖中的86類別清單，大致或具體地分類影像。 如需文字格式的完整分類，請參閱[類別分類](category-taxonomy.md)。

![類別分類法中所有類別的分組清單](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>影像分類範例

下列 JSON 回應說明根據視覺功能進行範例影像分類時，電腦視覺傳回的內容。

![公寓大樓屋頂上的女人](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

下表所列的是一般的影像集和電腦視覺針對每個影像所傳回的類別。

| Image | 類別 |
|-------|----------|
| ![拍全家福的四個人](./Images/family_photo.png) | people_group |
| ![坐在草地上的小狗](./Images/cute_dog.png) | animal_dog |
| ![日落時站在山岩上的人](./Images/mountain_vista.png) | outdoor_mountain |
| ![桌上的一堆圓麵包](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>使用 API

分類功能是[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 的一部分。 您可以透過原生 SDK 或 REST 呼叫來呼叫此 API。 包含`Categories`在**visualFeatures**查詢參數中。 然後，當您取得完整的 JSON 回應時，只要剖析`"categories"`區段內容的字串即可。

* [快速入門：電腦視覺 .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [快速入門：分析影像（REST API）](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>後續步驟

瞭解[標記影像](concept-tagging-images.md)和[描述影像](concept-describing-images.md)的相關概念。
