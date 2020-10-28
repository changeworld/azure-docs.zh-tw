---
title: 容錯移轉叢集執行個體
description: 了解 Azure 虛擬機器上的 SQL Server 容錯移轉叢集執行個體 (FCI)。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 6f216a7f0851661efc61a771fc35feb71e77fd1f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792475"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SQL Server 容錯移轉叢集執行個體
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介紹在 Azure 虛擬機器 (VM) 上使用 SQL Server 容錯移轉叢集執行個體 (FCI) 時的功能差異。 

## <a name="overview"></a>概觀

Azure VM 上的 SQL Server 會使用 Windows Server 容錯移轉叢集 (WSFC) 功能，透過伺服器執行個體層級的備援來提供本機高可用性：容錯移轉叢集執行個體。 FCI 是安裝在所有 WSFC (或僅在叢集) 節點上 (而且可能跨多個子網路) 的單一 SQL Server 執行個體。 在網路上，FCI 看起來就像是在單一電腦上執行的 SQL Server 執行個體。 但是，如果目前的節點變得無法使用，FCI 就會提供從一個 WSFC 節點容錯移轉到另一個節點的服務。

本文其餘部分著重於容錯移轉叢集執行個體與 Azure VM 上的 SQL Server 搭配使用時的差異。 若要深入了解容錯移轉叢集技術，請參閱： 

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集執行個體](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

Azure 虛擬機器上的 SQL Server 容錯移轉叢集執行個體支援使用磁碟見證、雲端見證或檔案共用見證來進行叢集仲裁。

若要深入了解，請參閱 [Azure 中 SQL Server VM 的仲裁最佳做法](hadr-cluster-best-practices.md#quorum)。 


## <a name="storage"></a>儲存體

在傳統的內部部署叢集環境中，Windows 容錯移轉叢集會使用兩個節點都可存取的存放區域網路 (SAN) 作為共用儲存體。 SQL Server 檔案裝載在共用儲存體上，而且每次只有使用中的節點才能存取檔案。 

Azure VM 上的 SQL Server 提供各種選項，作為用來部署 SQL Server 容錯移轉叢集執行個體的共用儲存體解決方案： 

||[Azure 共用磁碟](../../../virtual-machines/windows/disks-shared.md)|[進階檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[儲存空間直接存取 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**作業系統最低版本**| 全部 |Windows Server 2012|Windows Server 2016|
|**最低 SQL Server 版本**|全部|SQL Server 2012|SQL Server 2016|
|**支援的 VM 可用性** |具有鄰近放置群組的可用性設定組 |可用性設定組與可用性區域|可用性設定組 |
|**支援 FileStream**|是|否|是 |
|**Azure Blob 快取**|否|否|是|

本節其餘部分列出 Azure VM 上 SQL Server 可用的每個儲存體選項有何優點和限制。 

### <a name="azure-shared-disks"></a>Azure 共用磁碟

[Azure 共用磁碟](../../../virtual-machines/windows/disks-shared.md)是 [Azure 受控磁碟](../../../virtual-machines/managed-disks-overview.md)的功能。 Windows Server 容錯移轉叢集支援將 Azure 共用磁碟與容錯移轉叢集執行個體搭配使用。 

**支援的 OS** ：全部   
**支援的 SQL 版本** ：全部     

**優點** ： 
- 可用於想要遷移至 Azure 的應用程式，同時讓其保持原本的高可用性和災害復原 (HADR) 架構。 
- 可以將叢集應用程式原封不動地遷移至 Azure，因為其支援 SCSI 持續保留 (SCSI PR)。 
- 支援共用的 Azure 進階 SSD 和 Azure Ultra 磁碟儲存體。
- 可使用單一共用磁碟或等量分割多個共用磁碟來建立共用存放集區。 
- 支援 Filestream。


**限制** ： 
- 虛擬機器必須放在相同的可用性設定組和鄰近放置群組中。
- 不支援可用性區域。
- 不支援進階固態硬碟快取。
 
若要開始使用，請參閱 [SQL Server 容錯移轉叢集執行個體與 Azure 共用磁碟](failover-cluster-instance-azure-shared-disks-manually-configure.md)。 

### <a name="storage-spaces-direct"></a>儲存空間直接存取

[儲存空間直接存取](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)是 Azure 虛擬機器上的容錯移轉叢集所支援的 Windows Server 功能。 其提供了以軟體為基礎的虛擬 SAN。

**支援的 OS** ：Windows Server 2016 及更新版本   
**支援的 SQL 版本** ：SQL Server 2016 及更新版本   


**優點：** 
- 足夠的網路頻寬可提供穩固且高效能的共用儲存體解決方案。 
- 支援 Azure Blob 快取，因此可從快取在本機提供讀取。 (更新會同時複寫到這兩個節點。) 
- 支援 FileStream。 

**限制：**
- 僅適用於 Windows Server 2016 和更新版本。 
- 不支援可用性區域。
- 需要將相同的磁碟容量連結到這兩部虛擬機器。 
- 由於要進行持續的磁碟複寫，因此需要高網路頻寬才能實現高效能。 
- 需要較大的 VM 大小，並支付兩倍的儲存體費用，因為儲存體要連結至每個 VM。 

若要開始使用，請參閱 [SQL Server 容錯移轉叢集執行個體與儲存空間直接存取](failover-cluster-instance-storage-spaces-direct-manually-configure.md)。 

### <a name="premium-file-share"></a>進階檔案共用

[進階檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md)是 [Azure 檔案儲存體](../../../storage/files/index.yml)的功能。 進階檔案共用會透過 SSD 來支援，並持續保持低延遲。 在 Windows Server 2012 或更新版本上的 SQL Server 2012 或更新版本中，其完全支援與容錯移轉叢集執行個體搭配使用。 進階檔案共用提供更大的彈性，因為您無需停機即可調整檔案共用的大小和規模。

**支援的 OS** ：Windows Server 2012 及更新版本   
**支援的 SQL 版本** ：SQL Server 2012 及更新版本   

**優點：** 
- 只有適用於虛擬機器的共用儲存體解決方案會散佈到多個可用性區域。 
- 完全受控的檔案系統，具有個位數延遲和可高載的 I/O 效能。 

**限制：**
- 僅適用於 Windows Server 2012 和更新版本。 
- 不支援 FileStream。 


若要開始使用，請參閱 [SQL Server 容錯移轉叢集執行個體與進階檔案共用](failover-cluster-instance-premium-file-share-manually-configure.md)。 

### <a name="partner"></a>Partner

您可以找到具有受支援儲存體的合作夥伴叢集解決方案。 

**支援的 OS** ：全部   
**支援的 SQL 版本** ：全部   

其中一個範例使用 SIOS DataKeeper 作為儲存體。 如需詳細資訊，請參閱部落格文章[容錯移轉叢集和 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)。

### <a name="iscsi-and-expressroute"></a>iSCSI 和 ExpressRoute

您也可以透過 Azure ExpressRoute 公開 iSCSI 目標共用區塊儲存體。 

**支援的 OS** ：全部   
**支援的 SQL 版本** ：全部   

例如，NetApp 私用儲存體 (NPS) 會透過 ExpressRoute 使用 Equinix 將 iSCSI 目標公開至 Azure VM。

對於 Microsoft 合作夥伴所提供的共用儲存體和資料複寫解決方案，如有關於存取容錯移轉資料的任何問題，請與廠商連絡。

## <a name="connectivity"></a>連線能力

Azure 虛擬機器上的 SQL Server 容錯移轉叢集執行個體使用[分散式網路名稱 (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md) 或[虛擬網路名稱 (VNN) 搭配 Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) 來將流量路由傳送至 SQL Server 執行個體 (不論目前擁有叢集資源的節點為何)。 對 SQL Server FCI 使用特定功能和 DNN 時有另外的考量。 若要深入了解，請參閱 [DNN 與 SQL Server FCI 的互通性](failover-cluster-instance-dnn-interoperability.md)。 

如需叢集連線選項的詳細資訊，請參閱[將 HADR 連線路由傳送至 Azure VM 上的 SQL Server](hadr-cluster-best-practices.md#connectivity)。 

## <a name="limitations"></a>限制

針對 Azure 虛擬機器上的 SQL Server 容錯移轉叢集執行個體，請考慮下列限制。 

### <a name="lightweight-resource-provider"></a>輕量資源提供者   
目前，只能透過 [SQL Server IaaS 代理程式延伸模組](sql-server-iaas-agent-extension-automate-management.md)的[輕量管理模式](sql-vm-resource-provider-register.md#management-modes)來支援 Azure 虛擬機器上 SQL Server 容錯移轉叢集執行個體。 若要從完整的延伸模組模式變更為輕量，請刪除對應 VM 的 **SQL 虛擬機器** 資源，然後在輕量模式中使用 SQL VM 資源提供者來註冊這些資源。 使用 Azure 入口網站刪除 **SQL 虛擬機器** 資源時，請清除正確虛擬機器旁邊的核取方塊。 

完整的延伸模組支援自動化備份、修補和進階入口網站管理等功能。 以輕量管理模式重新安裝代理程式後，這些功能對 SQL Server VM 即不再有效。

### <a name="msdtc"></a>MSDTC 

Azure 虛擬機器支援在儲存體位於叢集共用磁碟區 (CSV) 和 [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) 上的 Windows Server 2019 上使用 Microsoft 分散式交易協調器 (MSDTC)，也支援在使用 Azure 共用磁碟的 SQL Server VM 上使用 MSDTC。 

在 Azure 虛擬機器上，具有叢集共用磁碟區的 Windows Server 2016 或更早版本不支援 MSDTC，因為：

- 叢集化的 MSDTC 資源無法設為使用共用儲存體。 若在 Windows Server 2016 上建立 MSDTC 資源，即使有儲存體可用，系統也不會顯示任何可用的共用儲存體。 Windows Server 2019 中已修正此問題。
- 基本負載平衡器不處理 RPC 連接埠。


## <a name="next-steps"></a>後續步驟

請檢閱[叢集設定最佳做法](hadr-cluster-best-practices.md)，以便[針對 FCI 來準備 SQL Server VM](failover-cluster-instance-prepare-vm.md)。 

如需詳細資訊，請參閱 

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集執行個體](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)