---
title: 容器支援
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 037b9746571678e24e80bd76e7e2ed173ab8eb90
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97677635"
---
## <a name="create-an-anomaly-detector-resource"></a>建立 Anomaly Detector 資源

1. 登入<a href="https://portal.azure.com" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
1. 選取 [<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">建立異常 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>偵測器資源]。
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` -每秒10次呼叫，每月20K 筆交易。 <br> 或：<br> `S0` -每秒80個呼叫|
    |資源群組|選取可用的資源群組|

1. 按一下 [建立]  ，並等待系統建立資源。 建立之後，流覽至資源頁面
1. 收集已設定 `endpoint` 和 API 金鑰：

    |入口網站中的 [金鑰和端點] 索引標籤|設定|值|
    |--|--|--|
    |**概觀**|端點|複製端點。 看起來像 ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**[索引鍵]**|API 金鑰|複製這兩個金鑰的1。 它是32英數位元字串，沒有空格或連字號 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 。|



