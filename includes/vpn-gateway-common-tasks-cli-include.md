---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 61082333afc88abef0a8d8a57d1f1b1d893b6148
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67173837"
---
### <a name="to-view-local-network-gateways"></a>檢視區域網路閘道

若要檢視區域網路閘道清單，請使用 [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway) 命令。

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>確認共用金鑰值

請確認共用金鑰值和您用於 VPN 裝置組態的值相同。 如果不同，請使用裝置的值再次執行連線，或是以傳回的值將裝置更新。 這兩個值必須相符。 若要檢視共用金鑰，請使用 [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection)。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>若要檢視 VPN 閘道公用 IP 位址：

若要尋找虛擬網路閘道的公用 IP 位址，請使用 [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) 命令。 為了方便閱讀，系統會將此範例的輸出格式化為以資料表格式顯示公用 IP 的清單。

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
