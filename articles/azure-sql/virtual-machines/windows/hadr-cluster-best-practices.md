---
title: 叢集設定最佳做法
description: 當您在 Azure 虛擬機器上設定 SQL Server 的高可用性和嚴重損壞修復（HADR）（例如支援的仲裁或連接路由選項）時，請瞭解支援的叢集設定。
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
ms.openlocfilehash: d20ac5964ef70618d4d7dc2d4a7fe7d7d01284ce
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965475"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>叢集設定的最佳作法（在 Azure Vm 上 SQL Server）
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

叢集用於 Azure 虛擬機器（Vm）上具有 SQL Server 的高可用性和嚴重損壞修復（HADR）。 

本文針對[容錯移轉叢集實例（fci）](failover-cluster-instance-overview.md)和[可用性群組](availability-group-overview.md)提供叢集設定最佳作法，當您搭配 Azure vm 上的 SQL Server 使用它們時。 


## <a name="networking"></a>網路功能

針對每個伺服器（叢集節點）和單一子網使用單一 NIC。 Azure 網路有實體冗余，因此 Azure 虛擬機器來賓叢集上不需要額外的 Nic 和子網。 叢集驗證報告會警告您節點只能在單一網路上觸達。 您可以在 Azure 虛擬機器來賓容錯移轉叢集上忽略此警告。

## <a name="quorum"></a>Quorum

雖然兩個節點的叢集在沒有[仲裁資源](/windows-server/storage/storage-spaces/understand-quorum)的情況下可運作，但客戶絕對需要使用仲裁資源來提供生產支援。 叢集驗證不會傳遞任何沒有仲裁資源的叢集。 

在技術上，有三個節點的叢集在沒有仲裁資源的情況下，可能會在單一節點遺失（向下到兩個節點）時存活。 但是在叢集停機到兩個節點之後，有可能會有執行的風險： 

- **空間中的磁碟分割**（split 大腦）：由於伺服器、NIC 或交換器問題，叢集節點會在網路上分開。 
- **時間分割**（amnesia）：節點會加入或重新加入叢集，並嘗試不適當地宣告叢集群組或叢集角色的擁有權。 

仲裁資源會針對這些問題的其中一個來保護叢集。 

若要在 Azure Vm 上以 SQL Server 設定仲裁資源，您可以使用下列見證類型： 


