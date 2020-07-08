---
title: Azure Migrate 中的 Hyper-v 遷移支援
description: 瞭解 Azure Migrate 的 Hyper-v 遷移支援。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 5dd2ae134e57fc8c719a27c25ddc58e769367065
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771221"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Hyper-v 遷移的支援矩陣

本文摘要說明使用[Azure Migrate：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool)來遷移 hyper-v vm 的支援設定和限制。 如果您要尋找評估 Hyper-v Vm 以遷移至 Azure 的相關資訊，請參閱[評估支援對照表](migrate-support-matrix-hyper-v.md)。

## <a name="migration-limitations"></a>移轉限制

您一次最多可以選取10部 Vm 來進行複寫。 如果您想要遷移更多電腦，請以10個群組進行複寫。


## <a name="hyper-v-host-requirements"></a>Hyper-v 主機需求

| **支援**                | **詳細資料**               
| :-------------------       | :------------------- |
| **部署**       | Hyper-v 主機可以是獨立或部署在叢集中。 <br/>Azure Migrate 複寫軟體（Hyper-v 複寫提供者）安裝在 Hyper-v 主機上。|
| **權限**           | 您需要 Hyper-v 主機的系統管理員許可權。 |
| **主機作業系統** | Windows Server 2019、Windows Server 2016 或 Windows Server 2012 R2。 |
| **連接埠存取** |  HTTPS 埠443上用來傳送 VM 複寫資料的輸出連線。


## <a name="hyper-v-vms"></a>Hyper-V VM

| **支援**                  | **詳細資料**               
| :----------------------------- | :------------------- |
| **作業系統** | Azure 支援的所有[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統。 |
**Windows Server 2003** | 針對執行 Windows Server 2003 的 Vm，您必須先[安裝 hyper-v Integration Services](prepare-windows-server-2003-migration.md) ，才能進行遷移。 | 
**Azure 中的 Linux Vm** | 有些 VM 可能需要變更，才能在 Azure 中執行。<br/><br/> 針對 Linux，Azure Migrate 會自動為這些作業系統進行變更：<br/> -Red Hat Enterprise Linux 6.5 +、7.0 +<br/> -CentOS 6.5 +、7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS<br/> -Debian 7、8。 對於其他作業系統，您必須手動進行[必要的變更](prepare-for-migration.md#linux-machines)。
| **Azure 的必要變更** | 有些 VM 可能需要變更，才能在 Azure 中執行。 請先手動進行調整，再進行遷移。 相關文章包含如何執行這項操作的指示。 |
| **Linux 開機**                 | 如果/boot 是在專用磁碟分割上，它應該位於 OS 磁片上，而不會散佈到多個磁片上。<br/> 如果/boot 是根（/）分割區的一部分，則 '/' 磁碟分割應該位於 OS 磁片上，而不是跨越其他磁片。 |
| **UEFI 開機**                  | Azure 中已遷移的 VM 會自動轉換為 BIOS 開機 VM。 VM 應僅執行 Windows Server 2012 和更新版本。 OS 磁片最多隻能有五個磁碟分割或更少，且 OS 磁片的大小應小於 300 GB。|
| **磁碟大小**                  | 2 TB 適用于 OS 磁片，4 TB 適用于資料磁片。|
| **磁片編號** | 每個 VM 最多16個磁片。|
| **加密的磁片/磁片區**    | 不支援遷移。|
| **RDM/傳遞磁片**      | 不支援遷移。|
| **共用磁片** | 不支援使用共用磁片的 Vm 進行遷移。|
| **NFS**                        | 裝載為 Vm 上之磁片區的 NFS 磁片區不會複寫。|
| **ISCSI**                      | 不支援具有 iSCSI 目標的 Vm 進行遷移。
| **目標磁片**                | 您只能使用受控磁片遷移至 Azure Vm。 |
| **IPv6** | 不支援。|
| **NIC 小組** | 不支援。|
| **Azure Site Recovery** | 如果 VM 已啟用 Azure Site Recovery 的複寫，您就無法使用 Azure Migrate Server 遷移進行複寫。|
| **連接埠** | HTTPS 埠443上用來傳送 VM 複寫資料的輸出連線。|

### <a name="url-access-public-cloud"></a>URL 存取（公用雲端）

Hyper-v 主機上的複寫提供者軟體將需要存取這些 Url。

**URL** | **詳細資料**
--- | ---
login.microsoftonline.com | 使用 Active Directory 的存取控制和身分識別管理。
backup.windowsazure.com | 複寫資料傳輸和協調。
*.hypervrecoverymanager.windowsazure.com | 用於複寫管理。
*.blob.core.windows.net | 將資料上傳至儲存體帳戶。 
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
time.windows.com | 驗證系統與通用時間之間的時間同步處理。

### <a name="url-access-azure-government"></a>URL 存取（Azure Government）

Hyper-v 主機上的複寫提供者軟體將需要存取這些 Url。

**URL** | **詳細資料**
--- | ---
login.microsoftonline.us | 使用 Active Directory 的存取控制和身分識別管理。
backup.windowsazure.us | 複寫資料傳輸和協調。
*.hypervrecoverymanager.windowsazure.us | 用於複寫管理。
*.blob.core.usgovcloudapi.net | 將資料上傳至儲存體帳戶。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
time.nist.gov | 驗證系統與通用時間之間的時間同步處理。

## <a name="azure-vm-requirements"></a>Azure VM 需求

複寫至 Azure 的所有內部部署 Vm 都必須符合此表中摘要說明的 Azure VM 需求。

**元件** | **需求** | **詳細資料**
--- | --- | ---
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則檢查會失敗。
資料磁碟計數 | 16或更少。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 最多 4,095 GB | 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。
VM 名稱 | 從 1 到 63 個字元。<br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。
在遷移後連接-Windows | 若要在遷移後連線至執行 Windows 的 Azure Vm：<br/><br/> -在遷移之前，請在內部部署 VM 上啟用 RDP。 確定已針對 [公用]**** 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆]**** > [允許的應用程式]**** 中已針對所有設定檔允許 RDP。<br/><br/> -若為站對站 VPN 存取，請啟用 rdp，並允許**Windows 防火牆**中的 rdp  ->  **允許的應用程式和功能**用於**網域和專用**網。 此外，請檢查作業系統的 SAN 原則是否設定為**OnlineAll**。 [深入了解](prepare-for-migration.md)。 |
在遷移後連接-Linux | 若要在使用 SSH 進行遷移之後連線到 Azure Vm：<br/><br/> 在進行遷移之前，請在內部部署機器上，確認安全殼層服務已設定為 [啟動]，且防火牆規則允許 SSH 連線。<br/><br/> -在遷移之後，在 Azure VM 上，允許已容錯移轉 VM 上的網路安全性群組規則之 SSH 埠的連入連線，以及它所連接的 Azure 子網。 此外，請新增 VM 的公用 IP 位址。 |  

## <a name="next-steps"></a>後續步驟

[遷移 Hyper-v vm](tutorial-migrate-hyper-v.md)以進行遷移。
