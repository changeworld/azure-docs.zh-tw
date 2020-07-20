---
title: SAP HANA on Azure (大型執行個體) 的網路架構 | Microsoft Docs
description: SAP HANA on Azure (大型執行個體) 部署方式的網路架構。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b3bc87b183803c0854542d6925af7429b593d2af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81605179"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (大型執行個體) 網路架構

Azure 網路服務的架構是在 HANA 大型執行個體上成功部署 SAP 應用程式的關鍵元件。 一般而言，SAP HANA on Azure (大型執行個體) 部署具有較大的 SAP 架構，其中包含數個擁有不同資料庫大小、CPU 資源耗用量及記憶體使用量的不同 SAP 解決方案。 可能並非所有的 IT 系統都位於 Azure 中。 您的 SAP 環境通常是混合使用 NetWeaver、S/4HANA 和 SAP Hana 和其他 DBMS 的 DBMS 點和 SAP 應用程式觀點。 Azure 提供不同的服務，可讓您在 Azure 中執行不同的 DBMS、NetWeaver 和 S/4HANA 系統。 Azure 也為您提供網路技術，讓 Azure 看起來像是虛擬資料中心的內部部署軟體部署

除非您的完整 IT 系統裝載于 Azure 中。 Azure 網路功能是用來將內部部署環境與您的 Azure 資產連線，讓 Azure 看起來像您的虛擬資料中心。 使用的 Azure 網路功能為： 

