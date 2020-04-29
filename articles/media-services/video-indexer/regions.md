---
title: 影片索引器可使用的區域 - Azure
titleSuffix: Azure Media Services
description: 本文討論可使用 Azure 媒體服務影片索引子的 Azure 區域。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c91b38fcbfb9b517651adead010408425e519a82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80382744"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>影片索引器所在的 Azure 區域

影片索引器 API 包含一個 **location** 參數，您應該將此參數設定為路由傳送呼叫的目標 Azure 區域。 這必須是[影片索引器可使用的 Azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)。

## <a name="locations"></a>位置

必須為 **location** 參數指定 Azure 區域代碼名稱作為其值。 如果您要在預覽模式中使用影片索引器，應該放置 *"trial"* 作為值。 否則，若要取得您帳戶所在且應該路由傳送呼叫之 Azure 區域的代碼名稱，您可以在 [Azure CLI](/cli/azure) 中執行下面這一行：

```azurecli-interactive
az account list-locations
```

執行以上所示的這一行之後，您會取得所有 Azure 區域的清單。 瀏覽至包含您要尋找之 *displayName* 的 Azure 區域，並使用其 *name* 值作為 **location** 參數。

例如，若是美國西部 2 (下方顯示) 的 Azure 區域，您將使用 "westus2" 作為 **location** 參數。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>後續步驟

- [使用 API 自訂語言模型](customize-language-model-with-api.md)
- [使用 API 自訂品牌模型](customize-brands-model-with-api.md)
- [使用 API 自訂人員模型](customize-person-model-with-api.md)
