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
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25d911869c95baba6ac9db3b893292e702e9c0e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81273200"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署

本文介紹在 Azure IaaS 中部署 SAP ASE 時，幾個要考量到的地方。 在閱讀本文件之前，您應該先參閱[適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](dbms_guide_general.md)文件，以及 [Azure 上的 SAP 工作負載文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)中的其他指南。 本檔涵蓋在 Linux 和 Windows 作業系統上執行的 SAP ASE。 Azure 上支援的最低版本是 SAP ASE 16.0.02 （版本16支援套件2）。 建議您部署最新版本的 SAP 和最新的修補程式等級。  建議使用最少的 SAP ASE 16.0.03.07 （版本16支援套件3修補程式等級7）。  您可以在[目標 ASE 16.0 發行排程和 CR 清單資訊](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)中找到最新版本的 SAP。

除了下列位置的 SAP 產品可用性矩陣以外，還會找到有關 SAP 應用程式或安裝媒體位置的版本支援的其他資訊：

- [SAP 支援附注 #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP 支援附注 #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP 支援附注 #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP 支援附注 #1973241](https://launchpad.support.sap.com/#/notes/1973241)

批註：在 SAP 世界內外的檔中，產品的名稱會被視為 Sybase ASE 或 SAP ASE，或在某些情況下都是。 為了保持一致，我們會在本檔中使用**SAP ASE**的名稱。

## <a name="operating-system-support"></a>作業系統支援
SAP 產品可用性對照表包含每個 SAP 應用程式支援的作業系統和 SAP 核心組合。  Linux 散發版 SUSE 12. x、SUSE 15. x、Red Hat 7. x 完全受到支援。  不支援作為 SAP ASE 的作業系統 Oracle Linux。  建議使用最新可用的 Linux 版本。 Windows 客戶應使用 Windows Server 2016 或 Windows Server 2019 發行版本。  較舊版本的 Windows （例如 Windows 2012）在技術上受到支援，但最新的 Windows 版本一律是建議的做法。


## <a name="specifics-to-sap-ase-on-windows"></a>Windows 上 SAP ASE 專屬的詳細資料
從 Microsoft Azure 開始，您即可將現有的 SAP ASE 應用程式遷移至 Azure 虛擬機器。 Azure 虛擬機器中的 SAP ASE 可讓您輕鬆地將這些應用程式移轉到 Microsoft Azure，藉以減少部署、管理和維護企業級應用程式的擁有權總成本。 透過 Azure 虛擬機器中的 SAP ASE，系統管理員和開發人員仍然可以使用可在內部部署使用的相同開發和管理工具。

Microsoft Azure 提供許多不同的虛擬機器類型，可讓您執行最小的 SAP 系統和架構，以至具有數千個使用者的大型 SAP 系統和架構。 [Sap 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)中提供了不同 sap 認證之 VM SKU 的 sap 大小調整 sap 數目。

如需在 Windows 上安裝 SAP ASE 的檔，請參閱[適用于 windows 的 SAP Ase 安裝指南](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

[鎖定記憶體中的分頁] 是一項設定，可防止 SAP ASE 資料庫緩衝區被分頁出來。 這項設定適用于具有大量記憶體的大型忙碌系統。 請聯絡 BC-DB-SYB 以取得詳細資訊。 


## <a name="linux-operating-system-specific-settings"></a>Linux 作業系統特定設定
在 Linux Vm 上，以 `saptune` 設定檔執行的 SAP-ASE Linux 大型頁面應該預設為啟用，而且可以使用命令進行驗證  

`cat /proc/meminfo` 

頁面大小通常是 2048 KB。 如需詳細資訊，請參閱[Linux 上的龐大頁面](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html)一文 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>適用于 SAP ASE 部署的 VM 和磁片結構建議

在[sap 支援附注](https://launchpad.support.sap.com/#/notes/1928533)中列出的任何 vm 類型都支援 sap ASE For Sap NetWeaver 應用程式 #1928533 一般用於中型大小 SAP ASE 資料庫伺服器的 vm 類型包含 Esv3。  大型的多 tb 資料庫可以利用 M 系列的 VM 類型。 藉由啟用 M 系列寫入加速器，可以改善 SAP ASE 交易記錄磁片寫入效能。 由於 SAP ASE 執行記錄檔寫入的方式，因此應謹慎地使用 SAP ASE 來測試寫入加速器。  [請參閱 SAP 支援附注 #2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) ，並考慮執行效能測試。  
寫入加速器僅適用于交易記錄檔磁片。 磁片層級快取應設定為 [無]。 如果 Azure 寫入加速器不會顯示與其他 DBMS 類似的改良功能，千萬別驚訝。 根據 SAP ASE 寫入交易記錄檔的方式，Azure 寫入加速器不需要加速。
建議將不同的磁片用於資料裝置和記錄裝置。  系統資料庫 sybsecurity 且 `saptools` 不需要專用磁片，而且可以放在包含 SAP 資料庫資料和記錄裝置的磁片上 

![SAP ASE 的儲存體設定](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>檔案系統，等量大小 & IO 平衡 
除非另有設定，否則 SAP ASE 會依序將資料寫入磁片儲存體裝置。 這表示具有四個裝置的空白 SAP ASE 資料庫，只會將資料寫入到第一個裝置。  只有當第一個裝置已滿時，才會將其他磁片裝置寫入。  每個 SAP ASE 裝置的讀取和寫入 IO 數量可能會不同。 若要在所有可用的 Azure 磁片之間平衡磁片 IO，必須使用 Windows 儲存空間或 Linux LVM2。 在 Linux 上，建議使用 XFS 檔案系統來格式化磁片。 LVM 等量區大小應使用效能測試進行測試。 128 KB 等量大小是不錯的起點。 在 Windows 上，應該測試 NTFS 配置單位大小（au）。 64 KB 可用來做為起始值。 

建議您設定自動資料庫擴充，如在 SAP 調適型伺服器企業和[sap 支援](https://launchpad.support.sap.com/#/notes/1815695)[中設定自動資料庫空間擴充](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)一文中所述 #1815695。 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Azure 虛擬機器上的 SAP ASE 範例、磁片和檔案系統設定 
下列範本顯示 Linux 和 Windows 的範例設定。 確認虛擬機器和磁片設定之前，請確定個別 VM 的網路和儲存體頻寬配額足以滿足商務需求。 同時請記住，不同的 Azure VM 類型具有可連結至 VM 的磁片數目上限不同。 例如，E4s_v3 VM 的儲存體 IO 輸送量限制為 48 MB/秒。 如果資料庫備份活動所需的儲存體輸送量要求超過 48 MB/秒，則具有更多儲存頻寬輸送量的較大 VM 類型是無法避免的。 設定 Azure 儲存體時，您也必須記住，特別是使用[Azure Premium 儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance)時，每 GB 容量的輸送量和 IOPS 都會有所變更。 如需瞭解 Azure 中有[哪些磁片類型](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types)，請參閱此主題的詳細資訊。 特定 Azure VM 類型的配額記載于與它連結的[記憶體優化虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/sizes-memory)和文章文章中。 

> [!NOTE]
>  如果 DBMS 系統已從內部部署移至 Azure，建議您在 VM 上執行監視，並評估 CPU、記憶體、IOPS 和儲存體輸送量。 比較觀察到的尖峰值與前述文章中記載的 VM 配額限制

下面提供的範例僅供參考，而且可以根據個別需求加以修改。 由於 SAP ASE 的設計，資料裝置的數目與其他資料庫的重要性並不相同。 本檔中詳述的資料裝置數目僅為指南。 

針對資料庫大小介於 50 GB – 250 GB （例如 SAP solution Manager）的小型 SAP ASE DB 伺服器，其設定範例可能如下所示：

| 組態 | Windows | Linux | 註解 |
| --- | --- | --- | --- |
| VM 類型 | E4s_v3 （4 vCPU/32 GB RAM） | E4s_v3 （4 vCPU/32 GB RAM） | --- |
| 加速網路 | 啟用 | 啟用 | ---|
| SAP ASE 版本 | 16.0.03.07 或更高版本 | 16.0.03.07 或更高版本 | --- |
| 資料裝置數目 | 4 | 4 | ---|
| 記錄裝置數 | 1 | 1 | --- |
| 暫存裝置數目 | 1 | 1 | SAP BW 工作負載的更多 |
| 作業系統 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7。6 | --- |
| 磁片匯總 | 儲存空間 | LVM2 | --- |
| 檔案系統 | NTFS | XFS |
| 格式區塊大小 | 需要工作負載測試 | 需要工作負載測試 | --- |
| 資料磁片的數目和類型 | Premium 儲存體： 2 x P10 （RAID0） | Premium 儲存體： 2 x P10 （RAID0）| Cache = 唯讀 |
| 記錄檔磁片的數目和類型 | Premium 儲存體： 1 x P20  | Premium 儲存體： 1 x P20 | Cache = 無 |
| ASE MaxMemory 參數 | 90% 的實體 RAM | 90% 的實體 RAM | 假設單一實例 |
| 備份裝置數目 | 4 | 4| --- |
| 備份磁片的數目和類型 | 1 | 1 | --- |


針對資料庫大小介於 250 GB – 750 GB （例如較小的 SAP Business Suite 系統）的中型 SAP ASE DB 伺服器，其設定範例可能如下所示：

| 組態 | Windows | Linux | 註解 |
| --- | --- | --- | --- |
| VM 類型 | E16s_v3 （16 vCPU/128 GB RAM） | E16s_v3 （16 vCPU/128 GB RAM） | --- |
| 加速網路 | 啟用 | 啟用 | ---|
| SAP ASE 版本 | 16.0.03.07 或更高版本 | 16.0.03.07 或更高版本 | --- |
| 資料裝置數目 | 8 | 8 | ---|
| 記錄裝置數 | 1 | 1 | --- |
| 暫存裝置數目 | 1 | 1 | SAP BW 工作負載的更多 |
| 作業系統 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7。6 | --- |
| 磁片匯總 | 儲存空間 | LVM2 | --- |
| 檔案系統 | NTFS | XFS |
| 格式區塊大小 | 需要工作負載測試 | 需要工作負載測試 | --- |
| 資料磁片的數目和類型 | Premium 儲存體： 4 x P20 （RAID0） | Premium 儲存體： 4 x P20 （RAID0）| Cache = 唯讀 |
| 記錄檔磁片的數目和類型 | Premium 儲存體： 1 x P20  | Premium 儲存體： 1 x P20 | Cache = 無 |
| ASE MaxMemory 參數 | 90% 的實體 RAM | 90% 的實體 RAM | 假設單一實例 |
| 備份裝置數目 | 4 | 4| --- |
| 備份磁片的數目和類型 | 1 | 1 | --- |

針對資料庫大小介於 750 GB – 2000 GB （例如較大的 SAP Business Suite 系統）的小型 SAP ASE DB 伺服器，其設定範例可能如下所示：

| 組態 | Windows | Linux | 註解 |
| --- | --- | --- | --- |
| VM 類型 | E64s_v3 （64 vCPU/432 GB RAM） | E64s_v3 （64 vCPU/432 GB RAM） | --- |
| 加速網路 | 啟用 | 啟用 | ---|
| SAP ASE 版本 | 16.0.03.07 或更高版本 | 16.0.03.07 或更高版本 | --- |
| 資料裝置數目 | 16 | 16 | ---|
| 記錄裝置數 | 1 | 1 | --- |
| 暫存裝置數目 | 1 | 1 | SAP BW 工作負載的更多 |
| 作業系統 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7。6 | --- |
| 磁片匯總 | 儲存空間 | LVM2 | --- |
| 檔案系統 | NTFS | XFS |
| 格式區塊大小 | 需要工作負載測試 | 需要工作負載測試 | --- |
| 資料磁片的數目和類型 | Premium 儲存體： 4 x P30 （RAID0） | Premium 儲存體： 4 x P30 （RAID0）| Cache = 唯讀 |
| 記錄檔磁片的數目和類型 | Premium 儲存體： 1 x P20  | Premium 儲存體： 1 x P20 | Cache = 無 |
| ASE MaxMemory 參數 | 90% 的實體 RAM | 90% 的實體 RAM | 假設單一實例 |
| 備份裝置數目 | 4 | 4| --- |
| 備份磁片的數目和類型 | 1 | 1 | --- |


適用于小型 SAP ASE DB 伺服器的設定範例，其資料庫大小為 2 TB +，例如較大的全域使用的 SAP Business Suite 系統，可能如下所示：

| 組態 | Windows | Linux | 註解 |
| --- | --- | --- | --- |
| VM 類型 | M 系列（1.0 到 4.0 TB 的 RAM）  | M 系列（1.0 到 4.0 TB 的 RAM） | --- |
| 加速網路 | 啟用 | 啟用 | ---|
| SAP ASE 版本 | 16.0.03.07 或更高版本 | 16.0.03.07 或更高版本 | --- |
| 資料裝置數目 | 32 | 32 | ---|
| 記錄裝置數 | 1 | 1 | --- |
| 暫存裝置數目 | 1 | 1 | SAP BW 工作負載的更多 |
| 作業系統 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7。6 | --- |
| 磁片匯總 | 儲存空間 | LVM2 | --- |
| 檔案系統 | NTFS | XFS |
| 格式區塊大小 | 需要工作負載測試 | 需要工作負載測試 | --- |
| 資料磁片的數目和類型 | Premium 儲存體： 4 + x P30 （RAID0） | Premium 儲存體： 4 + x P30 （RAID0）| Cache = 唯讀，請考慮 Azure Ultra 磁片 |
| 記錄檔磁片的數目和類型 | Premium 儲存體： 1 x P20  | Premium 儲存體： 1 x P20 | Cache = 無，請考慮 Azure Ultra 磁片 |
| ASE MaxMemory 參數 | 90% 的實體 RAM | 90% 的實體 RAM | 假設單一實例 |
| 備份裝置數目 | 16 | 16 | --- |
| 備份磁片的數目和類型 | 4 | 4 | 使用 LVM2/儲存空間 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Azure 上 SAP ASE 的備份 & 還原考慮
增加資料和備份裝置的數目會增加備份和還原效能。 建議您將裝載 SAP ASE 備份裝置的 Azure 磁片等量分割，如稍早所示的表格中所顯示。 請小心平衡備份裝置和磁片的數目，並確保備份輸送量不應超過 40%-總 VM 輸送量配額的50%。 建議使用 SAP 備份壓縮做為預設值。 您可以在下列文章中找到更多詳細資料：

- [SAP 支援附注 #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP 支援附注 #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP 支援附注 #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

請勿使用磁片磁碟機 D:\或/temp 空間做為資料庫或記錄傾印目的地。

### <a name="impact-of-database-compression"></a>資料庫壓縮的影響
在 i/o 頻寬可能變成限制因素的設定中，可減少 IOPS 的量值可能有助於延展工作負載，其中一個可以在 Azure 之類的 IaaS 案例中執行。 因此，建議您在將現有的 SAP 資料庫上傳至 Azure 之前，確定已使用 SAP ASE 壓縮。

在上傳至 Azure 之前套用壓縮的建議是出自下列數個因素︰

* 降低要上傳至 Azure 的資料量
* 假設您可以使用功能更強大的硬體，其在內部部署中使用更多 CPU 或更高 I/O 頻寬或更少 I/O 延遲，則執行壓縮的持續時間較短
* 較小的資料庫大小可能會使磁碟配置的成本降低

資料壓縮和 LOB 壓縮可以在 Azure 虛擬機器上裝載的 VM 中運作，如同它在內部部署中的運作方式。 如需如何檢查現有 SAP ASE 資料庫中是否已經使用壓縮的詳細資訊，請參閱[SAP 支援附注 1750510](https://launchpad.support.sap.com/#/notes/1750510)。 如需 SAP ASE 資料庫壓縮的詳細資訊，請參閱[sap 支援附注 #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Azure 上 SAP ASE 的高可用性 
HADR 使用者指南會詳細說明2個節點 SAP ASE 「永遠開啟」解決方案的安裝和設定。  此外，也支援第三個嚴重損壞修復節點。 SAP ASE 支援許多高可用性設定，包括共用磁片和原生 OS 叢集（浮動 IP）。 Azure 上唯一支援的設定是使用沒有浮動 IP 的錯誤管理員。  浮動 IP 位址方法將無法在 Azure 上使用。  SAP 核心是「HA 感知」應用程式，並瞭解主要和次要 SAP ASE 伺服器。 SAP ASE 與 Azure 之間沒有緊密的整合，因此不會使用 Azure 內部負載平衡器。 因此，標準的 SAP ASE 檔應遵循從[SAP ASE HADR 使用者指南](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)開始 

> [!NOTE]
> Azure 上唯一支援的設定是使用沒有浮動 IP 的錯誤管理員。  浮動 IP 位址方法將無法在 Azure 上使用。 

### <a name="third-node-for-disaster-recovery"></a>嚴重損壞修復的第三個節點
除了使用 SAP ASE Always On 來進行本機高可用性之外，您可能會想要將設定延伸至另一個 Azure 區域中的非同步複寫節點。 這類案例的檔可在[這裡](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)找到。

## <a name="sap-ase-database-encryption--ssl"></a>& SSL 的 SAP ASE 資料庫加密 
SAP 軟體布建管理員（SWPM）提供在安裝期間加密資料庫的選項。  如果您想要使用加密，建議使用 SAP 完整資料庫加密。  請參閱中記載的詳細資料：

- [SAP 支援附注 #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP 支援附注 #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP 支援附注 #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP 支援附注 #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> 如果 SAP ASE 資料庫已加密，則備份傾印壓縮將無法使用。 另請參閱[SAP 支援附注 #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Azure 部署檢查清單上的 SAP ASE
 
- 部署 SAP ASE 16.0.03.07 或更高版本
- 更新至 FaultManager 和 SAPHostAgent 的最新版本和修補程式
- 部署可用的最新認證 OS，例如 Windows 2019、Suse 15.1 或 Redhat 7.6 或更高版本
- 使用 SAP 認證的 Vm –建議您使用高記憶體的 Azure VM Sku （例如 Es_v3 或適用于 x 大型系統 M 系列 VM Sku）
- 將 VM 的磁片 IOPS 和總 VM 匯總輸送量配額與磁片設計進行比對。  部署足夠的磁片數目
- 使用具有正確等量大小和檔案系統的 Windows 儲存空間或 Linux LVM2 來匯總磁片
- 建立足夠數目的裝置，供資料、記錄、暫存及備份之用
- 針對 x 大型系統，請考慮使用 UltraDisk 
- `saptune`在 LINUX OS 上執行 SAP-ASE 
- 使用 DB 加密保護資料庫–在 Azure Key Vault 中手動儲存金鑰 
- 完成[Azure 上的 SAP 檢查清單](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 
- 設定記錄備份與完整備份 
- 測試 HA/DR、備份和還原，以及執行壓力 & 大量測試 
- 確認自動資料庫延伸模組正在運作 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>使用 DBACockpit 來監視資料庫實例
針對使用 SAP ASE 作為資料庫平台的 SAP 系統，可以存取 DBACockpit 作為交易 DBACockpit 中內嵌的瀏覽器視窗或作為 Webdynpro。 不過，僅在 DBACockpit 的 Webdynpro 實施中提供監視和管理資料庫的完整功能。

做為內部部署系統，需要使用數個步驟，才能啟用 DBACockpit 之 Webdynpro 實作所使用的所有 SAP NetWeaver 功能。 遵循[SAP 支援附注 #1245200](https://launchpad.support.sap.com/#/notes/1245200) ，以啟用 webdynpros 的使用方式並產生所需的資訊。 遵循上述附注中的指示時，您也會設定網際網路通訊管理員（ `ICM` ）以及要用於 HTTP 和 HTTPs 連接的埠。 Http 的預設設定看起來如下︰

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

此外，在交易 DBACockpit 中產生的連結看起來如下︰

> HTTPs： \/ / \<fullyqualifiedhostname> ： 44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> HTTP： \/ / \<fullyqualifiedhostname> ： 8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

取決於裝載 SAP 系統的 Azure 虛擬機器是否連線到您的 AD 和 DNS，您需要確定 ICM 會使用完整的主機名稱，此名稱可在您嘗試從中開啟 DBACockpit 的電腦上加以解析。 請參閱[SAP 支援附注 #773830](https://launchpad.support.sap.com/#/notes/773830) ，以瞭解 ICM 如何根據設定檔參數判斷完整主機名稱，並在必要時明確設定參數 ICM/host_name_full。

如果您已在僅限雲端的案例中部署 VM，但內部部署與 Azure 之間沒有跨單位連線，則您需要定義公用 IP 位址和 `domainlabel` 。 然後 VM 的公用 DNS 名稱格式如下所示︰

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

如需更多與 DNS 名稱相關的詳細資料，請參閱 [這裡] [azurerm-vs-virtual-machines-azurerm-versus-azuresm 找到]。

將 SAP 設定檔參數 icm/host_name_full 設定為 Azure VM 的 DNS 名稱，其連結看起來可能如下︰

> HTTPs： \/ /mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> HTTP： \/ /mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

在此情況下，您需要確定︰

* 在 Azure 入口網站中，針對用來與 ICM 通訊的 TCP/IP 連接埠，將輸入規則新增至網路安全性群組
* 針對用來與 ICM 通訊的 TCP/IP 連接埠，將輸入規則新增至 Windows 防火牆組態

針對自動匯入所有可用修正的功能，建議定期套用適用於您 SAP 版本的修正集合 SAP 附註︰

* [SAP 支援附注 #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP 支援附注 #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP 支援附注 #1882376](https://launchpad.support.sap.com/#/notes/1882376)

您可以在下列 SAP 附註中找到適用於 SAP ASE 之 DBA Cockpit 的進一步資訊︰

* [SAP 支援附注 #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP 支援附注 #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP 支援附注 #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP 支援附注 #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP 支援附注 #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP 支援附注 #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP 支援附注 #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP 支援附注 #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>適用于 SAP ASE & 白皮書的實用連結，請注意
[SAP ASE 16.0.03.07 檔](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US)的起始頁面提供下列各項檔的連結：

- SAP ASE 學習旅程-管理 & 監視
- SAP ASE 學習旅程-安裝 & 升級

很有説明。 另一個有用的檔是 sap[應用程式，適用于 sap 彈性伺服器企業的遷移和執行時間最佳做法](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)。

其他實用的 SAP 支援注意事項如下：

- [SAP 支援附注 #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP 支援附注 #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP 支援附注 #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP 支援附注 #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP 支援附注 #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP 支援附注 #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP 支援附注 #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 支援附注 #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP 支援附注 #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP 支援附注 #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP 支援附注 #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP 支援附注 #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 支援附注 #1588316](https://launchpad.support.sap.com/#/notes/158831) 

其他資訊發佈于 

- [SAP 彈性伺服器企業上的 SAP 應用程式](https://community.sap.com/topics/applications-on-ase)
- [SAP ASE 資訊中心](http://infocenter.sybase.com/help/index.jsp) 
- [使用第三個 DR 節點設定的 SAP ASE Always on](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

每月電子報會透過[SAP 支援附注發佈 #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>後續步驟
請參閱[Azure 上的 SAP 工作負載一文：規劃和部署檢查清單](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

