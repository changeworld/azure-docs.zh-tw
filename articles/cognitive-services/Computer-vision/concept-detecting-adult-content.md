---
title: 成人， 活潑， 血腥內容 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 與使用電腦視覺 APi 檢測圖像中的成人內容相關的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718522"
---
# <a name="detect-adult-content"></a>檢測成人內容

電腦視覺可以檢測圖像中的成人材料，以便開發人員可以限制這些圖像在其軟體中的顯示。 內容旗標會搭配介於零與一的分數套用，使開發人員可以根據自己的喜好對結果進行解譯。

> [!NOTE]
> 大部分此功能由[Azure 內容檢閱者](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview)服務提供。 此替代方案為適用於更嚴格內容仲裁案例 (例如文字仲裁和人為檢閱工作流程) 的解決方案。

## <a name="content-flag-definitions"></a>內容旗標定義

在"成人"分類中，有幾個不同的類別：

- **成人**圖像被定義為那些明確性的性質，並經常描繪裸體和性行為。
- **淫穢**圖像被定義為在自然界中具有性暗示性的圖像，通常比標記為**成人**的圖像包含較少的色情內容。
- **戈裡**圖像被定義為那些描繪戈爾的圖像。

## <a name="use-the-api"></a>使用 API

您可以使用[分析圖像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 檢測成人內容。 將`Adult`的值添加到**visual功能**查詢參數時，API 將返回三個布林屬性&mdash;`isAdultContent`，`isRacyContent`並在`isGoryContent`&mdash;其 JSON 回應中返回 。 該方法還返回相應的屬性&mdash;`adultScore`，`racyScore`並`goreScore`&mdash;表示每個類別的置信度分數介於零和 1 之間。

- [快速入門：分析圖像（.NET SDK）](./quickstarts-sdk/csharp-analyze-sdk.md)
- [快速入門：分析圖像（REST API）](./quickstarts/csharp-analyze.md)
