---
title: 高可用性、嚴重損壞修復、商務持續性
description: 瞭解適用于 Azure Vm 上 SQL Server 的高可用性、嚴重損壞修復 (HADR) 和商務持續性選項，例如 Always On 可用性群組、容錯移轉叢集實例、資料庫鏡像、記錄傳送和備份 & 還原至 Azure 儲存體。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: 3b18582cdb34708c379725d361d1fcc5e608ae38
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462746"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上 SQL Server 的商務持續性和 HADR
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

商務持續性表示在發生嚴重損壞時繼續您的業務、規劃復原，以及確保您的資料具有高可用性。 Azure 虛擬機器上的 SQL Server 有助於降低高可用性和嚴重損壞修復的成本 (HADR) 資料庫解決方案。 

 (Vm) 的虛擬機器上支援大部分的 SQL Server HADR 解決方案，作為僅限 Azure 和混合式解決方案。 在僅限 Azure 解決方案中，整個 HADR 系統會在 Azure 中執行。 在混合式組態中，解決方案的一部分會在 Azure 中執行，而其他部分會在組織中的內部部署執行。 Azure 環境的彈性可讓您將解決方案部分或完全移動至 Azure，以滿足 SQL Server 資料庫系統的預算與 HADR 需求。

本文會比較和對照 Azure Vm 上的 SQL Server 所提供的商務持續性解決方案。 

## <a name="overview"></a>概觀

您必須確保資料庫系統具有服務等級協定 (SLA) 所需的 HADR 功能。 Azure 提供高可用性機制（例如雲端服務的服務修復和虛擬機器的失敗修復偵測），本身並不保證您可以符合 SLA。 雖然這些機制可協助保護虛擬機器的高可用性，但它們並不會保護在 VM 內執行之 SQL Server 的可用性。 

當 VM 在線上且狀況良好時，SQL Server 實例可能會失敗。 即使是 Azure 所提供的高可用性機制，也可讓 Vm 停機，因為從軟體或硬體故障復原和作業系統升級等事件。

