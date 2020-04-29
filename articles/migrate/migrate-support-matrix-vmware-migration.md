---
title: Azure Migrate 中的 VMware 遷移支援
description: 深入瞭解 Azure Migrate 中的 VMware VM 遷移支援。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: eee16b244ae4f9d517bdd42a0b7f37b1494ac480
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538132"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 遷移的支援矩陣

本文摘要說明使用[Azure Migrate：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)來遷移 VMware vm 的支援設定和限制。 如果您要尋找評估 VMware Vm 以遷移至 Azure 的相關資訊，請參閱[評估支援對照表](migrate-support-matrix-vmware.md)。


## <a name="migration-options"></a>移轉選項

您可以透過幾種方式來遷移 VMware Vm：

- 使用無代理程式遷移：遷移 Vm，而不需要在其上安裝任何專案。 您會部署用於無代理程式遷移的[Azure Migrate 設備](migrate-appliance.md)。
- 以代理程式為基礎的遷移：在 VM 上安裝代理程式以進行複寫。 針對以代理程式為基礎的遷移，您需要部署複寫[設備](migrate-replication-appliance.md)。

請參閱[這篇文章](server-migrate-overview.md)，以找出您想要使用的方法。

## <a name="migration-limitations"></a>移轉限制

- 您一次最多可以選取10部 Vm 來進行複寫。 如果您想要遷移更多電腦，請以10個群組進行複寫。
- 針對 VMware 無代理程式移轉，您最多可以同時執行 100 個複寫。

## <a name="agentless-vmware-servers"></a>無代理程式-VMware 伺服器

**VMware** | **詳細資料**
--- | ---
**VMware vCenter Server** | 版本5.5、6.0、6.5 或6.7。
**VMware vSphere ESXI 主機** | 版本5.5、6.0、6.5 或6.7。
**vCenter Server 許可權** | 無代理程式遷移會使用[遷移設備](migrate-appliance.md)。 設備需要下列許可權：<br/><br/> - **資料存放區。流覽**：允許流覽 VM 記錄檔，以針對快照集的建立和刪除進行疑難排解。<br/><br/> - **資料存放區. LowLevelFileOperations**：允許在資料存放區瀏覽器中進行讀取/寫入/刪除/重新命名作業，以針對建立和刪除快照集進行疑難排解。<br/><br/> - **VirtualMachine。 DiskChangeTracking**：允許啟用或停用 VM 磁片的變更追蹤，以便在快照之間提取已變更的資料區塊。<br/><br/> - **VirtualMachine。 DiskLease**：允許 VM 的磁片租用作業，使用 VMware vSphere 虛擬磁片開發工具組（VDDK）讀取磁片。<br/><br/> - **VirtualMachine。 AllowDiskAccess**：（特別針對 vSphere 6.0 和更新版本）允許在 VM 上開啟磁片，以使用 VDDK 在磁片上進行隨機讀取存取。<br/><br/> - **VirtualMachine。 AllowReadOnlyDiskAccess**：允許在 VM 上開啟磁片，使用 VDDK 讀取磁片。<br/><br/> - **VirtualMachine。 AllowDiskRandomAccess**：允許在 VM 上開啟磁片，使用 VDDK 讀取磁片。<br/><br/> - **VirtualMachine. AllowVirtualMachineDownload**：允許與 VM 相關聯之檔案的讀取作業，以下載記錄，並在發生失敗時進行疑難排解。<br/><br/> -* * VirtualMachine. SnapshotManagement. * * *：允許建立和管理 VM 快照集以進行複寫。<br/><br/> - **虛擬機器。請關閉電源**：允許 VM 在遷移至 Azure 期間關閉電源。



## <a name="agentless-vmware-vms"></a>無代理程式-VMware Vm

