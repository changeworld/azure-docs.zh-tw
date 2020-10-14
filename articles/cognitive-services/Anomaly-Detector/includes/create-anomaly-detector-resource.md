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
ms.openlocfilehash: e896ac5f4625d36060d713d66fa885f8b24756f1
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014729"
---
## <a name="create-an-anomaly-detector-resource"></a>建立 Anomaly Detector 資源

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [ [建立 **異常**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) 偵測器資源]
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` - 最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [建立]  ，並等待系統建立資源。 建立之後，流覽至資源頁面
1. 收集已設定 `endpoint` 和 API 金鑰：

    |入口網站的 [資源] 索引標籤|設定|值|
    |--|--|--|
    |**概觀**|端點|複製端點。 看起來像 `https://westus2.api.cognitive.microsoft.com/`|
    |**[索引鍵]**|API 金鑰|複製這兩個金鑰的1。 它是32英數位元字串，沒有空格或連字號 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 。|



