---
title: Azure Migrate 中的 VMware 遷移支援
description: 瞭解 Azure Migrate 中 VMware VM 遷移的支援。
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 7a7713021683c394e609a302a1aa6fcb282484e5
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544200"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 遷移的支援矩陣

本文摘要說明使用 [Azure Migrate：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool) 來遷移 VMware vm 的支援設定和限制。 如果您要尋找有關評估 VMware Vm 以遷移至 Azure 的資訊，請參閱 [評量支援矩陣](migrate-support-matrix-vmware.md)。


## <a name="migration-options"></a>移轉選項

您可以透過幾種方式來遷移 VMware Vm：

- **使用無代理程式遷移** ：遷移 vm，而不需要在其上安裝任何專案。 您部署 [Azure Migrate 設備](migrate-appliance.md) 以進行無代理程式的遷移。
- **使用以代理** 程式為基礎的遷移：在 VM 上安裝代理程式以進行複寫。 針對以代理程式為基礎的遷移，您可以部署複寫 [設備](migrate-replication-appliance.md)。

請參閱 [這篇文章](server-migrate-overview.md) ，以找出您想要使用的方法。

## <a name="migration-limitations"></a>移轉限制

- 您最多可以選取10個 Vm 以進行複寫。 如果您想要遷移更多電腦，請以10個群組進行複寫。
- 針對 VMware 無代理程式遷移，您最多可以同時執行300個複寫。

## <a name="agentless-migration"></a>無代理程式遷移 

本節摘要說明無代理程式遷移的需求。

### <a name="vmware-requirements-agentless"></a> (無代理程式) 的 VMware 需求

此表格摘要列出 VMware 虛擬程式需求。

**VMware** | **詳細資料**
--- | ---
**VMware vCenter Server** | 版本5.5、6.0、6.5、6.7、7.0。
**VMware vSphere ESXI 主機** | 版本5.5、6.0、6.5、6.7、7.0。
**vCenter Server 許可權** | 無代理程式遷移使用 [遷移設備](migrate-appliance.md)。 設備需要 vCenter Server 中的這些許可權：<br/><br/> - **資料存放區。流覽** ：允許流覽 VM 記錄檔，以針對快照建立和刪除進行疑難排解。<br/><br/> - **資料存放區. FileManagement** ：允許資料存放區瀏覽器中的讀取/寫入/刪除/重新命名作業，以疑難排解快照集的建立和刪除。<br/><br/> - **VirtualMachine.Config。變更追蹤** ：允許啟用或停用 VM 磁片的變更追蹤，以提取快照之間變更的資料區塊。<br/><br/> - **VirtualMachine.Config。DiskLease** ：允許 VM 的磁片租用作業，使用 VMware vSphere 的虛擬磁片開發工具組 (VDDK) 來讀取磁片。<br/><br/> - **VirtualMachine DiskAccess** ：特別針對 vSphere 6.0 和更新版本 () 允許在 VM 上開啟磁片，以在使用 VDDK 的磁片上進行隨機讀取存取。<br/><br/> - **VirtualMachine： DiskRandomRead** ：允許在 VM 上開啟磁片，以使用 VDDK 來讀取磁片。<br/><br/> - **VirtualMachine： DiskRandomAccess** ：允許在 VM 上開啟磁片，以使用 VDDK 來讀取磁片。<br/><br/> - **VirtualMachine. GetVmFiles** ：允許在與 VM 相關聯的檔案上進行讀取作業，以下載記錄，並在發生失敗時進行疑難排解。<br/><br/> - **VirtualMachine。 \**_：允許建立和管理 VM 快照集以進行複寫。 <br/> <br/>-_* VirtualMachine。關機** ：允許在遷移至 Azure 期間關閉 VM 的電源。



### <a name="vm-requirements-agentless"></a> (無代理程式) 的 VM 需求

此表格摘要列出 VMware Vm 的無代理程式遷移需求。

