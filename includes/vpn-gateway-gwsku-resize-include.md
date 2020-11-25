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
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010757"
---
您可以使用 `Resize-AzVirtualNetworkGateway` PowerShell Cmdlet 來升級或降級 Generation1 或 Generation2 SKU (所有 VpnGw SKU 都可以調整大小，但基本 SKU 除外)。 如果您使用基本閘道 SKU，[ 會改為使用這些指示](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)調整您的閘道大小。

下列 PowerShell 範例示範將閘道 SKU 的大小調整為 VpnGw2。

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```