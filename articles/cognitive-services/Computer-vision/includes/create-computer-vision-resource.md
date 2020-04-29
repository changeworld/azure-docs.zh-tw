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
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876065"
---
## <a name="create-an-computer-vision-resource"></a>建立電腦視覺資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下 [[建立**電腦視覺**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)資源]。
1. 輸入所有必要的設定：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` - 最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [建立]  ，並等待系統建立資源。 資源建立後，請瀏覽至 [資源] 頁面。
1. 收集已`{ENDPOINT_URI}`設定`{API_KEY}`的和，請參閱[收集必要參數](../computer-vision-how-to-install-containers.md#gathering-required-parameters)以取得詳細資料。
