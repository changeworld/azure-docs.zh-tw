---
title: 容器支援
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8dbe36693e551bd03022f4a00044b75b912c834a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875097"
---
## <a name="create-an-anomaly-detector-resource"></a>建立 Anomaly Detector 資源

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 按一下 [[建立**異常**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)偵測器資源]
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` - 最低定價層|
    |資源群組|選取可用的資源群組|
    |預覽確認核取方塊（必要）|您是否已閱讀**預覽**通知|

1. 按一下 [建立]  ，並等待系統建立資源。 建立之後，流覽至 [資源] 頁面
1. 收集已`endpoint`設定的和 API 金鑰：

    |入口網站的 [資源] 索引標籤|設定|值|
    |--|--|--|
    |**概觀**|端點|複製端點。 看起來類似`https://westus2.api.cognitive.microsoft.com/`|
    |**[索引鍵]**|API 金鑰|複製這兩個金鑰的1。 它是32英數位元字串， `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`不含空格或連字號。|



