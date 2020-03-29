---
title: 容器支援
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73522462"
---
## <a name="create-an-anomaly-detector-resource"></a>建立 Anomaly Detector 資源

1. 登錄到[Azure 門戶](https://portal.azure.com)
1. 按一下["創建**異常檢測器"**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)資源
1. 輸入所有必需的設置：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` - 最低定價層|
    |資源群組|選取可用的資源群組|
    |預覽確認核取方塊（必需）|是否已閱讀**預覽通知**|

1. 按一下 [建立]****，並等待系統建立資源。 創建後，導航到資源頁
1. 收集已`endpoint`配置的 API 金鑰：

    |門戶中的資源選項卡|設定|值|
    |--|--|--|
    |**概觀**|端點|複製終結點。 它看起來類似于`https://westus2.api.cognitive.microsoft.com/`|
    |**鑰匙**|API 金鑰|複製兩個鍵中的 1 個。 它是一個 32 個字母數位字元字串，沒有空格或破折`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`號。|



