---
title: 架構：遷移至 Azure 虛擬 WAN
description: 瞭解如何遷移至 Azure 虛擬 WAN。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8dfcdd8195824cb732df2c0c70c338e69630c5cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753129"
---
# <a name="migrate-to-azure-virtual-wan"></a>移轉移至 Azure 虛擬 WAN

Azure 虛擬 WAN 可讓公司簡化其全球連線，以便從 Microsoft 全球網路的規模中獲益。 本文提供的技術詳細資料可讓想要從現有客戶管理的中樞和輪輻拓撲遷移到使用受 Microsoft 管理之虛擬 WAN 中樞的設計。

如需 Azure 虛擬 WAN 為採用以雲端為中心的新式企業通用網路之企業所提供之優點的相關資訊，請參閱[全球傳輸網路架構和虛擬 WAN](virtual-wan-global-transit-network-architecture.md)。

![中樞和輪輻 ](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
 **圖： AZURE 虛擬 WAN**

數以千計的客戶已採用 Azure 中樞和輪輻連線模型，來利用 Azure 網路的預設可轉移路由行為，以建立簡單且可擴充的雲端網路。 Azure 虛擬 WAN 是以這些概念為基礎，而且引進了新功能，可允許全域連線拓撲，不僅在內部部署位置和 Azure 之間，還可讓客戶利用 Microsoft 網路的規模來加強其現有的全球網路。

本文說明如何將現有的混合式環境遷移至虛擬 WAN。

## <a name="scenario"></a>狀況

Contoso 是全球金融組織，在歐洲和亞洲都有辦公室。 他們打算將其現有的應用程式從內部部署資料中心移至 Azure，並根據手動中樞和輪輻架構建立基礎設計，包括適用于混合式連線的區域客戶管理中樞虛擬網路。 在移至雲端式技術的過程中，網路小組已負責確保其連線能力已針對企業發展而優化。

下圖顯示現有全域網路的高階觀點，包括多個 Azure 區域的連線能力。

![Contoso 現有的網路拓撲 ](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
 **圖： contoso 現有的網路拓撲**

您可以從現有的網路拓撲了解下列幾點：

- 中樞和輪輻拓撲會用於多個區域，包括連線回一般私人 WAN 的 ExpressRoute 進階線路。

- 這其中一些網站也具有直接通往 Azure 的 VPN 通道，以觸達裝載於 Microsoft 雲端的應用程式。

## <a name="requirements"></a>規格需求

分派給網路小組的任務是提供可支援 Contoso 移轉至雲端的全球網路模型，而且必須在成本、規模和效能方面進行最佳化。 總而言之，必須符合下列需求：

- 為總公司 (HQ) 和分公司提供已最佳化的雲端裝載應用程式路徑。
- 移除對現有內部部署資料中心（DC）的依賴，以進行 VPN 終止，同時保留下列連接路徑：
  - **分支對 VNet**： VPN 連線的辦公室必須能夠存取在本機 Azure 區域中遷移至雲端的應用程式。
  - **分支對中樞對中樞對 VNet**： VPN 連線的辦公室必須能夠存取遷移至遠端 Azure 區域中雲端的應用程式。
  - **分支對分支**：區域 VPN 連線的辦公室必須能夠與彼此和 ExpressRoute 連線的總部/DC 網站進行通訊。
  - **分支對中樞對中樞**：全域分隔的 VPN 連線辦公室必須能夠彼此通訊，以及任何 ExpressRoute 連線的 HQ 總部/DC 網站。
  - **分支對網際網路**：已連線的網站必須能夠與網際網路通訊。 此流量必須經過篩選和記錄。
  - **Vnet 對 vnet**：相同區域中的輪輻虛擬網路必須能夠彼此通訊。
  - **Vnet 對中樞對中樞對 vnet**：不同區域中的輪輻虛擬網路必須能夠彼此通訊。
- 提供 Contoso 漫遊使用者（膝上型電腦和電話）存取公司資源的能力，而不是在公司網路上。

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure 虛擬 WAN 架構

下圖顯示使用 Azure 虛擬 WAN 來符合上一節中所述需求之已更新目標拓撲的高階觀點。

![Contoso 虛擬 WAN 架構 ](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
 **圖： AZURE 虛擬 wan 架構**

摘要：

- 歐洲的 HQ 會維持與 ExpressRoute 連線，而歐洲的內部部署 DC 則會完全移轉至 Azure 並立即解除委任。
- 亞洲的 DC 和 HQ 仍會連線到私人 WAN。 Azure 虛擬 WAN 現在用來加強當地的電訊網路，並提供全球連線能力。
- 部署在西歐和南東亞 Azure 區域的 azure 虛擬 WAN 中樞，以提供 ExpressRoute 和 VPN 連線裝置的連接中樞。
- 中樞也會為使用全球網狀網路的 OpenVPN 連線能力，針對跨多個用戶端類型的漫遊使用者提供 VPN 終止點，讓您不僅可以存取移轉至 Azure 的應用程式，也能存取內部部署中剩餘的任何資源。
- Azure 虛擬 WAN 會提供虛擬網路內資源的網際網路連線能力。

Azure 虛擬 WAN 也會提供遠端網站的網際網路連線能力。 透過合作夥伴整合支援本地網際網路分組，以將存取 SaaS 服務 (例如 Office 365) 最佳化。

## <a name="migrate-to-virtual-wan"></a>遷移至虛擬 WAN

本節說明遷移至 Azure 虛擬 WAN 的各種步驟。

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>步驟1：單一區域客戶管理的中樞和輪輻

下圖顯示在推出 Azure 虛擬 WAN 之前，Contoso 的單一區域拓朴：

![單一區域拓撲 ](./media/migrate-from-hub-spoke-topology/figure1.png)
 **圖1：單一區域手動中樞和輪輻**

為了維持中樞和輪輻的方法，客戶管理的中樞虛擬網路包含數個函式區塊：

- 共用服務（多個輪輻所需的任何一般函數）。 範例： Contoso 會在基礎結構即服務（IaaS）虛擬機器上使用 Windows Server 網域控制站。
- IP/路由防火牆服務會透過協力廠商網路虛擬設備提供，可啟用輪輻對輪輻第 3 層 IP 路由。
- 網際網路輸入/輸出服務 (包括適用於輸入 HTTPS 要求的 Azure 應用程式閘道)，以及在虛擬機器上執行的協力廠商 Proxy 服務 (用以篩選對網際網路資源的輸出存取)。
- 可連線至內部部署網路的 ExpressRoute 和 VPN 虛擬網路閘道。

### <a name="step-2-deploy-virtual-wan-hubs"></a>步驟2：部署虛擬 WAN 中樞

在每個區域中部署虛擬 WAN 中樞。 如下列文章所述，使用 VPN 閘道和 ExpressRoute 閘道來設定虛擬 WAN 中樞：

- [教學課程：使用 Azure 虛擬 WAN 來建立站對站連線](virtual-wan-site-to-site-portal.md)
- [教學課程：使用 Azure 虛擬 WAN 建立 ExpressRoute 關聯](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Azure 虛擬 WAN 必須使用標準 SKU 來啟用本文中顯示的部分流量路徑。

![部署虛擬 WAN 中樞 ](./media/migrate-from-hub-spoke-topology/figure2.png)
 **圖2：客戶管理的中樞和輪輻到虛擬 WAN 遷移**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>步驟3：將遠端網站（ExpressRoute 和 VPN）連接到虛擬 WAN

將虛擬 WAN 中樞連線到現有的 ExpressRoute 線路，並透過網際網路將站對站 Vpn 設定為任何遠端分支。

> [!NOTE]
> ExpressRoute 線路必須升級為進階 SKU 類型，才能連線到虛擬 WAN 中樞。

![將遠端網站連線到虛擬 WAN ](./media/migrate-from-hub-spoke-topology/figure3.png)
 **圖3：客戶管理的中樞和輪輻到虛擬 wan 遷移**

此時，內部部署網路設備會開始接收路由，反映指派給虛擬 WAN 管理的中樞 VNet 的 IP 位址空間。 在此階段中，遠端 VPN 連線的分支將會看到兩個連至輪輻虛擬網路中任何現有應用程式的路徑。 這些裝置應設定為繼續使用通道給客戶管理的中樞，以確保在轉換階段期間進行對稱式路由。

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>步驟4：透過虛擬 WAN 測試混合式連線能力

在使用受控虛擬 WAN 中樞進行生產環境連線之前，建議您先設定測試輪輻虛擬網路和虛擬 WAN VNet 連線。 驗證此測試環境的連線會透過 ExpressRoute 和站對站 VPN 來運作，然後繼續進行後續步驟。

![透過虛擬 WAN 測試混合式連線 ](./media/migrate-from-hub-spoke-topology/figure4.png)
 **圖4：客戶管理的中樞和輪輻至虛擬 wan 遷移**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>步驟5：轉換虛擬 WAN 中樞的連線能力

![將連線轉換至虛擬 WAN 中樞 ](./media/migrate-from-hub-spoke-topology/figure5.png)
 **圖5：客戶管理的中樞和輪輻到虛擬 wan 遷移**

**答**： 將來自輪輻虛擬網路的現有對等互連連線，刪除到舊的客戶管理中樞。 完成步驟 a-c 之前，無法存取輪輻虛擬網路中的應用程式。

**b.** 透過 VNet 連線將輪輻虛擬網路連線到虛擬 WAN 中樞。

**c**。 移除任何先前在輪輻虛擬網路內用來進行輪輻對輪輻通訊的使用者定義路由 (UDR)。 這個路徑現在會透過虛擬 WAN 中樞內可用的動態路由來啟用。

**d.** 客戶管理的中樞中現有的 ExpressRoute 和 VPN 閘道現在已解除委任，以允許下一步（e）。

**e**。 透過新的 VNet 連線，將舊版客戶管理的中樞（中樞虛擬網路）連接到虛擬 WAN 中樞。

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>步驟6：舊中樞變成共用服務輪輻

我們現在已重新設計 Azure 網路，讓虛擬 WAN 中樞成為新拓撲的中心點。

![舊的中樞變成共用服務輪輻 ](./media/migrate-from-hub-spoke-topology/figure6.png)
 **圖6：客戶管理的中樞和輪輻到虛擬 WAN 遷移**

由於虛擬 WAN 中樞是受管理的實體，而且不允許部署自訂資源（例如虛擬機器），因此共用服務區塊現在會以輪輻虛擬網路的形式存在，並透過 Azure 應用程式閘道或網路虛擬裝置，來裝載網際網路輸入之類的功能。 共用服務環境與後端虛擬機器之間的流量現在會通過虛擬 WAN 管理的中樞。

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>步驟7：優化內部部署連線能力，以充分利用虛擬 WAN

在此階段中，Contoso 幾乎已完成將商務應用程式移轉至 Microsoft Cloud 的過程，而內部部署 DC 中只剩下一些舊版應用程式。

![將內部部署連線優化，以充分利用虛擬 WAN ](./media/migrate-from-hub-spoke-topology/figure7.png)
 **圖7：客戶管理的中樞和輪輻到虛擬 wan 遷移**

為了利用 Azure 虛擬 WAN 的完整功能，Contoso 決定解除委任其舊版內部部署 VPN 連線。 任何繼續存取 HQ 或 DC 網路的分支，均能使用 Azure 虛擬 WAN 內建的傳輸路由來傳輸 Microsoft 全球網路。

> [!NOTE]
> 若客戶想要利用 Microsoft 骨幹來補充其現有的私人 WAN，則 ExpressRoute Global Reach 是另一種選擇。

## <a name="end-state-architecture-and-traffic-paths"></a>結束狀態架構和流量路徑

![結束狀態架構和流量路徑 ](./media/migrate-from-hub-spoke-topology/figure8.png)
 **圖：雙重區域虛擬 WAN**

此節透過查看一些範例流量流程，來提供此拓撲如何符合原始需求的摘要。

### <a name="path-1"></a>路徑 1

路徑1顯示從亞洲 S2S VPN 連接分支到南部東亞區域中的 Azure VNet 的流量流程。

流量的路由如下所示：

- 亞洲分支透過可復原的 S2S BGP 啟用的通道連接到南部東亞虛擬 WAN 中樞。

- 亞洲虛擬 WAN 中樞會在本地將流量路由傳送到連線的 VNet。

![流程 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>路徑 2

路徑2顯示從 ExpressRoute 連線歐洲 HQ 到南部東亞區域中的 Azure VNet 的流量流程。

流量的路由如下所示：

- 歐洲總部透過 premium ExpressRoute 線路連接到西歐虛擬 WAN 中樞。

- 虛擬 WAN 中樞對中樞全球連線能力，可讓您將流量傳輸到遠端區域中連線的 VNet。

![流程 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>路徑 3

[路徑 3] 顯示從亞洲內部部署 DC 連接到「私人 WAN」到歐盟已連線的分支的流量流程。

流量的路由如下所示：

- 亞洲 DC 會連線到本地私人 WAN 電訊廠商。

- ExpressRoute 線路會在本機終止，而在私人 WAN 中則會連線到南部東亞的虛擬 WAN 中樞。

- 虛擬 WAN 中樞對中樞全域連線能力可讓流量傳輸。

![流程 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>路徑 4

路徑4顯示從南部東亞區域中的 Azure VNet 到西歐區域的 Azure VNet 的流量流程。

流量的路由如下所示：

- 虛擬 WAN 中樞對中樞全域連線能力讓您能夠在沒有進階使用者設定的情況下，原生傳輸所有連線的 Azure VNet。

![流程 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>路徑 5

路徑5顯示從漫遊 VPN （P2S）使用者到西歐區域中的 Azure VNet 的流量流程。

流量的路由如下所示：

- 膝上型電腦和行動裝置使用者會使用 OpenVPN 用戶端，以透明的方式連線到西歐的 P2S VPN 閘道。

- 西歐虛擬 WAN 中樞會在本地將流量路由傳送到連線的 VNet。

![流程 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>透過 Azure 防火牆的安全性與原則控制

Contoso 現在已驗證所有分支和 Vnet 之間的連線是否符合本文稍早所述的需求。 為了符合其安全性控制和網路隔離的需求，他們需要繼續透過中樞網路來分隔和記錄流量。 此功能先前是由網路虛擬裝置（NVA）執行。 Contoso 也想要解除委任其現有的 proxy 服務，並利用原生的 Azure 服務進行輸出網際網路篩選。

![透過 Azure 防火牆的安全性與原則控制 ](./media/migrate-from-hub-spoke-topology/security-policy.png)
 **圖：虛擬 WAN 中的 Azure 防火牆（安全的虛擬中樞）**

以下是將 Azure 防火牆引進虛擬 WAN 中樞，以啟用統一的原則控制點所需的高層級步驟。 如需此程式的詳細資訊和安全虛擬中樞的概念，請參閱[Azure 防火牆管理員](../firewall-manager/index.yml)。

1. 建立 Azure 防火牆原則。
2. 將防火牆原則連結到 Azure 虛擬 WAN 中樞。 此步驟可讓現有的虛擬 WAN 中樞作為安全的虛擬中樞運作，並部署所需的 Azure 防火牆資源。

> [!NOTE]
> 如果 Azure 防火牆部署在標準虛擬 WAN 中樞（SKU： Standard）中： V2V、B2V、V2I 和 B2I FW 原則只會針對源自 Vnet 的流量強制執行，且分支會連線到已部署 Azure FW 的特定中樞（受保護的中樞）。 即使遠端分支和 VNet 是透過虛擬 WAN 中樞連線到中樞連結，來自遠端 Vnet 的流量以及連接到相同虛擬 WAN 中其他虛擬 WAN 中樞的分支也不會是「防火牆」。 跨中樞防火牆支援位於 Azure 虛擬 WAN 和防火牆管理員藍圖上。

下列路徑顯示使用 Azure 受保護虛擬中樞啟用的連線路徑：

### <a name="path-6"></a>路徑 6

路徑6顯示相同區域內 Vnet 之間的安全流量。

流量的路由如下所示：

- 連線到相同安全虛擬中樞的虛擬網路，現在會透過 Azure 防火牆路由傳送流量。

- Azure 防火牆可以將原則套用到這些流程。

![流程 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>路徑 7

路徑7顯示從 Azure VNet 到網際網路或協力廠商安全性服務的流量流程。

流量的路由如下所示：

- 連線到安全虛擬中樞的虛擬網路可以使用安全中樞作為網際網路存取的中心點，將流量傳送到網際網路上的公用目的地。

- 此流量可以使用 Azure 防火牆 FQDN 規則在本機進行篩選，或傳送給協力廠商安全性服務進行檢查。

![流程 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>路徑 8

路徑8顯示來自分支對網際網路或協力廠商安全性服務的流量流程。

流量的路由如下所示：

- 連接到安全虛擬中樞的分支可以使用安全中樞作為網際網路存取的中心點，將流量傳送到網際網路上的公用目的地。

- 此流量可以使用 Azure 防火牆 FQDN 規則在本機進行篩選，或傳送給協力廠商安全性服務進行檢查。

![流程 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 虛擬 WAN](virtual-wan-about.md)
