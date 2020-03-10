---
title: Azure 上的 IBM DB2 pureScale
description: 在此文章中，我們會顯示用於在 Azure 上執行 IBM DB2 pureScale 環境的架構。
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: d8309a69c9c38610fa7bea3fee202a60d836980c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945060"
---
# <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM DB2 pureScale

IBM DB2 pureScale 環境提供適用於 Azure 的資料庫叢集，可在 Linux 作業系統上提供高可用性與延展性。 本文顯示在 Azure 上執行 DB2 pureScale 的架構。

## <a name="overview"></a>概觀

企業有長時間使用傳統關係資料庫管理系統（RDBMS）平臺來滿足其線上交易處理（OLTP）需求。 如今，有許多企業都已將其大型主機型資料庫環境移轉到 Azure 做為延伸處理能力、降低成本及維持穩定之作業成本結構的方式。 移轉通常是將傳統平台現代化的第一個步驟。 

在近期，企業客戶會將其在 z/OS 上執行的 IBM DB2 環境重新裝載到 Azure 上的 IBM DB2 pureScale。 Db2 pureScale 資料庫叢集解決方案可在 Linux 作業系統上提供高可用性和擴充性。 客戶在安裝 Db2 pureScale 之前，已順利執行 Db2，做為在 Azure 上大規模系統中的單一虛擬機器（VM）上的獨立、相應增加實例。 

Linux 上的 IBM DB2 pureScale 雖然與原始環境不同，但針對在大型主機上之 Parallel Sysplex 設定中執行的 z/OS 提供與 IBM DB2 類似的高可用性與延展性。 在此案例中，叢集會透過 iSCSI 連線到共用存放裝置叢集。 我們使用了 GlusterFS 檔案系統，這是一套免費、可擴充、開放原始碼的分散式檔案系統，專為雲端儲存體優化。 不過，IBM 已不再支援此解決方案。 若要維護 IBM 的支援，您必須使用支援的 iSCSI 相容檔案系統。 Microsoft 提供儲存空間直接存取（S2D）做為選項

