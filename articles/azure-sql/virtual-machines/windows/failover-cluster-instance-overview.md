---
title: 容錯移轉叢集執行個體
description: 瞭解在 Azure 虛擬機器上使用 SQL Server (Fci) 的容錯移轉叢集實例。
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
ms.openlocfilehash: 6d77855f095c59b47156af735f4581076ce5a09c
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89611632"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上具有 SQL Server 的容錯移轉叢集實例
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介紹當您使用容錯移轉叢集實例 (FCI) ，以在 Azure 虛擬機器 (Vm) 上 SQL Server 的功能差異。 

## <a name="overview"></a>概觀

Azure Vm 上的 SQL Server 會使用 Windows Server 容錯移轉叢集 (WSFC) 功能，透過伺服器實例層級的冗余提供本機高可用性：容錯移轉叢集實例。 FCI 是在 WSFC (上安裝的 SQL Server 的單一實例，或只是叢集) 節點，而且可能會跨多個子網。 在網路上，FCI 似乎是在單一電腦上執行 SQL Server 的實例。 但是，如果目前節點無法使用，FCI 會提供從某個 WSFC 節點容錯移轉到另一個 WSFC 節點的功能。

本文的其餘部分著重于搭配 Azure Vm 上的 SQL Server 使用容錯移轉叢集實例的差異。 若要深入瞭解容錯移轉叢集技術，請參閱： 

