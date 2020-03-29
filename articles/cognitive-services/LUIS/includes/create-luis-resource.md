---
title: 創建 LUIS 資源
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465914"
---
## <a name="create-a-luis-resource"></a>創建 LUIS 資源

1. 登錄到[Azure 門戶](https://portal.azure.com)
1. 按一下[建立 **Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 輸入所有必需的設置：

    |設定|值|
    |--|--|
    |名稱|所需的名稱 (2-64 個字元)|
    |訂用帳戶|選取適當的訂用帳戶|
    |Location|選取任何附近和可用的位置|
    |定價層|`F0` - 最低定價層|
    |資源群組|選取可用的資源群組|

1. 按一下 [建立]****，並等待系統建立資源。 資源建立後，請瀏覽至 [資源] 頁面。
1. 收集配置`endpoint`的 API 金鑰，請參閱[收集所需的參數](#gathering-required-parameters)。

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