此文章說明用於此 Azure 移轉的架構。 客戶使用 Red Hat Linux 7.4 來測試設定。 您可以從 Azure Marketplace 取得此版本。 選擇 Linux 發行版本之前，請務必確認目前支援的版本。 如需詳細資訊，請參閱 [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) 與 [GlusterFS](https://docs.gluster.org/en/latest/) 文件 (英文)。

此文章是您 DB2 實作計畫的起點。 您的商業需求將會不同，但相同的模式皆適用。 您也可以將此架構模式用於 Azure 上的線上分析處理 (OLAP) 應用程式。

此文章不涵蓋從適用於 z/OS 的 IBM DB2 資料庫移轉到 Linux 上執行之 IBM DB2 pureScale 的差異與可能的移轉工作。 它也不提供從 DB2 z/OS 移動到 DB2 pureScale 的大小估計與工作負載分析。 

為協助您判斷適用於您環境的最佳 DB2 pureScale 架構，我們建議您完整地估計大小，並提出假設。 在來源系統上，務必考慮 DB2 z/OS Parallel Sysplex 搭配使用資料共用架構、耦合工具 (Coupling Facility) 組態，及分散式資料設備 (DDF) 使用狀況統計資料。

> [!NOTE]
> 此文章說明進行 DB2 移轉的其中一個方法，但還有其他方法。 例如，DB2 pureScale 也可以在虛擬內部部署環境中執行。 IBM 在各種設定中支援 Microsoft Hyper-V 上的 DB2。 如需詳細資訊，請參閱 IBM 知識中心中的 [DB2 pureScale 虛擬化架構](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) \(英文\)。

## <a name="architecture"></a>架構

為在 Azure 上支援高可用性與延展性，您可以為 DB2 pureScale 使用相應放大的共用資料結構。 客戶移轉使用下列範例架構。

![在顯示儲存體和網路功能的 Azure 虛擬機器上的 DB2 pureScale](media/db2-purescale-on-azure/pureScaleArchitecture.png "在顯示儲存體和網路功能的 Azure 虛擬機器上的 DB2 pureScale")


此圖說明 DB2 pureScale 叢集所需的邏輯層。 這些包括適用於用戶端、適用於管理、適用於快取、適用於資料庫引擎與適用於共用儲存體的虛擬機器。 

除了資料庫引擎節點之外，此圖也包括兩個用於叢集快取工具 (CF) 的節點。 資料庫引擎本身至少會使用兩個節點。 屬於 pureScale 叢集的 DB2 伺服器稱為成員。 

叢集會透過 iSCSI 連線到三個節點的共用儲存體叢集，以提供向外延展儲存體和高可用性。 DB2 pureScale 是安裝在執行 Linux 的 Azure 虛擬機器上。

此方法是一個範本，您可以針對您組織的大小與規模修改此範本。 此方法依據：

-   兩個或多個資料庫成員會與至少兩個 CF 節點合併。 這兩個節點會管理全域緩衝區集區 (GBP)，以讓共用記憶體與全域鎖定管理員 (GLM) 服務控制共用存取並鎖定來自作用中成員的內容。 一個 CF 節點做為主要，而另一個節點做為次要容錯移轉 CF 節點。 為避免環境中有單一失敗點，DB2 pureScale 叢集至少需要四個節點。

-   高效能共用儲存體 (在圖中以 P30 大小顯示)。 每個節點都會使用此儲存體。

-   適用於資料成員與共用儲存體的高效能網路功能。

### <a name="compute-considerations"></a>計算考量

此架構會在 Azure 虛擬機器上執行應用程式、儲存體與資料層。 [部署安裝程式指令碼](https://aka.ms/db2onazure)會建立下列項目：

-   DB2 pureScale 叢集。 您在 Azure 上需要的計算資源類型取決於您的安裝程式。 一般而言，您可以使用下列兩種方式：

    -   使用多節點、高效能計算 (HPC) 樣式的網路，在這裡小型到中型執行個體會存取共用儲存體。 針對設定的此 HPC 類型，Azure 記憶體最佳化 E 系列或儲存體最佳化 L 系列[虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)提供所需的計算資源。

    -   為資料引擎使用較少的大型虛擬機器執行個體。 對於大型執行個體，最大記憶體最佳化的 [M 系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)虛擬機器適用於大量的記憶體內工作負載。 視用於執行 DB2 的邏輯分割 (LPAR) 大小而定，您可能會需要專用的執行個體。

-   DB2 CF 使用記憶體最佳化虛擬機器，例如 E 系列或 L 系列。

-   共用存放裝置叢集，使用執行 Linux 的 Standard\_DS4\_v2 虛擬機器。

-   管理 jumpbox 是執行 Linux\_v2 虛擬機器的標準\_DS2。  替代方案是 Azure 防禦，這是一項服務，可為您虛擬網路中的所有 Vm 提供安全的 RDP/SSH 體驗。

-   用戶端是執行 v 的標準 \_DS3\_v2 虛擬機器 (用於測試)。

-   *選擇性*。 見證伺服器。 只有特定的舊版 Db2 pureScale 才需要此項。 此範例使用執行 Linux 的標準\_DS3\_v2 虛擬機器（用於 DB2 pureScale）。

> [!NOTE]
> DB2 pureScale 叢集需要至少兩個 DB2 執行個體。 此外也需要快取執行個體與鎖定管理員執行個體。

### <a name="storage-considerations"></a>儲存體考量

就像 Oracle RAC 一樣，DB2 pureScale 是高效能區塊 I/O、相應放大資料庫。 我們建議您使用符合您需求的最大 [Azure 進階 SSD](disks-types.md) 選項。 較小的儲存體選項可能適用於開發及測試環境，而生產環境通常需要較大的儲存體容量。 範例架構因為 [P30](https://azure.microsoft.com/pricing/details/managed-disks/) 的 IOPS 速率與大小和價格的原因而使用它。 不論大小為何，使用進階儲存體都能獲得最佳效能。

DB2 pureScale 使用共用所有項目架構，其中資料可供所有叢集節點存取。 Premium 儲存體必須在多個實例之間共用，不論是視需要或在專用實例上。

大型的 DB2 pureScale 叢集會需要 200 TB 以上的進階共用儲存體 (具有 100,000 的 IOPS)。 DB2 pureScale 支援您可在 Azure 上使用的 iSCSI 區塊介面。 ISCSI 介面需要共用儲存體叢集，您可以使用 S2D 或其他工具來執行此叢集。 此類型的解決方案會在 Azure 中建立虛擬儲存區域網路 (vSAN) 裝置。 DB2 pureScale 會使用 vSAN 來安裝用來在虛擬機器之間共用資料的叢集檔案系統。

### <a name="networking-considerations"></a>網路考量

IBM 會針對 DB2 pureScale 叢集中的所有成員建議 InfiniBand 網路功能。 DB2 pureScale 也會為 CF 使用遠端直接記憶體存取 (RDMA) (若可用)。

在設定期間，您會建立 Azure [資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)以包含所有虛擬機器。 一般來說，您會根據資源的存留期以及將管理資源的人員來群組資源。 此架構中的虛擬機器需要[加速網路功能](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) \(英文\)。 這個 Azure 功能可透過單一根目錄 I/O 虛擬化 (SR-IOV) 提供一致的超低網路延遲給虛擬機器。

每部 Azure 虛擬機器都是部署到有子網路的虛擬網路中：主要、Gluster FS 前端 (gfsfe)、Gluster FS 後端 (bfsbe)、DB2 pureScale (db2be) 與 DB2 pureScale 前端 (db2fe)。 安裝指令碼也會在主要子網路中的虛擬機器上建立主要 [NIC](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)。

使用[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)限制虛擬網路內的網路流量並隔離不同的子網路。

在 Azure，DB2 pureScale 必須使用 TCP/IP 做為儲存體的網路連線。

## <a name="next-steps"></a>後續步驟

-   [在 Azure 上部署此架構](deploy-ibm-db2-purescale-azure.md)