**支援** | **詳細資料**
--- | ---
**支援的作業系統** | 您可以遷移 Azure 所支援的 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 和 [Linux](../virtual-machines/linux/endorsed-distros.md) 作業系統。
**Azure 中的 Windows Vm** | 您可能需要在遷移之前對 Vm [進行一些變更](prepare-for-migration.md#verify-required-changes-before-migrating) 。 
**Azure 中的 Linux Vm** | 有些 VM 可能需要變更，才能在 Azure 中執行。<br/><br/> 針對 Linux，Azure Migrate 會自動為這些作業系統進行變更：<br/> -Red Hat Enterprise Linux 7.8、7.7、7.6、7.5、7.4、7.0、6。x<br/> -分幣 OS 7.7、7.6、7.5、7.4、6。x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19.04、19.10、14.04 LTS、16.04 LTS、18.04 LTS<br/> -Debian 7、8 <br/> Oracle Linux 7.7、7.7-CI<br/> 針對其他作業系統，您必須手動進行 [必要的變更](prepare-for-migration.md#verify-required-changes-before-migrating) 。
**Linux 開機** | 如果/boot 是在專用磁碟分割上，它應該位於 OS 磁片上，而不會分散到多個磁片。<br/> 如果/boot 是根 (/) 磁碟分割的一部分，則 '/' 磁碟分割應位於 OS 磁片上，而不是跨越其他磁片。
**UEFI 開機** | 支援。 以 UEFI 為基礎的 Vm 將會遷移至 Azure 第2代 Vm。 
**磁碟大小** | 2 TB 的 OS 磁片 (BIOS 開機) ;4 TB 的 OS 磁片 (UEFI 開機) ;適用于資料磁片的 32 TB。
**磁片限制** |  每個 VM 最多60個磁片。
**加密的磁片/磁片區** | 具有加密磁片/磁片區的 Vm 不支援遷移。
**共用磁碟叢集** | 不支援。
**獨立磁片** | 不支援。
**RDM/傳遞磁片** | 如果 Vm 具有 RDM 或傳遞磁片，這些磁片將不會複寫至 Azure。
**NFS** | 在 Vm 上掛接為磁片區的 NFS 磁片區將不會被覆寫。
**iSCSI 目標** | 具有 iSCSI 目標的 Vm 不支援無代理程式遷移。
**多重路徑 IO** | 不支援。
**儲存體 vMotion** | 不支援。 如果 VM 使用儲存體 vMotion，複寫將無法運作。
**組合的 Nic** | 不支援。
**IPv6** | 不支援。
**目標磁片** | Vm 只能遷移至受控磁片， (標準 HDD、標準 SSD、Azure 中的 premium SSD) 。
**同時複寫** | 每 vCenter Server 300 部 Vm。 如果您有更多，請以300批次的方式遷移它們。


### <a name="appliance-requirements-agentless"></a> (無代理程式) 的設備需求

無代理程式遷移使用 [Azure Migrate 設備](migrate-appliance.md)。 您可以使用 OVA 範本將設備部署為 VMware VM，並匯入 vCenter Server 或使用 [PowerShell 腳本](deploy-appliance-script.md)。

- 了解 VMware 的[設備需求](migrate-appliance.md#appliance---vmware)。
- 了解設備必須在[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中存取的 URL。
- 在 Azure Government 中，您必須[使用指令碼](deploy-appliance-script-government.md)來部署設備。

### <a name="port-requirements-agentless"></a>埠需求 (無代理程式) 

**裝置** | **[連接]**
--- | ---
設備 | 埠443上的輸出連線，可將複寫的資料上傳至 Azure，並與 Azure Migrate 服務進行通訊，以協調複寫和遷移。
vCenter 伺服器 | 埠443上的輸入連接可讓設備協調複寫-建立快照集、複製資料、發行快照集
vSphere/ESXI 主機 | TCP 埠902上的輸入，可讓設備從快照集複寫資料。

## <a name="agent-based-migration"></a>以代理程式為基礎的遷移 


本節摘要說明以代理程式為基礎的遷移需求。


### <a name="vmware-requirements-agent-based"></a>以代理程式為基礎的)  (VMware 需求

下表摘要列出 VMware 虛擬化伺服器的評量支援和限制。

**VMware 需求** | **詳細資料**
--- | ---
**VMware vCenter Server** | 版本5.5、6.0、6.5 或6.7。
**VMware vSphere ESXI 主機** | 版本5.5、6.0、6.5 或6.7。
**vCenter Server 許可權** | VCenter Server 的唯讀帳戶。

### <a name="vm-requirements-agent-based"></a>以代理程式為基礎的)  (VM 需求

此表格摘要說明您想要使用以代理程式為基礎的遷移來遷移之 VMware Vm 的 VMware VM 支援。

**支援** | **詳細資料**
--- | ---
**機器工作負載** | Azure Migrate 支援 Active Directory、SQL server 等任何工作負載的遷移 (假設在支援的電腦上執行 ) 。
**作業系統** | 如需最新資訊，請參閱 Site Recovery 的 [作業系統支援](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) 。 Azure Migrate 提供相同的 VM 作業系統支援。
**Linux 檔案系統/來賓儲存體** | 如需最新資訊，請參閱 Site Recovery 的 [Linux 檔案系統支援](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) 。 Azure Migrate 具有相同的 Linux 檔案系統支援。
**網路/儲存體** | 如需最新資訊，請參閱 Site Recovery 的 [網路](../site-recovery/vmware-physical-azure-support-matrix.md#network) 和 [儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#storage) 必要條件。 Azure Migrate 提供相同的網路/儲存體需求。
**Azure 需求** | 如需最新資訊，請參閱 [Azure 網路](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、 [儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和 Site Recovery 的 [計算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) 需求。 Azure Migrate 的 VMware 遷移需求相同。
**行動服務** | 行動服務代理程式必須安裝在您想要遷移的每個 VM 上。
**UEFI 開機** | 支援。 以 UEFI 為基礎的 Vm 將會遷移至 Azure 第2代 Vm。 
**UEFI-安全開機**         | 不支援遷移。
**目標磁片** | Vm 只能遷移至受控磁片， (標準 HDD、標準 SSD、Azure 中的 premium SSD) 。
**磁碟大小** | 2 TB 的 OS 磁片 (BIOS 開機) ;4 TB 的 OS 磁片 (UEFI 開機) ;適用于資料磁片的 8 TB。
**磁片限制** |  每個 VM 最多63個磁片。
**加密的磁片/磁片區** | 具有加密磁片/磁片區的 Vm 不支援遷移。
**共用磁碟叢集** | 不支援。
**獨立磁片** | 支援。
**傳遞磁片** | 支援。
**NFS** | 在 Vm 上掛接為磁片區的 NFS 磁片區將不會被覆寫。
**iSCSI 目標** | 支援。
**多重路徑 IO** | 不支援。
**儲存體 vMotion** | 支援
**組合的 Nic** | 不支援。
**IPv6** | 不支援。




### <a name="appliance-requirements-agent-based"></a>以代理程式為基礎的)  (設備需求

當您使用 Azure Migrate hub 中提供的 OVA 範本來設定複寫設備時，設備會執行 Windows Server 2016 並符合支援需求。 如果您在實體伺服器上手動設定複寫設備，請確定它符合需求。

- 瞭解 VMware 的複寫 [設備需求](migrate-replication-appliance.md#appliance-requirements) 。
- MySQL 必須安裝在此設備上。 深入瞭解 [安裝選項](migrate-replication-appliance.md#mysql-installation)。
- 瞭解複寫設備在 [公用](migrate-replication-appliance.md#url-access) 和 [政府](migrate-replication-appliance.md#azure-government-url-access) 雲端中所需存取的 url。
- 檢查複寫設備需要存取的 [埠](migrate-replication-appliance.md#port-access) 。

### <a name="port-requirements-agent-based"></a>以代理程式為基礎的)  (埠需求

**裝置** | **[連接]**
--- | ---
VM | 在 Vm 上執行的行動服務會與內部部署複寫設備通訊， (設定伺服器在埠 HTTPS 443 輸入上) ，以進行複寫管理。<br/><br/> VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
複寫設備 | 複寫設備會透過埠 HTTPS 443 輸出來協調 Azure 的複寫。
處理序伺服器 | 進程伺服器會接收復寫資料、將其優化並加密，然後透過埠443輸出將它傳送至 Azure 儲存體。<br/> 依預設，進程伺服器會在複寫設備上執行。

## <a name="azure-vm-requirements"></a>Azure VM 需求

使用無代理程式或以代理程式為基礎的遷移將所有複寫到 Azure (的內部部署 Vm) 必須符合此表中摘要說明的 Azure VM 需求。 

**元件** | **Requirements** 
--- | --- | ---
客體作業系統 | 驗證支援的 VMware VM 作業系統以進行遷移。<br/> 您可以遷移在支援的作業系統上執行的任何工作負載。 
客體作業系統架構 | 64 位元。 
作業系統磁碟大小 | 最多 2,048 GB。 
作業系統磁碟計數 | 1 
資料磁碟計數 | 64 或以下。 
資料磁碟大小 | 最高 8095 GB
網路介面卡 | 支援多個介面卡。
共用 VHD | 不支援。 
FC 磁碟 | 不支援。 
BitLocker | 不支援。<br/><br/> 在您遷移電腦之前，必須先停用 BitLocker。
VM 名稱 | 從 1 到 63 個字元。<br/><br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 
在遷移後連接-Windows | 若要在遷移之後連線到執行 Windows 的 Azure Vm：<br/><br/> -在遷移之前，請在內部部署 VM 上啟用 RDP。<br/><br/> 確定已針對 [公用]  設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆]  > [允許的應用程式]  中已針對所有設定檔允許 RDP。<br/><br/> 針對站對站 VPN 存取，請啟用 rdp，並允許 **Windows 防火牆** 中的 rdp 允許  ->  **網域和專用** 網的 **應用程式和功能** 。<br/><br/> 此外，請確定作業系統的 SAN 原則設定為 **OnlineAll** 。 [深入了解](prepare-for-migration.md)。
在遷移後連接-Linux | 使用 SSH 進行遷移之後，使用 SSH 連線到 Azure Vm：<br/><br/> 在遷移之前，請在內部部署機器上，確認安全殼層服務是否設定為 [啟動]，且防火牆規則允許 SSH 連線。<br/><br/> 在容錯移轉之後，在 Azure VM 上，允許 SSH 埠的連入連線連線到已容錯移轉的 VM 上的網路安全性群組規則，以及其所連接的 Azure 子網。<br/><br/> 此外，新增 VM 的公用 IP 位址。  


## <a name="next-steps"></a>下一步

[選取](server-migrate-overview.md) VMware 遷移選項。
