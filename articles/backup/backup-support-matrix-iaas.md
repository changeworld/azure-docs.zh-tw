---
title: Azure VM 備份的支援矩陣
description: 摘要說明使用 Azure 備份服務來備份 Azure VM 時的支援設定和限制。
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: d86ce94c62ec9f25b364e9fdc963e3043b274722
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80389285"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM 備份的支援矩陣

您可以使用[Azure 備份服務](backup-overview.md)來備份內部部署機器和工作負載，以及 Azure 虛擬機器（vm）。 本文摘要說明使用 Azure 備份備份 Azure Vm 時的支援設定和限制。

其他支援矩陣：

- Azure 備份的[一般支援矩陣](backup-support-matrix.md)
- Azure 備份 server/System Center Data Protection Manager （DPM）備份的[支援矩陣](backup-support-matrix-mabs-dpm.md)
- 使用 Microsoft Azure 復原服務（MARS）代理程式進行備份的[支援矩陣](backup-support-matrix-mars-agent.md)

## <a name="supported-scenarios"></a>支援的案例

以下說明如何使用 Azure 備份服務來備份和還原 Azure VM。

**案例** | **Backup** | **代理程式** |**還原**
--- | --- | --- | ---
直接備份 Azure VM  | 備份整個 VM。  | Azure VM 上不需要其他代理程式。 Azure 備份會安裝並使用在 VM 上執行的[AZURE vm 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)程式的擴充功能。 | 以下列方式進行還原：<br/><br/> - **建立基本 VM**。 如果 VM 沒有任何特殊設定（例如多個 IP 位址），這會很有用。<br/><br/> - **還原 VM 磁碟**。 還原磁碟。 然後將它連結至現有的 VM，或使用 PowerShell 從磁片建立新的 VM。<br/><br/> - **取代 VM 磁碟**。 如果有 VM 存在，且該 VM 使用受控磁碟 (未加密)，您可以還原某個磁碟，並用它來取代 VM 上現有的磁碟。<br/><br/> - **還原特定檔案/資料夾**。 您可以從 VM （而不是從整個 VM）還原檔案/資料夾。
直接備份 Azure VM (僅限 Windows)  | 備份特定檔案/資料夾/磁片區。 | 安裝[Azure 復原服務代理程式](backup-azure-file-folder-backup-faq.md)。<br/><br/> 您可以將 MARS 代理程式與 Azure VM 代理程式的備份擴充功能一起執行，以在檔案/資料夾層級備份 VM。 | 還原特定資料夾/檔案。
將 Azure VM 備份至備份伺服器  | 備份檔案/資料夾/磁片區;系統狀態/裸機檔案;應用程式資料至 System Center DPM 或 Microsoft Azure 備份 Server （MABS）。<br/><br/> DPM/MABS 接著會備份至備份保存庫。 | 在 VM 上安裝 DPM/MABS 保護代理程式。 在 DPM/MABS 上安装 MARS 代理程式。| 還原檔案/資料夾/磁碟區、系統狀態/裸機檔案、應用程式資料。

