---
title: 容錯移轉叢集執行個體
description: 瞭解 Azure 虛擬機器上具有 SQL Server 的容錯移轉叢集實例（Fci）。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 8a5374bf15798fd7e53f0d93e69f2f40a2d57b94
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533813"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上具有 SQL Server 的容錯移轉叢集實例
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

當您使用 Azure 虛擬機器（Vm）上的 SQL Server 容錯移轉叢集實例（FCI）時，本文會介紹功能差異。 

## <a name="overview"></a>概觀

Azure Vm 上的 SQL Server 會使用 Windows Server 容錯移轉叢集（WSFC）功能，透過伺服器實例層級的冗余來提供本機高可用性：容錯移轉叢集實例。 FCI 是安裝在 WSFC （或單純叢集）節點上，而且可能跨多個子網的單一 SQL Server 實例。 在網路上，FCI 似乎是在單一電腦上執行 SQL Server 的實例。 但是，如果目前的節點變得無法使用，FCI 會提供從一個 WSFC 節點容錯移轉到另一個。

本文的其餘部分著重于容錯移轉叢集實例與 Azure Vm 上的 SQL Server 搭配使用時的差異。 若要深入瞭解容錯移轉叢集技術，請參閱： 

- [Windows 叢集技術](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集實例](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

在 Azure 上使用 SQL Server 的容錯移轉叢集實例虛擬機器支援使用磁片見證、雲端見證或叢集仲裁的檔案共用見證。

若要深入瞭解，請參閱[Azure 中 SQL Server vm 的仲裁最佳做法](hadr-cluster-best-practices.md#quorum)。 


## <a name="storage"></a>存放裝置

在傳統的內部部署叢集環境中，Windows 容錯移轉叢集會使用兩個節點都可存取的存放區域網路（SAN）做為共用存放裝置。 SQL Server 檔案是裝載在共用存放裝置上，而且只有使用中的節點可以一次存取檔案。 

Azure Vm 上的 SQL Server 提供各種選項，作為部署 SQL Server 容錯移轉叢集實例的共用儲存體解決方案： 

||[Azure 共用磁碟](../../../virtual-machines/windows/disks-shared.md)|[Premium 檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[儲存空間直接存取 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**最低 OS 版本**| 全部 |Windows Server 2012|Windows Server 2016|
|**最低 SQL Server 版本**|全部|SQL Server 2012|SQL Server 2016|
|**支援的 VM 可用性** |具有鄰近放置群組的可用性設定組 |可用性設定組和可用性區域|可用性設定組 |
|**支援 FileStream**|是|否|是 |
|**Azure blob 快取**|否|否|是|

本節的其餘部分列出適用于 Azure Vm 上 SQL Server 的每個儲存體選項的優點和限制。 

### <a name="azure-shared-disks"></a>Azure 共用磁碟

[Azure 共用磁片](../../../virtual-machines/windows/disks-shared.md)是[azure 受控磁片](../../../virtual-machines/windows/managed-disks-overview.md)的一項功能。 Windows Server 容錯移轉叢集支援將 Azure 共用磁片與容錯移轉叢集實例搭配使用。 

**支援的作業系統**：全部   
**支援的 SQL 版本**：全部     

**優點**： 
- 適用于想要遷移至 Azure 的應用程式，同時保持其高可用性和嚴重損壞修復（HADR）架構。 
- 可以將叢集應用程式遷移至 Azure，因為支援 SCSI 持續保留（SCSI PR）。 
- 支援適用于所有 SQL Server 版本的共用 Azure 進階 SSD，以及 SQL Server 2019 的共用 Azure Ultra 磁碟儲存體。 
- 可以使用單一共用磁片或 stripe 多個共用磁片來建立共用存放集區。 
- 支援 Filestream。


**限制**： 
- 虛擬機器必須放在相同的可用性設定組和鄰近放置群組中。
- 不支援可用性區域。
- 不支援進階 SSD 的磁碟快取。
 
若要開始使用，請參閱[SQL Server 容錯移轉叢集實例與 Azure 共用磁片](failover-cluster-instance-azure-shared-disks-manually-configure.md)。 

### <a name="storage-spaces-direct"></a>儲存空間直接存取

[儲存空間直接存取](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)是 Azure 虛擬機器上的容錯移轉叢集所支援的 Windows Server 功能。 它提供了以軟體為基礎的虛擬 SAN。

**支援的作業系統**： Windows Server 2016 和更新版本   
**支援的 SQL 版本**： SQL Server 2016 和更新版本   


**各種** 
- 足夠的網路頻寬可提供穩固且高效能的共用儲存體解決方案。 
- 支援 Azure blob 快取，因此可從快取在本機提供讀取。 （更新會同時複寫到這兩個節點）。 
- 支援 FileStream。 

**一定**
- 僅適用于 Windows Server 2016 和更新版本。 
- 不支援可用性區域。
- 需要將相同的磁片容量連接到這兩部虛擬機器。 
- 由於進行中的磁片複寫，因此需要高網路頻寬才能達到高效能。 
- 需要較大的 VM 大小，並針對儲存體付費，因為儲存體已附加至每個 VM。 

若要開始使用，請參閱[SQL Server 具有儲存空間直接存取的容錯移轉叢集實例](failover-cluster-instance-storage-spaces-direct-manually-configure.md)。 

### <a name="premium-file-share"></a>Premium 檔案共用

[Premium 檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)是[Azure 檔案儲存體](../../../storage/files/index.yml)的功能。 高階檔案共用會受到 SSD 的支援，並持續保持低延遲。 在 Windows Server 2012 或更新版本上的 SQL Server 2012 或更新版本的容錯移轉叢集實例，則完全支援使用它們。 Premium 檔案共用可提供您更大的彈性，因為您可以調整檔案共用的大小，而不需要停機。

**支援的作業系統**： Windows Server 2012 和更新版本   
**支援的 SQL 版本**： SQL Server 2012 和更新版本   

**各種** 
- 只有適用于虛擬機器的共用儲存體解決方案會散佈到多個可用性區域。 
- 完全受控的檔案系統，具有單一位數的延遲和高載 i/o 效能。 

**一定**
- 僅適用于 Windows Server 2012 和更新版本。 
- 不支援 FileStream。 


若要開始使用，請參閱[SQL Server 具有 Premium 檔案共用的容錯移轉叢集實例](failover-cluster-instance-premium-file-share-manually-configure.md)。 

### <a name="partner"></a>Partner

有支援的儲存體的合作夥伴叢集解決方案。 

**支援的作業系統**：全部   
**支援的 SQL 版本**：全部   

其中一個範例使用 SIOS DataKeeper 作為儲存體。 如需詳細資訊，請參閱 blog 專案[容錯移轉叢集和 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)。

### <a name="iscsi-and-expressroute"></a>iSCSI 和 ExpressRoute

您也可以透過 Azure ExpressRoute 公開 iSCSI 目標共用區塊儲存體。 

**支援的作業系統**：全部   
**支援的 SQL 版本**：全部   

例如，NetApp 私用儲存體 (NPS) 會透過 ExpressRoute 使用 Equinix 將 iSCSI 目標公開至 Azure VM。

如需 Microsoft 合作夥伴提供的共用儲存體和資料複寫解決方案，請洽詢廠商，以瞭解在容錯移轉時存取資料的任何相關問題。

## <a name="connectivity"></a>連線能力

在 Azure 上使用 SQL Server 的容錯移轉叢集實例虛擬機器使用[分散式網路名稱（DNN）](hadr-distributed-network-name-dnn-configure.md)或[虛擬網路名稱（VNN）搭配 Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md)來將流量路由傳送至 SQL Server 實例，而不論目前擁有叢集資源的節點為何。 使用特定功能和具有 SQL Server FCI 的 DNN 時，還有其他考慮。 若要深入瞭解，請參閱[DNN 與 SQL SERVER FCI 的互通性](failover-cluster-instance-dnn-interoperability.md)。 

如需叢集連線選項的詳細資訊，請參閱[將 HADR 連接路由至 Azure vm 上的 SQL Server](hadr-cluster-best-practices.md#connectivity)。 

## <a name="limitations"></a>限制

請考慮使用 Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例的下列限制。 

### <a name="lightweight-resource-provider"></a>輕量資源提供者   
此時，只有[SQL Server IaaS 代理程式擴充](sql-server-iaas-agent-extension-automate-management.md)功能的[輕量管理模式](sql-vm-resource-provider-register.md#management-modes)，才支援 Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例。 若要從完整延伸模式變更為輕量，請刪除對應 Vm 的**sql 虛擬機器**資源，然後在輕量模式中向 sql VM 資源提供者註冊。 當您使用 Azure 入口網站刪除**SQL 虛擬機器**資源時，請清除正確虛擬機器旁的核取方塊。 

完整的延伸模組支援自動化備份、修補和進階入口網站管理等功能。 在輕量管理模式中重新安裝代理程式之後，這些功能將無法在 SQL Server Vm 上使用。

### <a name="msdtc"></a>MSDTC   
Azure 虛擬機器支援 Windows Server 2019 上的 MSDTC 和叢集共用磁片區（CSV）和[Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)上的儲存體。

在 Azure 虛擬機器上，Windows Server 2016 或更早版本不支援 MSDTC，因為：

- 叢集 MSDTC 資源無法設為使用共用儲存體。 若在 Windows Server 2016 上建立 MSDTC 資源，即使有儲存體可用，系統也不會顯示任何可用的共用儲存體。 Windows Server 2019 中已修正此問題。
- 基本負載平衡器不會處理 RPC 連接埠。


## <a name="next-steps"></a>後續步驟

查看叢集設定的[最佳做法](hadr-cluster-best-practices.md)，然後您就可以[準備 SQL Server VM 以進行 FCI](failover-cluster-instance-prepare-vm.md)。 

如需詳細資訊，請參閱 

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

