---
title: 成人、猥褻、暴力內容-電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 偵測影像中成人內容的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532613"
---
# <a name="detect-adult-content"></a>偵測成人內容

電腦視覺可以偵測影像中的成人內容，讓開發人員可以限制在其軟體中顯示這些影像。 內容旗標會套用至0到1之間的分數，讓開發人員可以根據自己的喜好設定來解讀結果。

> [!NOTE]
> 這大部分功能都是由 [Azure 內容仲裁](../content-moderator/overview.md) 服務所提供。 此替代方案為適用於更嚴格內容仲裁案例 (例如文字仲裁和人為檢閱工作流程) 的解決方案。

## <a name="content-flag-definitions"></a>內容旗標定義

「成人」分類包含數個不同的類別：

- **成人** 影像本質上是明確的，且通常會顯示裸體和性行為。
- **猥褻** 影像本質上是暗示性的暗示，通常會包含比標記為 **成人** 的影像更少的色情內容。
- **暴力** 影像會顯示血糖/而且高爾。

## <a name="use-the-api"></a>使用 API

您可以使用 [分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API 來偵測成人內容。 當您將的值新增 `Adult` 至 **visualFeatures** query 參數時，API 會 &mdash; `isAdultContent` `isRacyContent` `isGoryContent` &mdash; 在其 JSON 回應中傳回三個布林值屬性、和。 方法也會傳回對應的屬性 &mdash; `adultScore` ，以及 `racyScore` `goreScore` &mdash; 代表每個個別類別的信賴分數（介於零和1之間）。

- [快速入門：電腦視覺 REST API 或用戶端程式庫](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
