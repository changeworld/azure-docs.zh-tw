---
title: 查看虛擬中樞的有效路由： Azure 虛擬 WAN |Microsoft Docs
description: 在 Azure 虛擬 WAN 中檢視表 w 虛擬中樞的有效路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515846"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>查看虛擬中樞的有效路由

您可以在 Azure 入口網站中查看虛擬 WAN 中樞的所有路由。 若要查看路由，請流覽至 [虛擬中樞]，然後選取 [**路由-> 查看有效路由**]。

## <a name="understanding-routes"></a><a name="understand"></a>瞭解路由

下列範例可協助您進一步瞭解虛擬 WAN 路由的顯示方式。

在此範例中，我們有一個具有三個中樞的虛擬 WAN。 第一個中樞位於美國東部區域，第二個中樞位於西歐區域，而第三個中樞位於美國西部區域。 在虛擬 WAN 中，所有中樞都是互連的。 在此範例中，我們會假設「美國東部」和「西歐」中樞具有來自內部部署分支（輪輻）和「Azure 虛擬網路」（輪輻）的連接。

具有網路虛擬裝置（10.4.0.6）的 Azure VNet 輪輻（10.4.0.0/16）會進一步對等互連至 VNet （10.5.0.0/16）。 如需中樞路由表的詳細資訊，請參閱本文稍後的[其他資訊](#abouthubroute)。

在此範例中，我們也假設西歐分支1已連接到美國東部中樞，以及西歐中樞。 「美國東部」的 ExpressRoute 線路會將分支2連接到「美國東部」中樞。

![圖表](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>查看有效路由

當您在入口網站中選取 [查看有效路由] 時，它會產生 [美國東部] 中樞的[中樞路由表](#routetable)中所顯示的輸出。

若要將此設為觀點，第一行表示「美國東部中樞」已瞭解 10.20.1.0/24 （分支1）的路由，原因是 VPN*下一個躍點類型*連線（「下一個躍點」 VPN 閘道 Instance0 IP 10.1.0.6，Instance1 IP 10.1.0.7）。 *路由來源*指向資源識別碼。 *As path*表示分支1的 as 路徑。

### <a name="hub-route-table"></a><a name="routetable"></a>中樞路由表

使用資料表底部的捲軸來查看 "AS Path"。

| **前置詞** |  **下一個躍點類型** | **下一個躍點** |  **路由來源** |**AS 路徑** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|虛擬網路連接| 連結上 |  |  |
|10.5.0.0/16| IP 位址| 10.4.0.6|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/microsoft.network/virtualhubs/easthub_1/routetables/table_1| |
|0.0.0.0/0| IP 位址| `<Azure Firewall IP>` |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/microsoft.network/virtualhubs/easthub_1/routetables/table_1| |
|10.22.1.0/16| 遠端中樞|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/microsoft.network/virtualhubs/westhub_| 4848-22000 |
|10.9.0.0/16| 遠端中樞|  連結上 |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/microsoft.network/virtualhubs/westhub_1| |

>[!NOTE]
> 如果 [美國東部] 和 [西歐] 中樞未在範例拓撲中彼此通訊，則已學習的路由（10.9.0.0/16）不存在。 中樞只會公告直接連線到它們的網路。
>

## <a name="additional-information"></a><a name="additional"></a>其他資訊

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>關於中樞路由表

您可以建立虛擬中樞路由並將路由套用到虛擬中樞路由表。 您可以將多個路由套用到虛擬中樞路由表。 這可讓您透過 IP 位址（通常是輪輻 VNet 中的網路虛擬裝置（NVA））來設定目的地 VNet 的路由。 如需 Nva 的詳細資訊，請參閱將[流量從虛擬中樞路由傳送至 NVA](virtual-wan-route-table-portal.md)。

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>關於預設路由（0.0.0.0/0）

虛擬中樞能夠將已學習的預設路由傳播至虛擬網路、站對站 VPN，以及連線上的旗標為 [已啟用] 時的 ExpressRoute 連線。 當您編輯虛擬網路連線、VPN 連線或 ExpressRoute 連線時，就會顯示此旗標。 根據預設，中樞 VNet、ExpressRoute 和 VPN 連線上的 ' EnableInternetSecurity ' 一律為 false。

預設路由不是來自虛擬 WAN 中樞。 如果因為在中樞中部署防火牆，或另一個已連線的網站已啟用強制通道，而虛擬 WAN 中樞已經學習到預設路由，則會進行傳播。

## <a name="next-steps"></a>後續步驟

如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 Wan 總覽](virtual-wan-about.md)。