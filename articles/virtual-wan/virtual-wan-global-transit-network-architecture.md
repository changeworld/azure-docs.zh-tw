---
title: 架構：全域傳輸網路架構
titleSuffix: Azure Virtual WAN
description: 瞭解 Azure 虛擬 WAN 如何藉由啟用雲端工作負載之間的廣泛、任意對任何連線，來允許全域傳輸網路架構。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: cherylmc
ms.openlocfilehash: e3a0eaeebbc0659b217051c6e98d67803896f2e1
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102318"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>全球傳輸網路架構和虛擬 WAN

新式企業需要跨雲端和內部部署的超分散式應用程式、資料和使用者之間進行廣泛的連接。 企業採用全球傳輸網路架構來合併、連接及控制以雲端為中心的現代化全球企業 IT 資源。

全球傳輸網路架構是以傳統的中樞和輪輻連線模型為基礎，其中雲端裝載的網路「中樞」可在可能跨不同類型「輪輻」散發的端點之間啟用可轉移的連線。

在此模型中，輪輻可以是：
* 虛擬網路 (Vnet) 
* 實體分支網站
* 遠端使用者
* Internet

![中樞和輪輻](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**圖1：全域傳輸中樞和輪輻網路**

[圖 1] 顯示全球傳輸網路的邏輯觀點，其中地理位置分散的使用者、實體網站與 Vnet 透過雲端託管的網路中樞互相互連。 此架構可啟用網路端點之間的邏輯單一躍點傳輸連線。

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>使用虛擬 WAN 的全球傳輸網路

Azure 虛擬 WAN 是 Microsoft 管理的雲端網路服務。 這項服務所組成的所有網路元件都是由 Microsoft 託管和管理。 如需有關虛擬 WAN 的詳細資訊，請參閱 [虛擬 Wan 總覽](virtual-wan-about.md) 文章。

Azure 虛擬 WAN 允許全域傳輸網路架構，方法是在 Vnet、分支網站、SaaS 和 PaaS 應用程式和使用者的全球分散式雲端工作負載集合之間啟用無所不在的任意對任意連線。

![Azure 虛擬 WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**圖2：全域傳輸網路和虛擬 WAN**

在 Azure 虛擬 WAN 架構中，會在 Azure 區域中布建虛擬 WAN 中樞，讓您可以選擇將分支、Vnet 和遠端使用者連線。 實體分支網站會依高階、標準 ExpressRoute 或站對站 Vpn 連線至中樞、Vnet 會透過 VNet 連線連線到中樞，而遠端使用者可以使用使用者 VPN (點對站 Vpn) ，直接連線到中樞。 虛擬 WAN 也支援跨區域 VNet 連線，其中一個區域中的 VNet 可以連接到不同區域中的虛擬 WAN 中樞。

您可以建立虛擬 WAN，方法是在具有最多輪輻 (分支、Vnet、使用者) ，然後將其他區域中的輪輻連接至中樞的區域中建立單一虛擬 WAN 中樞。 當企業使用量大多在具有一些遠端輪輻的區域中時，這是個不錯的選擇。  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>中樞對中樞連線能力

企業雲端的使用量可以橫跨多個雲端區域，最適合 (延遲的) 從最接近其實體網站和使用者的區域存取雲端。 全域傳輸網路架構的其中一個主要原則，是在所有雲端和內部部署網路端點之間啟用跨區域連接。 這表示，在某個區域中連線到雲端的分支流量，可以使用 [Azure 全球網路](https://azure.microsoft.com/global-infrastructure/global-network/)所啟用的中樞對中樞連線能力，連接到不同區域中的另一個分支或 VNet。

![跨區域](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**圖3：虛擬 WAN 跨區域連線能力**

當單一虛擬 WAN 中有多個中樞啟用時，中樞會透過中樞對中樞連結自動互相連線，因此可在跨多個區域散發的分支和 Vnet 之間啟用全球連線能力。 

此外，所有屬於相同虛擬 WAN 的中樞都可以與不同的地區存取和安全性原則產生關聯。 如需詳細資訊，請參閱本文稍後的 [安全性和原則控制](#security) 。

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>任意對任何連線能力

全域傳輸網路架構可透過虛擬 WAN 中樞提供任何對任何連線能力。 此架構可消除或減少輪輻之間的完整網狀或部分網狀連線需求，而這在建立和維護時較為複雜。 此外，中樞與輪輻與網狀架構網路中的路由控制更容易設定和維護。

全域架構) 內容中的任何對任何連線 (，可讓具有全域散發的使用者、分支、資料中心、Vnet 和應用程式的企業透過「傳輸」中樞 (的) 彼此連線。 Azure 虛擬 WAN 會作為全球傳輸系統。

![任何](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**圖4：虛擬 WAN 流量路徑**

Azure 虛擬 WAN 支援下列全域傳輸連接路徑。 括弧中的字母會對應到圖4。

* 分支對 VNet () 
* 分支對分支 (b) 
  * ExpressRoute 全球接觸和虛擬 WAN
* 遠端使用者對 VNet (c) 
* 遠端使用者對分支 (d) 
* VNet 對 VNet (e) 
* 分支對中樞-中樞對分支 (f) 
* 分支對中樞-中樞對 VNet (g) 
* VNet 對中樞-中樞對 VNet (h) 

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>分支對 VNet () 和分支對 VNet 跨區域 (g) 

分支對 VNet 是 Azure 虛擬 WAN 所支援的主要路徑。 此路徑可讓您將分支連線至部署在 Azure Vnet 中的 Azure IAAS 企業工作負載。 分支可透過 ExpressRoute 或站對站 VPN 連線至虛擬 WAN。 流量傳輸至透過 VNet 連線連線到虛擬 WAN 中樞的 Vnet。 虛擬 WAN 不需要明確的 [閘道傳輸](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) ，因為虛擬 wan 會自動啟用閘道傳輸至分支網站。 請參閱 [虛擬 Wan 合作夥伴](virtual-wan-configure-automation-providers.md) 文章，以瞭解如何將 SD-WAN CPE 連接至虛擬 wan。

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute 全球接觸和虛擬 WAN

ExpressRoute 是將內部部署網路連線到 Microsoft 雲端的私用和具彈性的方式。 虛擬 WAN 支援 Express Route 線路連接。 使用 Express Route 將分支網站連接至虛擬 WAN 時，需要 1) Premium 或標準電路 2) 電路位於全球觸達啟用的位置。

ExpressRoute Global 觸及是適用于 ExpressRoute 的附加元件功能。 透過全球接觸，您可以將 ExpressRoute 線路連結在一起，以在內部部署網路之間建立私人網路。 使用 ExpressRoute 連線到 Azure 虛擬 WAN 的分支，需要 ExpressRoute 全球連線才能彼此通訊。

在此模型中，使用 ExpressRoute 連線到虛擬 WAN 中樞的每個分支都可以使用分支對 VNet 路徑連接到 Vnet。 分支對分支的流量不會傳輸中樞，因為 ExpressRoute Global 觸及可透過 Azure WAN 提供更佳的路徑。

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>分支對分支 (b) 和分支對分支的跨區域 (f) 

您可以使用 ExpressRoute 線路和/或站對站 VPN 連線，將分支連線到 Azure 虛擬 WAN 中樞。 您可以將分支連線至位於最接近分支的區域中的虛擬 WAN 中樞。

此選項可讓企業利用 Azure 骨幹來連接分支。 不過，即使這項功能可供使用，您還是應該權衡透過 Azure 虛擬 WAN 連接分支的優點，以及使用私人 WAN。  

> [!NOTE]
> 在虛擬 WAN 中停用分支對分支連線能力-虛擬 WAN 可以設定為停用分支對分支連線能力。 此組態會封鎖 VPN (S2S 和 P2S) 和 Express Route 連線的網站之間的路由傳播。 此設定不會影響分支對 Vnet 和 Vnet 對 Vnet 路由傳播和連線能力。 若要使用 Azure 入口網站來設定此設定：在 [虛擬 WAN 設定] 功能表下，選擇 [設定：分支對分支停用]。 

### <a name="remote-user-to-vnet-c"></a>遠端使用者對 VNet (c) 

您可以使用從遠端使用者用戶端到虛擬 WAN 的點對站連線，對 Azure 啟用直接、安全的遠端存取。 企業遠端使用者不再需要使用公司 VPN hairpin 至雲端。

### <a name="remote-user-to-branch-d"></a>遠端使用者對分支 (d) 

遠端使用者到分支路徑可讓使用點對站連線到 Azure 的遠端使用者，透過雲端傳輸來存取內部部署工作負載和應用程式。 此路徑可讓遠端使用者彈性地存取在 Azure 和內部部署環境中部署的工作負載。 企業可以在 Azure 虛擬 WAN 中啟用集中式雲端式安全遠端存取服務。

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet 對 VNet 傳輸 (e) 和 VNet 對 VNet 跨區域 (h) 

VNet 對 VNet 傳輸可讓 Vnet 彼此連線，以便互相連接跨多個 Vnet 所執行的多層式應用程式。 （選擇性）您可以透過 VNet 對等互連將 Vnet 彼此連線，這可能適用于某些情況下，透過 VWAN 中樞傳輸的情況並不是必要的。


## <a name="force-tunneling-and-default-route-in-azure-virtual-wan"></a><a name="DefaultRoute"></a>Azure 虛擬 WAN 中的強制通道和預設路由

您可以藉由在虛擬 WAN 的 VPN、ExpressRoute 或虛擬網路連線上設定啟用預設路由，來啟用強制通道。

如果連線上的 [啟用預設旗標] 為 [已啟用]，則虛擬中樞會將已學習的預設路由傳播至虛擬網路/站對站 VPN/ExpressRoute 連線。 

當使用者編輯虛擬網路連線、VPN 連線或 ExpressRoute 連線時，此旗標為可見。 根據預設，當網站或 ExpressRoute 線路連線至中樞時，會停用此旗標。 新增虛擬網路連線以將 VNet 連線到虛擬中樞時，依預設會啟用旗標。 預設路由不是源自於虛擬 WAN 中樞；如果虛擬 WAN 中樞由於在中樞內部署防火牆而學習預設路由，或其他連線的網站已啟用強制通道，則會傳播預設路由。

## <a name="security-and-policy-control"></a><a name="security"></a>安全性和原則控制

Azure 虛擬 WAN 中樞會跨混合式網路連接所有網路端點，而且可能會看到所有傳輸網路流量。 虛擬 WAN 中樞可以轉換成安全虛擬中樞，方法是在 VWAN 中樞內部署 Azure 防火牆，以啟用雲端式安全性、存取和原則控制。 Azure 防火牆管理員可以執行虛擬 WAN 中樞內的 Azure 防火牆協調流程。

[Azure 防火牆管理員](https://go.microsoft.com/fwlink/?linkid=2107683) 提供管理和調整全域傳輸網路安全性的功能。 Azure 防火牆管理員可讓您透過協力廠商和 Azure 防火牆，集中管理路由、全域原則管理、先進的網際網路安全性服務。

![使用 Azure 防火牆的安全虛擬中樞](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**圖5：使用 Azure 防火牆的安全虛擬中樞**

> [!NOTE]
> 目前不支援與防火牆之間的中樞。 中樞之間的流量會直接移至每個中樞內的 Azure 防火牆。

虛擬 WAN 的 Azure 防火牆支援下列全球安全傳輸連接路徑。 括弧中的字母會對應到圖5。

* VNet 對 VNet 安全傳輸 (e) 
* VNet 對網際網路或協力廠商安全性服務 (我) 
* 分支對網際網路或協力廠商安全性服務 (j) 

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 對 VNet 安全傳輸 (e) 

VNet 對 VNet 保護的傳輸可讓 Vnet 透過虛擬 WAN 中樞內的 Azure 防火牆互相連接。

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet 對網際網路或協力廠商安全性服務 (我) 

VNet 對網際網路可讓 Vnet 透過虛擬 WAN 中樞內的 Azure 防火牆連接到網際網路。 經由受支援的協力廠商安全性服務傳送至網際網路的流量不會流經 Azure 防火牆。 您可以使用 Azure 防火牆管理員，透過支援的協力廠商安全性服務來設定 Vnet 對網際網路路徑。  

### <a name="branch-to-internet-or-third-party-security-service-j"></a>分支對網際網路或協力廠商安全性服務 (j) 
分支對網際網路可讓分支透過虛擬 WAN 中樞內的 Azure 防火牆連接到網際網路。 經由受支援的協力廠商安全性服務傳送至網際網路的流量不會流經 Azure 防火牆。 您可以使用 Azure 防火牆管理員，透過支援的協力廠商安全性服務來設定分支對網際網路路徑。 

### <a name="how-do-i-enable-default-route-00000-in-a-secured-virtual-hub"></a>如何? 啟用安全虛擬中樞內的預設路由 (0.0.0.0/0) 

在虛擬 WAN 中樞中部署的 Azure 防火牆 (安全虛擬中樞) 可以設定為網際網路的預設路由器或受信任的安全性提供者， (透過 VPN 或 Express Route) 、輪輻 Vnet 和使用者 (透過 P2S VPN) 連線的所有分支。 您必須使用 Azure 防火牆管理員來完成此設定。  請參閱將流量路由傳送至您的中樞，以設定來自分支的所有流量 (包括使用者) ，以及透過 Azure 防火牆 Vnet 至網際網路。 

這是兩個步驟的設定：

1. 使用安全虛擬中樞路由設定功能表來設定網際網路流量路由。 設定可透過防火牆將流量傳送至網際網路的 Vnet 和分支。

2. 設定 (Vnet 和分支) 可透過中樞或受信任的安全性提供者中的 Azure FW，將流量路由傳送至網際網路的連線 (0.0.0.0/0) 。 此步驟可確保預設路由會傳播至透過連線連接到虛擬 WAN 中樞的選定分支和 Vnet。 

### <a name="force-tunneling-traffic-to-on-premises-firewall-in-a-secured-virtual-hub"></a>在安全的虛擬中樞內強制將流量通道傳送至內部部署防火牆

如果已從 (VPN 或 ER) 網站的其中一個分支 (via BGP) 學習到預設路由，則會將此預設路由覆寫為從 Azure 防火牆管理員設定學習的預設路由。 在此情況下，從 Vnet 輸入中樞的所有流量，以及目的地為網際網路的分支，將會路由至 Azure 防火牆或受信任的安全性提供者。

> [!NOTE]
> 目前沒有任何選項可針對源自 Vnet、分支或使用者的網際網路系結流量，選取內部部署防火牆或 Azure 防火牆 (以及信任的安全性提供者) 。 從 Azure 防火牆管理員設定學習的預設路由，一律優先于從其中一個分支學習的預設路由。


## <a name="next-steps"></a>後續步驟

使用虛擬 WAN 建立連線，並在 VWAN hub (s) 中部署 Azure 防火牆。

* [使用虛擬 WAN 的站對站連線](virtual-wan-site-to-site-portal.md)
* [使用虛擬 WAN 的 ExpressRoute 連接](virtual-wan-expressroute-portal.md)
* [在 VWAN 中部署 Azure FW 的 Azure 防火牆管理員](https://go.microsoft.com/fwlink/?linkid=2107683)
