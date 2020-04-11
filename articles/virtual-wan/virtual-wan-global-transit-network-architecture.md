---
title: 架構:全球交通網路架構
titleSuffix: Azure Virtual WAN
description: 瞭解虛擬廣域網路架構
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 9515058bc78a2d56dc1734c046dac5d5b04f68d9
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113169"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>全球交通網路架構和虛擬廣域網路

現代企業需要跨雲和本地的超分散式應用程序、數據和用戶之間無處不在的連接。 企業正在採用全球交通網路架構來整合、連接和控制以雲為中心的現代全球企業 IT 足跡。

全球傳輸網路架構基於經典的集線器和分支連接模型,雲託管網路「集線器」支援可在不同類型「分支」之間分佈的端點之間的傳輸連接。

在此模型中,分支可以是:
* 虛擬網路(VNet)
* 實體分支網站
* 遠端使用者
* Internet

![樞紐和輻條](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**圖1:全球中轉樞紐和輻條網路**

圖 1 顯示了全球傳輸網路的邏輯視圖,其中地理分佈的使用者、物理網站和 VNet 通過雲中託管的網路中心互連。 此體系結構支援網路終結點之間的邏輯一躍傳輸連接。

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>具有虛擬廣網域網路的全球傳輸網路

Azure 虛擬 WAN 是 Microsoft 管理的雲端網路服務。 此服務由的所有網路元件都由 Microsoft 託管和管理。 有關虛擬 WAN 的詳細資訊,請參閱[虛擬 WAN 概述](virtual-wan-about.md)一文。

Azure 虛擬 WAN 透過 VNet、分支網站、SaaS 和 PaaS 應用程式以及使用者中實現全球分散式雲端工作負載集之間的無所不在的任意連接,允許全球傳輸網路架構。

![Azure 虛擬 WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**圖 2:全球交通網路和虛擬廣域網**

在 Azure 虛擬 WAN 體系結構中,虛擬 WAN 集線器預配在 Azure 區域中,您可以選擇將分支、VNet 和遠端使用者連接到這些區域。 物理分支網站透過進階 ExpressRoute 或網站到網站 VPN 連接到集線器,VNet 透過 VNet 連接連接到集線器,遠端使用者可以使用使用者 VPN(點對點 VPN)直接連接到集線器。 虛擬 WAN 還支援跨區域 VNet 連接,其中一個區域中的 VNet 可以連接到不同區域中的虛擬 WAN 集線器。

通過在具有最多分支(分支、VNet、使用者)的區域創建單個虛擬 WAN 中心,然後將其他區域中的分支連接到中心,可以建立虛擬廣域網。 當企業足跡主要位於具有幾個遠端分支的一個區域時,這是一個不錯的選擇。  
  
## <a name="hub-to-hub-connectivity-preview"></a><a name="hubtohub"></a>集線器到集線器連接(預覽)

企業雲佔用空間可以跨越多個雲區域,最好(延遲)從最接近其物理網站和用戶的區域訪問雲。 全球交通網路架構的關鍵原則之一是在所有雲和本地網路端點之間實現跨區域連接。 這意味著,從連接到一個區域中的雲的分支的流量可以使用[Azure Global Network](https://azure.microsoft.com/global-infrastructure/global-network/)啟用的集線器到集線器連接到達另一個分支或不同區域中的 VNet。

![跨區域](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**圖 3:虛擬廣域網跨區域連接**

當在單個虛擬 WAN 中啟用多個集線器時,通過集線器到集線器鏈路自動互連集線器,從而實現分佈在多個區域的分支和 Vnets 之間的全域連接。 

此外,屬於同一虛擬 WAN 的集線器可以與不同的區域訪問和安全策略相關聯。 有關詳細資訊,請參閱本文後面的[安全和策略控制](#security)。

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>任何任何連線

全球交通網路架構支援透過虛擬廣域網集線器進行任意連接。 此體系結構消除了或減少了在分支之間實現完全網格或部分網格連接的需要,這些連接在構建和維護方面更為複雜。 此外,樞紐和輻條網路與網狀網路中的路由控制更易於配置和維護。

任意到任意連接(在全域體系結構的上下文中)允許具有全域分散式使用者、分支、資料中心、VNet 和應用程式的企業通過"傳輸"集線器相互連接。 Azure 虛擬 WAN 充當全域傳輸系統。

![任何到任何](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**圖 4:虛擬廣域網流量路徑**

Azure 虛擬 WAN 支援以下全域傳輸連接路徑。 括弧中的字母映射到圖 4。

* 分支到 VNet (a)
* 分支到分支 (b)
  * 快速路由全球涵蓋和虛擬廣域網路
* 遠端使用者到 VNet (c)
* 遠端使用者到分支 (d)
* VNet 到 VNet (e)
* 分支到集線器-集線器到分支 (f)
* 分支到集線器-集線器到 VNet (g)
* VNet 到集線器-集線器到 VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>分支到 VNet (a) 與分支到 VNet 跨區域 (g)

分支到 VNet 是 Azure 虛擬 WAN 支援的主要路徑。 此路徑允許您將分支連接到 Azure VNet 中部署的 Azure IAAS 企業工作負荷。 分支機構可以通過 ExpressRoute 或網站到網站 VPN 連接到虛擬 WAN。 以 VNet 連接連接到虛擬廣域網集線器的流量傳輸到 VNet。 虛擬 WAN 不需要顯式[閘道傳輸](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity),因為虛擬 WAN 會自動啟用到分支網站的閘道傳輸。 有關如何將 SD-WAN CPE 連接到虛擬廣域網的[虛擬 WAN 合作夥伴](virtual-wan-configure-automation-providers.md)文章。

### <a name="expressroute-global-reach-and-virtual-wan"></a>快速路由全球涵蓋和虛擬廣域網路

ExpressRoute 是一種將本地網路連接到 Microsoft 雲的專用且有彈性的方式。 虛擬廣域網支援快速路由電路連接。 將分支網站連接到具有快速路由的虛擬廣域網路需要 1) 進階電路 2) 電路必須位於啟用全域覆蓋的位置。

ExpressRoute 全球覆蓋是快速路由的附加功能。 借助 Global 覆蓋,您可以將 ExpressRoute 電路連結在一起,在本地網路之間建立專用網路。 使用 ExpressRoute 連接到 Azure 虛擬 WAN 的分支需要快速路由全域覆蓋彼此通信。

在此模型中,使用 ExpressRoute 連接到虛擬 WAN 中心的每個分支都可以使用分支到 VNet 路徑連接到 VNet。 分支到分支的流量不會傳輸集線器,因為 ExpressRoute 全域覆蓋線支援在 Azure WAN 上實現更優化的路徑。

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>分支到分支 (b) 和分支到分支跨區域 (f)

分支可以使用 ExpressRoute 電路和/或網站到網站 VPN 連接連接到 Azure 虛擬 WAN 集線器。 您可以將分支連接到離分支最近的區域中的虛擬 WAN 中心。

此選項允許企業利用 Azure 主幹來連接分支。 但是,即使此功能可用,也應權衡通過 Azure 虛擬 WAN 與使用專用 WAN 連接分支的好處。  

### <a name="remote-user-to-vnet-c"></a>遠端使用者到 VNet (c)

您可以使用從遠端使用者用戶端到虛擬 WAN 的點對點連接,對 Azure 進行直接、安全的遠端訪問。 企業遠端使用者不再需要使用公司 VPN 向雲髮夾。

### <a name="remote-user-to-branch-d"></a>遠端使用者到分支 (d)

遠端使用者到分支路徑允許使用點對點連接的遠端用戶通過雲傳輸訪問本地工作負載和應用程式。 此路徑使遠端用戶能夠靈活地訪問部署在 Azure 和本地的工作負載。 企業可以在 Azure 虛擬 WAN 中啟用基於雲的中央安全遠端訪問服務。

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet 到 VNet 傳輸 (e) 與 VNet 到 VNet 跨區域 (h)

VNet 到 VNet 傳輸使 VNet 能夠相互連接,以便互連跨多個 VNet 實現的多層應用程式。 或者,您可以通過 VNet 對等互連將 VNet 彼此連接,這可能適用於不需要透過 VWAN 集線器進行傳輸的某些情況。

## <a name="security-and-policy-control"></a><a name="security"></a>安全與原則控制

Azure 虛擬廣域網集線器互連了混合網路中的所有網路端點,並可能看到所有傳輸網路流量。 通過在 VWAN 集線器內部署 Azure 防火牆,實現基於雲端的安全、訪問和策略控制,可以將虛擬 WAN 集線器轉換為安全的虛擬中心。 虛擬 WAN 中心中的 Azure 防火牆的編排可以由 Azure 防火牆管理器執行。

[Azure 防火牆管理員](https://go.microsoft.com/fwlink/?linkid=2107683)提供了管理和擴展全域傳輸網路安全性的功能。 Azure 防火牆管理員提供通過第三方以及 Azure 防火牆集中管理路由、全域策略管理、進階 Internet 安全服務的能力。

![使用 Azure 防火牆保護虛擬中心](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**圖 5:使用 Azure 防火牆的安全虛擬中心**

虛擬 WAN 的 Azure 防火牆支援以下全域安全傳輸連接路徑。 括弧中的字母映射到圖 5。

* VNet 到 VNet 安全傳輸 (e)
* VNet 到 Internet 或第三方安全服務 (i)
* 分支機構到互聯網或第三方安全服務 (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 到 VNet 安全傳輸 (e)

VNet 到 VNet 安全傳輸使 VNet 能夠透過虛擬 WAN 集線器中的 Azure 防火牆相互連接。

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet 到 Internet 或第三方安全服務 (i)

VNet 到 Internet 或第三方安全傳輸使 VNet 能夠透過虛擬 WAN 中心的 Azure 防火牆連接到 Internet 或受支援的第三方安全服務。

### <a name="branch-to-internet-or-third-party-security-service-j"></a>分支機構到互聯網或第三方安全服務 (j)
分支到 Internet 或第三方安全傳輸使分支能夠透過虛擬 WAN 中心的 Azure 防火牆連接到 Internet 或受支援的第三方安全服務。

## <a name="next-steps"></a>後續步驟

使用虛擬 WAN 創建連接,並在 VWAN 中心中部署 Azure 防火牆。

* [使用虛擬 WAN 的網站到網站連接](virtual-wan-site-to-site-portal.md)
* [使用虛擬 WAN 的快速路由連線](virtual-wan-expressroute-portal.md)
* [Azure 防火牆管理器在 VWAN 中部署 Azure FW](https://go.microsoft.com/fwlink/?linkid=2107683)
