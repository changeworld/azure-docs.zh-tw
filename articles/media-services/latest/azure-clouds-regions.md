---
title: 可用 Azure 媒體服務 v3 的雲端和區域
description: 本文會討論可使用 Azure 媒體服務 v3 的 Azure 雲端和區域。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 9c54482dc188141c4fc697bd43628b81e0ddc29f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597134"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>存在 Azure 媒體服務 v3 的雲端和區域

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 媒體服務 v3 可透過 Azure Resource Manager 資訊清單在全域 Azure、Azure Government、Azure 德國、Azure China 21Vianet 中使用。 但是，並非所有 Azure 雲端中都提供所有的媒體服務功能。 本文件概述主要媒體服務 v3 元件的可用性。

## <a name="feature-availability-in-azure-clouds"></a>Azure 雲端中的功能可用性

| 功能|全域 Azure 區域 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | 可用 | 無法使用 | 無法使用 | 無法使用 |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  可用 | 無法使用 | 無法使用 | 無法使用 |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  可用 | 無法使用 | 無法使用 | 無法使用 |
| [StandardEncoderPreset](encoding-concept.md) | 可用 | 可用 | 可用 | 可用 |
| [LiveEvents](live-streaming-overview.md) | 可用 | 可用 | 可用 | 可用 |
| [StreamingEndpoints](streaming-endpoint-concept.md) | 可用 | 可用 | 可用 | 可用 |

## <a name="feature-availability-in-preview"></a>預覽版中的功能可用性

[LiveTranscription](live-transcription.md) 適用于下欄區域：

- 東南亞
- 西歐
- 北歐
- 美國東部
- 美國中部
- 美國中南部
- 美國西部 2
- 巴西南部

## <a name="regionsgeographieslocations"></a>區域/地理位置/位置

[部署 Azure 媒體服務服務的區域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>區域碼名稱

如果需要提供**位置**參數，則需要提供區域代碼名稱作為**位置**值。 若要取得您帳戶所在且應該路由傳送呼叫之區域的代碼名稱，您可以在 [Azure CLI](/cli/azure/?view=azure-cli-latest) 中執行下面這一行

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
* [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>後續步驟

[媒體服務 v3 概觀](media-services-overview.md)