||[磁碟見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[雲端見證](/windows-server/failover-clustering/deploy-cloud-witness)  |[檔案共用見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**支援的 OS**| 全部 |Windows Server 2016+| Windows Server 2012 +|
|**支援的 SQL Server 版本**|SQL Server 2019|SQL Server 2016 +|SQL Server 2016 +|



### <a name="disk-witness"></a>磁碟見證

磁片見證是叢集可用儲存群組中的小型叢集磁片。 此磁片具有高度可用性，而且可以在節點之間進行故障切換。 它包含叢集資料庫的複本，其預設大小通常小於 1 GB。 

將 Azure 共用磁片設定為磁片見證。 

若要開始使用，請參閱[設定磁片見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。


**支援的作業系統**：全部    
**支援的 SQL 版本**： SQL Server 2019   


### <a name="cloud-witness"></a>雲端見證

雲端見證是一種容錯移轉叢集仲裁見證，它會使用 Microsoft Azure 來提供叢集仲裁的投票。 預設大小約為 1 MB，且只包含時間戳記。 雲端見證適用于多個網站、多個區域和多個區域中的部署。

若要開始使用，請參閱[設定雲端見證](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)。


**支援的作業系統**： Windows Server 2016 和更新版本   
**支援的 SQL 版本**： SQL Server 2016 和更新版本     


### <a name="file-share-witness"></a>檔案共用見證

「檔案共用見證」是 SMB 檔案共用，通常是在執行 Windows Server 的檔案伺服器上設定。 它會在見證記錄檔中維護叢集資訊，但不會儲存叢集資料庫的複本。 在 Azure 中，您可以將 Azure 檔案[共用](../../../storage/files/storage-how-to-create-file-share.md)設定為做為檔案共用見證使用，或者您也可以在個別的虛擬機器上使用檔案共用。

如果您要使用另一個 Azure 檔案共用，您可以使用用來[掛接 Premium 檔案共用](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share)的相同程式來掛接它。 

若要開始使用，請參閱[設定檔案共用見證](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。


**支援的作業系統**： Windows Server 2012 和更新版本   
**支援的 SQL 版本**： SQL Server 2016 和更新版本   


## <a name="connectivity"></a>連線能力

在傳統的內部部署網路環境中，SQL Server 容錯移轉叢集實例似乎是單一電腦上執行的單一 SQL Server 實例。 由於容錯移轉叢集實例會從節點容錯移轉到節點，因此實例的虛擬網路名稱（VNN）會提供統一的連接點，並允許應用程式連接到 SQL Server 實例，而不需要知道哪一個節點目前為使用中狀態。 發生容錯移轉時，虛擬網路名稱會在啟動後向新的作用中節點註冊。 此程式對連接到 SQL Server 的用戶端或應用程式而言是透明的，這可讓用戶端或應用程式在失敗期間的停機時間降到最低。 

使用具有 Azure Load Balancer 或分散式網路名稱（DNN）的 VNN，將流量路由傳送到容錯移轉叢集實例的 VNN，以及 Azure Vm 上的 SQL Server。 DNN 功能目前僅適用于 Windows Server 2016 （或更新版本）虛擬機器上的 SQL Server 2019 CU2 和更新版本。 

下表比較 HADR 連接支援性： 

| |**虛擬網路名稱 (VNN)**  |**分散式網路名稱 (DNN)**  |
|---------|---------|---------|
|**最低 OS 版本**| Windows Server 2012 | Windows Server 2016|
|**最低 SQL Server 版本** |SQL Server 2012 |SQL Server 2019 CU2|
|**支援的 HADR 解決方案** | 容錯移轉叢集執行個體 <br/> 可用性群組 | 容錯移轉叢集執行個體|


### <a name="virtual-network-name-vnn"></a>虛擬網路名稱 (VNN)

因為虛擬 IP 存取點在 Azure 中的運作方式不同，所以您需要設定[Azure Load Balancer](../../../load-balancer/index.yml) ，以將流量路由傳送至 FCI 節點的 IP 位址。 在 Azure 虛擬機器中，負載平衡器會保留叢集 SQL Server 資源所依賴之 VNN 的 IP 位址。 負載平衡器會散發抵達前端的輸入流程，然後將流量路由傳送至後端集區所定義的實例。 您可以使用負載平衡規則和健康情況探查來設定流量流程。 有了 SQL Server FCI，後端集區實例就是執行 SQL Server 的 Azure 虛擬機器。 

當您使用負載平衡器時，會有些許的容錯移轉延遲，因為健康狀態探查預設會每隔10秒執行一次運作檢查。 

若要開始使用，請瞭解如何[設定 FCI 的 Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md)。 

**支援的作業系統**： Windows Server 2012 和更新版本   
**支援的 SQL 版本**： SQL Server 2012 和更新版本   
**支援的 HADR 解決方案**：容錯移轉叢集實例和可用性群組 


### <a name="distributed-network-name-dnn"></a>分散式網路名稱 (DNN)

分散式網路名稱是新的 Azure 功能，適用于 SQL Server 2019 CU2。 DNN 提供另一種方式，讓 SQL Server 用戶端不需使用負載平衡器，就能連接到 SQL Server 容錯移轉叢集實例。 

建立 DNN 資源時，叢集會將 DNS 名稱與叢集中所有節點的 IP 位址系結。 SQL 用戶端會嘗試連接到這份清單中的每個 IP 位址，以找出容錯移轉叢集實例目前執行所在的節點。 您可以 `MultiSubnetFailover=True` 在連接字串中指定，以加速此程式。 此設定會指示提供者以平行方式嘗試所有 IP 位址，讓用戶端可以立即連線到 FCI。 

建議您在負載平衡器上使用分散式網路名稱，原因可能是： 
- 端對端解決方案較健全，因為您不再需要維護負載平衡器資源。 
- 排除負載平衡器探查可將容錯移轉期間降至最低。 
- DNN 會使用 Azure Vm 上的 SQL Server，簡化容錯移轉叢集實例的布建和管理。 

大部分的 SQL Server 功能都會以透明的方式與 FCI 搭配使用。 在這些情況下，您可以直接將現有的 VNN DNS 名稱取代為 DNN DNS 名稱，或使用現有的 VNN DNS 名稱來設定 DNN 值。 不過，有些伺服器端元件需要將 VNN 名稱對應至 DNN 名稱的網路別名。 特定案例可能需要明確使用 DNN DNS 名稱，例如當您在伺服器端設定中定義特定 Url 時。 

若要開始使用，請瞭解如何[設定 FCI 的 DNN 資源](hadr-distributed-network-name-dnn-configure.md)。 

**支援的作業系統**： Windows Server 2016 和更新版本   
**支援的 SQL 版本**： SQL Server 2019 和更新版本   
**支援的 HADR 解決方案**：僅限容錯移轉叢集實例


## <a name="limitations"></a>限制

當您使用 FCI 或可用性群組，以及在 Azure 虛擬機器上 SQL Server 時，請考慮下列限制。 

### <a name="msdtc"></a>MSDTC 
Azure 虛擬機器支援 Windows Server 2019 上的 Microsoft 分散式交易協調器（MSDTC）與叢集共用磁片區（CSV）和[Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)上的儲存體。

在 Azure 虛擬機器上，Windows Server 2016 或更早版本不支援 MSDTC，因為：

- 叢集 MSDTC 資源無法設為使用共用儲存體。 若在 Windows Server 2016 上建立 MSDTC 資源，即使有儲存體可用，系統也不會顯示任何可用的共用儲存體。 Windows Server 2019 中已修正此問題。
- 基本負載平衡器不處理 RPC 連接埠。


## <a name="next-steps"></a>後續步驟

在您為解決方案決定適當的最佳做法之後，請從[準備 SQL SERVER VM 以進行 FCI](failover-cluster-instance-prepare-vm.md)開始。 您也可以使用[Azure CLI](availability-group-az-cli-configure.md)或[Azure 快速入門範本](availability-group-quickstart-template-configure.md)來建立可用性群組。 

