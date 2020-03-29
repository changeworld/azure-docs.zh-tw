---
title: 創建認知服務文本分析資源
titleSuffix: Azure Cognitive Services
description: 了解如何建立認知服務文字分析資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383484"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>創建認知服務文本分析資源

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 選擇 **"創建資源**"，然後轉到**AI + 機器學習** > **文本分析**。
   或者，轉到[創建文本分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。
1. 輸入所有必需的設置：

    |設定|值|
    |--|--|
    |名稱|輸入名稱（2-64 個字元）。|
    |訂用帳戶|選取適當的訂用帳戶。|
    |Location|選擇附近的位置。|
    |定價層| 輸入**S**，標準定價層。|
    |資源群組|選擇可用資源組。|

1. 選擇 **"創建**"，然後等待創建資源。 您的瀏覽器會自動重定向到新創建的資源頁面。
1. 收集配置`endpoint`的 API 金鑰：

    |門戶中的資源選項卡|設定|值|
    |--|--|--|
    |**概觀**|端點|複製終結點。 它看起來類似于`https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`。|
    |**鑰匙**|API 金鑰|複製兩個鍵之一。 它是一個 32 個字元的字母數位字串，沒有空格或破折號`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`：<>。|
