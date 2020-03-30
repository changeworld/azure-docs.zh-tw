---
title: Azure VM 備份的支援矩陣
description: 摘要說明使用 Azure 備份服務來備份 Azure VM 時的支援設定和限制。
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: d86ce94c62ec9f25b364e9fdc963e3043b274722
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389285"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM 備份的支援矩陣

可以使用 Azure[備份服務](backup-overview.md)備份本地電腦和工作負荷以及 Azure 虛擬機器 （VM）。 本文總結了使用 Azure 備份備份 Azure VM 時的支援設置和限制。

其他支援矩陣：

- Azure 備份[的一般支援矩陣](backup-support-matrix.md)
- Azure 備份伺服器/系統中心資料保護管理器 （DPM） 備份[的支援矩陣](backup-support-matrix-mabs-dpm.md)
- [使用](backup-support-matrix-mars-agent.md)Microsoft Azure 恢復服務 （MARS） 代理支援備份矩陣

## <a name="supported-scenarios"></a>支援的案例

以下說明如何使用 Azure 備份服務來備份和還原 Azure VM。

**案例** | **備份** | **代理** |**還原**
--- | --- | --- | ---
直接備份 Azure VM  | 備份整個 VM。  | Azure VM 上不需要其他代理。 Azure 備份安裝並使用擴展到在 VM 上運行的[Azure VM 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。 | 以下列方式進行還原：<br/><br/> - **創建基本 VM**。 如果 VM 沒有特殊配置（如多個 IP 位址），則此功能非常有用。<br/><br/> - **還原 VM 磁碟**。 還原磁碟。 然後將其附加到現有 VM，或使用 PowerShell 從磁片創建新 VM。<br/><br/> - **取代 VM 磁碟**。 如果有 VM 存在，且該 VM 使用受控磁碟 (未加密)，您可以還原某個磁碟，並用它來取代 VM 上現有的磁碟。<br/><br/> - **還原特定檔案/資料夾**。 可以從 VM 而不是從整個 VM 還原檔/資料夾。
直接備份 Azure VM (僅限 Windows)  | 備份特定檔/資料夾/卷。 | 安裝[Azure 恢復服務代理](backup-azure-file-folder-backup-faq.md)。<br/><br/> 您可以將 MARS 代理程式與 Azure VM 代理程式的備份擴充功能一起執行，以在檔案/資料夾層級備份 VM。 | 還原特定資料夾/檔案。
將 Azure VM 備份至備份伺服器  | 備份檔案/資料夾/卷;系統狀態/裸機檔;應用資料到系統中心 DPM 或 Microsoft Azure 備份伺服器 （MABS）。<br/><br/> 然後，DPM/MABS 備份到備份保存庫。 | 在 VM 上安裝 DPM/MABS 保護代理。 在 DPM/MABS 上安装 MARS 代理程式。| 還原檔案/資料夾/磁碟區、系統狀態/裸機檔案、應用程式資料。

瞭解有關[使用備份伺服器進行備份](backup-architecture.md#architecture-back-up-to-dpmmabs)以及[有關支援要求的更多詳細資訊](backup-support-matrix-mabs-dpm.md)。

>[!NOTE]
> **Azure 備份現在支援使用 Azure 虛擬機器備份解決方案進行選擇性磁片備份和還原。**
>
>如今，Azure 備份支援使用虛擬機器備份解決方案一起備份 VM 中的所有磁片（作業系統和資料）。 使用排除磁片功能，您可以選擇從 VM 中的許多資料磁片備份一個或多個資料磁片。 這為備份和恢復需求提供了高效且經濟高效的解決方案。 每個復原點都包含備份操作中包含的磁片的資料，這進一步允許您在還原操作期間從給定復原點復原磁碟子集。 這適用于從快照和保存庫還原。
>
>**要註冊預覽版，請寫信給我們：AskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>支援的備份動作

**動作** | **支援**
--- | ---
備份關閉/離線 VM 的 VM | 支援。<br/><br/> 僅建立絕對一致快照集，而非應用程式一致快照集。
遷移到託管磁片後備份磁片 | 支援。<br/><br/> 備份會繼續運作。 您不需要執行任何動作。
啟用資源群組鎖定後備份受控磁碟 | 不支援。<br/><br/> Azure 備份無法刪除較舊的還原點，並且當達到還原點的最大限制時，備份將開始失敗。
修改 VM 的備份原則 | 支援。<br/><br/> 將使用新策略中的計畫和保留設置來備份 VM。 如果延長保留期設定，就會標示現有的復原點，並加以保留。 如果它們被減少，則現有復原點將在下一個清理作業中被刪掉並最終刪除。
取消備份作業| 在快照集程序中支援。<br/><br/> 在快照集傳輸至保存庫期間不支援。
將 VM 備份至不同的區域或訂用帳戶 |不支援。
每日備份 (透過 Azure VM 擴充功能) | 每天執行一次排程備份。<br/><br/>Azure 備份服務每天最多支援 9 個按需備份，但 Microsoft 建議每天不超過 4 次按需備份，以確保最佳性能。
每日備份 (透過 MARS 代理程式) | 每天執行三次排程備份。
每日備份 (透過 DPM/MABS) | 每天執行兩次排程備份。
每月/每年備份| 在使用 Azure VM 擴充功能進行備份時不支援。 僅支援每日和每週備份。<br/><br/> 您可以設定依每月/每年的保留期間來保留每日/每週備份的原則。
自動時鐘調整 | 不支援。<br/><br/> 備份 VM 時，Azure 備份不會自動調整夏令時更改。<br/><br/>  請視需要手動修改原則。
[混合式備份的安全性功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |不支援禁用安全功能。
備份其電腦時間已更改的 VM | 不支援。<br/><br/> 如果在為該 VM 啟用備份後，電腦時間更改為將來的日期和時間;如果為該 VM 啟用備份後，電腦時間將更改為將來的約會時間。但是，即使時間更改還原，也不能保證成功備份。  

## <a name="operating-system-support-windows"></a>作業系統支援 (Windows)

下表總結了備份 Windows Azure VM 時支援的作業系統。

**案例** | **作業系統支援**
--- | ---
使用 Azure VM 代理程式擴充功能進行備份 | - Windows 10 用戶端（僅限 64 位） <br/><br/>- Windows 伺服器 2019 （資料中心/資料中心核心/標準） <br/><br/> - Windows 伺服器 2016 （資料中心/資料中心核心/標準） <br/><br/> - Windows 伺服器 2012 R2 （資料中心/標準） <br/><br/> - Windows 伺服器 2008 R2 （RTM 和 SP1 標準）  <br/><br/> - Windows 伺服器 2008 （僅限 64 位）
使用 MARS 代理程式進行備份 | [支援的](backup-support-matrix-mars-agent.md#supported-operating-systems)作業系統。
使用 DPM/MABS 進行備份 | 使用 [MABS](backup-mabs-protection-matrix.md) 和 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) 進行備份時支援的作業系統。

Azure 備份不支援 32 位元作業系統。

## <a name="support-for-linux-backup"></a>Linux 備份的支援

以下是您備份 Linux 機器時所支援的項目。

**動作** | **支援**
--- | ---
使用 Linux Azure VM 代理程式備份 Linux Azure VM | 檔案一致備份。<br/><br/> 使用[自訂指令碼](backup-azure-linux-app-consistent.md)進行應用程式一致備份。<br/><br/> 在還原期間，您可以創建新的 VM、復原磁碟並使用它創建 VM，或復原磁碟，並用它來替換現有 VM 上的磁片。 您也可以還原個別檔案和資料夾。
使用 MARS 代理程式備份 Linux Azure VM | 不支援。<br/><br/> MARS 代理程式只能安裝在 Windows 機器上。
使用 DPM/MABS 備份 Linux Azure VM | 不支援。

## <a name="operating-system-support-linux"></a>作業系統支援 (Linux)

針對 Azure VM Linux 備份，Azure 備份支援 [Azure 所背書的 Linux 散發套件清單](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。 請注意：

- Azure 備份不支援 Core OS Linux。
- Azure 備份不支援 32 位元作業系統。
- 只要 Linux 的[Azure VM 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)在 VM 上可用，並且只要 Python 受支援，其他自帶的 Linux 發行版本可能就起作用。
- 如果 Azure 備份未安裝 Python 版本 2.7，則不支援代理配置的 Linux VM。

## <a name="backup-frequency-and-retention"></a>備份頻率和保留期

**設定** | **限制**
--- | ---
每個受保護實例（機器/工作負載）的最大復原點 | 9999.
復原點的到期時間上限 | 沒有限制。
備份至保存庫的頻率上限 (Azure VM 擴充功能) | 一天一次
備份至保存庫的頻率上限 (MARS 代理程式) | 每天備份三次。
備份至 DPM/MABS 的頻率上限 | SQL Server 每 15 分鐘一班。<br/><br/> 其他工作負載每小時一次。
復原點保留期 | 每日、每週、每月和每年。
最大保留期間 | 視備份頻率而定。
DPM/MABS 磁碟上的復原點 | 64 表示檔案伺服器，448 表示應用伺服器。<br/><br/> 內部部署 DPM 的磁帶復原點沒有限制。

## <a name="supported-restore-methods"></a>支援的還原方法

**還原選項** | **詳細資料**
--- | ---
**建立新的 VM** | 從還原點快速建立及啟動基本 VM 並加以執行。<br/><br/> 您可以為 VM 指定名稱，選擇要放置該 VM 的資源組和虛擬網路 （VNet），並為還原的 VM 指定存儲帳戶。 必須在與源 VM 相同的區域中創建新 VM。
**還原磁碟** | 還原 VM 磁片，然後可用於創建新 VM。<br/><br/> Azure 備份提供一個範本，協助您自訂和建立 VM。 <br/><br> 還原作業會產生範本，您可以下載並使用該範本來指定自訂 VM 設定，並建立 VM。<br/><br/> 磁片將複製到指定的資源組。<br/><br/> 或者，您可以將磁碟連結至現有 VM，或使用 PowerShell 建立新的 VM。<br/><br/> 此選項十分適用於自訂 VM、新增備份時沒有的組態設定，或新增必須使用範本或 PowerShell 來配置的設定。
**取代現有的** | 您可以還原磁碟，然後使用該磁碟來取代現有 VM 上的磁碟。<br/><br/> 目前的 VM 必須存在。 如果已刪除此選項，則無法使用此選項。<br/><br/> Azure 備份在替換磁片之前獲取現有 VM 的快照，並將其存儲在指定的暫存位置。 連接到 VM 的現有磁片將替換為選定的還原點。<br/><br/> 快照將複製到保存庫，並根據保留原則保留。 <br/><br/> 更換磁片操作後，原始磁片將保留在資源組中。 如果不需要原始磁片，可以選擇手動刪除它們。 <br/><br/>針對未加密的受控 VM 支援取代現有的項目。 不支援非受控磁碟、[一般化的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) 或[使用自訂映像建立的](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) \(英文\) VM。<br/><br/> 如果還原點中的磁碟數目多於或少於目前的 VM，則還原點中的磁碟數目只會反映該 VM 組態。<br><br> 對於連結資源（如[使用者分配的託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)或[金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)），VM 不支援替換現有資源，因為在執行還原時，備份用戶端應用對這些資源沒有許可權。
**跨區域（次要區域）** | 跨區域還原可用於還原次要區域中的 Azure VM，次要區域是 Azure[配對區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)。<br><br> 如果備份是在次要區域中完成的，則可以還原所選復原點的所有 Azure VM。<br><br> 此功能可用於以下選項：<br> * [創建 VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [復原磁碟](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> 我們目前不支援["替換現有磁片"](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)選項。<br><br> 權限<br> 備份管理員和應用程式管理員可以對次要區域執行還原操作。

## <a name="support-for-file-level-restore"></a>檔案層級還原的支援

**還原** | **支援**
--- | ---
跨作業系統還原檔案 | 您可以在任何與備份的 VM 具有相同 (或相容) 作業系統的機器上還原檔案。 請參閱[相容作業系統表](backup-azure-restore-files-from-vm.md#system-requirements)。
從加密的 VM 還原檔案 | 不支援。
從有網路限制的儲存體帳戶還原檔案 | 不支援。
使用 Windows 儲存空間還原 VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原檔案。
使用 LVM/RAID 陣列還原 Linux VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原。
還原具有特殊網路設定的檔案 | 不支援在相同的 VM 上還原。 <br/><br/> 請在相容的 VM 上還原。

## <a name="support-for-vm-management"></a>VM 管理的支援

下表總結了在 VM 管理工作（如添加或替換 VM 磁片）期間對備份的支援。

**還原** | **支援**
--- | ---
跨訂用帳戶/區域 (region)/區域 (zone) 進行還原。 | 不支援。
還原至現有的 VM | 使用更換磁碟選項。
在儲存體帳戶已啟用 Azure 儲存體服務加密 (SSE) 的情況下還原磁碟 | 不支援。<br/><br/> 請還原至未啟用 SSE 的帳戶。
還原至混合儲存體帳戶 |不支援。<br/><br/> 根據儲存體帳戶類型，所有已還原的磁碟都將是進階或標準磁碟，而非混合磁碟。
將 VM 直接還原至可用性設定組 | 對於託管磁片，您可以復原磁碟並使用範本中的可用性集選項。<br/><br/> 不支援非受控磁碟。 對於非受控磁碟，請還原磁碟，然後在可用性設定組中建立 VM。
升級到託管 VM 後恢復非託管 VM 的備份| 支援。<br/><br/> 您可以還原磁碟，然後建立受控 VM。
在 VM 移轉至受控磁碟之前將 VM 還原至還原點 | 支援。<br/><br/> 您可以還原至非受控磁碟 (預設值)、將還原的磁碟轉換為受控磁碟，然後使用受控磁碟建立 VM。
還原已刪除的 VM。 | 支援。<br/><br/> 您可以從復原點還原 VM。
透過入口網站還原屬於多網域控制站 (DC) 組態的 DC VM | 如果使用 PowerShell 復原磁碟並創建 VM，則支援。
在不同的虛擬網路中還原 VM |支援。<br/><br/> 虛擬網路必須位於同一訂閱和區域中。

## <a name="vm-compute-support"></a>VM 計算支援

**計算** | **支援**
--- | ---
VM 大小 |至少有 2 個 CPU 核心和 1 GB RAM 的任何 Azure VM 大小。<br/><br/> [深入了解。](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
備份[可用性設定組](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets)中的 VM | 支援。<br/><br/> 不能使用 選項快速建立 VM 來還原可用集中的 VM。 相反，在還原 VM 時，復原磁碟並使用它來部署 VM，或復原磁碟，並使用它替換現有磁片。
備份使用[混合使用權益 （HUB）](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)部署的 VM | 支援。
備份在[規模集中](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)部署的 VM |不支援。
備份從[Azure 應用商店](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)部署的 VM<br/><br/> （由微軟協力廠商發佈） |支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。 我們不會還原支援為 VM 的 Azure 應用商店 VM，因為這些需要購買資訊，但只需要作為磁片。
備份從自訂映射部署的 VM（協力廠商） |支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。
備份遷移到 Azure 的 VM| 支援。<br/><br/> 若要備份 VM，必須在已移轉的機器上安裝 VM 代理程式。
備份多 VM 一致性 | Azure 備份不跨多個 VM 提供資料和應用程式一致性。
使用[診斷設置進行備份](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | 不支援。 <br/><br/> 如果使用["創建新"](backup-azure-arm-restore-vms.md#create-a-vm)選項觸發具有診斷設置的 Azure VM 的還原，則還原將失敗。
恢復區域固定 VM | 支援（適用于 2019 年 1 月之後備份的 VM 以及[可用區域](https://azure.microsoft.com/global-infrastructure/availability-zones/)）。<br/><br/>我們目前支援還原到固定在 VM 中的同一區域。 但是，如果區域不可用，還原將失敗。
第 2 代 VM | 支援 <br> Azure 備份支援第[2 代 VM](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)的備份和恢復。 當這些 VM 從復原點還原時，它們將作為[Gen2 VM](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)還原。

## <a name="vm-storage-support"></a>VM 儲存體支援

**元件** | **支援**
--- | ---
Azure VM 資料磁碟 | 備份具有 16 個或更少資料磁片的 VM。<BR> 若要註冊具有 16 個以上磁碟 (最多 32 個磁碟) 的 VM 私人預覽版，請利用 AskAzureBackupTeam@microsoft.com 寫信給我們
資料磁碟大小 | 單個磁片大小最多可為 32 TB，VM 中的所有磁片最多可組合 256 TB。
儲存體類型 | 標準硬碟、標準 SSD、高級 SSD。
受控磁碟 | 支援。
加密磁碟 | 支援。<br/><br/> 可以備份使用 Azure 磁片加密啟用的 Azure VM（無論是否使用 Azure AD 應用）。<br/><br/> 無法在檔/資料夾級別恢復加密 VM。 您必須恢復整個 VM。<br/><br/> 您可以對已受到 Azure 備份保護的 VM 啟用加密。
已啟用寫入加速器的磁碟 | 不支援。<br/><br/> Azure 備份會自動排除在備份期間啟用寫入加速器的磁片。 由於它們未備份，因此無法從 VM 的復原點還原這些磁片。
備份&還原重複的 VM/磁片 | Azure 備份不支援重復資料消除。 有關詳細資訊，請參閱[本文](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  - Azure 備份不會在恢復服務保存庫中的 VM 中重複重複 <br/> <br/>  - 如果在還原期間存在處於重復資料消除狀態的 VM，則無法還原檔，因為保存庫不了解格式。 但是，您將能夠成功執行完整的 VM 還原。
將磁碟新增至受保護的 VM | 支援。
在受保護的 VM 上調整磁碟大小 | 支援。
共用存放裝置| 不支援使用群集共用卷 （CSV） 或橫向擴展檔案伺服器備份 VM。 CSV 編寫器在備份期間可能會失敗。 還原時，包含 CSV 卷的磁片可能不會出現。
[共用磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | 不支援。

## <a name="vm-network-support"></a>VM 網路支援

**元件** | **支援**
--- | ---
網路介面數量 | 特定 Azure VM 大小所支援的 NIC 數目上限。<br/><br/> 在還原程序中建立 VM 時，系統會建立 NIC。<br/><br/> 已還原 VM 上的 NIC 數目，會與在 VM 啟用保護時的 NIC 數目相對應。 啟用保護後刪除 NIC 不會影響計數。
內部/外部負載平衡器 |支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
多個保留的 IP 位址 |支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
具有多個網路介面卡的 VM| 支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
具有公用 IP 位址的 VM| 支援。<br/><br/> 將現有公共 IP 位址與 NIC 關聯，或創建一個位址，並在還原完成後將其與 NIC 關聯。
NIC/子網路上的網路安全性群組 (NSG)。 |支援。
靜態 IP 位址 | 不支援。<br/><br/> 從還原點創建的新 VM 將分配動態 IP 位址。<br/><br/> 對於經典 VM，不能備份具有保留 IP 位址且沒有定義的終結點的 VM。
動態 IP 位址 |支援。<br/><br/> 如果源 VM 上的 NIC 使用動態 IP 定址，則預設情況下，還原的 VM 上的 NIC 也將使用它。
Azure 流量管理員| 支援。<br/><br/>如果備份的 VM 位於流量管理器中，請手動將還原的 VM 添加到同一流量管理器實例。
Azure DNS |支援。
自訂 DNS |支援。
透過 HTTP Proxy 建立輸出連線 | 支援。<br/><br/> 不支援已驗證的 Proxy。
虛擬網路服務端點| 支援。<br/><br/> 防火牆和虛擬網路存儲帳戶設置應允許從所有網路進行訪問。

## <a name="vm-security-and-encryption-support"></a>VM 安全和加密支援

Azure 備份支援傳輸中和待用資料的加密：

對 Azure 的網路流量：

- 使用進階加密標準 256 對從伺服器到恢復服務保存庫的備份流量進行加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。
- 備份資料會在復原服務保存庫中以加密格式儲存。
- 只有您持有可將此資料解除鎖定的複雜密碼。 Microsoft 無法解密在任何時間點備份的資料。

  > [!WARNING]
  > 設置保存庫後，只有您有權訪問加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。

資料安全性：

- 備份 Azure VM 時，需要在虛擬機器*中*設置加密。
- Azure 備份支援 Azure 磁片加密，在 Windows 虛擬機器上使用 BitLocker，在 Linux 虛擬機器上使用**dm-crypt。**
- 在後端，Azure 備份會使用 [Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，以保護待用資料。

**機器** | **傳輸中** | **待用**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | ![是][green] | ![是][green]
Azure VM | ![是][green] | ![是][green]
使用 DPM 的內部部署/Azure VM | ![是][green] | ![是][green]
使用 MABS 的內部部署/Azure VM | ![是][green] | ![是][green]

## <a name="vm-compression-support"></a>VM 壓縮支援

備份支援壓縮備份流量，如下表摘要。 請注意：

- 對於 Azure VM，VM 擴展程式通過存儲網路直接從 Azure 存儲帳戶讀取資料。 沒有必要壓縮此流量。
- 如果使用 DPM 或 MABS，則可以在資料備份到 DPM/MABS 之前壓縮資料來節省頻寬。

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮到保存庫 （HTTPS）**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | NA | ![是][green]
Azure VM | NA | NA
使用 DPM 的內部部署/Azure VM | ![是][green] | ![是][green]
使用 MABS 的內部部署/Azure VM | ![是][green] | ![是][green]

## <a name="next-steps"></a>後續步驟

- [備份 Azure VM。](backup-azure-arm-vms-prepare.md)
- [直接備份 Windows 機器](tutorial-backup-windows-server-to-azure.md)，而不使用備份伺服器。
- [設定 MABS](backup-azure-microsoft-azure-backup.md) 以備份至 Azure，然後將工作負載備份至 MABS。
- [設定 DPM](backup-azure-dpm-introduction.md) 以備份至 Azure，然後將工作負載備份至 DPM。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
