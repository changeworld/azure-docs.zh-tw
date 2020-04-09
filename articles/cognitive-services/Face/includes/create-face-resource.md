---
title: 容器支援
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878355"
---
## <a name="create-an-face-resource"></a>建立資源

1. 登入[Azure 門戶](https://portal.azure.com)
1. 點選[「建立 **」**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)資源
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` - 最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [建立]****，並等待系統建立資源。 建立後,瀏覽到資源頁
1. 收集已`endpoint`設定的 API 金鑰:

    |門戶中的資源選項卡|設定|值|
    |--|--|--|
    |**概觀**|端點|複製終結點。 看起來類似於`https://face.cognitiveservices.azure.com/face/v1.0`|
    |**索引鍵**|API 金鑰|複製兩個鍵中的 1 個。 它是一個 32 個字母數位字元字串,沒有空格或破`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`折 號。|