- Azure 虛擬網路會連接到[ExpressRoute](https://azure.microsoft.com/services/expressroute/)線路，以連接到您的內部部署網路資產。
- 將內部部署連線至 Azure 的 ExpressRoute 線路，其最小頻寬應為[1 Gbps 或更高](https://azure.microsoft.com/pricing/details/expressroute/)。 此最低頻寬可讓您有足夠的頻寬在內部部署系統與執行於 VM 的系統之間傳輸資料。 它也讓內部部署使用者有足夠的頻寬可連線至 Azure 系統。
- Azure 中的所有 SAP 系統都會在虛擬網路中設定，以便彼此通訊。
- 內部部署裝載的 Active Directory 和 DNS 會透過 ExpressRoute 從內部部署擴充到 Azure，或在 Azure 中執行完成。

針對將 HANA 大型實例整合到 Azure 資料中心網路網狀架構的特定案例，也會使用 Azure ExpressRoute 技術


> [!NOTE] 
> 只有一個 Azure 訂用帳戶只能連結到特定 Azure 區域中 HANA 大型實例戳記內的一個租使用者。 相反地，單一 HANA 大型實例戳記租使用者只能連結到一個 Azure 訂用帳戶。 Azure 中其他可計費的物件也有此需求。

如果 SAP Hana on Azure （大型實例）部署在多個不同的 Azure 區域中，則會在 HANA 大型實例戳記中部署個別的租使用者。 只要這些執行個體都是相同 SAP 架構的一部分，您便可以在同一個 Azure 訂用帳戶下執行兩者。 

> [!IMPORTANT] 
> Azure （大型實例）上的 SAP Hana 僅支援 Azure Resource Manager 部署方法。

 

## <a name="additional-virtual-network-information"></a>其他虛擬網路資訊

若要將虛擬網路連線到 ExpressRoute，必須建立 Azure ExpressRoute 閘道。 如需詳細資訊，請參閱[關於 expressroute 的 expressroute 閘道](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

Azure ExpressRoute 閘道可搭配 ExpressRoute 用於 Azure 外部的基礎結構或 Azure 大型實例戳記。 只要這些連線來自不同的 Microsoft enterprise edge 路由器，您就可以將 Azure ExpressRoute 閘道連接到最多四個不同的 ExpressRoute 線路。 如需詳細資料，請參閱 [Azure 上的 SAP HANA (大型執行個體) 基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> 透過 ExpressRoute 閘道可達到的最大輸送量為 10 Gbps，使用 ExpressRoute 連線。 在位於虛擬網路中的 VM 與內部部署系統之間複製檔案 (以單一複製串流的形式)，並不會達到不同閘道 SKU 的最大輸送量。 若要利用 ExpressRoute 閘道的完整頻寬，請使用多個串流。 或者，您必須以單一檔案的平行資料流複製不同的檔案。


## <a name="networking-architecture-for-hana-large-instance"></a>適用於 HANA 大型執行個體的網路架構
適用於 HANA 大型執行個體的網路架構，可區分為四個不同的部分：

- 內部部署網路和 Azure 的 ExpressRoute 連線。 這個部分是客戶網域，會透過 ExpressRoute 連線至 Azure。 此 Expressroute 線路會由您身為客戶完全付費。 頻寬應該夠大，足以處理您的內部部署資產與您所連線的 Azure 區域之間的網路流量。 請參閱下圖右下方的區域。
- 如先前所討論的 Azure 網路服務，使用虛擬網路，再次需要新增 ExpressRoute 閘道。 這個部分是您需要針對應用程式需求、安全性及合規性需求尋找適當設計的區域。 是否使用 HANA 大型執行個體，是您在虛擬網路的數目和要選擇的 Azure 閘道 SKU 方面所應考量的另一個要點。 請查看圖中的右上方。
- HANA 大型執行個體透過 ExpressRoute 技術連線至 Azure。 這部分已部署且會由 Microsoft 來處理。 您唯一需要做的，是在將資產部署至 HANA 大型執行個體後提供一些 IP 位址範圍，再將 ExpressRoute 線路連線至虛擬網路。 如需詳細資料，請參閱 [Azure 上的 SAP HANA (大型執行個體) 基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 身為客戶的 Azure 資料中心網路網狀架構與 HANA 大型實例單位之間的連線，不需要額外付費。
- HANA 大型實例戳記內的網路功能，大部分都是透明的。

![連線至 SAP HANA on Azure (大型執行個體) 與內部部署環境的虛擬網路](./media/hana-overview-architecture/image1-architecture.png)

由於您使用 HANA 大型執行個體，因此內部部署資產必須透過 ExpressRoute 連線至 Azure 的需求並不會變更。 以一或多個虛擬網路執行 VM 的需求也不會變更 (這些 VM 會裝載應用程式層，連線至裝載於 HANA 大型執行個體單位中的 HANA 執行個體)。 

與 Azure 中的 SAP 部署的差異為：

- 客戶租用戶的 HANA 大型執行個體單位會透過另一個 ExpressRoute 線路連線至您的虛擬網路。 若要將負載條件分開，內部部署至 Azure 虛擬網路的 ExpressRoute 線路以及 Azure 虛擬網路與 HANA 大型實例之間的線路，不會共用相同的路由器。
- SAP 應用程式層和 HANA 大型執行個體之間的工作負載設定檔是不同性質的許多小型要求與高載，例如從 SAP HANA 到應用程式層的資料傳輸 (結果集)。
- 相較於在內部部署與 Azure 之間交換資料的典型案例，SAP 應用程式架構對於網路延遲更敏感。
- Azure ExpressRoute 閘道至少有兩個 ExpressRoute 連接。 一個從內部部署連線的線路，和一個從 HANA 大型實例連線的電路。 這只會留下來自不同 Msee 的另兩個額外線路的空間，以連線到 ExpressRoute 閘道上的。 這項限制與 ExpressRoute 快速路徑的使用無關。 所有連線的線路會共用 ExpressRoute 閘道傳入資料的最大頻寬。

使用 HANA 大型實例戳記的修訂3，在 Vm 與 HANA 大型實例單位之間經歷的網路延遲，可以高於一般的 VM 對 VM 網路來回延遲。 相依於 Azure 區域，測量到的值可超過 0.7 毫秒的來回延遲，這在 [SAP 附註 #1100926 - 常見問題集：網路效能](https://launchpad.support.sap.com/#/notes/1100926/E)中已歸類為低於平均值。 根據測量 Azure VM 與 HANA 大型執行個體單位之間的網路來回延遲時所使用的 Azure 區域和工具，測量的延遲最高可達 2 毫秒左右。 不過，客戶可順利在 SAP HANA 大型執行個體上部署以 SAP HANA 為基礎的生產環境 SAP 應用程式。 請務必在 Azure HANA 大型執行個體中徹底測試您的商務程序。 新的功能（稱為 ExpressRoute 快速路徑）能夠減少 HANA 大型實例和 Azure 中的應用層 Vm 之間的網路延遲（請參閱下文）。 

有了 HANA 大型實例戳記的修訂4，部署到「HANA 大型實例」戳記的 Azure Vm 之間的網路延遲，有經驗可以符合平均值或高於 SAP Note 中所記載的平均分類[#1100926-FAQ：](https://launchpad.support.sap.com/#/notes/1100926/E)如果已設定 Azure ExpressRoute 快速路徑，則為網路效能（請參閱下文）。 若要以接近修訂版4的 HANA 大型實例單位來部署 Azure Vm，您需要利用[Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)。 「鄰近性放置群組」如何用來在與修訂版4託管 HANA 大型實例單位相同的 Azure 資料中心內尋找 SAP 應用層的方式，會在[Azure 鄰近放置群組中說明，以取得 sap 應用程式的最佳網路延遲](sap-proximity-placement-scenarios.md)。

若要在 Vm 與 HANA 大型實例之間提供具決定性的網路延遲，您必須選擇 ExpressRoute 閘道 SKU。 不同於內部部署與 VM 之間的流量模式，VM 和 HANA 大型執行個體之間的流量模式可以開發很小但高載的要求和資料磁碟區來進行傳輸。 為了妥善處理這類高載，強烈建議您使用 UltraPerformance 閘道 SKU。 針對類型 II 類別的 HANA 大型實例 Sku，您必須使用 UltraPerformance 閘道 SKU 做為 ExpressRoute 閘道。

> [!IMPORTANT] 
> 考慮到 SAP 應用程式與資料庫層之間的整體網路流量，因此僅支援使用虛擬網路的 HighPerformance 或 UltraPerformance 閘道 SKU 來連線至 SAP HANA on Azure (大型執行個體)。 對於 HANA 大型實例類型 II Sku，只支援 UltraPerformance 閘道 SKU 作為 ExpressRoute 閘道。 使用 ExpressRoute 快速路徑時，適用例外狀況（請參閱下文）

### <a name="expressroute-fast-path"></a>ExpressRoute 快速路徑
為了降低延遲，在5月2019推出併發行 ExpressRoute 快速路徑，以供 HANA 大型實例的特定連線到裝載 SAP 應用程式 Vm 的 Azure 虛擬網路。 到目前為止，解決方案的主要差異在於，Vm 與 HANA 大型實例之間的資料流程不會再透過 ExpressRoute 閘道路由傳送。 相反地，指派在 Azure 虛擬網路子網中的 Vm 會直接與專用的 enterprise edge 路由器通訊。 

> [!IMPORTANT] 
> ExpressRoute 快速路徑功能需要執行 SAP 應用程式 Vm 的子網位於已連線到 HANA 大型實例的相同 Azure 虛擬網路中。 位於 Azure 虛擬網路中的 Vm 與直接連線到 HANA 大型實例單位的 Azure 虛擬網路對等互連，並不會受益于 ExpressRoute 快速路徑。 作為典型的中樞和輪輻虛擬網路設計，其中 ExpressRoute 線路會針對中樞虛擬網路進行連線，而包含 SAP 應用層（輪輻）的虛擬網路則會對等互連，因此 ExpressRoute 快速路徑的優化將無法正常執行。 在除了中，ExpressRoute 快速路徑目前不支援使用者定義的路由規則（UDR）。 如需詳細資訊，請參閱[ExpressRoute 虛擬網路閘道和 FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)。 


如需有關如何設定 ExpressRoute 快速路徑的詳細資訊，請參閱[將虛擬網路連線到 HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)檔。    

> [!NOTE]
> 需要 UltraPerformance ExpressRoute 閘道，才能使用 ExpressRoute 快速路徑


## <a name="single-sap-system"></a>單一 SAP 系統

先前顯示的內部部署基礎結構會透過 ExpressRoute 連線至 Azure。 ExpressRoute 線路會連接到 Microsoft enterprise edge 路由器（MSEE）。 如需詳細資訊，請參閱 [ExpressRoute 技術概觀](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 建立路由之後，它會連接到 Azure 骨幹。

> [!NOTE] 
> 若要在 Azure 中執行 SAP 架構，請連線至距離 SAP 架構中的 Azure 區域最近的 Enterprise Edge 路由器。 HANA 大型實例戳記會透過專用的企業邊緣路由器裝置連線，以將 Azure IaaS 與 HANA 大型實例戳記中 Vm 之間的網路延遲降至最低。

裝載 SAP 應用程式實例之 Vm 的 ExpressRoute 閘道會連線到一個連接到內部部署的 ExpressRoute 線路。 相同的虛擬網路會連線至專門用來連接到大型執行個體戳記的個別 Enterprise Edge 路由器。 使用 ExpressRoute 快速路徑時，從 HANA 大型實例到 SAP 應用層 Vm 的資料流程不會再透過 ExpressRoute 閘道路由傳送，而是會減少網路來回延遲。

此系統是單一 SAP 系統的簡明範例。 SAP 應用程式層裝載於 Azure 中。 SAP HANA 資料庫執行於 SAP HANA on Azure (大型執行個體) 上。 假設有 2 Gbps 或 10 Gbps 輸送量的 ExpressRoute 閘道頻寬並不代表瓶頸。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>多個 SAP 系統或大型 SAP 系統

如果部署多個 SAP 系統或大型 SAP 系統來連線到 Azure 上的 SAP Hana （大型實例），ExpressRoute 閘道的輸送量可能會變成瓶頸。 或者，您想要在不同的 Azure 虛擬網路中隔離生產和非生產系統。 在這種情況下，請將應用程式層分成多個虛擬網路。 針對如下的情況，您也可以建立會連線至 HANA 大型執行個體的特殊虛擬網路：

- 直接從「HANA 大型執行個體」中的 HANA 執行個體備份到 Azure 中裝載 NFS 共用的 VM。
- 將大型備份或其他檔案從「HANA 大型執行個體」單位複製到在 Azure 中管理的磁碟空間。

使用不同的虛擬網路來裝載管理儲存體的 Vm，以便在 HANA 大型實例和 Azure 之間進行大量資料傳輸。 這種安排可避免將大型檔案或資料從 HANA 大型實例傳輸到 Azure 的結果，而 ExpressRoute 閘道上會提供執行 SAP 應用層的 Vm。 

讓網路架構更具彈性：

- 針對單一的較大 SAP 應用程式層，運用多個虛擬網路。
- 相較於將所部署的 SAP 系統結合在相同虛擬網路下的個別子網路中，為這些 SAP 系統中的每一個系統部署一個個別的虛擬網路。

  適用於 SAP HANA on Azure (大型執行個體) 的更有彈性網路架構：

![跨多個虛擬網路部署 SAP 應用程式層](./media/hana-overview-architecture/image4-networking-architecture.png)

視規則和限制而定，您想要在裝載不同 SAP 系統之 Vm 的不同虛擬網路之間套用，您應該將這些虛擬網路對等互連。 如需關於虛擬網路對等互連的詳細資訊，請參閱[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="routing-in-azure"></a>Azure 中的路由

根據預設部署，三個網路路由考慮對於 Azure （大型實例）上的 SAP Hana 很重要：

* 您只能透過 Azure Vm 和專用 ExpressRoute 連線（而不是直接從內部部署）存取 Azure 上的 SAP Hana （大型實例）。 從內部部署環境對 HANA 大型執行個體單位的直接存取 (如 Microsoft 所提供)，是無法立即執行的。 可轉移的路由會因為目前用於 SAP HANA 大型執行個體的 Azure 網路架構而受限。 有些系統管理用戶端及所有需要直接存取權的應用程式 (例如在內部部署環境中執行的 SAP Solution Manager) 會無法連線至 SAP HANA 資料庫。 針對例外狀況，請檢查「直接路由至 HANA 大型實例」一節。

* 如果您將 HANA 大型實例單位部署在兩個不同的 Azure 區域中，以進行嚴重損壞修復，過去會套用相同的暫時性路由限制。 換句話說，某個區域中 HANA 大型實例單位的 IP 位址（例如，美國西部）並未路由傳送至部署在另一個區域中的 HANA 大型實例單位（例如美國東部）。 這項限制獨立于跨區域使用 Azure 網路對等互連，或跨連接將 HANA 大型實例單位連線到虛擬網路的 ExpressRoute 線路。 如需以圖形檢視，請參閱「在多個區域中使用 HANA 大型執行個體單位」一節中的圖表。 這項限制（隨附于已部署的架構）禁止立即使用 HANA 系統複寫作為嚴重損壞修復功能。 如需最近的變更，請查閱「在多個區域中使用 HANA 大型實例單位」一節。 

* Azure 上的 SAP Hana （大型實例）單位具有指派給您要求 HANA 大型實例部署時所提交之伺服器 IP 集區位址範圍的 IP 位址。 如需詳細資料，請參閱 [Azure 上的 SAP HANA (大型執行個體) 基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 此 IP 位址可透過將 Azure 虛擬網路連接到 HANA 大型實例的 Azure 訂用帳戶和線路來存取。 從該伺服器 IP 集區位址範圍指派的 IP 位址會直接指派給硬體單位。 它*不會*再經過 NAT 處理，因為這是此解決方案的第一次部署才會有的情況。 

### <a name="direct-routing-to-hana-large-instances"></a>直接路由傳送至 HANA 大型實例

根據預設，在這些情況下，可轉移路由無法運作：

* 在 HANA 大型實例單位與內部部署之間。

* 在兩個不同區域中部署的 HANA 大型實例路由之間。

有三種方式可在這些情況下啟用轉移路由：

- 路由資料的反向 Proxy，往返方向皆有可能。 例如，F5 BIG NGINX 與流量管理員部署于 Azure 虛擬網路中，並將其連線到 HANA 大型實例和內部部署，做為虛擬防火牆/流量路由解決方案。
- 在 Linux VM 中使用 [IPTables 規則](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)，以啟用在內部部署位置和 HANA 大型執行個體單位之間的路由，或不同區域中 HANA 大型執行個體單位之間的路由。 執行 IPTables 的 VM 必須部署在連接到 HANA 大型實例和內部部署的 Azure 虛擬網路中。 VM 必須據以調整大小，因此 VM 的網路輸送量足以滿足預期的網路流量。 如需 VM 網路頻寬的詳細資訊，請參閱[Azure 中的 Linux 虛擬機器大小一](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)文。
- [Azure 防火牆](https://azure.microsoft.com/services/azure-firewall/)是另一個解決方案，可啟用內部部署與 HANA 大型實例單位之間的直接流量。 

這些解決方案的所有流量都會透過 Azure 虛擬網路路由傳送，因此流量可能會額外受到所使用的軟體應用裝置或 Azure 網路安全性群組的限制，因此，您可以封鎖或明確允許來自內部部署的特定 IP 位址或 IP 位址範圍，以存取 HANA 大型實例。 

> [!NOTE]  
> 請注意，Microsoft 並不提供與協力廠商網路設備或 IPTables 相關自定義解決方案的實作和支援。 必須由所使用元件的廠商或整合者提供支援。 

#### <a name="express-route-global-reach"></a>Express Route 全球範圍
Microsoft 引進了一種新功能，稱為[ExpressRoute Global 的延伸](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)。 在兩種情況下，全域延伸可用於 HANA 大型實例：

- 允許從內部部署直接存取部署在不同區域的 HANA 大型實例單位
- 啟用部署在不同區域中的 HANA 大型實例單位之間的直接通訊


##### <a name="direct-access-from-on-premises"></a>從內部部署直接存取
在提供全球觸達的 Azure 區域中，您可以要求將內部部署網路連線至連線到您的 HANA 大型實例單位之 Azure 虛擬網路的 ExpressRoute 線路的全球觸達功能。 ExpressRoute 線路的內部部署端有一些成本含意。 如需價格，請查看[全球接觸附加](https://azure.microsoft.com/pricing/details/expressroute/)元件的價格。 與連接 HANA 大型實例單位與 Azure 的線路無關，您不需要支付任何額外的費用。 

> [!IMPORTANT]  
> 當您使用全球範圍來啟用 HANA 大型實例單位與內部部署資產之間的直接存取時，網路資料和控制流程**不會透過 Azure 虛擬網路路由傳送**，而是直接在 Microsoft 企業 exchange 路由器之間進行。 因此，任何 NSG 或 ASG 規則，或您在 Azure 虛擬網路中部署的任何類型的防火牆、NVA 或 proxy 都不會觸及。 **如果您使用 ExpressRoute 全球範圍來啟用從內部部署到 HANA 大型實例單位限制的直接存取，則需要在內部部署端的防火牆中定義存取 HANA 大型實例單位的許可權** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>連接不同 Azure 區域中的 HANA 大型實例
同樣地，因為 ExpressRoute Global 觸及可以用來將內部部署連線到 HANA 大型實例單位，所以它可以用來連接在兩個不同區域中為您部署的兩個 HANA 大型實例租使用者。 隔離是您的 HANA 大型實例租使用者在這兩個區域中用來連線到 Azure 的 ExpressRoute 線路。 將兩個部署在兩個不同區域中的 HANA 大型實例租使用者連線不會產生額外費用。 

> [!IMPORTANT]  
> 不同 HANA 大型實例租使用者之間網路流量的資料流程和控制流程，將不會透過 azure 網路路由傳送。 因此，您無法使用 Azure 功能或 Nva 來強制執行兩個 HANA 大型實例租使用者之間的通訊限制。 

如需如何啟用 ExpressRoute 全域觸達的詳細資訊，請參閱[將虛擬網路連線到 HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)檔。


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型執行個體的網際網路連線
HANA 大型執行個體*無法*直接連線至網際網路。 此限制可能會使您的一些能力受到限制，例如，直接向 OS 廠商註冊 OS 映像的能力。 您可能需要使用本機 SUSE Linux Enterprise Server Subscription Management Tool 伺服器或 Red Hat Enterprise Linux Subscription Manager。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 與 HANA 大型執行個體之間的資料加密
HANA 大型執行個體與 VM 之間的資料傳輸並未加密。 不過，您可以純粹針對 HANA DBMS 端與 JDBC/ODBC 型應用程式之間的交換，啟用流量加密。 如需詳細資訊，請參閱 [SAP 提供的這份文件](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多個區域中使用 HANA 大型執行個體單位

若要實現嚴重損壞修復集，您必須在多個 Azure 區域中有 SHANA 的大型實例單位。 即使使用 Azure [全域 Vnet 對等互連]，在兩個不同區域中的 HANA 大型實例租使用者之間，預設無法使用可轉移路由。 不過，全域觸及會開啟您在兩個不同區域中布建的 HANA 大型實例單位之間的通訊路徑。 ExpressRoute Global 觸及的這種使用案例可讓：

 - HANA 系統複寫，不需要任何額外的 proxy 或防火牆
 - 在兩個不同區域中的 HANA 大型實例單位之間複本備份，以執行系統複製或系統重新整理


![連線至不同 Azure 區域中的 Azure 大型執行個體戳記的虛擬網路](./media/hana-overview-architecture/image8-multiple-regions.png)

此圖顯示兩個區域中的不同虛擬網路如何連線到兩個不同的 ExpressRoute 線路，以用來連接到兩個 Azure 區域（灰階）中的 SAP Hana Azure （大型實例）。 這兩個交叉連線的原因是要防止任一端的 Msee 中斷。 兩個 Azure 區域中兩個虛擬網路之間的通訊流程，應透過兩個不同區域（藍色虛線）中兩個虛擬網路的[全域對等互連](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/)來處理。 紅色的 red 行描述 ExpressRoute Global 連線，這可讓您在兩個不同區域中租使用者的 HANA 大型實例單位彼此通訊。 

> [!IMPORTANT] 
> 如果您使用多個 ExpressRoute 線路，就應該在前面加上「AS 路徑」和使用「本機喜好 BGP」設定來確保流量路由正確。

**後續步驟**
- 請參閱 [SAP HANA (大型執行個體) 儲存體架構](hana-storage-architecture.md)
