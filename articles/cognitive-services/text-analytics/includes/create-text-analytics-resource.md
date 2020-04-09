---
title: 建立認知服務文字分析資源
titleSuffix: Azure Cognitive Services
description: 了解如何建立認知服務文字分析資源。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876392"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>建立認知服務文字分析資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選擇 **'建立資源**',然後轉到**AI + 機器學習** > **文字分析**。
   或, 轉到[建立文字分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。
1. 輸入所有必要的設定:

    |設定|值|
    |--|--|
    |名稱|輸入名稱(2-64 個字元)。|
    |訂用帳戶|選取適當的訂用帳戶。|
    |Location|選擇附近的位置。|
    |定價層| 輸入**S**,標準定價層。|
    |資源群組|選擇可用資源組。|

1. 選擇 **"建立**",然後等待建立資源。 您的瀏覽器會自動重定向到新創建的資源頁面。
1. 收集設定`endpoint`的 API 金鑰:

    |門戶中的資源選項卡|設定|值|
    |--|--|--|
    |**概觀**|端點|複製終結點。 看起來看起來類似`https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`。|
    |**索引鍵**|API 金鑰|複製兩個鍵之一。 它是一個 32 個字元的字母數字字串,沒有空格或破折`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`號 :<>。|