**支援** | **詳細資料**
--- | ---
**支援的作業系統** | Azure 支援的[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統可使用無代理程式遷移來進行遷移。
**Azure 的必要變更** | 有些 VM 可能需要變更，才能在 Azure 中執行。 Azure Migrate 會針對下列作業系統自動進行這些變更：<br/> -Red Hat Enterprise Linux 6.5 +、7.0 +<br/> -CentOS 6.5 +、7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS<br/> -Debian 7、8<br/><br/> 如果是其他作業系統，您必須在進行遷移之前手動進行調整。 相關文章包含如何執行這項操作的指示。
**Linux 開機** | 如果/boot 是在專用磁碟分割上，它應該位於 OS 磁片上，而不會散佈到多個磁片上。<br/> 如果/boot 是根（/）分割區的一部分，則 '/' 磁碟分割應該位於 OS 磁片上，而不是跨越其他磁片。
**UEFI 開機** | 不支援使用 UEFI 開機的 Vm 進行遷移。
**磁碟大小** | 2 TB 的 OS 磁片;適用于資料磁片的 8 TB。
**磁片限制** |  每個 VM 最多60個磁片。
**加密的磁片/磁片區** | 不支援使用加密磁片/磁片區的 Vm 進行遷移。
**共用磁碟叢集** | 不支援。
**獨立磁片** | 不支援。
**RDM/傳遞磁片** | 如果 Vm 有 RDM 或 passthrough 磁片，這些磁片將不會複寫到 Azure。
**NFS** | 裝載為 Vm 上之磁片區的 NFS 磁片區不會複寫。
**iSCSI 目標** | 無代理程式遷移不支援具有 iSCSI 目標的 Vm。
**多重路徑 IO** | 不支援。
**儲存體 vMotion** | 不支援。 如果 VM 使用儲存體 vMotion，複寫將無法正常執行。
**組合的 Nic** | 不支援。
**IPv6** | 不支援。
**目標磁片** | Vm 只能遷移至 Azure 中的受控磁片（標準 HDD、premium SSD）。
**同時複寫** | 每個 vCenter Server 100 個 Vm。 如果您有更多，請以100的批次方式來遷移它們。


## <a name="agentless-azure-migrate-appliance"></a>無代理程式 Azure Migrate 設備 

無代理程式遷移會使用[Azure Migrate 設備](migrate-appliance.md)。 您可以使用 OVA 範本將設備部署為 VMWare VM、匯入 vCenter Server，或使用[PowerShell 腳本](deploy-appliance-script.md)。

- 瞭解 VMware 的[設備需求](migrate-appliance.md#appliance---vmware)。
- 瞭解設備在[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中存取所需的 url。
- 在 Azure Government 中，您必須使用腳本來部署應用裝置。

## <a name="agentless-ports"></a>無代理程式-埠

**裝置** | **建立**
--- | ---
Appliance (設備) | 埠443上的輸出連線可將複寫的資料上傳至 Azure，以及與 Azure Migrate 服務進行通訊，以協調複寫與遷移。
vCenter 伺服器 | 埠443上的輸入連線可讓設備協調複寫-建立快照集、複製資料、發行快照集
vSphere/EXSI 主機 | TCP 埠902上用於設備的輸入，以從快照集複寫資料。


## <a name="agent-based-vmware-servers"></a>以代理程式為基礎的 VMware 伺服器
下表摘要說明 VMware 虛擬化伺服器的評量支援和限制。

**VMware 需求** | **詳細資料**
--- | ---
**VMware vCenter Server** | 版本5.5、6.0、6.5 或6.7。
**VMware vSphere ESXI 主機** | 版本5.5、6.0、6.5 或6.7。
**vCenter Server 許可權** | VCenter Server 的唯讀帳戶。

## <a name="agent-based-vmware-vms"></a>以代理程式為基礎的 VMware Vm

此表格摘要說明您想要使用代理程式型遷移來遷移之 VMware vm 的 VMware VM 支援。

**支援** | **詳細資料**
--- | ---
**機器工作負載** | Azure Migrate 支援在支援的機器上，遷移執行的任何工作負載（例如 Active Directory、SQL server 等）。
**作業系統** | 如需最新資訊，請參閱 Site Recovery 的[作業系統支援](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure Migrate 提供相同的 VM 作業系統支援。
**Linux 檔案系統/來賓儲存體** | 如需最新資訊，請參閱[Linux 檔案系統](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)的 Site Recovery 支援。 Azure Migrate 具有相同的 Linux 檔案系統支援。
**網路/儲存體** | 如需最新資訊，請參閱 Site Recovery 的[網路](../site-recovery/vmware-physical-azure-support-matrix.md#network)和[儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#storage)必要條件。 Azure Migrate 提供相同的網路/儲存需求。
**Azure 需求** | 如需最新資訊，請參閱 Site Recovery 的[Azure 網路](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[計算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)需求。 Azure Migrate 對於 VMware 遷移具有相同的需求。
**行動服務** | 行動服務代理程式必須安裝在您想要遷移的每個 VM 上。
**UEFI 開機** | Azure 中已遷移的 VM 會自動轉換為 BIOS 開機 VM。<br/><br/> OS 磁片應該有最多四個磁碟分割，而且磁片區應該使用 NTFS 格式化。
**目標磁片** | Vm 只能遷移至 Azure 中的受控磁片（標準 HDD、premium SSD）。
**磁碟大小** | 2 TB 的 OS 磁片;適用于資料磁片的 8 TB。
**磁片限制** |  每個 VM 最多63個磁片。
**加密的磁片/磁片區** | 不支援使用加密磁片/磁片區的 Vm 進行遷移。
**共用磁碟叢集** | 不支援。
**獨立磁片** | 支援。
**傳遞磁片** | 支援。
**NFS** | 裝載為 Vm 上之磁片區的 NFS 磁片區不會複寫。
**iSCSI 目標** | 無代理程式遷移不支援具有 iSCSI 目標的 Vm。
**多重路徑 IO** | 不支援。
**儲存體 vMotion** | 支援
**組合的 Nic** | 不支援。
**IPv6** | 不支援。




## <a name="agent-based-replication-appliance"></a>以代理程式為基礎的複寫應用裝置 

當您使用 Azure Migrate 中樞提供的 OVA 範本來設定複寫設備時，設備會執行 Windows Server 2016，並符合支援需求。 如果您在實體伺服器上手動設定複寫設備，請確定它符合需求。

- 瞭解 VMware 的複寫[設備需求](migrate-replication-appliance.md#appliance-requirements)。
- MySQL 必須安裝在設備上。 瞭解[安裝選項](migrate-replication-appliance.md#mysql-installation)。
- 瞭解複寫設備在[公用](migrate-replication-appliance.md#url-access)和[政府](migrate-replication-appliance.md#azure-government-url-access)雲端中需要存取的 url。
- 檢查複寫設備需要存取的[埠](migrate-replication-appliance.md#port-access)。

## <a name="agent-based-ports"></a>以代理程式為基礎的埠

**裝置** | **建立**
--- | ---
VM | 在 Vm 上執行的行動服務會與埠 HTTPS 443 輸入上的內部部署複寫設備（設定伺服器）進行通訊，以進行複寫管理。<br/><br/> VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
複寫設備 | 複寫設備會透過埠 HTTPS 443 輸出來協調與 Azure 的複寫。
處理序伺服器 | 進程伺服器會接收復寫資料、將其優化並加以加密，並透過埠443輸出將它傳送至 Azure 儲存體。<br/> 根據預設，進程伺服器會在複寫設備上執行。

## <a name="azure-vm-requirements"></a>Azure VM 需求

複寫至 Azure 的所有內部部署 Vm 都必須符合此表中摘要說明的 Azure VM 需求。 當 Site Recovery 執行複寫的必要條件檢查時，如果不符合某些需求，此檢查將會失敗。

**元件** | **Requirements** | **詳細資料**
--- | --- | ---
客體作業系統 | 驗證支援的 VMware VM 作業系統以進行遷移。<br/> 您可以遷移在支援的作業系統上執行的任何工作負載。 | 若不支援，則檢查會失敗。
客體作業系統架構 | 64 位元。 | 若不支援，則檢查會失敗。
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則檢查會失敗。
資料磁碟計數 | 64 或以下。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 最多 4,095 GB | 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。
VM 名稱 | 從 1 到 63 個字元。<br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。
在遷移後連接-Windows | 若要在遷移後連線至執行 Windows 的 Azure Vm：<br/> -在遷移之前，在內部部署 VM 上啟用 RDP。 確定已針對 [公用]**** 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆]**** > [允許的應用程式]**** 中已針對所有設定檔允許 RDP。<br/> 針對站對站 VPN 存取，請啟用 rdp，並允許**Windows 防火牆** -> 中的 rdp**允許的應用程式和功能**用於**網域和專用**網。 此外，請檢查作業系統的 SAN 原則是否設定為**OnlineAll**。 [深入了解](prepare-for-migration.md)。 |
在遷移後連接-Linux | 若要在使用 SSH 進行遷移之後連線到 Azure Vm：<br/> 在進行遷移之前，請在內部部署機器上，確認安全殼層服務已設定為 [啟動]，且防火牆規則允許 SSH 連線。<br/> 容錯移轉之後，在 Azure VM 上，允許已容錯移轉的 VM 上的網路安全性群組規則之 SSH 埠的連入連線，以及它所連接的 Azure 子網。 此外，請新增 VM 的公用 IP 位址。 |  


## <a name="next-steps"></a>後續步驟

[選取](server-migrate-overview.md)[VMware 遷移] 選項。
