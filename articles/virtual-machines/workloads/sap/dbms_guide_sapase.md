---
title: 適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署 | Microsoft Docs
description: 適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 007e8d87c670376ad334c1c4e58fd93995930b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616244"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署

本文介紹在 Azure IaaS 中部署 SAP ASE 時，幾個要考量到的地方。 在閱讀本文件之前，您應該先參閱[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)文件，以及 [Azure 上的 SAP 工作負載文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)中的其他指南。 本文檔介紹在 Linux 和 Windows 作業系統上運行的 SAP ASE。 Azure 上受支援的最低版本是 SAP ASE 16.0 修補程式級別 2。  建議部署最新版本的 SAP 和最新的修補程式級別。  建議至少使用 SAP ASE 16.3 修補程式級別 7。  SAP 的最新版本可在[目標 ASE 16.0 發佈計畫和 CR 清單資訊](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)中找到。

除了在以下位置的 SAP 產品可用性矩陣中，請參閱有關 SAP 應用程式或安裝介質位置的發佈支援的其他資訊：

- [SAP 支援說明#2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP 支援說明#1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP 支援說明#1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP 支援說明#1973241](https://launchpad.support.sap.com/#/notes/1973241)

備註：在整個 SAP 世界內外的文檔中，產品名稱被引用為 Sybase ASE 或 SAP ASE，或者在某些情況下同時引用兩者。 為了保持一致，我們在本文檔中使用 SAP **ASE**的名稱。

## <a name="operating-system-support"></a>作業系統支援
SAP 產品可用性矩陣包含每個 SAP 應用程式支援的作業系統和 SAP 內核組合。  完全支援 Linux 發行版本 SUSE 12.x、SUSE 15.x、紅帽 7.x。  甲骨文 Linux 作為 SAP ASE 的作業系統不受支援。  建議使用最新的 Linux 版本。 Windows 客戶應使用 Windows 伺服器 2016 或 Windows 伺服器 2019 版本。  Windows 2012 等舊版本在技術上是受支援的，但始終建議使用最新的 Windows 版本。


## <a name="specifics-to-sap-ase-on-windows"></a>Windows 上 SAP ASE 專屬的詳細資料
從 Microsoft Azure 開始，您即可將現有的 SAP ASE 應用程式遷移至 Azure 虛擬機器。 Azure 虛擬機器中的 SAP ASE 可讓您輕鬆地將這些應用程式移轉到 Microsoft Azure，藉以減少部署、管理和維護企業級應用程式的擁有權總成本。 透過 Azure 虛擬機器中的 SAP ASE，系統管理員和開發人員仍然可以使用可在內部部署使用的相同開發和管理工具。

Microsoft Azure 提供許多不同的虛擬機器類型，可讓您執行最小的 SAP 系統和架構，以至具有數千個使用者的大型 SAP 系統和架構。 SAP 調整不同 SAP 認證 VM SKU 的 SAPS 編號，#1928533 在[SAP 支援說明](https://launchpad.support.sap.com/#/notes/1928533)中提供。

在 Windows 上安裝 SAP ASE 的文檔可在[適用于 Windows 的 SAP ASE 安裝指南](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)中找到

鎖定記憶體中的頁面是一種設置，它可以防止 SAP ASE 資料庫緩衝區被分頁。 此設置對於記憶體多的大型繁忙系統非常有用。 有關詳細資訊，請聯繫 BC-DB-SYB。 


## <a name="linux-operating-system-specific-settings"></a>Linux 作業系統特定設置
在 Linux VM`saptune`上，預設情況下應啟用具有設定檔 SAP-ASE Linux 大頁面，並且可以使用命令進行驗證  

`cat /proc/meminfo` 

頁面大小通常為 2048 KB。 有關詳細資訊，請參閱[Linux 上的文章"大頁面](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html)" 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>有關 SAP ASE 部署的 VM 和磁片結構的建議

SAP NetWeaver 應用程式 SAP ASE 支援 SAP[支援說明](https://launchpad.support.sap.com/#/notes/1928533)中列出的任何 VM 類型#1928533用於中型 SAP ASE 資料庫伺服器的典型 VM 類型包括 Esv3。  大型多位元組資料庫可以利用 M 系列 VM 類型。 通過啟用 M 系列寫入加速器，可以改進 SAP ASE 事務日誌磁片寫入性能。 由於 SAP ASE 執行日誌寫入的方式，應謹慎使用 SAP ASE 測試寫入加速器。  [查看 SAP 支援說明#2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)並考慮運行效能測試。  
寫入加速器專為事務日誌磁片而設計。 磁片級別緩存應設置為 NONE。 如果 Azure 寫入加速器未顯示與其他 DBMS 類似的改進，請不要感到驚訝。 根據 SAP ASE 寫入事務日誌的方式，Azure 寫入加速器可能很少或根本沒有加速。
建議為數據設備和日誌設備單獨使用磁片。  系統資料庫是 syb`saptools`安全性的，不需要專用磁片，可以放置在包含 SAP 資料庫資料和日誌設備的磁片上 

![SAP ASE 的存儲配置](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>檔案系統、條帶大小& IO 平衡 
SAP ASE 將資料按順序寫入磁片存放裝置，除非另有配置。 這意味著具有四個設備的空 SAP ASE 資料庫將僅將資料寫入第一個設備。  僅當第一個設備已滿時，其他磁片設備才會寫入。  每個 SAP ASE 設備的讀取和寫入 IO 量可能不同。 為了平衡所有可用 Azure 磁片的磁片 IO，需要使用 Windows 存儲空間或 Linux LVM2。 在 Linux 上，建議使用 XFS 檔案系統格式化磁片。 應通過效能測試測試 LVM 條帶大小。 128 KB 條帶大小是一個很好的起點。 在 Windows 上，應測試 NTFS 分配單位大小 （AUS）。 64 KB 可用作起始值。 

建議配置自動資料庫擴展，如 #1815695[配置 SAP 自我調整伺服器企業中自動資料庫空間擴展](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)和 SAP[支援說明](https://launchpad.support.sap.com/#/notes/1815695)中所述。 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>在 Azure 虛擬機器、磁片和檔案系統配置上對 SAP ASE 進行示例 
下面的範本顯示了 Linux 和 Windows 的示例配置。 在確認虛擬機器和磁片配置之前，請確保單個 VM 的網路和存儲頻寬配額足以滿足業務需求。 還要記住，不同的 Azure VM 類型具有可附加到 VM 的不同最大磁片數。 例如，E4s_v3 VM 具有 48 MB/秒存儲 IO 輸送量的限制。 如果資料庫備份活動所需的存儲輸送量超過 48 MB/秒，則更大的 VM 類型具有更高的存儲頻寬輸送量是不可避免的。 配置 Azure 存儲時，還需要記住，特別是使用[Azure 高級存儲](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance)時，輸送量和 IOPS 每 GB 容量確實會發生變化。 請參閱本文中有關此主題[的詳細資訊，Azure 中有哪些磁片類型可用？](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) 特定 Azure VM 類型的配額記錄在文章["記憶體優化的虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/sizes-memory)"和連結到它的文章中。 

> [!NOTE]
>  如果 DBMS 系統從本地移動到 Azure，建議對 VM 執行監視並評估 CPU、記憶體、IOPS 和存儲輸送量。 將觀察到的峰值值與上述文章中記錄的 VM 配額限制進行比較

下面給出的示例是為了說明目的，可以根據個人需要加以修改。 由於 SAP ASE 的設計，資料設備的數量不如其他資料庫重要。 本文檔中詳述的資料設備數量僅是指南。 

資料庫大小介於 50 GB 和 250 GB 的小型 SAP ASE 資料庫伺服器（如 SAP 解決方案管理器）的配置示例可能類似于

| 組態 | Windows | Linux | 註解 |
| --- | --- | --- | --- |
| VM 類型 | E4s_v3 （4 vCPU/32 GB 記憶體） | E4s_v3 （4 vCPU/32 GB 記憶體） | --- |
| 加速網路 | 啟用 | 啟用 | ---|
| SAP ASE 版本 | 16.3 PL 7 或更高 | 16.3 PL 7 或更高 | --- |
| 資料設備 * | 4 | 4 | ---|
| 日誌設備 * | 1 | 1 | --- |
| 溫度設備 * | 1 | 1 | 更多用於 SAP BW 工作負載 |
| 作業系統 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 或 RHEL 7.6 | --- |
| 磁片聚合 | 儲存空間 | LVM2 | --- |
| 檔案系統 | NTFS | Xfs |
| 格式塊大小 | 需要工作負載測試 | 需要工作負載測試 | --- |
| * 和類型的資料磁片 | 高級存儲：2 x P10 （RAID0） | 高級存儲：2 x P10 （RAID0）| 緩存 = 唯讀 |
| 日誌磁片的 * 和類型 | 高級存儲：1 x P20  | 高級存儲：1 x P20 | 緩存 = 無 |
| ASE 最大記憶體參數 | 90% 的物理 RAM | 90% 的物理 RAM | 假設單個實例 |
| 備份裝置 * | 4 | 4| --- |
| 備份磁片的 * 和類型 | 1 | 1 | --- |


資料庫大小介於 250 GB 和 750 GB 的中型 SAP ASE 資料庫伺服器（如較小的 SAP 業務套件系統）的配置示例可能類似于

| 組態 | Windows | Linux | 註解 |
| --- | --- | --- | --- |
| VM 類型 | E16s_v3 （16 vCPU/128 GB 記憶體） | E16s_v3 （16 vCPU/128 GB 記憶體） | --- |
| 加速網路 | 啟用 | 啟用 | ---|
| SAP ASE 版本 | 16.3 PL 7 或更高 | 16.3 PL 7 或更高 | --- |
| 資料設備 * | 8 | 8 | ---|
| 日誌設備 * | 1 | 1 | --- |
| 溫度設備 * | 1 | 1 | 更多用於 SAP BW 工作負載 |
| 作業系統 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 或 RHEL 7.6 | --- |
| 磁片聚合 | 儲存空間 | LVM2 | --- |
| 檔案系統 | NTFS | Xfs |
| 格式塊大小 | 需要工作負載測試 | 需要工作負載測試 | --- |
| * 和類型的資料磁片 | 高級存儲：4 x P20 （RAID0） | 高級存儲：4 x P20 （RAID0）| 緩存 = 唯讀 |
| 日誌磁片的 * 和類型 | 高級存儲：1 x P20  | 高級存儲：1 x P20 | 緩存 = 無 |
| ASE 最大記憶體參數 | 90% 的物理 RAM | 90% 的物理 RAM | 假設單個實例 |
| 備份裝置 * | 4 | 4| --- |
| 備份磁片的 * 和類型 | 1 | 1 | --- |

資料庫大小介於 750 GB 和 2000 GB 的小型 SAP ASE 資料庫伺服器（如較大的 SAP 業務套件系統）的配置示例可能類似于

| 組態 | Windows | Linux | 註解 |
| --- | --- | --- | --- |
| VM 類型 | E64s_v3 （64 vCPU/432 GB 記憶體） | E64s_v3 （64 vCPU/432 GB 記憶體） | --- |
| 加速網路 | 啟用 | 啟用 | ---|
| SAP ASE 版本 | 16.3 PL 7 或更高 | 16.3 PL 7 或更高 | --- |
| 資料設備 * | 16 | 16 | ---|
| 日誌設備 * | 1 | 1 | --- |
| 溫度設備 * | 1 | 1 | 更多用於 SAP BW 工作負載 |
| 作業系統 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 或 RHEL 7.6 | --- |
| 磁片聚合 | 儲存空間 | LVM2 | --- |
| 檔案系統 | NTFS | Xfs |
| 格式塊大小 | 需要工作負載測試 | 需要工作負載測試 | --- |
| * 和類型的資料磁片 | 高級存儲：4 x P30 （RAID0） | 高級存儲：4 x P30 （RAID0）| 緩存 = 唯讀 |
| 日誌磁片的 * 和類型 | 高級存儲：1 x P20  | 高級存儲：1 x P20 | 緩存 = 無 |
| ASE 最大記憶體參數 | 90% 的物理 RAM | 90% 的物理 RAM | 假設單個實例 |
| 備份裝置 * | 4 | 4| --- |
| 備份磁片的 * 和類型 | 1 | 1 | --- |


資料庫大小為 2 TB+的小型 SAP ASE 資料庫伺服器（如全球使用的較大 SAP 業務套件系統）的配置示例可能類似于

| 組態 | Windows | Linux | 註解 |
| --- | --- | --- | --- |
| VM 類型 | M 系列（1.0 至 4.0 TB RAM）  | M 系列（1.0 至 4.0 TB RAM） | --- |
| 加速網路 | 啟用 | 啟用 | ---|
| SAP ASE 版本 | 16.3 PL 7 或更高 | 16.3 PL 7 或更高 | --- |
| 資料設備 * | 32 | 32 | ---|
| 日誌設備 * | 1 | 1 | --- |
| 溫度設備 * | 1 | 1 | 更多用於 SAP BW 工作負載 |
| 作業系統 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 或 RHEL 7.6 | --- |
| 磁片聚合 | 儲存空間 | LVM2 | --- |
| 檔案系統 | NTFS | Xfs |
| 格式塊大小 | 需要工作負載測試 | 需要工作負載測試 | --- |
| * 和類型的資料磁片 | 高級存儲：4+ x P30 （RAID0） | 高級存儲：4+ x P30 （RAID0）| 緩存 = 唯讀，考慮 Azure 超級磁片 |
| 日誌磁片的 * 和類型 | 高級存儲：1 x P20  | 高級存儲：1 x P20 | 緩存 = 無，請考慮 Azure 超磁片 |
| ASE 最大記憶體參數 | 90% 的物理 RAM | 90% 的物理 RAM | 假設單個實例 |
| 備份裝置 * | 16 | 16 | --- |
| 備份磁片的 * 和類型 | 4 | 4 | 使用 LVM2/存儲空間 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>備份& Azure 上 SAP ASE 的還原注意事項
增加資料和備份裝置的數量可提高備份和恢復性能。 建議將託管 SAP ASE 備份裝置的 Azure 磁片條帶，如前面所示的表所示。 應注意平衡備份裝置和磁片的數量，並確保備份輸送量不應超過 VM 輸送量配額總數的 40%-50%。 建議將 SAP 備份壓縮作為預設值。 更多詳細資訊請參閱文章：

- [SAP 支援說明#1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP 支援說明#1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP 支援說明#1585981](https://launchpad.support.sap.com/#/notes/1585981) 

請勿使用磁碟機 D：*或 /temp 空間作為資料庫或日誌轉儲目標。

### <a name="impact-of-database-compression"></a>資料庫壓縮的影響
在 I/O 頻寬可能成為限制因素的配置中，減少 IOPS 的度量值可能有助於擴展在 IaaS 方案中（如 Azure）中運行的工作負荷。 因此，建議您在將現有的 SAP 資料庫上傳至 Azure 之前，確定已使用 SAP ASE 壓縮。

在上傳至 Azure 之前套用壓縮的建議是出自下列數個因素︰

* 降低要上傳至 Azure 的資料量
* 假設您可以使用功能更強大的硬體，其在內部部署中使用更多 CPU 或更高 I/O 頻寬或更少 I/O 延遲，則執行壓縮的持續時間較短
* 較小的資料庫大小可能會使磁碟配置的成本降低

資料壓縮和 LOB 壓縮可以在 Azure 虛擬機器上裝載的 VM 中運作，如同它在內部部署中的運作方式。 有關如何檢查壓縮是否已在現有 SAP ASE 資料庫中使用的詳細資訊，請查看[SAP 支援說明 1750510](https://launchpad.support.sap.com/#/notes/1750510)。 有關 SAP ASE 資料庫壓縮的更多詳細資訊，請查看[SAP 支援說明#2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Azure 上的 SAP ASE 高可用性 
HADR 使用者指南詳細介紹了 2 節點 SAP ASE"始終打開"解決方案的設置和配置。  此外，還支援第三個災害復原節點。 SAP ASE 支援許多高可用配置，包括共用磁片和本機作業系統群集（浮動 IP）。 Azure 上唯一受支援的配置是使用沒有浮動 IP 的故障管理器。  浮動 IP 位址方法在 Azure 上不起作用。  SAP 內核是一個"HA 感知"應用程式，瞭解主和輔助 SAP ASE 伺服器。 SAP ASE 和 Azure 之間沒有緊密的集成，不使用 Azure 內部負載等化器。 因此，應遵循標準的 SAP ASE 文檔，從[SAP ASE HADR 使用者指南開始](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> Azure 上唯一受支援的配置是使用沒有浮動 IP 的故障管理器。  浮動 IP 位址方法在 Azure 上不起作用。 

### <a name="third-node-for-disaster-recovery"></a>災害復原的第三個節點
除了將 SAP ASE 始終打開用於本地高可用性之外，您可能希望將配置擴展到另一個 Azure 區域中的非同步複製節點。 有關此類方案的文檔可[在此處](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)找到。

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE 資料庫加密& SSL 
SAP 軟體預配管理器 （SWPM） 提供了在安裝期間加密資料庫的選項。  如果要使用加密，建議使用 SAP 完整資料庫加密。  請參閱記錄在：

- [SAP 支援說明#2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP 支援說明#2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP 支援說明#2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP 支援說明#2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> 如果 SAP ASE 資料庫已加密，則備份轉儲壓縮將不起作用。 另請參閱[SAP 支援說明#2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Azure 部署檢查表上的 SAP ASE
 
- 部署 SAP ASE 16.3 PL7 或更高版本
- 更新到故障管理器和 SAPHostAgent 的最新版本和修補程式
- 部署在最新的認證作業系統上，如 Windows 2019、Suse 15.1 或 Redhat 7.6 或更高版本
- 使用 SAP 認證 VM – 建議使用高記憶體 Azure VM SKU，如Es_v3或 x 大型系統 M 系列 VM SKU
- 將 VM 的磁片 IOPS 和 VM 總聚合輸送量配額與磁片設計相匹配。  部署足夠數量的磁片
- 使用 Windows 存儲空間或 Linux LVM2 聚合具有正確條帶大小和檔案系統的磁片
- 為數據、日誌、臨時和備份目的創建足夠數量的設備
- 考慮將超磁片用於 X 大型系統 
- 在`saptune`Linux 作業系統上運行 SAP-ASE 
- 使用資料庫加密保護資料庫 – 在 Azure 金鑰保存庫中手動存儲金鑰 
- 在[Azure 檢查表上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)完成 SAP 
- 配置記錄備份和完整備份 
- 測試 HA/DR、備份和恢復以及執行壓力&體積測試 
- 確認自動資料庫擴展工作 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>使用 DBACockpit 監視資料庫實例
針對使用 SAP ASE 作為資料庫平台的 SAP 系統，可以存取 DBACockpit 作為交易 DBACockpit 中內嵌的瀏覽器視窗或作為 Webdynpro。 但是，監視和管理資料庫的全部功能僅在 DBACockpit 的 Webdynpro 實現中可用。

做為內部部署系統，需要使用數個步驟，才能啟用 DBACockpit 之 Webdynpro 實作所使用的所有 SAP NetWeaver 功能。 按照[SAP 支援說明#1245200，](https://launchpad.support.sap.com/#/notes/1245200)啟用 Webdynpros 的使用並生成所需的網路。 按照上述說明的說明操作時，您還可以配置 Internet 通信管理器 （）`ICM`以及用於 HTTP 和 HTTPs 連接的埠。 Http 的預設設定看起來如下︰

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

此外，在交易 DBACockpit 中產生的連結看起來如下︰

> HTTPs：\//\<完全限定的主機名稱>：44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> HTTP：\//\<完全限定的主機名稱>：8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

取決於裝載 SAP 系統的 Azure 虛擬機器是否連線到您的 AD 和 DNS，您需要確定 ICM 會使用完整的主機名稱，此名稱可在您嘗試從中開啟 DBACockpit 的電腦上加以解析。 請參閱[SAP 支援說明#773830](https://launchpad.support.sap.com/#/notes/773830)以瞭解 ICM 如何根據設定檔參數確定完全限定的主機名稱，並在必要時顯式設置參數 icm/host_name_full。

如果在僅雲方案中部署了 VM，而本地和 Azure 之間沒有跨界連接，則需要定義公共 IP 位址和 。 `domainlabel` 然後 VM 的公用 DNS 名稱格式如下所示︰

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

有關 DNS 名稱的更多詳細資訊，請參閱 [此處][虛擬機器-azurerm-對-azuresm]。

將 SAP 設定檔參數 icm/host_name_full 設定為 Azure VM 的 DNS 名稱，其連結看起來可能如下︰

> HTTPs：\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> HTTP：\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

在此情況下，您需要確定︰

* 在 Azure 入口網站中，針對用來與 ICM 通訊的 TCP/IP 連接埠，將輸入規則新增至網路安全性群組
* 針對用來與 ICM 通訊的 TCP/IP 連接埠，將輸入規則新增至 Windows 防火牆組態

針對自動匯入所有可用修正的功能，建議定期套用適用於您 SAP 版本的修正集合 SAP 附註︰

* [SAP 支援說明#1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP 支援說明#1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP 支援說明#1882376](https://launchpad.support.sap.com/#/notes/1882376)

您可以在下列 SAP 附註中找到適用於 SAP ASE 之 DBA Cockpit 的進一步資訊︰

* [SAP 支援說明#1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP 支援說明#1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP 支援說明#1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP 支援說明#1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP 支援說明#1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP 支援說明#1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP 支援說明#1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP 支援說明#1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>SAP ASE 的有用連結、說明&白皮書
[Sybase ASE 16.3 PL7 文檔](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US)的起始頁提供了指向各種文檔的連結，其中的文檔包括：

- SAP ASE 學習之旅 - 管理&監控
- SAP ASE 學習之旅 - 安裝&升級

是有説明的。 另一個有用的文檔是[SAP 自我調整伺服器企業遷移和運行時最佳實踐上的 SAP 應用程式](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)。

其他有用的 SAP 支援說明包括：

- [SAP 支援說明#2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP 支援說明#1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP 支援說明#2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP 支援說明#1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP 支援說明#1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP 支援說明#1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP 支援說明#2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 支援說明#1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP 支援說明#2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP 支援說明#1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP 支援說明#1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP 支援說明#2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 支援說明#1588316](https://launchpad.support.sap.com/#/notes/158831) 

其他資訊發佈于 

- [SAP 自我調整伺服器企業上的 SAP 應用程式](https://community.sap.com/topics/applications-on-ase)
- [Sybase資訊中心](http://infocenter.sybase.com/help/index.jsp) 

每月電子報通過 SAP[支援說明發佈#2381575](https://launchpad.support.sap.com/#/notes/2381575) 

[Sybase ASE 始終打開，帶有第 3 個 DR 節點設置](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199) 

## <a name="next-steps"></a>後續步驟
查看 Azure[上的 SAP 工作負荷文章：規劃和部署檢查表](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

