---
title: 架構：全域傳輸網路架構
titleSuffix: Azure Virtual WAN
description: 瞭解適用于虛擬 WAN 的全球傳輸網路架構
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: c32d42de5290bff63a897e7b9d5c8a2b1bf04ce4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786966"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>全球傳輸網路架構和虛擬 WAN

現代化企業需要跨雲端和內部部署的超分散式應用程式、資料和使用者之間進行廣泛的連線。 企業採用全球傳輸網路架構來整合、連接及控制以雲端為中心的現代化全球企業 IT 使用量。

全球傳輸網路架構是以傳統的中樞和輪輻連線模式為基礎，其中雲端託管網路「中樞」可在可能分散于不同類型「輪輻」的端點之間啟用可轉移的連線。

在此模型中，輪輻可以是：
* 虛擬網路（Vnet）
* 實體分支網站
* 遠端使用者
* Internet

![中樞和輪輻](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**圖1：全域傳輸中樞和輪輻網路**

[圖 1] 顯示全球傳輸網路的邏輯觀點，其中散佈各地的使用者、實體網站和 Vnet 透過雲端中裝載的網路中樞進行互連。 此架構可啟用網路端點之間的邏輯單一躍點傳輸連線。

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>具有虛擬 WAN 的全球傳輸網路

Azure 虛擬 WAN 是受 Microsoft 管理的雲端網路服務。 此服務所組成的所有網路元件都是由 Microsoft 託管及管理。 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 Wan 總覽](virtual-wan-about.md)文章。

Azure 虛擬 WAN 可讓您在 Vnet、分公司、SaaS 和 PaaS 應用程式和使用者的全域分散式雲端工作負載集合之間，啟用通用的任意對任意連線，以允許全球傳輸網路架構。

