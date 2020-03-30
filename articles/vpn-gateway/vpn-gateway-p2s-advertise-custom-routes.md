---
title: Azure VPN 閘道：為 P2S VPN 用戶端通告自訂路由
description: 向點對點用戶端通告自訂路由的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 7a904857b8aa0ed2aa18fc2a1b81fe31541e6f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151902"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>通告 P2S VPN 用戶端的自訂路由

您可能希望將自訂路由通告給所有點對點 VPN 用戶端。 例如，當您在 VNet 中啟用了存儲終結點，並希望遠端使用者能夠通過 VPN 連接訪問這些存儲帳戶時。 您可以將存儲端點的 IP 位址通告給所有遠端使用者，以便存儲帳戶的流量通過 VPN 隧道，而不是公共 Internet。

![Azure VPN 閘道多網站連接範例](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>通告自訂路由

要通告自訂路由，`Set-AzVirtualNetworkGateway cmdlet`請使用 。 下面的示例演示如何為[Contoso 存儲帳戶表](https://contoso.table.core.windows.net)通告 IP。

1. ping *contoso.table.core.windows.net*並記下 IP 位址。 例如：

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 執行下列 PowerShell 命令：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 要添加多個自訂路由，請使用昏迷和空格分隔位址。 例如：

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>查看自訂路由

使用以下示例查看自訂路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>刪除自訂路由

使用以下示例刪除自訂路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>後續步驟

有關其他 P2S 路由資訊，請參閱[有關點到網站路由](vpn-gateway-about-point-to-site-routing.md)的資訊。
