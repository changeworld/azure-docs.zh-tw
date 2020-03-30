---
title: SQL Server 高可用性和災害復原 | Microsoft Docs
description: 討論適用於在「Azure 虛擬機器」中執行之 SQL Server 的各種 HADR 策略。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: f58bb534728660b85f7d16910dde7a37914fd571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249759"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Azure 虛擬機器中的 SQL Server 高可用性和災害復原

內含 SQL Server 的 Microsoft Azure 虛擬機器 (VM) 可協助降低高可用性和嚴重損壞修復 (HADR) 資料庫解決方案的成本。 Azure 虛擬機器中支援大部分的 SQL Server HADR 解決方案 (僅限 Azure 以及混合式解決方案兩種)。 在僅限 Azure 解決方案中，整個 HADR 系統會在 Azure 中執行。 在混合式組態中，解決方案的一部分會在 Azure 中執行，而其他部分會在組織中的內部部署執行。 Azure 環境的彈性可讓您將解決方案部分或完全移動至 Azure，以滿足 SQL Server 資料庫系統的預算與 HADR 需求。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>了解 HADR 解決方案的需求
您可以決定是否要讓資料庫系統擁有服務等級協定 (SLA) 需要的 HADR 功能。 即使 Azure 提供高可用性機制 (例如雲端服務的服務修復，以及虛擬機器的失敗修復偵測)，也無法保證您能夠符合需要的 SLA。 這些機制能保護 VM 的高可用性，但無法保護在 VM 中執行之 SQL Server 的高可用性。 因此，即使 VM 保持連線且運作正常，SQL Server 執行個體仍可能會失敗。 此外，Azure 提供的高可用性機制甚至會允許因為事件 (例如，從軟體或硬體失敗修復，以及作業系統升級) 導致的 VM 停機。

