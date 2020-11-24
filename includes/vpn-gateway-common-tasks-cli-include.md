---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 923620c1c9719d857cc848507a4c5507f528d34e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563508"
---
### <a name="to-view-local-network-gateways"></a>檢視區域網路閘道

若要檢視區域網路閘道清單，請使用 [az network local-gateway list](/cli/azure/network/local-gateway) 命令。

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>確認共用金鑰值

請確認共用金鑰值和您用於 VPN 裝置組態的值相同。 如果不同，請使用裝置的值再次執行連線，或是以傳回的值將裝置更新。 這兩個值必須相符。 若要檢視共用金鑰，請使用 [az network vpn-connection-list](/cli/azure/network/vpn-connection)。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>若要檢視 VPN 閘道公用 IP 位址：

若要尋找虛擬網路閘道的公用 IP 位址，請使用 [az network public-ip list](/cli/azure/network/public-ip) 命令。 為了方便閱讀，系統會將此範例的輸出格式化為以資料表格式顯示公用 IP 的清單。

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```