Azure 中的地理位置冗余儲存體 (GRS) 會以稱為「異地複寫」的功能來執行。 GRS 可能不是您資料庫的適當嚴重損壞修復解決方案。 由於異地複寫會以非同步方式傳送資料，因此可能會在嚴重損壞時遺失最新的更新。 異地複寫 [支援](#geo-replication-support) 一節中涵蓋了異地複寫限制的詳細資訊。

## <a name="deployment-architectures"></a>部署架構
Azure 支援下列商務持續性 SQL Server 技術：

* [Always On 可用性群組](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always On 容錯移轉叢集實例 (Fci) ](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [記錄傳送](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [使用 Azure Blob 儲存體 SQL Server 備份和還原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [資料庫鏡像](/sql/database-engine/database-mirroring/database-mirroring-sql-server) -SQL Server 2016 中已淘汰

您可以結合這些技術來實行具有高可用性和嚴重損壞修復功能的 SQL Server 解決方案。 根據您所使用的技術，混合式部署可能需要使用 Azure 虛擬網路的 VPN 通道。 下列各節將說明一些範例部署架構。

## <a name="azure-only-high-availability-solutions"></a>僅限 Azure：高可用性解決方案

您可以擁有高可用性解決方案，以便在具有 Always On 可用性群組的資料庫層級 SQL Server。 您也可以在實例層級建立具有 Always On 容錯移轉叢集實例的高可用性解決方案。 如需額外的保護，您可以在容錯移轉叢集實例上建立可用性群組，以在這兩個層級建立冗余。 

| 技術 | 範例架構 |
| --- | --- |
| **可用性群組** |在相同區域的 Azure VM 中執行的可用性複本提供高可用性。 由於 Windows 容錯移轉叢集需要使用 Active Directory 網域，因此您需要設定網域控制站 VM。<br/><br/> 為了提高冗余和可用性，Azure Vm 可以部署在[可用性群組總覽](availability-group-overview.md)中所記載的不同[可用性區域](../../../availability-zones/az-overview.md)中。 如果可用性群組中的 SQL Server Vm 部署在可用性區域中，則請使用 [azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) 作為接聽程式，如 [AZURE SQL VM CLI](availability-group-az-cli-configure.md) 和 [azure 快速入門範本](availability-group-quickstart-template-configure.md) 文章中所述。<br/> ![可用性群組](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>如需詳細資訊，請參閱[在 Azure (GUI) 中設定可用性群組](availability-group-azure-marketplace-template-configure.md)。 |
| **容錯移轉叢集執行個體** |SQL Server Vm 支援容錯移轉叢集實例。 因為 FCI 功能需要共用儲存體，所以有五個解決方案可搭配 Azure Vm 上的 SQL Server 使用： <br/><br/> -使用適用于 Windows Server 2019 的 [Azure 共用磁片](failover-cluster-instance-azure-shared-disks-manually-configure.md) 。 共用受控磁片是允許將受控磁片同時連結至多個虛擬機器的 Azure 產品。 叢集中的 Vm 可以根據叢集應用程式透過 SCSI 持續保留 (SCSI PR) 所選擇的保留，來讀取或寫入連接的磁片。 SCSI PR 是業界標準的儲存體解決方案，可供在存放區域網路上執行的應用程式使用， (SAN) 內部部署。 在受控磁片上啟用 SCSI PR，可讓您依原樣將這些應用程式遷移至 Azure。 <br/><br/>-使用[儲存空間直接存取 \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md)提供適用于 Windows Server 2016 和更新版本的軟體式虛擬 SAN。<br/><br/>-使用適用于 Windows Server 2012 和更新版本的 [Premium 檔案共用](failover-cluster-instance-premium-file-share-manually-configure.md) 。 Premium 檔案共用是支援 SSD、持續低延遲，而且完全支援與 FCI 搭配使用。<br/><br/>-使用合作夥伴解決方案所支援的儲存體進行叢集化。 如需使用 SIOS DataKeeper 的特定範例，請參閱 blog 專案 [容錯移轉叢集和 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)。<br/><br/>-透過 Azure ExpressRoute 使用遠端 iSCSI 目標的共用區塊儲存體。 例如，NetApp 私用儲存體 (NPS) 會透過 ExpressRoute 使用 Equinix 將 iSCSI 目標公開至 Azure VM。<br/><br/>若為 Microsoft 合作夥伴提供的共用儲存體和資料複寫解決方案，請洽詢廠商，以取得任何與存取容錯移轉資料相關的問題。<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>僅限 Azure：嚴重損壞修復解決方案
您可以使用可用性群組、資料庫鏡像，或使用儲存體 blob 進行備份和還原，以在 Azure 中為您的 SQL Server 資料庫提供嚴重損壞修復解決方案。

| 技術 | 範例架構 |
| --- | --- |
| **可用性群組** |為了進行嚴重損壞修復，可用性複本會在 Azure VM 的多個資料中心執行。 此跨區域解決方案可協助防止整個網站中斷。 <br/> ![可用性群組](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>在區域內，所有複本都應位於相同的雲端服務和相同的虛擬網路中。 因為每個區域都會有個別的虛擬網路，所以這些解決方案需要網路對網路連線。 如需詳細資訊，請參閱 [使用 Azure 入口網站設定網路對網路連接](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。 如需詳細指示，請參閱 [設定跨不同 Azure 區域的 SQL Server Always On 可用性群組](availability-group-manually-configure-multiple-regions.md)。|
| **資料庫鏡像** |為了進行嚴重損壞修復，主體、鏡像和伺服器會在不同的資料中心內執行。 您必須使用伺服器憑證部署它們。 Azure VM 上的 SQL Server 2008 或 SQL Server 2008 R2 不支援 SQL Server 資料庫鏡像。 <br/>![資料庫鏡像](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **使用 Azure Blob 儲存體進行備份和還原** |生產資料庫直接備份到不同資料中心的 Blob 儲存體，以進行嚴重損壞修復。<br/>![備份與還原](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>如需詳細資訊，請參閱 [備份和還原 Azure vm 上的 SQL Server](../../../azure-sql/virtual-machines/windows/backup-restore.md)。 |
| **使用 Azure Site Recovery 將 SQL Server 複寫和容錯移轉至 Azure** |單一 Azure 資料中心內的生產 SQL Server 實例會直接複寫至不同 Azure 資料中心的 Azure 儲存體，以進行嚴重損壞修復。<br/>![使用 Azure Site Recovery 複寫](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>如需詳細資訊，請參閱[使用 SQL Server 嚴重損壞修復和 Azure Site Recovery 保護 SQL Server](../../../site-recovery/site-recovery-sql.md)。 |


## <a name="hybrid-it-disaster-recovery-solutions"></a>混合式 IT：災害復原解決方案
您可以使用可用性群組、資料庫鏡像、記錄傳送，以及使用 Azure Blob 儲存體進行備份和還原，在混合式 IT 環境中為您的 SQL Server 資料庫提供嚴重損壞修復解決方案。

| 技術 | 範例架構 |
| --- | --- |
| **可用性群組** |為了進行跨網站的嚴重損壞修復，部分可用性複本會在 Azure VM 中執行，而其他複本會在內部部署執行。 生產網站可以是內部部署或是在 Azure 資料中心。<br/>![可用性群組](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>由於所有可用性複本都必須位於相同的容錯移轉叢集中，因此該叢集必須同時跨這兩個網路 (多重子網路的容錯移轉叢集)。 此組態需要 Azure 與內部部署網路之間的 VPN 連線。<br/><br/>若要成功對資料庫進行災害復原，您也應該在災害復原網站安裝複本網域控制站。|
| **資料庫鏡像** |一個合作夥伴在 Azure VM 中執行，另一個在內部部署執行，以使用伺服器憑證進行跨網站嚴重損壞修復。 合作夥伴不需要位於相同的 Active Directory 網域，也不需要 VPN 連線。<br/>![資料庫鏡像](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>另一個資料庫鏡像案例，則是為了進行跨網站災害復原復，讓一個合作夥伴在 Azure VM 中執行，並讓其他合作夥伴在相同 Active Directory 網域的內部部署中執行。 需要 [Azure 虛擬網路與內部部署之間的 VPN 連線](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。<br/><br/>若要成功對資料庫進行災害復原，您也應該在災害復原網站安裝複本網域控制站。 Azure VM 上的 SQL Server 2008 或 SQL Server 2008 R2 不支援 SQL Server 資料庫鏡像。 |
| **記錄傳送** |為了進行跨網站嚴重損壞修復，一個合作夥伴會在 Azure VM 中執行，而其他合作夥伴會在內部部署中執行。 記錄傳送依賴 Windows 檔案共用，因此需要 Azure 虛擬網路和內部部署網路之間的 VPN 連線。<br/>![記錄傳送](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>若要成功對資料庫進行災害復原，您也應該在災害復原網站安裝複本網域控制站。 |
| **使用 Azure Blob 儲存體進行備份和還原** |內部部署生產資料庫直接備份到 Azure Blob 儲存體，以進行嚴重損壞修復。<br/>![備份與還原](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>如需詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 備份和還原](../../../azure-sql/virtual-machines/windows/backup-restore.md)。 |
| **使用 Azure Site Recovery 將 SQL Server 複寫和容錯移轉至 Azure** |內部部署生產環境 SQL Server 實例會直接複寫到 Azure 儲存體，以進行嚴重損壞修復。<br/>![使用 Azure Site Recovery 複寫](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>如需詳細資訊，請參閱[使用 SQL Server 嚴重損壞修復和 Azure Site Recovery 保護 SQL Server](../../../site-recovery/site-recovery-sql.md)。 |


## <a name="free-dr-replica-in-azure"></a>Azure 中的免費 DR 複本

如果您有 [軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，則可以使用 SQL Server 執行混合式嚴重損壞修復 (DR) 計畫，而不會產生被動損壞修復實例的額外授權成本。

在下圖中，安裝程式會使用在 Azure 虛擬機器上執行的 SQL Server，此虛擬機器使用12個核心做為內部部署 SQL Server 部署（使用12個核心）的災難修復複本。 在過去，您必須為內部部署和 Azure 虛擬機器部署授權12個核心的 SQL Server。 新的權益可提供在 Azure 虛擬機器上執行的被動複本權益。 現在您只需要授權12個在內部部署執行的核心 SQL Server，只要在 Azure 虛擬機器上符合被動複本的災難復原準則即可。

![Azure 中的免費災難修復複本](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

如需詳細資訊，請參閱[產品授權條款](https://www.microsoft.com/licensing/product-licensing/products)。 

若要啟用此權益，請移至您 [SQL Server 的虛擬機器資源](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)。 選取 [設定] 下的 [**設定**]，**然後選擇**[ **SQL Server 授權**] 下的 [嚴重損壞**修復**] 選項。 選取核取方塊以確認此 SQL Server VM 將用來作為被動複本，然後選取 [套用 **] 以儲存** 您的設定。 

![在 Azure 中設定嚴重損壞修復複本](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Azure 中 SQL Server HADR 的重要考量
與內部部署、非虛擬化的 IT 基礎結構相較之下，Azure VM、儲存體和網路各有不同的作業特性。 若要在 Azure 中成功實施 HADR SQL Server 解決方案，您必須瞭解這些差異，並設計您的解決方案來配合這些差異。

### <a name="high-availability-nodes-in-an-availability-set"></a>可用性設定組中的高可用性節點
Azure 中的可用性設定組可讓您將高可用性節點放入不同的容錯網域和更新網域中。 Azure 平臺會為可用性設定組中的每部虛擬機器指派一個更新網域和一個容錯網域。 資料中心內的這項設定可確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可供使用，且符合99.95% 的 Azure SLA。 

若要設定高可用性設定，請將所有參與 SQL Server 的虛擬機器放在相同的可用性設定組中，以避免在維護事件期間發生應用程式或資料遺失。 只有相同雲端服務內的節點可以參與相同的可用性設定組。 如需詳細資訊，請參閱[管理虛擬機器的可用性](../../../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="high-availability-nodes-in-an-availability-zone"></a>可用性區域中的高可用性節點
可用性區域是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 區域內可用性區域的實體區隔可確保至少有一部虛擬機器可供使用且符合99.99% 的 Azure SLA，以協助保護應用程式和資料不受資料中心失敗的影響。 

若要設定高可用性，請將參與的 SQL Server 虛擬機器分散到區域中的可用性區域。 可用性區域之間的網路對網路傳輸將會產生額外費用。 如需詳細資訊，請參閱[可用性區域](/azure/availability-zones/az-overview)。 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Azure 網路中的容錯移轉叢集行為
Azure 中不符合 RFC 規範的 DHCP 服務可能會導致建立特定容錯移轉叢集設定失敗。 發生這種失敗的原因是，叢集網路名稱指派了重複的 IP 位址，例如與其中一個叢集節點相同的 IP 位址。 當您使用相依于 Windows 容錯移轉叢集功能的可用性群組時，就會發生這種問題。

當雙節點叢集建立且上線時，請考慮以下案例：

1. 叢集已上線，然後節點1要求叢集網路名稱有動態指派的 IP 位址。
2. DHCP 服務不會提供 Node1 專屬 IP 位址以外的任何 IP 位址，因為 DHCP 服務會辨識出該要求來自節點節點本身。
3. Windows 偵測到重複的位址已指派給節點1和容錯移轉叢集的網路名稱，且預設叢集群組無法上線。
4. 預設叢集群組會移至節點2。 節點2會將 Node1 的 IP 位址視為叢集 IP 位址，並將預設叢集群組上線。
5. 當節點2嘗試建立與節點1之間的連接時，在節點1上導向的封包不會離開節點，因為它會將 Node1 的 IP 位址解析 節點2無法建立與節點節點的連線，然後失去仲裁併關閉叢集。
6. 節點1可以將封包傳送至節點2，但節點1無法回復。 NODE1 會失去仲裁並關閉叢集。

您可以將未使用的靜態 IP 位址指派給叢集網路名稱，以使叢集網路名稱上線，以避免此情況。 例如，您可以使用連結-本機 IP 位址，例如 169.254.1.1) 指派。 若要簡化此程序，請參閱[在 Azure 中為可用性群組設定 Windows 容錯移轉叢集](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx) \(英文\)。

如需詳細資訊，請參閱[在 Azure (GUI) 中設定可用性群組](availability-group-azure-marketplace-template-configure.md)。

### <a name="support-for-availability-group-listeners"></a>可用性群組接聽程式的支援
在執行 Windows Server 2012 和更新版本的 Azure Vm 上，支援可用性群組接聽程式。 透過使用在為可用性群組節點的 Azure VM 上啟用的負載平衡端點，即可提供支援。 您必須遵循特殊設定步驟，讓接聽程式適用于在 Azure 中執行的用戶端應用程式，以及在內部部署環境中執行的用戶端應用程式。

設定接聽程式有主要兩個選項：外部 (公用) 或內部。 外部 (公用) 接聽程式會使用網際網路對向負載平衡器，並與可透過網際網路存取的公用虛擬 IP 相關聯。 內部接聽程式會使用內部負載平衡器，而且僅支援相同虛擬網路內的用戶端。 對於各個負載平衡器類型，您必須啟用「伺服器直接回傳」。 

如果可用性群組跨越多個 Azure 子網 (例如跨 Azure 區域) 的部署，則用戶端連接字串必須包含 `MultisubnetFailover=True` 。 這會導致對於不同子網路中的複本進行平行連接嘗試。 如需設定接聽程式的指示，請參閱 [在 Azure 中設定可用性群組的 ILB 接聽程式](availability-group-listener-powershell-configure.md)。


您仍可以透過直接連接至服務執行個體，分別連接至各個可用性複本。 此外，由於可用性群組與資料庫鏡像用戶端回溯相容，因此只要複本的設定類似資料庫鏡像，您就可以連接到可用性複本，例如資料庫鏡像夥伴：

* 有一個主要複本和一個次要複本。
* 次要複本已設定為不可讀取的 (可 **讀取次要** 選項設定為 [ **否** ]) 。

以下是範例用戶端連接字串，其對應至使用 ADO.NET 或 SQL Server Native Client 的類似資料庫鏡像設定：

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

如需用戶端連線的詳細資訊，請參閱：

* [搭配 SQL Server Native Client 使用連接字串關鍵字](https://msdn.microsoft.com/library/ms130822.aspx)
* [將用戶端連接至資料庫鏡像工作階段 (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [連線至混合式 IT 中的可用性群組接聽程式 (英文)](https://docs.microsoft.com/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [可用性群組接聽程式、用戶端連線及應用程式容錯移轉 (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [搭配可用性群組使用資料庫鏡像連接字串](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>混合式 IT 中的網路延遲
假設您的內部部署網路與 Azure 之間可能有較長的網路延遲，以部署您的 HADR 解決方案。 當您要將複本部署至 Azure 時，請使用非同步認可，而不是同步處理模式的同步認可。 當您要在內部部署和 Azure 中部署資料庫鏡像伺服器時，請使用高效能模式，而不是高安全性模式。

### <a name="geo-replication-support"></a>異地複寫支援
Azure 磁碟中的異地複寫不支援將相同資料庫的資料檔與記錄檔儲存在不同的磁碟上。 GRS 會在每個磁碟上進行獨立與非同步變更複寫。 此機制能保證異地複寫複本之單一磁碟內的寫入順序，但無法為多個磁碟的跨異地複寫複本保證。 如果您將資料庫設定為將其資料檔和其記錄檔儲存在不同的磁片上，則在嚴重損壞之後復原的磁片可能會包含比記錄檔更新的資料檔複本，這會中斷預先寫入記錄檔的 SQL Server，而 ACID 屬性 (不可部分完成性、一致性、隔離和持久性) 的交易。 

如果您沒有在儲存體帳戶上停用異地複寫的選項，請將資料庫的所有資料和記錄檔保留在同一個磁片上。 如果您因為資料庫大小而必須使用一個以上的磁片，請部署先前所列的其中一個嚴重損壞修復解決方案，以確保資料的冗余。

## <a name="next-steps"></a>接下來的步驟

決定 [可用性群組](availability-group-overview.md) 或 [容錯移轉叢集實例](failover-cluster-instance-overview.md) 是否為您企業的最佳商務持續性解決方案。 然後，請參閱為您的環境設定高可用性和嚴重損壞修復的 [最佳作法](hadr-cluster-best-practices.md) 。 