深入瞭解如何[使用備份伺服器](backup-architecture.md#architecture-back-up-to-dpmmabs)和[支援需求](backup-support-matrix-mabs-dpm.md)來進行備份。

>[!NOTE]
> **Azure 備份現在支援使用 Azure 虛擬機器備份解決方案進行選擇性磁片備份和還原。**
>
>目前，Azure 備份支援使用虛擬機器備份解決方案，同時備份 VM 中的所有磁片（作業系統和資料）。 使用 [排除磁片] 功能時，您可以選擇從 VM 中的多個資料磁片備份一或數個。 這為您的備份和還原需求提供有效率且符合成本效益的解決方案。 每個復原點都包含備份作業中包含的磁片資料，這可讓您在還原作業期間，擁有從指定復原點還原的磁片子集。 這適用于從快照集和保存庫還原兩者。
>
>**若要註冊預覽版，請于前寫信給我們AskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>支援的備份動作

**動作** | **支援**
--- | ---
備份已關閉/離線 VM 的 VM | 支援。<br/><br/> 僅建立絕對一致快照集，而非應用程式一致快照集。
遷移至受控磁片之後備份磁片 | 支援。<br/><br/> 備份會繼續運作。 您不需要執行任何動作。
啟用資源群組鎖定後備份受控磁碟 | 不支援。<br/><br/> Azure 備份無法刪除較舊的還原點，而且當達到還原點的最大限制時，備份將會開始失敗。
修改 VM 的備份原則 | 支援。<br/><br/> 系統會使用新原則中的排程和保留期設定來備份 VM。 如果延長保留期設定，就會標示現有的復原點，並加以保留。 如果降低，現有的復原點將會在下一次清除作業中剪除，最後刪除。
取消備份作業| 在快照集程序中支援。<br/><br/> 在快照集傳輸至保存庫期間不支援。
將 VM 備份至不同的區域或訂用帳戶 |不支援。
每日備份 (透過 Azure VM 擴充功能) | 每天執行一次排程備份。<br/><br/>Azure 備份服務每天支援最多9個隨選備份，但 Microsoft 建議每日不超過四個隨選備份，以確保最佳效能。
每日備份 (透過 MARS 代理程式) | 每天執行三次排程備份。
每日備份 (透過 DPM/MABS) | 每天執行兩次排程備份。
每月/每年備份| 在使用 Azure VM 擴充功能進行備份時不支援。 僅支援每日和每週備份。<br/><br/> 您可以設定依每月/每年的保留期間來保留每日/每週備份的原則。
自動時鐘調整 | 不支援。<br/><br/> 備份 VM 時，Azure 備份不會自動調整日光節約時間的變更。<br/><br/>  請視需要手動修改原則。
[混合式備份的安全性功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |不支援停用安全性功能。
備份其電腦時間已變更的 VM | 不支援。<br/><br/> 如果在啟用該 VM 的備份之後，電腦時間已變更為未來的日期時間，不過，即使還原時間變更，也不保證成功的備份。  

## <a name="operating-system-support-windows"></a>作業系統支援 (Windows)

下表摘要說明備份 Windows Azure Vm 時支援的作業系統。

**案例** | **OS 支援**
--- | ---
使用 Azure VM 代理程式擴充功能進行備份 | -Windows 10 用戶端（僅限64位） <br/><br/>-Windows Server 2019 （Datacenter/Datacenter Core/Standard） <br/><br/> -Windows Server 2016 （Datacenter/Datacenter Core/Standard） <br/><br/> -Windows Server 2012 R2 （Datacenter/Standard） <br/><br/> -Windows Server 2008 R2 （RTM 和 SP1 標準）  <br/><br/> -Windows Server 2008 （僅限64位）
使用 MARS 代理程式進行備份 | [支援](backup-support-matrix-mars-agent.md#supported-operating-systems)的作業系統。
使用 DPM/MABS 備份 | 使用 [MABS](backup-mabs-protection-matrix.md) 和 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) 進行備份時支援的作業系統。

Azure 備份不支援 32 位元作業系統。

## <a name="support-for-linux-backup"></a>Linux 備份的支援

以下是您備份 Linux 機器時所支援的項目。

**動作** | **支援**
--- | ---
使用 Linux Azure VM 代理程式備份 Linux Azure VM | 檔案一致備份。<br/><br/> 使用[自訂指令碼](backup-azure-linux-app-consistent.md)進行應用程式一致備份。<br/><br/> 在還原期間，您可以建立新的 VM、復原磁碟並使用它來建立 VM，或復原磁碟並使用它來取代現有 VM 上的磁片。 您也可以還原個別檔案和資料夾。
使用 MARS 代理程式備份 Linux Azure VM | 不支援。<br/><br/> MARS 代理程式只能安裝在 Windows 機器上。
使用 DPM/MABS 備份 Linux Azure VM | 不支援。

## <a name="operating-system-support-linux"></a>作業系統支援 (Linux)

針對 Azure VM Linux 備份，Azure 備份支援 [Azure 所背書的 Linux 散發套件清單](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。 請注意：

- Azure 備份不支援 Core OS Linux。
- Azure 備份不支援 32 位元作業系統。
- 只要 VM 上有適用于[Linux 的 AZURE VM 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)，而且只要支援 Python，其他自備 Linux 散發套件就可以運作。
- 如果未安裝 Python 版本2.7，Azure 備份不支援已設定 proxy 的 Linux VM。

## <a name="backup-frequency-and-retention"></a>備份頻率和保留期

**設定** | **限制**
--- | ---
每個受保護實例的復原點上限（機器/工作負載） | 9999。
復原點的到期時間上限 | 無限制。
備份至保存庫的頻率上限 (Azure VM 擴充功能) | 一天一次。
備份至保存庫的頻率上限 (MARS 代理程式) | 每天備份三次。
備份至 DPM/MABS 的頻率上限 | SQL Server 每15分鐘。<br/><br/> 針對其他工作負載，一小時一次。
復原點保留期 | 每日、每週、每月和每年。
最大保留期間 | 視備份頻率而定。
DPM/MABS 磁碟上的復原點 | 64（適用于檔案伺服器）和448（適用于應用程式伺服器）。<br/><br/> 內部部署 DPM 的磁帶復原點沒有限制。

## <a name="supported-restore-methods"></a>支援的還原方法

**還原選項** | **詳細資料**
--- | ---
**建立新的 VM** | 從還原點快速建立及啟動基本 VM 並加以執行。<br/><br/> 您可以指定 VM 的名稱，並選取將在其中放置資源群組和虛擬網路（VNet），並為還原的 VM 指定儲存體帳戶。 必須在與來源 VM 相同的區域中建立新的 VM。
**還原磁碟** | 還原 VM 磁片，然後可以用來建立新的 VM。<br/><br/> Azure 備份提供一個範本，協助您自訂和建立 VM。 <br/><br> 還原作業會產生範本，您可以下載並使用該範本來指定自訂 VM 設定，並建立 VM。<br/><br/> 磁片會複製到您指定的資源群組。<br/><br/> 或者，您可以將磁碟連結至現有 VM，或使用 PowerShell 建立新的 VM。<br/><br/> 此選項十分適用於自訂 VM、新增備份時沒有的組態設定，或新增必須使用範本或 PowerShell 來配置的設定。
**取代現有的** | 您可以還原磁碟，然後使用該磁碟來取代現有 VM 上的磁碟。<br/><br/> 目前的 VM 必須存在。 如果已刪除，則無法使用此選項。<br/><br/> Azure 備份會在更換磁片之前取得現有 VM 的快照，並將其儲存在您指定的暫存位置。 已連線至 VM 的現有磁片會取代為選取的還原點。<br/><br/> 快照集會複製到保存庫，並根據保留原則加以保留。 <br/><br/> 取代磁片操作之後，原始磁片會保留在資源群組中。 如果不需要，您可以選擇手動刪除原始磁片。 <br/><br/>針對未加密的受控 VM 支援取代現有的項目。 不支援非受控磁碟、[一般化的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) 或[使用自訂映像建立的](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) \(英文\) VM。<br/><br/> 如果還原點中的磁碟數目多於或少於目前的 VM，則還原點中的磁碟數目只會反映該 VM 組態。<br><br> 已連結資源（例如[使用者指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)或[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)）的 vm 不支援取代現有的，因為備份用戶端應用程式在執行還原時，沒有這些資源的許可權。
**跨區域（次要地區）** | 跨區域還原可以用來還原次要區域中的 Azure Vm，也就是[azure 配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)。<br><br> 如果備份是在次要區域中完成，您可以為選取的復原點還原所有的 Azure Vm。<br><br> 這項功能適用于下列選項：<br> * [建立 VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [復原磁碟](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> 我們目前不支援 [[取代現有磁片](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)] 選項。<br><br> 權限<br> 次要地區的還原作業可以由備份管理員和應用程式管理員執行。

## <a name="support-for-file-level-restore"></a>檔案層級還原的支援

**還原** | **支援**
--- | ---
跨作業系統還原檔案 | 您可以在任何與備份的 VM 具有相同 (或相容) 作業系統的機器上還原檔案。 請參閱[相容的 OS 資料表](backup-azure-restore-files-from-vm.md#system-requirements)。
從加密的 VM 還原檔案 | 不支援。
從有網路限制的儲存體帳戶還原檔案 | 不支援。
使用 Windows 儲存空間還原 VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原檔案。
使用 LVM/RAID 陣列還原 Linux VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原。
還原具有特殊網路設定的檔案 | 不支援在相同的 VM 上還原。 <br/><br/> 請在相容的 VM 上還原。

## <a name="support-for-vm-management"></a>VM 管理的支援

下表摘要說明在 VM 管理工作（例如新增或更換 VM 磁片）期間的備份支援。

**還原** | **支援**
--- | ---
跨訂用帳戶/區域 (region)/區域 (zone) 進行還原。 | 不支援。
還原至現有的 VM | 使用更換磁碟選項。
在儲存體帳戶已啟用 Azure 儲存體服務加密 (SSE) 的情況下還原磁碟 | 不支援。<br/><br/> 請還原至未啟用 SSE 的帳戶。
還原至混合儲存體帳戶 |不支援。<br/><br/> 根據儲存體帳戶類型，所有已還原的磁碟都將是進階或標準磁碟，而非混合磁碟。
將 VM 直接還原至可用性設定組 | 針對受控磁片，您可以復原磁碟，並使用範本中的 [可用性設定組] 選項。<br/><br/> 不支援非受控磁碟。 對於非受控磁碟，請還原磁碟，然後在可用性設定組中建立 VM。
升級至受控 VM 之後，還原未受管理的 vm 備份| 支援。<br/><br/> 您可以還原磁碟，然後建立受控 VM。
在 VM 移轉至受控磁碟之前將 VM 還原至還原點 | 支援。<br/><br/> 您可以還原至非受控磁碟 (預設值)、將還原的磁碟轉換為受控磁碟，然後使用受控磁碟建立 VM。
還原已刪除的 VM。 | 支援。<br/><br/> 您可以從復原點還原 VM。
透過入口網站還原屬於多網域控制站 (DC) 組態的 DC VM | 如果您使用 PowerShell 復原磁碟並建立 VM，則支援。
還原不同虛擬網路中的 VM |支援。<br/><br/> 虛擬網路必須位於相同的訂用帳戶和區域中。

## <a name="vm-compute-support"></a>VM 計算支援

**Compute** | **支援**
--- | ---
VM 大小 |至少有 2 個 CPU 核心和 1 GB RAM 的任何 Azure VM 大小。<br/><br/> [深入了解。](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
備份[可用性設定組](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets)中的 VM | 支援。<br/><br/> 您無法使用快速建立 VM 的選項，來還原可用集合中的 VM。 相反地，當您還原 VM 時，請復原磁碟並使用它來部署 VM，或復原磁碟並使用它來取代現有的磁片。
備份使用[混合式使用優勢（中樞）](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)部署的 vm | 支援。
備份部署在[擴展集中](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)的 vm |不支援。
備份從[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)部署的 vm<br/><br/> （由 Microsoft、協力廠商發行） |支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。 我們不會還原做為 Vm 的 Azure Marketplace Vm，因為這些虛擬機器需要購買資訊，但只能做為磁片。
備份從自訂映射（協力廠商）部署的 Vm |支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。
備份遷移至 Azure 的 Vm| 支援。<br/><br/> 若要備份 VM，必須在已移轉的機器上安裝 VM 代理程式。
備份多部 VM 一致性 | Azure 備份不會提供跨多個 Vm 的資料和應用程式一致性。
使用[診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)進行備份  | 不支援。 <br/><br/> 如果使用 [[建立新](backup-azure-arm-restore-vms.md#create-a-vm)的] 選項來觸發使用診斷設定來還原 Azure VM，則還原會失敗。
還原區域釘選的 Vm | 支援（適用于在2019年1月之後備份且[可用性區域](https://azure.microsoft.com/global-infrastructure/availability-zones/)可供使用的 VM）。<br/><br/>我們目前支援還原至固定在 Vm 中的相同區域。 不過，如果區域無法使用，還原將會失敗。
Gen2 Vm | 支援 <br> Azure 備份支援[Gen2 vm](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)的備份與還原。 從復原點還原這些 Vm 時，它們會還原為[Gen2 vm](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)。

## <a name="vm-storage-support"></a>VM 儲存體支援

**元件** | **支援**
--- | ---
Azure VM 資料磁碟 | 備份具有16個或更少資料磁片的 VM。<BR> 若要註冊具有 16 個以上磁碟 (最多 32 個磁碟) 的 VM 私人預覽版，請利用 AskAzureBackupTeam@microsoft.com 寫信給我們
資料磁碟大小 | 針對 VM 中的所有磁片，個別磁片大小最高可達 32 TB，最大為 256 TB。
儲存體類型 | 標準 HDD、標準 SSD 進階 SSD。
受控磁碟 | 支援。
加密磁碟 | 支援。<br/><br/> 您可以備份使用 Azure 磁碟加密啟用的 Azure Vm （不論是否有 Azure AD 應用程式）。<br/><br/> 加密的 Vm 無法在檔案/資料夾層級復原。 您必須復原整個 VM。<br/><br/> 您可以對已受到 Azure 備份保護的 VM 啟用加密。
已啟用寫入加速器的磁碟 | 不支援。<br/><br/> Azure 備份會在備份期間自動排除已啟用寫入加速器的磁片。 因為它們不會備份，所以您將無法從 VM 的復原點還原這些磁片。
備份 & 還原重復資料刪除的 Vm/磁片 | Azure 備份不支援重復資料刪除。 如需詳細資訊，請參閱這[篇文章](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  -Azure 備份不會跨復原服務保存庫中的 Vm 刪除重複 <br/> <br/>  -如果還原期間有 Vm 處於重復資料刪除狀態，則無法還原檔案，因為保存庫不了解格式。 不過，您將能夠成功執行完整的 VM 還原。
將磁碟新增至受保護的 VM | 支援。
在受保護的 VM 上調整磁碟大小 | 支援。
共用存放裝置| 不支援使用叢集共用磁碟區（CSV）或向外延展檔案伺服器備份 Vm。 CSV 寫入器可能會在備份期間失敗。 還原時，包含 CSV 磁片區的磁片可能無法啟動。
[共用磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | 不支援。

## <a name="vm-network-support"></a>VM 網路支援

**元件** | **支援**
--- | ---
網路介面（Nic）數目 | 特定 Azure VM 大小所支援的 NIC 數目上限。<br/><br/> 在還原程序中建立 VM 時，系統會建立 NIC。<br/><br/> 已還原 VM 上的 NIC 數目，會與在 VM 啟用保護時的 NIC 數目相對應。 啟用保護之後移除 Nic 並不會影響計數。
內部/外部負載平衡器 |支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
多個保留的 IP 位址 |支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
具有多個網路介面卡的 VM| 支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
具有公用 IP 位址的 VM| 支援。<br/><br/> 將現有的公用 IP 位址與 NIC 建立關聯，或建立位址，並在還原完成後將其與 NIC 建立關聯。
NIC/子網路上的網路安全性群組 (NSG)。 |支援。
靜態 IP 位址 | 不支援。<br/><br/> 從還原點建立的新 VM 會被指派一個動態 IP 位址。<br/><br/> 對於傳統 Vm，您無法使用保留的 IP 位址和未定義的端點來備份 VM。
動態 IP 位址 |支援。<br/><br/> 如果來源 VM 上的 NIC 使用動態 IP 位址，則還原 VM 上的 NIC 預設也會使用它。
Azure 流量管理員| 支援。<br/><br/>如果備份的 VM 位於流量管理員中，請手動將還原的 VM 新增至相同的流量管理員實例。
Azure DNS |支援。
自訂 DNS |支援。
透過 HTTP Proxy 建立輸出連線 | 支援。<br/><br/> 不支援已驗證的 Proxy。
虛擬網路服務端點| 支援。<br/><br/> 防火牆與虛擬網路儲存體帳戶設定應允許來自所有網路的存取。

## <a name="vm-security-and-encryption-support"></a>VM 安全性和加密支援

Azure 備份支援傳輸中和待用資料的加密：

對 Azure 的網路流量：

- 從伺服器到復原服務保存庫的備份流量會使用進階加密標準256進行加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。
- 備份資料會在復原服務保存庫中以加密格式儲存。
- 只有您持有可將此資料解除鎖定的複雜密碼。 Microsoft 無法解密在任何時間點備份的資料。

  > [!WARNING]
  > 設定保存庫之後，只有您能夠存取加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。

資料安全性：

- 備份 Azure Vm 時，您*需要在虛擬機器內設定加密。*
- Azure 備份支援 Azure 磁碟加密，這會在 Windows 虛擬機器上使用 BitLocker，而在 Linux 虛擬機器上則是**crypt** 。
- 在後端，Azure 備份會使用 [Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，以保護待用資料。

**機器** | **傳輸中** | **待用**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | ![是][green] | ![是][green]
Azure VM | ![是][green] | ![是][green]
使用 DPM 的內部部署/Azure VM | ![是][green] | ![是][green]
使用 MABS 的內部部署/Azure VM | ![是][green] | ![是][green]

## <a name="vm-compression-support"></a>VM 壓縮支援

備份支援壓縮備份流量，如下表摘要所示。 請注意：

- 針對 Azure Vm，VM 擴充功能會透過儲存體網路直接從 Azure 儲存體帳戶讀取資料。 不需要壓縮此流量。
- 如果您使用的是 DPM 或 MABS，您可以在備份至 DPM/MABS 之前壓縮資料，藉以節省頻寬。

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮至保存庫（HTTPS）**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | NA | ![是][green]
Azure VM | NA | NA
使用 DPM 的內部部署/Azure VM | ![是][green] | ![是][green]
使用 MABS 的內部部署/Azure VM | ![是][green] | ![是][green]

## <a name="next-steps"></a>後續步驟

- [備份 Azure vm](backup-azure-arm-vms-prepare.md)。
- [直接備份 Windows 機器](tutorial-backup-windows-server-to-azure.md)，而不使用備份伺服器。
- [設定 MABS](backup-azure-microsoft-azure-backup.md) 以備份至 Azure，然後將工作負載備份至 MABS。
- [設定 DPM](backup-azure-dpm-introduction.md) 以備份至 Azure，然後將工作負載備份至 DPM。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