![Azure 虛擬 WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**圖2：全域傳輸網路和虛擬 WAN**

在 Azure 虛擬 WAN 架構中，會在 Azure 區域中布建虛擬 WAN 中樞，讓您可以選擇連接分支、Vnet 和遠端使用者。 實體分支網站會透過 Premium ExpressRoute 或站對站 Vpn 連線到中樞，Vnet 會透過 VNet 連線連接到中樞，而遠端使用者可以使用使用者 VPN （點對站 Vpn）直接連接到中樞。 虛擬 WAN 也支援跨區域 VNet 連線，其中一個區域中的 VNet 可以連接到不同區域中的虛擬 WAN 中樞。

您可以建立虛擬 WAN，方法是在擁有最多輪輻（分支、Vnet、使用者）的區域中建立單一虛擬 WAN 中樞，然後將其他區域中的輪輻連接到中樞。 當企業的使用量大多在具有一些遠端輪輻的一個區域中時，這是個不錯的選項。  
  
## <a name="hub-to-hub-connectivity-preview"></a><a name="hubtohub"></a>中樞對中樞連線能力（預覽）

企業雲端的使用量可以跨越多個雲端區域，而且最好是從最接近其實體網站和使用者的區域存取雲端的最佳（延遲）。 全球傳輸網路架構的其中一個主要原則，是啟用所有雲端和內部部署網路端點之間的跨區域連線。 這表示從一個區域中連線到雲端的分支流量，可以使用[Azure 全域網路](https://azure.microsoft.com/global-infrastructure/global-network/)所啟用的中樞對中樞連線，連接到不同區域中的另一個分支或 VNet。

![跨區域](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**圖3：虛擬 WAN 跨區域連線能力**

在單一虛擬 WAN 中啟用多個中樞時，中樞會透過中樞對中樞連結自動進行連線，因此可在分散于多個區域的分支和 Vnet 之間啟用全域連線能力。 

此外，所有屬於相同虛擬 WAN 的中樞都可以與不同的地區存取和安全性原則相關聯。 如需詳細資訊，請參閱本文稍後的[安全性與原則控制](#security)。

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>任何對任何連線能力

全域傳輸網路架構可透過虛擬 WAN 中樞提供任何對任何連線能力。 此架構會消除或減少輪輻之間的完整網格或部分網格連線需求，這在建立和維護時更為複雜。 此外，中樞和輪輻與網狀架構網路中的路由控制較容易設定和維護。

任何對任何連線能力（在全域架構的環境中）都可讓具有全域散發的使用者、分支、資料中心、Vnet 和應用程式的企業透過「傳輸」中樞彼此連接。 Azure 虛擬 WAN 會作為全域傳輸系統。

![任何](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**圖4：虛擬 WAN 流量路徑**

Azure 虛擬 WAN 支援下列全球傳輸連接路徑。 括弧中的字母會對應到 [圖 4]。

* 分支對 VNet （a）
* 分支對分支（b）
  * ExpressRoute 全球接觸和虛擬 WAN
* 遠端使用者對 VNet （c）
* 遠端使用者對分支（d）
* VNet 對 VNet （e）
* 分支對中樞-中樞對分支（f）
* 分支對中樞-中樞對 VNet （g）
* VNet 對中樞-中樞對 VNet （h）

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>分支對 VNet （a）和分支對 VNet 跨區域（g）

分支對 VNet 是 Azure 虛擬 WAN 支援的主要路徑。 此路徑可讓您將分支連線至 azure Vnet 中部署的 Azure IAAS 企業工作負載。 分支可以透過 ExpressRoute 或站對站 VPN 連線到虛擬 WAN。 流量會傳輸到透過 VNet 連線連接到虛擬 WAN 中樞的 Vnet。 虛擬 WAN 不需要明確的[閘道傳輸](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)，因為虛擬 wan 會自動啟用閘道傳輸至分支網站。 請參閱[虛擬 Wan 合作夥伴](virtual-wan-configure-automation-providers.md)文章，以瞭解如何將 SD-WAN CPE 連線至虛擬 WAN。

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute 全球接觸和虛擬 WAN

ExpressRoute 是將內部部署網路連線至 Microsoft Cloud 的私用和可復原方式。 虛擬 WAN 支援 Express Route 線路連線。 若要將分支網站連線到具有 Express Route 的虛擬 WAN，需要1） Premium 線路2）線路，以達到全球範圍的已啟用位置。

ExpressRoute Global 觸及是 ExpressRoute 的附加元件功能。 有了全球範圍，您可以將 ExpressRoute 線路連結在一起，以在內部部署網路之間建立私人網路。 使用 ExpressRoute 連線到 Azure 虛擬 WAN 的分支需要 ExpressRoute 全域觸達彼此通訊。

在此模型中，使用 ExpressRoute 連線到虛擬 WAN 中樞的每個分支，都可以使用分支對 VNet 路徑連接到 Vnet。 分支對分支流量不會傳輸中樞，因為 ExpressRoute Global 觸及可透過 Azure WAN 提供更佳的路徑。

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>分支對分支（b）和分支對分支的跨區域（f）

您可以使用 ExpressRoute 線路和/或站對站 VPN 連線，將分支連接到 Azure 虛擬 WAN 中樞。 您可以將分支連接到最接近分支區域中的虛擬 WAN 中樞。

此選項可讓企業利用 Azure 骨幹來連接分支。 不過，即使這項功能可供使用，您還是應該衡量透過 Azure 虛擬 WAN 連接分支的優點，與使用私人 WAN。  

> [!NOTE]
> 停用虛擬 WAN 中的分支對分支連線-虛擬 WAN 可以設定為停用分支對分支連線能力。 此組態將會封鎖 VPN （S2S 和 P2S）與 Express Route 連線網站之間的路由傳播。 此設定不會影響分支對 Vnet 和 Vnet 對 Vnet 路由傳播和連線能力。 使用 Azure 入口網站設定此設定：在 [虛擬 WAN 設定] 功能表底下，選擇 [設定：分支至分支-已停用]。 

### <a name="remote-user-to-vnet-c"></a>遠端使用者對 VNet （c）

您可以使用從遠端使用者用戶端到虛擬 WAN 的點對站連線，啟用對 Azure 的直接安全遠端存取。 企業遠端使用者不再需要使用公司 VPN hairpin 到雲端。

### <a name="remote-user-to-branch-d"></a>遠端使用者對分支（d）

遠端使用者到分支路徑可讓使用點對站連線至 Azure 的遠端使用者，透過雲端傳輸來存取內部部署工作負載和應用程式。 此路徑可讓遠端使用者彈性地存取部署在 Azure 和內部部署環境中的工作負載。 企業可以在 Azure 虛擬 WAN 中啟用以雲端為基礎的集中式安全遠端存取服務。

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet 對 VNet 傳輸（e）和 VNet 對 VNet 跨區域（h）

VNet 對 VNet 傳輸可讓 Vnet 彼此連線，以便互相連接跨多個 Vnet 執行的多層式應用程式。 （選擇性）您可以透過 VNet 對等互連彼此連線 Vnet，這可能適用于某些情況，而這些案例不需要透過 VWAN 中樞進行傳輸。

## <a name="security-and-policy-control"></a><a name="security"></a>安全性與原則控制

Azure 虛擬 WAN 中樞會在混合式網路上互連所有的網路端點，而且可能會看到所有傳輸網路流量。 虛擬 WAN 中樞可以轉換成安全的虛擬中樞，方法是將 Azure 防火牆部署在 VWAN 中樞內，以啟用雲端式安全性、存取和原則控制。 Azure 防火牆管理員可以執行虛擬 WAN 中樞中的 Azure 防火牆協調流程。

[Azure 防火牆管理員](https://go.microsoft.com/fwlink/?linkid=2107683)提供功能來管理和調整全球傳輸網路的安全性。 Azure 防火牆管理員透過協力廠商和 Azure 防火牆，提供集中管理路由、全域原則管理、先進的網際網路安全性服務的能力。

![受保護的虛擬中樞與 Azure 防火牆](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**圖5：使用 Azure 防火牆的受保護虛擬中樞**

適用于虛擬 WAN 的 Azure 防火牆支援下列全球保護的傳輸連接路徑。 括弧中的字母會對應到 [圖 5]。

* VNet 對 VNet 安全傳輸（e）
* VNet 對網際網路或協力廠商安全性服務（i）
* 分支對網際網路或協力廠商安全性服務（j）

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 對 VNet 安全傳輸（e）

VNet 對 VNet 安全傳輸可讓 Vnet 透過虛擬 WAN 中樞的 Azure 防火牆彼此連線。

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet 對網際網路或協力廠商安全性服務（i）

VNet 對網際網路或協力廠商的安全傳輸可讓 Vnet 透過虛擬 WAN 中樞的 Azure 防火牆連線到網際網路或支援的協力廠商安全性服務。

### <a name="branch-to-internet-or-third-party-security-service-j"></a>分支對網際網路或協力廠商安全性服務（j）
分支對網際網路或協力廠商安全傳輸可讓分支透過虛擬 WAN 中樞的 Azure 防火牆連線到網際網路或支援的協力廠商安全性服務。

## <a name="next-steps"></a>後續步驟

使用虛擬 WAN 建立連線，並在 VWAN hub 中部署 Azure 防火牆。

* [使用虛擬 WAN 的站對站連線](virtual-wan-site-to-site-portal.md)
* [使用虛擬 WAN 的 ExpressRoute 連線](virtual-wan-expressroute-portal.md)
* [在 VWAN 中部署 Azure FW 的 azure 防火牆管理員](https://go.microsoft.com/fwlink/?linkid=2107683)
