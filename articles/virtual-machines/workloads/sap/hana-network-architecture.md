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
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605179"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (大型執行個體) 網路架構

Azure 網路服務的架構是在 HANA 大型執行個體上成功部署 SAP 應用程式的關鍵元件。 一般而言，SAP HANA on Azure (大型執行個體) 部署具有較大的 SAP 架構，其中包含數個擁有不同資料庫大小、CPU 資源耗用量及記憶體使用量的不同 SAP 解決方案。 很可能並非所有 IT 系統都位於 Azure 中。 使用 NetWeaver、S/4HANA 和 SAP HANA 和其他 DBMS 的混合,您的 SAP 環境通常也是從 DBMS 點和 SAP 應用程式角度混合的。 Azure 提供了不同的服務,允許您在 Azure 中運行不同的 DBMS、NetWeaver 和 S/4HANA 系統。 Azure 還提供網路技術,使 Azure 看起來像本地軟體部署的虛擬資料中心

除非完整的 IT 系統託管在 Azure 中。 Azure 網路功能用於將本地世界與 Azure 資產連接起來,以使 Azure 看起來像您的虛擬資料中心。 使用 Azure 網路功能是: 

- Azure 虛擬網路連接到連接到本地網路資產的[ExpressRoute](https://azure.microsoft.com/services/expressroute/)電路。
- 在本地連接到 Azure 的 ExpressRoute 電路應具有 1 [Gbps 或更高的](https://azure.microsoft.com/pricing/details/expressroute/)最小頻寬。 此最低頻寬可讓您有足夠的頻寬在內部部署系統與執行於 VM 的系統之間傳輸資料。 它也讓內部部署使用者有足夠的頻寬可連線至 Azure 系統。
- Azure 中的所有 SAP 系統都設置在虛擬網路中,以便相互通訊。
- 本地託管的活動目錄和 DNS 通過本地的 ExpressRoute 擴展到 Azure,或在 Azure 中運行完成。

對於將 HANA 大型實體整合到 Azure 資料中心網路結構中的特定情況,也使用 Azure ExpressRoute 技術


> [!NOTE] 
> 只有一個 Azure 訂閱可以連結到特定 Azure 區域中 HANA 大型實例戳中的一個租戶。 相反,單個 HANA 大型實例戳記租戶只能連結到一個 Azure 訂閱。 Azure 中其他可計費的物件也有此需求。

如果 Azure 上的 SAP HANA(大型實例)部署在多個不同的 Azure 區域中,則在 HANA 大型實例戳中部署單獨的租戶。 只要這些執行個體都是相同 SAP 架構的一部分，您便可以在同一個 Azure 訂用帳戶下執行兩者。 

> [!IMPORTANT] 
> Azure 上的 SAP HANA(大型實例)僅支援 Azure 資源管理員部署方法。

 

## <a name="additional-virtual-network-information"></a>其他虛擬網路資訊

要將虛擬網路連接到 ExpressRoute,必須創建 Azure ExpressRoute 閘道。 有關詳細資訊,請參閱[有關快速路由的快線閘道](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

Azure ExpressRoute 閘道與 Azure 外部基礎結構的 ExpressRoute 閘道或 Azure 大型實例戳號一起使用。 只要這些連接來自不同的 Microsoft 企業邊緣路由器,就可以將 Azure ExpressRoute 閘道連接到最多四個不同的 ExpressRoute 電路。 如需詳細資料，請參閱 [Azure 上的 SAP HANA (大型執行個體) 基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

> [!NOTE] 
> 使用 ExpressRoute 閘道可以達到的最大輸送量是 10 Gbps,透過使用 ExpressRoute 連接。 在位於虛擬網路中的 VM 與內部部署系統之間複製檔案 (以單一複製串流的形式)，並不會達到不同閘道 SKU 的最大輸送量。 要利用 ExpressRoute 閘道的完整頻寬,請使用多個流。 或者，您必須以單一檔案的平行資料流複製不同的檔案。


## <a name="networking-architecture-for-hana-large-instance"></a>適用於 HANA 大型執行個體的網路架構
適用於 HANA 大型執行個體的網路架構，可區分為四個不同的部分：

- 內部部署網路和 Azure 的 ExpressRoute 連線。 這個部分是客戶網域，會透過 ExpressRoute 連線至 Azure。 此快速線路由您作為客戶全額支付。 帶寬應足夠大,以處理本地資產和要連接的 Azure 區域之間的網路流量。 請參閱下圖右下方的區域。
- Azure 網路服務(如前面所述)與虛擬網路一起,再次需要添加 ExpressRoute 閘道。 這個部分是您需要針對應用程式需求、安全性及合規性需求尋找適當設計的區域。 是否使用 HANA 大型執行個體，是您在虛擬網路的數目和要選擇的 Azure 閘道 SKU 方面所應考量的另一個要點。 請查看圖中的右上方。
- HANA 大型執行個體透過 ExpressRoute 技術連線至 Azure。 這部分已部署且會由 Microsoft 來處理。 您唯一需要做的，是在將資產部署至 HANA 大型執行個體後提供一些 IP 位址範圍，再將 ExpressRoute 線路連線至虛擬網路。 如需詳細資料，請參閱 [Azure 上的 SAP HANA (大型執行個體) 基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 作為客戶,Azure 數據中心網路結構與 HANA 大型實例單元之間的連接不收取額外費用。
- HANA 大型實例戳中的網路,這主要對於您而言是透明的。

![連線至 SAP HANA on Azure (大型執行個體) 與內部部署環境的虛擬網路](./media/hana-overview-architecture/image1-architecture.png)

由於您使用 HANA 大型執行個體，因此內部部署資產必須透過 ExpressRoute 連線至 Azure 的需求並不會變更。 以一或多個虛擬網路執行 VM 的需求也不會變更 (這些 VM 會裝載應用程式層，連線至裝載於 HANA 大型執行個體單位中的 HANA 執行個體)。 

與 Azure 中的 SAP 部署的差異為：

- 客戶租用戶的 HANA 大型執行個體單位會透過另一個 ExpressRoute 線路連線至您的虛擬網路。 為了分離負載條件,本地到 Azure 虛擬網路 ExpressRoute 電路以及 Azure 虛擬網路和 HANA 大型實例之間的電路不共用相同的路由器。
- SAP 應用程式層和 HANA 大型執行個體之間的工作負載設定檔是不同性質的許多小型要求與高載，例如從 SAP HANA 到應用程式層的資料傳輸 (結果集)。
- 相較於在內部部署與 Azure 之間交換資料的典型案例，SAP 應用程式架構對於網路延遲更敏感。
- Azure 快速路由閘道至少有兩個快速路由連接。 從本地連接的電路和從 HANA 大型實例連接的電路。 這僅為來自不同 MsEE 的其他兩個電路留出空間,以便連接到 ExpressRoute 閘道。 此限制與快速路由快速路徑的使用無關。 所有連接的電路共用 ExpressRoute 閘道傳入數據的最大頻寬。

使用 HANA 大型實例戳的修訂版 3,VM 和 HANA 大型實例單元之間的網路延遲可能高於典型的 VM 到 VM 網路往返延遲。 相依於 Azure 區域，測量到的值可超過 0.7 毫秒的來回延遲，這在 [SAP 附註 #1100926 - 常見問題集：網路效能](https://launchpad.support.sap.com/#/notes/1100926/E)中已歸類為低於平均值。 根據測量 Azure VM 與 HANA 大型執行個體單位之間的網路來回延遲時所使用的 Azure 區域和工具，測量的延遲最高可達 2 毫秒左右。 不過，客戶可順利在 SAP HANA 大型執行個體上部署以 SAP HANA 為基礎的生產環境 SAP 應用程式。 請務必在 Azure HANA 大型執行個體中徹底測試您的商務程序。 一個名為 ExpressRoute 快速路徑的新功能能夠顯著減少 Azure 中的 HANA 大型實例和應用程式層 VM 之間的網路延遲(見下文)。 

使用 HANA 大型實例戳的修訂版 4,部署在 HANA 大型實例戳附近的 Azure VM 之間的網路延遲會滿足[SAP Note #1100926 - 常見問題解答中](https://launchpad.support.sap.com/#/notes/1100926/E)記錄的平均或優於平均分類: 如果配置 Azure ExpressRoute 快速路徑,網路性能(見下文)。 為了在接近第 4 版本的 HANA 大型實體單元的地方部署 Azure VM,需要利用[Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)。 Azure 鄰近放置群組中介紹了如何使用鄰近放置群組搜尋與修訂版 4 託管 HANA 大型實體單元位於同一 Azure 資料中心中的 SAP 應用程式層的方式,[以便使用 SAP 應用程式實現最佳網路延遲](sap-proximity-placement-scenarios.md)。

為了在 VM 和 HANA 大型實例之間提供確定性網路延遲,選擇 ExpressRoute 閘道 SKU 至關重要。 不同於內部部署與 VM 之間的流量模式，VM 和 HANA 大型執行個體之間的流量模式可以開發很小但高載的要求和資料磁碟區來進行傳輸。 為了妥善處理這類高載，強烈建議您使用 UltraPerformance 閘道 SKU。 對於 HANA 大型實例 SKU 的 II 類,必須使用超高性能閘道 SKU 作為 ExpressRoute 閘道。

> [!IMPORTANT] 
> 考慮到 SAP 應用程式與資料庫層之間的整體網路流量，因此僅支援使用虛擬網路的 HighPerformance 或 UltraPerformance 閘道 SKU 來連線至 SAP HANA on Azure (大型執行個體)。 對於 HANA 大型實例類型 II SKU,僅支援超高性能閘道 SKU 作為 ExpressRoute 閘道。 使用快速路由快速路徑時適用例外情況(見下文)

### <a name="expressroute-fast-path"></a>快速路由快速路徑
為了降低延遲,ExpressRoute 快速路徑於 2019 年 5 月推出併發布了,用於將 HANA 大型實例與託管 SAP 應用程式 VM 的 Azure 虛擬網路的特定連接。 到目前為止,解決方案的主要區別是,VM和 HANA 大型實例之間的數據流不再通過 ExpressRoute 閘道路由。 相反,在 Azure 虛擬網路的子網中分配的 VM 直接與專用企業邊緣路由器通信。 

> [!IMPORTANT] 
> ExpressRoute 快速路徑功能要求運行 SAP 應用程式 VM 的子網位於連接到 HANA 大型實例的同一 Azure 虛擬網路中。 位於 Azure 虛擬網路中的 VM 與直接連接到 HANA 大型實例單元的 Azure 虛擬網路對等互連,因此不會從 ExpressRoute 快速路徑中受益。 因此,典型的集線器和分支虛擬網路設計,其中 ExpressRoute 電路與集線器虛擬網路連接,包含 SAP 應用程式層(分支)的虛擬網路正在對等,ExpressRoute 快速路徑的優化將不起作用。 在加法中,快速路由快速路徑今天不支援使用者定義的路由規則 (UDR)。 有關詳細資訊,請參閱[ExpressRoute 虛擬網路閘道和 FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)。 


有關如何設定 ExpressRoute 快速路徑的詳細資訊,請閱讀文件[將虛擬網路連線到 HANA 大型實體 。](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)    

> [!NOTE]
> 需要超高性能快速路由閘道才能使快速路由快速路徑正常工作


## <a name="single-sap-system"></a>單一 SAP 系統

先前顯示的內部部署基礎結構會透過 ExpressRoute 連線至 Azure。 ExpressRoute 電路連接到Microsoft企業邊緣路由器(MSEE)。 如需詳細資訊，請參閱 [ExpressRoute 技術概觀](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 建立路由後,它將連接到 Azure 主幹。

> [!NOTE] 
> 若要在 Azure 中執行 SAP 架構，請連線至距離 SAP 架構中的 Azure 區域最近的 Enterprise Edge 路由器。 HANA 大型實例戳透過專用企業邊緣路由器裝置連接,以最大程度地減少 Azure IaaS 和 HANA 大型實例戳中的 VM 之間的網路延遲。

承載 SAP 應用程式實例的 VM 的 ExpressRoute 閘道連接到連接到本地的一個 ExpressRoute 電路。 相同的虛擬網路會連線至專門用來連接到大型執行個體戳記的個別 Enterprise Edge 路由器。 使用 ExpressRoute 快速路徑,從 HANA 大型實例到 SAP 應用程式層 VM 的數據流不再透過 ExpressRoute 閘道路由,從而減少網路往返延遲。

此系統是單一 SAP 系統的簡明範例。 SAP 應用程式層裝載於 Azure 中。 SAP HANA 資料庫執行於 SAP HANA on Azure (大型執行個體) 上。 假設 ExpressRoute 閘道頻寬為 2 Gbps 或 10 Gbps 輸送量並不代表瓶頸。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>多個 SAP 系統或大型 SAP 系統

如果部署了多個 SAP 系統或大型 SAP 系統以在 Azure(大型實例)上連接到 SAP HANA,則 ExpressRoute 閘道的輸送量可能成為瓶頸。 或者,您希望將生產和非生產系統隔離到不同的 Azure 虛擬網路中。 在這種情況下，請將應用程式層分成多個虛擬網路。 針對如下的情況，您也可以建立會連線至 HANA 大型執行個體的特殊虛擬網路：

- 直接從「HANA 大型執行個體」中的 HANA 執行個體備份到 Azure 中裝載 NFS 共用的 VM。
- 將大型備份或其他檔案從「HANA 大型執行個體」單位複製到在 Azure 中管理的磁碟空間。

使用單獨的虛擬網路託管管理儲存的 VM,以便在 HANA 大型實例和 Azure 之間大規模傳輸數據。 這種安排避免了大型檔或數據傳輸從 HANA 大型實例傳輸到 Azure 的 ExpressRoute 閘道的影響,該閘道為執行 SAP 應用程式層的 VM 提供服務。 

讓網路架構更具彈性：

- 針對單一的較大 SAP 應用程式層，運用多個虛擬網路。
- 相較於將所部署的 SAP 系統結合在相同虛擬網路下的個別子網路中，為這些 SAP 系統中的每一個系統部署一個個別的虛擬網路。

  適用於 SAP HANA on Azure (大型執行個體) 的更有彈性網路架構：

![跨多個虛擬網路部署 SAP 應用程式層](./media/hana-overview-architecture/image4-networking-architecture.png)

根據規則和限制,您希望在託管不同 SAP 系統 VM 的不同虛擬網路之間應用,您應該對這些虛擬網路進行對等。 如需關於虛擬網路對等互連的詳細資訊，請參閱[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。


## <a name="routing-in-azure"></a>Azure 中的路由

預設情況下,在 Azure 上的 SAP HANA(大型實例)中,三個網路路由注意事項非常重要:

* Azure 上的 SAP HANA(大型實例)只能透過 Azure VM 和專用 ExpressRoute 連接進行存取,而不能直接從本地訪問。 從內部部署環境對 HANA 大型執行個體單位的直接存取 (如 Microsoft 所提供)，是無法立即執行的。 可轉移的路由會因為目前用於 SAP HANA 大型執行個體的 Azure 網路架構而受限。 有些系統管理用戶端及所有需要直接存取權的應用程式 (例如在內部部署環境中執行的 SAP Solution Manager) 會無法連線至 SAP HANA 資料庫。 有關例外情況,請檢查"直接路由到 HANA 大型實例"部分。

* 如果在兩個不同的 Azure 區域中部署了 HANA 大型實例單元以進行災難恢復,則與過去應用的瞬態路由限制相同。 換句話說,一個區域(例如,美國西部)中的 HANA 大型實例單元的 IP 位址未路由到部署在另一個區域(例如美國東部)中的 HANA 大型實例單元。 此限制與跨區域對等的 Azure 網路的使用無關,也獨立於將 HANA 大型實例單元連接到虛擬網路的 ExpressRoute 電路交叉連接。 如需以圖形檢視，請參閱「在多個區域中使用 HANA 大型執行個體單位」一節中的圖表。 此限制與部署的體系結構一起,禁止立即使用 HANA 系統複製作為災難恢復功能。 有關最近的更改,請查看"在多個區域中使用 HANA 大型實例單元"一節。 

* Azure(大型實例)單元上的 SAP HANA 具有在請求 HANA 大型實例部署時提交的伺服器 IP 池位址範圍的分配的 IP 位址。 如需詳細資料，請參閱 [Azure 上的 SAP HANA (大型執行個體) 基礎結構和連線](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 此 IP 位址可透過 Azure 虛擬網路連接到 HANA 大型實體的 Azure 訂閱和電路存取。 從該伺服器 IP 集區位址範圍指派的 IP 位址會直接指派給硬體單位。 它*不會*再經過 NAT 處理，因為這是此解決方案的第一次部署才會有的情況。 

### <a name="direct-routing-to-hana-large-instances"></a>直接路由到 HANA 大型實體

預設情況下,過渡路由在以下情況下不起作用:

* 在 HANA 大型實例單元和本地部署之間。

* 部署在兩個不同區域中的 HANA 大型實例路由之間。

在這些情況下,有三種方法可以啟用傳遞路由:

- 路由資料的反向 Proxy，往返方向皆有可能。 例如,F5 BIG-IP,NGINX,在 Azure 虛擬網路中部署了流量管理器,該虛擬網路連接到 HANA 大型實例,並作為虛擬防火牆/流量路由解決方案連接到本地。
- 在 Linux VM 中使用 [IPTables 規則](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)，以啟用在內部部署位置和 HANA 大型執行個體單位之間的路由，或不同區域中 HANA 大型執行個體單位之間的路由。 運行 IPTables 的 VM 需要部署在連接到 HANA 大型實例和本地的 Azure 虛擬網路中。 需要相應地調整 VM 的大小,以便 VM 的網路輸送量足以滿足預期的網路流量。 有關 VM 網路頻寬的詳細資訊,請查看[Azure 中 Linux 虛擬機器的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)文章。
- [Azure 防火牆](https://azure.microsoft.com/services/azure-firewall/)是另一個解決方案,用於在本地和 HANA 大型實例單元之間啟用直接流量。 

這些解決方案的所有流量都將透過 Azure 虛擬網路路由,因此,流量可能另外受到使用的軟設備或 Azure 網路安全組的限制,因此,某些 IP 位址或 IP 位址範圍從本地可能會被阻止或顯式允許訪問 HANA 大型實例。 

> [!NOTE]  
> 請注意，Microsoft 並不提供與協力廠商網路設備或 IPTables 相關自定義解決方案的實作和支援。 必須由所使用元件的廠商或整合者提供支援。 

#### <a name="express-route-global-reach"></a>快速路線全球覆寫
微軟推出了一種名為[「快速路由全球覆蓋」的](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)新功能。 全域覆蓋可用於兩種情況下的 HANA 大型實體:

- 開啟從本地直接存取部署在不同區域的 HANA 大型實體單元
- 開啟部署在不同區域的 HANA 大型實體單元之間的直接通訊


##### <a name="direct-access-from-on-premises"></a>從本地直接存取
在提供全域覆蓋的 Azure 區域中,您可以請求為 ExpressRoute 電路啟用全域覆蓋功能,該電路將本地網路連接到連接到 HANA 大型實例單元的 Azure 虛擬網路。 對 ExpressRoute 電路的本地端存在一些成本影響。 有關價格,請查看[全域覆蓋附加元件](https://azure.microsoft.com/pricing/details/expressroute/)的價格。 與將 HANA 大型實例單元連接到 Azure 的電路相關的電路沒有額外費用。 

> [!IMPORTANT]  
> 如果使用 Global 覆蓋實現在 HANA 大型實例單元和本地資產之間實現直接存取,則網路數據和控制流**不是透過 Azure 虛擬網路路由**的,而是直接在 Microsoft 企業交換路由器之間路由的。 因此,任何 NSG 或 ASG 規則,或您在 Azure 虛擬網路中部署的任何類型的防火牆、NVA 或代理都未被觸及。 **如果使用 ExpressRoute 全域覆寫功能,即可在本地端的防火牆中定義從本地到 HANA 大型實例單元的限制和訪問 HANA 大型實體單元的權限** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>在不同 Azure 區域中連接 HANA 大型實體
同樣,由於 ExpressRoute Global 覆蓋可用於將本地連接到 HANA 大型實例單元,因此可用於連接部署在兩個不同區域的兩個 HANA 大型實例租戶。 隔離是 HANA 大型實例租戶用於在兩個區域連接到 Azure 的 ExpressRoute 電路。 連接部署在兩個不同區域的兩個 HANA 大型實例租戶不收取額外費用。 

> [!IMPORTANT]  
> 不同 HANA 大型實例租戶之間的網路流量和控制流不會通過 azure 網路路由。 因此,不能使用 Azure 功能或 NVA 來強制實施兩個 HANA 大型實例租戶之間的通信限制。 

有關如何啟用 ExpressRoute Global 覆寫範圍的詳細資訊,請閱讀文件[將虛擬網路連線到 HANA 大型實體 。](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 大型執行個體的網際網路連線
HANA 大型執行個體*無法*直接連線至網際網路。 此限制可能會使您的一些能力受到限制，例如，直接向 OS 廠商註冊 OS 映像的能力。 您可能需要使用本機 SUSE Linux Enterprise Server Subscription Management Tool 伺服器或 Red Hat Enterprise Linux Subscription Manager。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 與 HANA 大型執行個體之間的資料加密
HANA 大型執行個體與 VM 之間的資料傳輸並未加密。 不過，您可以純粹針對 HANA DBMS 端與 JDBC/ODBC 型應用程式之間的交換，啟用流量加密。 如需詳細資訊，請參閱 [SAP 提供的這份文件](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>在多個區域中使用 HANA 大型執行個體單位

要實現災難恢復設置,需要在多個 Azure 區域中具有 SHANA 大型實例單元。 即使使用 Azure [全域 Vnet 對等互連],默認情況下,傳輸路由在兩個不同區域的 HANA 大型實例租戶之間不起作用。 但是,全域覆蓋在兩個不同的區域中預配了 HANA 大型實例單元之間的通信路徑。 ExpressRoute 全球覆寫的這種使用機制支援:

 - HANA 系統複製,無需任何其他代理或防火牆
 - 在兩個不同區域的 HANA 大型實體單元之間複製備份以執行系統複本或系統刷新


![連線至不同 Azure 區域中的 Azure 大型執行個體戳記的虛擬網路](./media/hana-overview-architecture/image8-multiple-regions.png)

下圖顯示了兩個區域中不同的虛擬網路如何連接到兩個不同的 ExpressRoute 電路,這些電路用於在 Azure 區域(大型實例)中連接到 Azure 上的 SAP HANA(大型實例)。 這兩個交叉連接的理由是,防止任何一側的 MSEE 中斷。 兩個 Azure 區域中的兩個虛擬網路之間的通信流應該通過兩個不同區域(藍色虛線)中的兩個虛擬網路的[全域對等互連](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/)進行處理。 粗紅線描述 ExpressRoute 全域覆蓋連接,它允許兩個不同區域租戶的 HANA 大型實例單元相互通信。 

> [!IMPORTANT] 
> 如果您使用多個 ExpressRoute 線路，就應該在前面加上「AS 路徑」和使用「本機喜好 BGP」設定來確保流量路由正確。

**後續步驟**
- 請參閱 [SAP HANA (大型執行個體) 儲存體架構](hana-storage-architecture.md)
