---
title: 透過 ExpressRoute 私用對等互連的站對站 VPN 連線
description: 本文可協助您透過 ExpressRoute 私人對等互連啟用站對站 VPN，以加密流量。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 016741606bad5536985a38b0e0664b39006e1df5
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776556"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering-preview"></a>透過 ExpressRoute 私用對等互連設定站對站 VPN 連線 (預覽版) 

您可以使用 RFC 1918 IP 位址，透過 ExpressRoute 私用對等互連來設定虛擬網路閘道的站對站 VPN。 這項設定提供下列優點：

* 透過私人對等互連的流量會經過加密。

* 連線到虛擬網路閘道的點對站使用者可透過站對站通道使用 ExpressRoute (，) 來存取內部部署資源。

>[!NOTE]
>這項功能僅支援區域冗余閘道。 例如，VpnGw1AZ、VpnGw2AZ 等。
>

若要完成此設定，請確認您符合下列必要條件：

* 您有一個運作中的 ExpressRoute 線路，它會連結到 VPN 閘道 (或將) 建立的 VNet。

* 您可以透過 ExpressRoute 線路，在 VNet 中透過 RFC1918 (私人) IP 來連線資源。

## <a name="routing"></a><a name="routing"></a>路由

[**圖 1** ] 顯示透過 ExpressRoute 私用對等互連的 VPN 連線範例。 在此範例中，您會在透過 ExpressRoute 私人對等互連連線到 Azure hub VPN 閘道的內部部署網路中看到網路。 這項設定的重要層面，是透過 ExpressRoute 和 VPN 路徑，在內部部署網路與 Azure 之間進行路由。

圖 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="圖1":::

建立連線很簡單：

1. 使用 ExpressRoute 線路和私用對等互連來建立 ExpressRoute 連線能力。

1. 使用本文中的步驟來建立 VPN 連線能力。

### <a name="traffic-from-on-premises-networks-to-azure"></a>從內部部署網路到 Azure 的流量

針對從內部部署網路到 Azure 的流量，會透過 ExpressRoute 私用對等互連 BGP 和 VPN BGP 來公告 Azure 首碼。 結果是) 從內部部署網路到 Azure 的兩個網路路由 (路徑：

•透過受 IPsec 保護的路徑進行一個網路路由。

•透過沒有 IPsec 保護的 ExpressRoute 直接進行一個網路路由。

若要將加密套用至通訊，您必須確定在 [ **圖 1**] 中已連線到 VPN 的網路上，透過內部部署 vpn 閘道的 Azure 路由優先于直接 ExpressRoute 路徑。

### <a name="traffic-from-azure-to-on-premises-networks"></a>從 Azure 到內部部署網路的流量

相同需求適用于從 Azure 到內部部署網路的流量。 若要確保在沒有 IPsec) 的情況下， (直接 ExpressRoute 路徑的 IPsec 路徑，您有兩個選項：

•在 vpn **連線網路的 VPN BGP 會話上公告更明確的首碼**。 您可以透過 ExpressRoute 私用對等互連來公告包含 VPN 連線網路的較大範圍，然後在 VPN BGP 會話中提供更具體的範圍。 例如，透過 ExpressRoute 公告 10.0.0.0/16，並透過 VPN 通告 10.0.1.0/24。

• **針對 VPN 和 ExpressRoute 通告脫離的首碼**。 如果 VPN 連線的網路範圍與其他 ExpressRoute 連線網路不相鄰，您可以分別在 VPN 和 ExpressRoute BGP 會話中通告首碼。 例如，透過 ExpressRoute 公告 10.0.0.0/24，並透過 VPN 通告 10.0.1.0/24。

在這兩個範例中，Azure 會透過 VPN 連線將流量傳送至 10.0.1.0/24，而不是直接透過沒有 VPN 保護的 ExpressRoute。

>[!Warning]
>如果您在 ExpressRoute 和 VPN 連線上公告相同的首碼，>Azure 將直接使用 ExpressRoute 路徑，而不使用 VPN 保護。
>

## <a name="portal-steps"></a><a name="portal"></a>入口網站步驟

1. 設定站對站連線。 如需相關步驟，請參閱 [站對站](vpn-gateway-howto-site-to-site-resource-manager-portal.md) 設定文章。 請務必為閘道挑選區域多餘的閘道 SKU。 

   區域冗余 Sku 在 SKU 結尾有 "AZ"。 例如， **VpnGw1AZ**。 區域多餘的閘道僅適用于可用性區域服務可用的區域。 如需支援可用性區域的區域相關資訊，請參閱 [支援可用性](../availability-zones/az-region.md)區域的區域。

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="圖1":::
1. 在閘道上啟用私人 Ip。 選取 **[** 設定]，然後將 [ **閘道私人 ip** ] 設定為 [ **啟用**]。 選取 [儲存] 來儲存變更。
1. 在 [ **總覽** ] 頁面上，選取 [ **查看更多** ] 以查看私人 IP 位址。 寫下此資訊，以供稍後在設定步驟中使用。

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="圖1" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. 若要在連線上啟用 [ **使用 Azure 私人 IP 位址** ]，  **請選取 [** 設定]。 將 [ **使用 Azure 私人 IP 位址** ] 設定為 [ **已啟用**]，然後選取 [ **儲存**]。

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="圖1":::
1. 從您的防火牆，偵測您在步驟3中記下的私人 IP。 私人 IP 應可透過 ExpressRoute 私用對等互連來連線。
1. 使用此私人 IP 做為您內部部署防火牆上的遠端 IP，以透過 ExpressRoute 私用對等互連來建立站對站通道。

## <a name="powershell-steps"></a><a name="powershell"></a>PowerShell 步驟

1. 設定站對站連線。 如需相關步驟，請參閱 [設定站對站 VPN](vpn-gateway-howto-site-to-site-resource-manager-portal.md) 文章。 請務必為閘道挑選區域多餘的閘道 SKU。 區域冗余 Sku 在 SKU 結尾有 "AZ"。 例如，VpnGw1AZ。
1. 使用下列 PowerShell 命令，將旗標設定為使用閘道上的私人 IP：

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   您應該會看到公用和私人 IP 位址。 記下輸出的 "TunnelIpAddresses" 區段底下的 IP 位址。 您將在稍後的步驟中使用這項資訊。
1. 使用下列 PowerShell 命令，將連接設定為使用私人 IP 位址：

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. 從您的防火牆，偵測您在步驟2中記下的私人 IP。 它應該可透過 ExpressRoute 私用對等互連來連線。
1. 使用此私人 IP 做為您內部部署防火牆上的遠端 IP，以透過 ExpressRoute 私用對等互連來建立站對站通道。

## <a name="next-steps"></a>後續步驟

如需 VPN 閘道的詳細資訊，請參閱 [什麼是 Vpn 閘道？](vpn-gateway-about-vpngateways.md)