再者，Azure 中的異地備援儲存體 (GRS) (使用名為異地複寫功能進行實作) 對您的資料庫而言，可能不足以當作嚴重損壞修復解決方案使用。 由於異地複寫會以非同步方式傳送資料，因此最近的更新可能會在發生嚴重損壞事件時遺失。 有關地理複寫限制的詳細資訊，皆記載於 [異地複寫不支援不同磁碟上的資料和記錄檔](#geo-replication-support) 一節。

## <a name="hadr-deployment-architectures"></a>HADR 部署架構
在 Azure 中支援的 SQL Server HADR 技術包括：

* [AlwaysOn 可用性群組](https://technet.microsoft.com/library/hh510230.aspx)
* [始終在容錯移轉叢集實例上](https://technet.microsoft.com/library/ms189134.aspx)
* [記錄傳送](https://technet.microsoft.com/library/ms187103.aspx)
* [使用 Azure Blob 儲存體服務的 SQL Server 備份及還原](https://msdn.microsoft.com/library/jj919148.aspx)
* [資料庫鏡像](https://technet.microsoft.com/library/ms189852.aspx) - SQL Server 2016 中已被取代

您可以將這些技術合併在一起，以實作同時具有高可用性及嚴重損壞修復功能的 SQL Server 解決方案。 根據您使用的技術而定，混合式部署可能會需要使用 Azure 虛擬網路的 VPN 通道。 下列各節會說明部分範例部署架構。

## <a name="azure-only-high-availability-solutions"></a>僅限 Azure：高可用性解決方案

您可以在含有 Always On 可用性群組 (稱為可用性群組) 的資料庫層級，具備適用於 SQL Server 的高可用性解決方案。 您也可以在含有 Always On 容錯移轉叢集執行個體 (容錯移轉叢集執行個體) 的執行個體層級，建立高可用性解決方案。 如需額外的備援，您可以藉由在容錯移轉叢集執行個體上建立可用性群組，在這兩個層級上建立備援。 

| 技術 | 範例架構 |
| --- | --- |
| **可用性群組** |在相同區域的 Azure VM 中執行的可用性複本提供高可用性。  由於 Windows 容錯移轉叢集需要使用 Active Directory 網域，因此您需要設定網域控制站 VM。<br/><br/> 為了更高的冗余性和可用性，Azure VM 可以部署在不同的[可用性區域](../../../availability-zones/az-overview.md)，如[可用性組概述](virtual-machines-windows-portal-sql-availability-group-overview.md)中所述。 如果可用性組中的 SQL Server VM 部署在可用性區域中，則對攔截器使用[標準負載等化器](../../../load-balancer/load-balancer-standard-overview.md)，如這些文章中所述 - [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) & [Azure 快速啟動範本](virtual-machines-windows-sql-availability-group-quickstart-template.md)。<br/> ![可用性群組](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>如需詳細資訊，請參閱[在 Azure (GUI) 中設定可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)。 |
| **容錯移轉叢集執行個體** |容錯移轉叢集實例 （FCI） 需要共用存儲，可通過 4 種不同的方式創建。<br/><br/>1. 使用[Windows Server \(2016 存儲空間直接 S2D\)](virtual-machines-windows-portal-sql-create-failover-cluster.md)在 Azure VM 中運行的雙節點容錯移轉叢集，提供基於軟體的虛擬 SAN。<br/><br/> 2. 使用[高級檔共用](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)在 Azure VM 中運行的雙節點容錯移轉叢集。 高級檔共用是 SSD 支援的一致低延遲檔共用，完全支援這些共用用於容錯移轉叢集實例。<br/><br/>3. 在 Azure VM 中運行的雙節點容錯移轉叢集，存儲由協力廠商群集解決方案支援。 有關使用 SIOS 資料保持器的特定示例，請參閱[使用容錯移轉叢集和協力廠商軟體 SIOS 資料保持器 的檔共用的高可用性](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)。<br/><br/>4. 在 Azure VM 中運行的雙節點容錯移轉叢集，通過 ExpressRoute 提供遠端 iSCSI 目標共用塊存儲。 例如，NetApp 私用儲存體 (NPS) 會透過 ExpressRoute 使用 Equinix 將 iSCSI 目標公開至 Azure VM。<br/><br/>對於協力廠商共用儲存體和資料複寫解決方案，針對有關存取容錯移轉資料的任何問題您應該連絡廠商。<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>僅限 Azure：災害復原解決方案
您可以使用可用性群組或資料庫鏡像，為 Azure 中的 SQL Server 資料庫提供災害復原解決方案，或者使用儲存體 Blob 進行備份和還原。

| 技術 | 範例架構 |
| --- | --- |
| **可用性組** |為了進行嚴重損壞修復，可用性複本會在 Azure VM 的多個資料中心執行。 這種跨區域解決方案可防止網站完全中斷。 <br/> ![可用性群組](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>在區域內，所有複本都應位於相同的雲端服務與 VNet 中。 由於每個區域會有不同的 VNet，因此這些解決方案會需要 VNet 對 VNet 連線。 如需詳細資訊，請參閱[使用 Azure 入口網站設定 VNet 對 VNet 連接](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。 如需詳細指示，請參閱[在不同區域中的 Azure 虛擬機器上設定 SQL Server 可用性群組](virtual-machines-windows-portal-sql-availability-group-dr.md)。|
| **資料庫鏡像** |為了進行嚴重損壞修復，主體、鏡像和伺服器會在不同的資料中心內執行。 您必須使用伺服器憑證進行部署。 SQL Server 資料庫鏡像不支援 SQL Server 2008 和 Azure VM 上的 SQL Server 2008 R2。 <br/>![資料庫鏡像](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **備份及還原與 Azure Blob 儲存體服務** |生產資料庫直接備份到不同資料中心的 blob 儲存體以進行災害復原。<br/>![備份與還原](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的備份與還原](virtual-machines-windows-sql-backup-recovery.md)。 |
| **使用 Azure Site Recovery 複寫 SQL Server 並將其容錯移轉至 Azure** |某個 Azure 資料中心的實際執行 SQL Server 直接複寫至不同 Azure 資料中心的 Azure 儲存體進行嚴重損壞修復。<br/>![使用 Azure Site Recovery 複寫](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>如需詳細資訊，請參閱[使用 SQL Server 嚴重損壞修復和 Azure Site Recovery 保護 SQL Server](../../../site-recovery/site-recovery-sql.md)。 |


## <a name="hybrid-it-disaster-recovery-solutions"></a>混合式 IT：災害復原解決方案
您可以使用可用性群組、資料庫鏡像及記錄傳送，為混合式 IT 環境中的 SQL Server 資料庫提供災害復原解決方案，以及使用 Azure Blob 儲存體進行備份和還原。

| 技術 | 範例架構 |
| --- | --- |
| **可用性組** |為了進行跨網站的嚴重損壞修復，部分可用性複本會在 Azure VM 中執行，而其他複本會在內部部署執行。 生產網站可以是內部部署或是在 Azure 資料中心。<br/>![可用性群組](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>由於所有可用性複本都必須位於相同的容錯移轉叢集中，因此該叢集必須同時跨這兩個網路 (多重子網路的容錯移轉叢集)。 此組態需要 Azure 與內部部署網路之間的 VPN 連線。<br/><br/>若要成功對資料庫進行災害復原，您也應該在災害復原網站安裝複本網域控制站。<br/><br/>很可能要在 SSMS 中使用「新增複本精靈」以將 Azure 複本新增至現有 Always On 可用性群組。 如需詳細資訊，請參閱教學課程：將您的 Always On 可用性群組延伸至 Azure。 |
| **資料庫鏡像** |為了使用伺服器憑證進行跨網站嚴重損壞修復，一個合作夥伴會在 Azure VM 中執行，而其他合作夥伴會在內部部署中執行。 合作夥伴不需要位於相同的 Active Directory 網域，且不需要 VPN 連線。<br/>![資料庫鏡像](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>另一個資料庫鏡像案例，則是為了進行跨網站災害復原復，讓一個合作夥伴在 Azure VM 中執行，並讓其他合作夥伴在相同 Active Directory 網域的內部部署中執行。 需要 [Azure 虛擬網路與內部部署之間的 VPN 連線](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。<br/><br/>若要成功對資料庫進行災害復原，您也應該在災害復原網站安裝複本網域控制站。 SQL Server 資料庫鏡像不支援 SQL Server 2008 和 Azure VM 上的 SQL Server 2008 R2。 |
| **記錄傳送** |為了進行跨網站嚴重損壞修復，一個合作夥伴會在 Azure VM 中執行，而其他合作夥伴會在內部部署中執行。 記錄傳送依賴 Windows 檔案共用，因此需要 Azure 虛擬網路和內部部署網路之間的 VPN 連線。<br/>![記錄傳送](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>若要成功對資料庫進行災害復原，您也應該在災害復原網站安裝複本網域控制站。 |
| **備份及還原與 Azure Blob 儲存體服務** |內部部署生產資料庫直接備份到 Azure blob 儲存體以進行災害復原。<br/>![備份與還原](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的備份與還原](virtual-machines-windows-sql-backup-recovery.md)。 |
| **使用 Azure Site Recovery 複寫 SQL Server 並將其容錯移轉至 Azure** |內部部署實際執行 SQL Server 直接複寫至 Azure 儲存體進行嚴重損壞修復。<br/>![使用 Azure Site Recovery 複寫](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>如需詳細資訊，請參閱[使用 SQL Server 嚴重損壞修復和 Azure Site Recovery 保護 SQL Server](../../../site-recovery/site-recovery-sql.md)。 |


## <a name="free-dr-replica-in-azure"></a>Azure 中的免費 DR 副本

如果您有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，則可以使用 SQL Server 實現混合災害復原 （DR） 計畫，而不會為被動 DR 實例帶來額外的許可成本。

在下圖中，安裝程式使用在 Azure 虛擬機器上運行的 SQL Server，利用 12 個內核作為使用 12 個內核進行本地 SQL Server 部署的災害復原副本。 過去，您需要為本地和 Azure 虛擬機器部署許可 12 個 SQL Server 核心。 新的權益提供了在 Azure 虛擬機器上運行的被動副本權益。 現在，只要滿足 Azure 虛擬機器上被動副本的災害復原條件，只需許可本地運行的 12 個 SQL Server 內核。

![Azure 中的免費 DR 副本](media/virtual-machines-windows-sql-high-availability-dr/free-dr-replica-azure.png)

有關詳細資訊，請參閱[產品授權條款](https://www.microsoft.com/licensing/product-licensing/products)。 

要啟用此優勢，請導航到[SQL Server 虛擬機器資源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)，選擇"在設置下**配置**"，然後在**SQL 伺服器許可證**下選擇 **"災害復原**"選項。 選擇該核取方塊以確認此 SQL Server VM 將用作被動副本，然後選擇"**應用"** 以保存設置。 

![在 Azure 中配置 DR 副本](media/virtual-machines-windows-sql-high-availability-dr/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Azure 中 SQL Server HADR 的重要考量
與內部部署、非虛擬化的 IT 基礎結構相較之下，Azure VM、儲存體和網路各有不同的作業特性。 若要在 Azure 中成功實作 HADR SQL Server 解決方案，您必須了解這些差異，並配合這些差異設計您的解決方案。

### <a name="high-availability-nodes-in-an-availability-set"></a>可用性設定組中的高可用性節點
Azure 中的可用性設定組可讓您將高可用性節點分別放入容錯網域 (FD) 和更新網域 (UD)。 基礎 Azure 平台會為可用性集合中的每部虛擬機器指派一個更新網域和一個容錯網域。 資料中心內的這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 99.95% 的 Azure SLA。 要配置高可用性設置，請將所有參與的 SQL 虛擬機器放在相同的可用性集中，以避免在維護事件期間丟失應用程式或資料丟失。 只有相同雲端服務內的節點可以參與相同的可用性設定組。 有關詳細資訊，請參閱[管理虛擬機器的可用性](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="high-availability-nodes-in-an-availability-zone"></a>可用性區域中的高可用性節點
「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 區域內可用性區域的物理分離可確保至少有一個虛擬機器可用並滿足 99.99% 的 Azure SLA，從而保護應用程式和資料免受資料中心故障的影響。 要配置高可用性，請將參與的 SQL 虛擬機器分佈在該區域的可用可用區域。 在可用性區域之間將額外產生 VM 對 VM 的資料傳輸費用。 有關詳細資訊，請參閱[可用性區域](/azure/availability-zones/az-overview)。 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Azure 網路中的容錯移轉叢集行為
由於您將重複的 IP 位址指派為叢集網路名稱 (例如，將相同的 IP 位址指派為其中一個叢集節點)，因此 Azure 中不符合 RFC 規範的 DHCP 服務可能會導致特定容錯移轉叢集組態的建立作業失敗。 這是您實作可用性群組 (取決於 Windows 容錯移轉叢集功能) 時會發生的問題。

當雙節點叢集建立且上線時，請考慮以下案例：

1. 當叢集上線後，NODE1 會要求一個叢集網路名稱的動態指派 IP 位址。
2. 由於 DHCP 服務會辨識來自 NODE1 本身的要求，因此除了 NODE1 本身的 IP 位址外，DHCP 服務不會指定任何 IP 位址。
3. 如果 Windows 偵測到同時指派給 NODE1 和容錯移轉叢集網路名稱的重複位址，則預設的叢集群組就會無法上線。
4. 此時預設的叢集群組就會移動至 NODE2，而該群組會將 NODE1 的 IP 位址視為叢集 IP 位址，並讓預設叢集群組上線。
5. 當 NODE2 嘗試與 NODE1 建立連線時，於 NODE1 導向的封包不會離開 NODE2，因為它將 NODE1 的 IP 位址解析至其本身。 NODE2 無法與 NODE1 建立連線，因此會失去仲裁並關閉叢集。
6. 在此同時，NODE1 可以傳送封包至 NODE2，但是 NODE2 無法回覆。 NODE1 會失去仲裁並關閉叢集。

若要避免這種情況，可以將未使用的靜態 IP 位址 (例如，連結本機的 IP 位址為 169.254.1.1) 指派至叢集網路名稱，使叢集網路名稱上線。 若要簡化此程序，請參閱[在 Azure 中為可用性群組設定 Windows 容錯移轉叢集](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx) \(英文\)。

有關詳細資訊，請參閱在[Azure （GUI） 中配置可用性組](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)。

### <a name="availability-group-listener-support"></a>可用性群組接聽程式支援
可用性群組接聽程式支援執行 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 的 Azure VM。 透過使用在為可用性群組節點的 Azure VM 上啟用的負載平衡端點，即可提供支援。 您必須遵循特殊組態步驟，以便接聽程式在 Azure 中執行的用戶端應用程式，以及在內部部署中執行的用戶端應用程式運作。

設定接聽程式有主要兩個選項：外部 (公用) 或內部。 外部 (公用) 接聽程式會使用網際網路對應負載平衡器，與可透過網際網路存取的公用虛擬 IP (VIP) 相關聯。 內部接聽程式會使用內部負載平衡器，只支援位於相同虛擬網路的用戶端。 對於各個負載平衡器類型，您必須啟用「伺服器直接回傳」。 

如果可用性群組跨越多個 Azure 子網路 (例如跨越多個 Azure 區域的部署)，用戶端連接字串就必須包含 "**MultisubnetFailover = True**"。 這會導致對於不同子網路中的複本進行平行連接嘗試。 如需有關設定接聽程式的指示，請參閱

* [設定 Azure 中可用性群組的 ILB 接聽程式](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)。
* [設定 Azure 中可用性群組的外部接聽程式](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md)。

您仍可以透過直接連接至服務執行個體，分別連接至各個可用性複本。 此外，由於可用性群組能與資料庫鏡像用戶端回溯相容，因此只要將可用性複本設定成類似資料庫鏡像，即可連接至這類複本 (例如資料庫鏡像合作夥伴)：

* 一個主要複本與一個次要複本
* 次要複本設定為不可讀取 (**[可讀取次要]** 選項設定為 **[否]**)

使用 ADO.NET 或 SQL Server Native Client 對應至類似此資料庫鏡像組態的範例用戶端連接字串如下：

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

如需用戶端連線的詳細資訊，請參閱：

* [搭配 SQL Server Native Client 使用連接字串關鍵字](https://msdn.microsoft.com/library/ms130822.aspx)
* [將用戶端連接至資料庫鏡像工作階段 (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [連線至混合式 IT 中的可用性群組接聽程式 (英文)](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [可用性群組接聽程式、用戶端連接及應用程式容錯移轉 (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [使用資料庫鏡像連接字串與可用性組](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>混合式 IT 中的網路延遲
假設內部部署網路與 Azure 之間可能會有一段時間發生嚴重網路延遲，您應該部署 HADR 解決方案。 當您將複本部署至 Azure 時，應該使用非同步認可，而不是同步處理模式的同步認可。 當您將資料庫鏡像伺服器同時部署至內部部署與 Azure 時，請使用高效能模式，而不是高安全性模式。

### <a name="geo-replication-support"></a>異地複寫支援
Azure 磁碟中的異地複寫不支援將相同資料庫的資料檔與記錄檔儲存在不同的磁碟上。 GRS 會在每個磁碟上進行獨立與非同步變更複寫。 此機制能保證異地複寫複本之單一磁碟內的寫入順序，但無法為多個磁碟的跨異地複寫複本保證。 如果您設定資料庫將其資料檔與記錄檔儲存在不同磁碟中，嚴重損壞後修復的磁碟可能會包含更新的資料檔 (而非記錄檔) 複本，該情形會中斷 SQL Server 與 ACID 屬性交易的預寫記錄。 如果您沒有可停用儲存體帳戶中異地複寫的選項，則應該將指定資料庫的所有資料和記錄檔保留在相同磁碟上。 如果因為資料庫大小的緣故，而必須使用一個以上的磁碟，則您需要部署以上列出的其中一個嚴重損壞修復解決方案，以確保能進行資料備援。

## <a name="next-steps"></a>後續步驟
如果您需要透過 SQL Server 建立 Azure 虛擬機器，請參閱 [在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

若要獲得在 Azure VM 上執行的 SQL Server 的最佳效能，請參閱 [Azure 虛擬機器中 SQL Server 的效能最佳作法](virtual-machines-windows-sql-performance.md)中的指引。

如需在 Azure VM 中執行 SQL Server 的其他相關主題，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)。

### <a name="other-resources"></a>其他資源
* [在 Azure 中安裝新的 Active Directory 樹系](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [在 Azure VM 中建立可用性群組的容錯移轉叢集](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) \(英文\)

