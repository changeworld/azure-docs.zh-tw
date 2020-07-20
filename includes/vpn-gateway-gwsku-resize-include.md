---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73495749"
---
您可以使用 `Resize-AzVirtualNetworkGateway` PowerShell Cmdlet 來升級或降級 generation1.xml 或 GENERATION2 SKU （所有 VpnGw sku 都可以調整大小，但基本 sku 除外）。 如果您使用基本閘道 SKU，[ 會改為使用這些指示](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)調整您的閘道大小。

下列 PowerShell 範例示範將閘道 SKU 的大小調整為 VpnGw2。

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

您也可以在 Azure 入口網站中調整閘道大小，請進入虛擬網路閘道的 [組態]**** 頁面，並從下拉式清單中選取不同的 SKU。
