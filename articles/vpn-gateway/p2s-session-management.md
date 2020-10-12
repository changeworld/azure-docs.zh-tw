---
title: Azure VPN 閘道：點對站 VPN 會話管理
description: 本文可協助您查看點對站 VPN 會話並中斷其連線。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2f2184507e17e3ecae40bb33be4202c183d32b77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274228"
---
# <a name="point-to-site-vpn-session-management"></a>點對站 VPN 會話管理

Azure 虛擬網路閘道可讓您輕鬆地查看並中斷目前點對站 VPN 會話的連線。 本文可協助您查看及中斷目前會話的連線。

>[!NOTE]
>會話狀態會每隔5分鐘更新一次。 它不會立即更新。
>

## <a name="portal"></a>入口網站

若要在入口網站中查看和中斷會話的連線：

1. 流覽至 VPN 閘道。
1. 在 [ **監視** ] 區段下，選取 [ **點對站會話**]。

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="入口網站範例":::
1. 您可以在 windowpane 中查看所有目前的會話。
1. 針對您要中斷連線的會話選取 **[...]** ，然後選取 **[中斷連線]**。

## <a name="powershell"></a>PowerShell

若要使用 PowerShell 來查看和中斷會話的連線：

1. 執行下列 PowerShell 命令，以列出作用中的會話：

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. 複製您要中斷連接之會話的 **VpnConnectionId** 。

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="入口網站範例":::
1. 若要中斷會話的連線，請執行下列命令：

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>後續步驟

如需點對站連線的詳細資訊，請參閱 [關於點對站 VPN](point-to-site-about.md)。
