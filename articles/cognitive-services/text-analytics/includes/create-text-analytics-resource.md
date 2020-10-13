---
title: 建立認知服務文字分析資源
titleSuffix: Azure Cognitive Services
description: 了解如何建立認知服務文字分析資源。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779172"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>建立認知服務文字分析資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [建立資源]，然後移至 [AI + 機器學習]  >  [文字分析]。
   或者，移至 [ [建立文字分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)]。
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|輸入名稱 (2-64 個字元)。|
    |訂閱|選取適當的訂閱。|
    |位置|選取附近的位置。|
    |定價層| 輸入 **S**，這代表標準定價層。|
    |資源群組|選取可用的資源群組。|

1. 選取 [建立]，然後等候系統建立資源。 您的瀏覽器會自動重新導向至新建立的資源頁面。
1. 收集已設定 `endpoint` 和 API 金鑰：

    |入口網站的 [資源] 索引標籤|設定|值|
    |--|--|--|
    |**概觀**|端點|複製端點。 它看起來類似于 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` 。|
    |**[索引鍵]**|API 金鑰|複製這兩個金鑰的其中一個。 它是32字元的英數位元字串，沒有空格或連字號： <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>。|
