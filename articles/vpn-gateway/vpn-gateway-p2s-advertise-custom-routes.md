---
title: Azure VPN 閘道：公告 P2S VPN 用戶端的自訂路由
description: 將自訂路由公告至點對站用戶端的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: a02bd5519b776a063646c11be2a34366fe429f99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89392386"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>公告 P2S VPN 用戶端的自訂路由

您可能會想要對所有點對站 VPN 用戶端通告自訂路由。 例如，當您在 VNet 中啟用儲存體端點，且希望遠端使用者能夠透過 VPN 連線存取這些儲存體帳戶時。 您可以將儲存體端點的 IP 位址通告給所有遠端使用者，讓儲存體帳戶的流量通過 VPN 通道，而不是公用網際網路。

![Azure VPN 閘道多網站連接範例](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>若要通告自訂路由

若要通告自訂路由，請使用 `Set-AzVirtualNetworkGateway cmdlet` 。 下列範例示範如何通告 [Contoso 儲存體帳戶資料表](https://contoso.table.core.windows.net)的 IP。

1. 偵測 *contoso.table.core.windows.net* 並記下 IP 位址。 例如：

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 執行下列 PowerShell 命令：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 若要新增多個自訂路由，請使用逗號和空格來分隔位址。 例如：

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>若要查看自訂路由

使用下列範例來查看自訂路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>刪除自訂路由

使用下列範例刪除自訂路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>後續步驟

如需其他 P2S 路由資訊，請參閱 [關於點對站路由](vpn-gateway-about-point-to-site-routing.md)。
