---
title: 影片索引器可使用的區域 - Azure
titleSuffix: Azure Media Services
description: 本文討論可使用 Azure 媒體服務影片索引子的 Azure 區域。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530927"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>影片索引器所在的 Azure 區域

影片索引器 API 包含一個 **location** 參數，您應該將此參數設定為路由傳送呼叫的目標 Azure 區域。 這必須是[影片索引器可使用的 Azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)。

## <a name="locations"></a>位置

`location`參數必須獲得 Azure 區功能變數代碼名稱作為其值。 如果您在預覽模式中使用影片索引子，您應該將 `"trial"` 其置於值。 `trial` 這是參數的預設值 `location` 。 否則，若要取得您帳戶所在且應該路由傳送呼叫之 Azure 區域的代碼名稱，您可以使用 Azure 入口網站或執行 [Azure CLI](/cli/azure) 命令。

### <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Video Indexer](https://www.videoindexer.ai/) 網站。
1. 選取頁面右上角的 [ **使用者帳戶** ]。
1. 在右上角尋找您帳戶的位置。  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="位置":::
    
###  <a name="cli-command"></a>CLI 命令

```azurecli-interactive
az account list-locations
```

執行以上所示的這一行之後，您會取得所有 Azure 區域的清單。 瀏覽至包含您要尋找之 *displayName* 的 Azure 區域，並使用其 *name* 值作為 **location** 參數。

例如，若是美國西部 2 (下方顯示) 的 Azure 區域，您將使用 "westus2" 作為 **location** 參數。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
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