- [Windows 叢集技術](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 容錯移轉叢集實例](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

在 Azure 虛擬機器上具有 SQL Server 的容錯移轉叢集實例，支援使用磁片見證、雲端見證或叢集仲裁的檔案共用見證。

若要深入瞭解，請參閱 [Azure 中 SQL Server vm 的仲裁最佳做法](hadr-cluster-best-practices.md#quorum)。 


## <a name="storage"></a>儲存體

在傳統的內部部署叢集環境中，Windows 容錯移轉叢集會使用存放區域網路 (SAN) ，而這兩個節點都可以用來作為共用存放裝置。 SQL Server 檔案裝載于共用儲存區，而且只有作用中的節點可以一次存取檔案。 

Azure Vm 上的 SQL Server 提供各種選項，作為部署 SQL Server 容錯移轉叢集實例的共用儲存體解決方案： 

||[Azure 共用磁碟](../../../virtual-machines/windows/disks-shared.md)|[進階檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[儲存空間直接存取 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**最低 OS 版本**| 全部 |Windows Server 2012|Windows Server 2016|
|**最小 SQL Server 版本**|全部|SQL Server 2012|SQL Server 2016|
|**支援的 VM 可用性** |具有鄰近位置群組的可用性設定組 |可用性設定組和可用性區域|可用性設定組 |
|**支援 FileStream**|是|否|是 |
|**Azure blob 快取**|否|否|是|

本節的其餘部分會列出 Azure Vm 上適用于 SQL Server 的每個儲存體選項的優點和限制。 

### <a name="azure-shared-disks"></a>Azure 共用磁碟

[Azure 共用磁片](../../../virtual-machines/windows/disks-shared.md) 是 [azure 受控磁片](../../../virtual-machines/managed-disks-overview.md)的一項功能。 Windows Server 容錯移轉叢集支援使用具有容錯移轉叢集實例的 Azure 共用磁片。 

**支援的作業系統**：全部   
**支援的 SQL 版本**：全部     

**優點**： 
- 適用于想要遷移至 Azure 的應用程式，同時保持其高可用性和嚴重損壞修復 (HADR) 架構。 
- 可將叢集應用程式遷移至 Azure 的方式，因為 scsi 持續保留 (SCSI PR) 支援。 
- 支援共用的 Azure 進階 SSD 和 Azure Ultra 磁片儲存體。
- 可以使用單一共用磁片或等量多個共用磁片來建立共用儲存集區。 
- 支援 Filestream。


**限制**： 
- 虛擬機器必須放在相同的可用性設定組和鄰近位置群組中。
- 不支援可用性區域。
- 不支援進階 SSD 磁碟快取。
 
若要開始使用，請參閱 [SQL Server 容錯移轉叢集實例與 Azure 共用磁片](failover-cluster-instance-azure-shared-disks-manually-configure.md)。 

### <a name="storage-spaces-direct"></a>儲存空間 Direct

[儲存空間直接存取](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 是 Azure 虛擬機器上的容錯移轉叢集所支援的 Windows Server 功能。 它提供以軟體為基礎的虛擬 SAN。

**支援的作業系統**： Windows Server 2016 和更新版本   
**支援的 SQL 版本**： SQL Server 2016 和更新版本   


**好處：** 
- 足夠的網路頻寬可提供強大且高效能的共用儲存體解決方案。 
- 支援 Azure blob 快取，因此可從快取在本機提供讀取。  (更新會同時複寫到兩個節點。 )  
- 支援 FileStream。 

**限制：**
- 僅適用于 Windows Server 2016 和更新版本。 
- 不支援可用性區域。
- 這兩部虛擬機器需要相同的磁片容量。 
- 由於進行中的磁片複寫，需要高網路頻寬才能達到高效能。 
- 需要較大的 VM 大小，並針對儲存體進行雙重支付，因為儲存體會連接到每個 VM。 

若要開始使用，請參閱 [使用儲存空間直接存取 SQL Server 容錯移轉叢集實例](failover-cluster-instance-storage-spaces-direct-manually-configure.md)。 

### <a name="premium-file-share"></a>Premium 檔案共用

[Premium 檔案共用](../../../storage/files/storage-how-to-create-premium-fileshare.md) 是 [Azure 檔案儲存體](../../../storage/files/index.yml)的功能。 Premium 檔案共用支援 SSD，且持續低延遲。 它們在 Windows Server 2012 或更新版本上，可與 SQL Server 2012 或更新版本的容錯移轉叢集實例搭配使用。 Premium 檔案共用可提供您更大的彈性，因為您可以調整檔案共用的大小並調整其大小，而不需要停機。

**支援的作業系統**： Windows Server 2012 和更新版本   
**支援的 SQL 版本**： SQL Server 2012 和更新版本   

**好處：** 
- 虛擬機器只會散佈在多個可用性區域的共用儲存體解決方案。 
- 具有單一位數延遲和高載 i/o 效能的完全受控檔案系統。 

**限制：**
- 僅適用于 Windows Server 2012 和更新版本。 
- 不支援 FileStream。 


若要開始使用，請參閱 [使用 Premium 檔案共用 SQL Server 容錯移轉叢集實例](failover-cluster-instance-premium-file-share-manually-configure.md)。 

### <a name="partner"></a>合作夥伴

有支援的儲存體的合作夥伴叢集解決方案。 

**支援的作業系統**：全部   
**支援的 SQL 版本**：全部   

其中一個範例使用 SIOS DataKeeper 作為儲存體。 如需詳細資訊，請參閱 blog 專案 [容錯移轉叢集和 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)。

### <a name="iscsi-and-expressroute"></a>iSCSI 和 ExpressRoute

您也可以透過 Azure ExpressRoute 公開 iSCSI 目標共用區塊儲存體。 

**支援的作業系統**：全部   
**支援的 SQL 版本**：全部   

例如，NetApp 私用儲存體 (NPS) 會透過 ExpressRoute 使用 Equinix 將 iSCSI 目標公開至 Azure VM。

若為 Microsoft 合作夥伴提供的共用儲存體和資料複寫解決方案，請洽詢廠商，以取得任何與存取容錯移轉資料相關的問題。

## <a name="connectivity"></a>連線能力

在 Azure 虛擬機器上使用 SQL Server 的容錯移轉叢集實例會使用 [分散式網路名稱 (DNN) ](hadr-distributed-network-name-dnn-configure.md) 或 [虛擬網路名稱 (VNN) 搭配 Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md) ，以將流量路由傳送至 SQL Server 實例，不論哪個節點目前擁有叢集資源。 使用特定功能和 DNN 搭配 SQL Server FCI 時，還有其他考慮。 若要深入瞭解，請參閱 [SQL SERVER FCI 的 DNN 互通性](failover-cluster-instance-dnn-interoperability.md) 。 

如需叢集連線選項的詳細資訊，請參閱 [將 HADR 連接路由至 Azure vm 上的 SQL Server](hadr-cluster-best-practices.md#connectivity)。 

## <a name="limitations"></a>限制

針對在 Azure 虛擬機器上具有 SQL Server 的容錯移轉叢集實例，請考慮下列限制。 

### <a name="lightweight-resource-provider"></a>輕量資源提供者   
目前，只有在[SQL Server IaaS 代理程式擴充](sql-server-iaas-agent-extension-automate-management.md)功能的[輕量管理模式下](sql-vm-resource-provider-register.md#management-modes)，才支援 Azure 虛擬機器上的 SQL Server 容錯移轉叢集實例。 若要從完整擴充模式變更為輕量，請刪除對應 Vm 的 **sql 虛擬機器** 資源，然後以輕量模式向 sql VM 資源提供者註冊它們。 當您使用 Azure 入口網站刪除 **SQL 虛擬機器** 資源時，請清除正確的虛擬機器旁的核取方塊。 

完整的延伸模組支援自動化備份、修補和進階入口網站管理等功能。 在輕量的管理模式下重新安裝代理程式之後，這些功能將無法用於 SQL Server Vm。

### <a name="msdtc"></a>MSDTC 

Azure 虛擬機器支援 Windows Server 2019 上的 Microsoft Distributed Transaction Coordinator (MSDTC) ，並在叢集共用磁片區 (CSV) 和 [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) ，或在使用 azure 共用磁片的 SQL Server vm 上提供存放裝置。 

在 Azure 虛擬機器上，Windows Server 2016 或更早版本的叢集共用磁片區不支援 MSDTC，原因如下：

- 叢集 MSDTC 資源無法設為使用共用儲存體。 若在 Windows Server 2016 上建立 MSDTC 資源，即使有儲存體可用，系統也不會顯示任何可用的共用儲存體。 Windows Server 2019 中已修正此問題。
- 基本負載平衡器不處理 RPC 連接埠。


## <a name="next-steps"></a>接下來的步驟

請參閱叢集設定的 [最佳做法](hadr-cluster-best-practices.md)，然後您可以 [準備 SQL Server VM 以進行 FCI](failover-cluster-instance-prepare-vm.md)。 

如需詳細資訊，請參閱 

- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

