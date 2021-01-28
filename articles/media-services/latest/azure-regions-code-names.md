---
title: Azure 媒體服務 v3 的雲端和區域
description: 本文討論用於端點的 Url，以及用於區域的程式碼。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 74f30ebb766ea34603c0a30455837c47222967c0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954322"
---
# <a name="regional-code-names-and-endpoints"></a>區域代碼名稱和端點

### <a name="region-code-name"></a>區域碼名稱

當 **位置** 參數用於命令或要求時，您需要提供區功能變數代碼名稱作為 **位置** 值。 若要取得您的帳戶所在區域的程式碼名稱，並將呼叫路由傳送至，您可以在 Azure CLI 中執行下面這一行。

```azurecli-interactive
az account list-locations
```

執行以上所示的這一行之後，您會取得所有 Azure 區域的清單。 瀏覽至包含您要尋找之 *displayName* 的 Azure 區域，並使用其 *name* 值作為 **location** 參數。

例如，若是美國西部 2 (下方顯示) 的 Azure 區域，您將使用 "westus2" 作為 **location** 參數。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>端點  

從不同國家/地區的 Azure 雲端連線到媒體服務帳戶時，下列端點非常重要。

### <a name="global-azure"></a>全域 Azure

| 服務 | 端點 |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| 驗證 | `https://login.microsoftonline.com/` |
| 權杖對象 | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| 服務 | 端點 |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| 驗證 | `https://login.microsoftonline.us/` |
| 權杖對象 | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure 德國

> [!NOTE]
> Azure 德國端點僅適用于德國的主權雲端。

| 服務 | 端點 |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| 驗證 | `https://login.microsoftonline.de/` |
| 權杖對象 | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure 中國 21Vianet

| 服務 | 端點 |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| 驗證 | `https://login.chinacloudapi.cn/` |
| 權杖對象 |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>另請參閱

* [Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)
* [區域代碼名稱和端點](azure-regions-code-names.md)
* [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>下一步

[媒體服務 v3 概觀](media-services-overview.md)
