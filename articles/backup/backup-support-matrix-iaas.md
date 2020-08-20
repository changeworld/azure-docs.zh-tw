---
title: Azure VM 備份的支援矩陣
description: 摘要說明使用 Azure 備份服務來備份 Azure VM 時的支援設定和限制。
ms.topic: conceptual
ms.date: 09/13/2019
ms.custom: references_regions
ms.openlocfilehash: cfa8a6c7cb2da35929151dfce09c0bc31ee69bb2
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88611529"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM 備份的支援矩陣

您可使用 [Azure 備份服務](backup-overview.md)來備份內部部署機器與工作負載，以及 Azure 虛擬機器 (VM)。 本文將摘要說明使用 Azure 備份來備份 Azure VM 時的支援設定與限制。

其他支援矩陣：

- Azure 備份的[一般支援矩陣](backup-support-matrix.md)
- Azure 備份 server/System Center Data Protection Manager (DPM) 備份的[支援矩陣](backup-support-matrix-mabs-dpm.md)
- 使用 Microsoft Azure 復原服務 (MARS) 代理程式進行備份的[支援矩陣](backup-support-matrix-mars-agent.md)

## <a name="supported-scenarios"></a>支援的案例

以下說明如何使用 Azure 備份服務來備份和還原 Azure VM。

**案例** | **Backup** | **代理程式** |**Restore**
--- | --- | --- | ---
直接備份 Azure VM  | 備份整部 VM。  | Azure VM 上不需要其他代理程式。 Azure 備份會安裝延伸模組，並將其用於在 VM 上執行的 [Azure VM 代理程式](../virtual-machines/extensions/agent-windows.md) (機器翻譯)。 | 以下列方式進行還原：<br/><br/> - **建立基本 VM**。 此方式適用於 VM 沒有特殊設定 (例如多個 IP 位址) 的情況。<br/><br/> - **還原 VM 磁碟**。 還原磁碟。 然後將其連結至現有 VM，或使用 PowerShell 從磁碟建立新的 VM。<br/><br/> - **取代 VM 磁碟**。 如果有 VM 存在，且該 VM 使用受控磁碟 (未加密)，您可以還原某個磁碟，並用它來取代 VM 上現有的磁碟。<br/><br/> - **還原特定檔案/資料夾**。 您可還原單一 VM (而非整個 VM) 中的檔案/資料夾。
直接備份 Azure VM (僅限 Windows)  | 備份特定檔案/資料夾/磁碟區。 | 安裝 [Azure 復原服務代理程式](backup-azure-file-folder-backup-faq.md)。<br/><br/> 您可以將 MARS 代理程式與 Azure VM 代理程式的備份擴充功能一起執行，以在檔案/資料夾層級備份 VM。 | 還原特定資料夾/檔案。
將 Azure VM 備份至備份伺服器  | 將檔案/資料夾/磁碟區、系統狀態/裸機檔案、應用程式資料備份至 System Center DPM 或 Microsoft Azure 備份伺服器 (MABS)。<br/><br/> 然後，DPM/MABS 會再備份至備份保存庫。 | 在 VM 上安裝 DPM/MABS 保護代理程式。 在 DPM/MABS 上安装 MARS 代理程式。| 還原檔案/資料夾/磁碟區、系統狀態/裸機檔案、應用程式資料。

深入了解使用[備份伺服器](backup-architecture.md#architecture-back-up-to-dpmmabs)的備份與[支援需求](backup-support-matrix-mabs-dpm.md)。

## <a name="supported-backup-actions"></a>支援的備份動作

**動作** | **支援**
--- | ---
備份已關閉/離線的 VM | 支援。<br/><br/> 僅建立絕對一致快照集，而非應用程式一致快照集。
移轉至受控磁碟後備份磁碟 | 支援。<br/><br/> 備份會繼續運作。 您不需要執行任何動作。
啟用資源群組鎖定後備份受控磁碟 | 不支援。<br/><br/> 在達到還原點上限時，Azure 備份將無法刪除較舊的還原點，且備份會開始失敗。
修改 VM 的備份原則 | 支援。<br/><br/> VM 會使用新原則中的排程和保留期設定進行備份。 如果延長保留期設定，就會標示現有的復原點，並加以保留。 如果縮短上述設定，則會在下一個清除作業中剪除現有的復原點，且最終加以刪除。
取消備份作業| 在快照集程序中支援。<br/><br/> 在快照集傳輸至保存庫期間不支援。
將 VM 備份至不同的區域或訂用帳戶 |不支援。<br><br>若要順利備份，虛擬機器必須位於與備份保存庫相同的訂用帳戶中。
每日備份 (透過 Azure VM 擴充功能) | 每天執行一次排程備份。<br/><br/>Azure 備份服務每天支援最多 9 次隨選備份，但 Microsoft 建議每日不超過 4 次隨選備份，以保持最佳效能。
每日備份 (透過 MARS 代理程式) | 每天執行三次排程備份。
每日備份 (透過 DPM/MABS) | 每天執行兩次排程備份。
每月/每年備份| 在使用 Azure VM 擴充功能進行備份時不支援。 僅支援每日和每週備份。<br/><br/> 您可以設定依每月/每年的保留期間來保留每日/每週備份的原則。
自動時鐘調整 | 不支援。<br/><br/> 在備份 VM 時，Azure 備份不會自動調整日光節約時間變更。<br/><br/>  請視需要手動修改原則。
[混合式備份的安全性功能](./backup-azure-security-feature.md) |不支援停用安全性功能。
備份其電腦時間已變更的 VM | 不支援。<br/><br/> 如果在啟用該 VM 的備份之後，電腦時間變更為未來的日期時間;但是，即使時間變更已還原，也不保證備份成功。
[虛擬機器擴展集中](../virtual-machine-scale-sets/overview.md)的 Azure vm | [協調流程模式](../virtual-machine-scale-sets/orchestration-modes.md#orchestration-modes)設定為3的 vm 支援備份和還原。 <br><br>不支援可用性設定組。

## <a name="operating-system-support-windows"></a>作業系統支援 (Windows)

下表摘要說明備份 Windows Azure VM 時所支援的作業系統。

**案例** | **OS 支援**
--- | ---
使用 Azure VM 代理程式擴充功能進行備份 | - Windows 10 用戶端 (僅限 64 位元) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> -Windows Server 2012 (Datacenter/Standard)  <br/><br/> - Windows Server 2008 R2 (RTM 及 SP1 Standard)  <br/><br/> - Windows Server 2008 (僅限 64 位元)
使用 MARS 代理程式進行備份 | [支援的](backup-support-matrix-mars-agent.md#supported-operating-systems)作業系統。
使用 DPM/MABS 備份 | 使用 [MABS](backup-mabs-protection-matrix.md) 和 [DPM](/system-center/dpm/dpm-protection-matrix) 進行備份時支援的作業系統。

Azure 備份不支援 32 位元作業系統。

## <a name="support-for-linux-backup"></a>Linux 備份的支援

以下是您備份 Linux 機器時所支援的項目。

**動作** | **支援**
--- | ---
使用 Linux Azure VM 代理程式備份 Linux Azure VM | 檔案一致備份。<br/><br/> 使用[自訂指令碼](backup-azure-linux-app-consistent.md)進行應用程式一致備份。<br/><br/> 在還原期間，您可建立新的 VM、還原磁碟並將其用於建立 VM，或還原磁碟並將其用於取代現有 VM 上的磁碟。 您也可以還原個別檔案和資料夾。
使用 MARS 代理程式備份 Linux Azure VM | 不支援。<br/><br/> MARS 代理程式只能安裝在 Windows 機器上。
使用 DPM/MABS 備份 Linux Azure VM | 不支援。

## <a name="operating-system-support-linux"></a>作業系統支援 (Linux)

針對 Azure VM Linux 備份，Azure 備份支援 [Azure 所背書的 Linux 散發套件清單](../virtual-machines/linux/endorsed-distros.md)。 請注意：

- Azure 備份不支援 Core OS Linux。
- Azure 備份不支援 32 位元作業系統。
- 只要 VM 上已有[適用於 Linux 的 Azure VM 代理程式](../virtual-machines/extensions/agent-linux.md) (機器翻譯)，且支援 Python，即可使用其他自備 Linux 發行版本。
- 如果未安裝 Python 2.7 版，Azure 備份不支援 proxy 設定的 Linux VM。

## <a name="backup-frequency-and-retention"></a>備份頻率和保留期

**設定** | **限制**
--- | ---
每個受保護執行個體的復原點數目上限 (電腦/工作負載) | 9999。
復原點的到期時間上限 | 沒有限制。
備份至保存庫的頻率上限 (Azure VM 擴充功能) | 每天 1 次。
備份至保存庫的頻率上限 (MARS 代理程式) | 每天備份三次。
備份至 DPM/MABS 的頻率上限 | 每隔 15 分鐘 (SQL Server)。<br/><br/> 每小時一次 (其他工作負載)。
復原點保留期 | 每日、每週、每月，以及每年。
最大保留期間 | 視備份頻率而定。
DPM/MABS 磁碟上的復原點 | 64 個 (檔案伺服器) 及 448 個 (應用程式伺服器)。<br/><br/> 內部部署 DPM 的磁帶復原點沒有限制。

## <a name="supported-restore-methods"></a>支援的還原方法

**還原選項** | **詳細資料**
--- | ---
**建立新的 VM** | 從還原點快速建立及啟動基本 VM 並加以執行。<br/><br/> 您可指定 VM 的名稱，選取要放置 VM 的資源群組和虛擬網路 (VNet)，以及為已還原的 VM 指定儲存體帳戶。 建立新 VM 的區域必須與來源 VM 相同。
**還原磁碟** | 還原 VM 磁碟，以便後續用於建立新 VM。<br/><br/> Azure 備份提供一個範本，協助您自訂和建立 VM。 <br/><br> 還原作業會產生範本，您可以下載並使用該範本來指定自訂 VM 設定，並建立 VM。<br/><br/> 磁碟會複製到所指定的資源群組。<br/><br/> 或者，您可以將磁碟連結至現有 VM，或使用 PowerShell 建立新的 VM。<br/><br/> 此選項十分適用於自訂 VM、新增備份時沒有的組態設定，或新增必須使用範本或 PowerShell 來配置的設定。
**取代現有的** | 您可以還原磁碟，然後使用該磁碟來取代現有 VM 上的磁碟。<br/><br/> 目前的 VM 必須存在。 如果已刪除，則無法使用此選項。<br/><br/> Azure 備份會在取代磁碟前，取得現有 VM 的快照集，並儲存於所指定的暫存位置， 然後使用所選還原點來取代連線到 VM 的現有磁碟。<br/><br/> 快照集會複製到保存庫，並根據保留原則加以保留。 <br/><br/> 完成取代磁碟作業之後，原始磁碟會保留在資源群組中。 如果不需要，您可以選擇手動刪除原始磁片。 <br/><br/>針對未加密的受控 VM 支援取代現有的項目。 不支援非受控磁碟、[一般化的 VM](../virtual-machines/windows/capture-image-resource.md) 或[使用自訂映像建立的](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) \(英文\) VM。<br/><br/> 如果還原點中的磁碟數目多於或少於目前的 VM，則還原點中的磁碟數目只會反映該 VM 組態。<br><br> 具有連結資源的 Vm （例如 [使用者指派的受控識別](../active-directory/managed-identities-azure-resources/overview.md) 和 [Key Vault](../key-vault/general/overview.md)）也支援「取代現有」。
**跨區域 (次要區域)** | 跨區域還原可用來還原次要區域 (即 [Azure 配對區域](../best-practices-availability-paired-regions.md#what-are-paired-regions) (機器翻譯)) 中的 Azure VM。<br><br> 如果備份是在次要區域中完成，即可將所有 Azure VM 還原至選取的復原點。<br><br> 這項功能適用於下列選項：<br> <li> [建立 VM](./backup-azure-arm-restore-vms.md#create-a-vm) <br> <li> [還原磁碟](./backup-azure-arm-restore-vms.md#restore-disks) (機器翻譯) <br><br> 目前不支援[取代現有磁碟](./backup-azure-arm-restore-vms.md#replace-existing-disks) (機器翻譯) 選項。<br><br> 權限<br> 次要地區的還原作業可由備份管理員和應用程式管理員執行。

## <a name="support-for-file-level-restore"></a>檔案層級還原的支援

**Restore** | **支援**
--- | ---
跨作業系統還原檔案 | 您可以在任何與備份的 VM 具有相同 (或相容) 作業系統的機器上還原檔案。 請參閱[相容的作業系統表格](backup-azure-restore-files-from-vm.md#system-requirements)。
從加密的 VM 還原檔案 | 不支援。
從有網路限制的儲存體帳戶還原檔案 | 不支援。
使用 Windows 儲存空間還原 VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原檔案。
使用 LVM/RAID 陣列還原 Linux VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原。
還原具有特殊網路設定的檔案 | 不支援在相同的 VM 上還原。 <br/><br/> 請在相容的 VM 上還原。

## <a name="support-for-vm-management"></a>VM 管理的支援

下表摘要說明在執行 VM 管理工作 (例如新增或取代 VM 磁碟) 期間的備份支援。

**Restore** | **支援**
--- | ---
跨訂用帳戶/區域 (region)/區域 (zone) 進行還原。 | 不支援。
還原至現有的 VM | 使用更換磁碟選項。
在儲存體帳戶已啟用 Azure 儲存體服務加密 (SSE) 的情況下還原磁碟 | 不支援。<br/><br/> 請還原至未啟用 SSE 的帳戶。
還原至混合儲存體帳戶 |不支援。<br/><br/> 根據儲存體帳戶類型，所有已還原的磁碟都將是進階或標準磁碟，而非混合磁碟。
將 VM 直接還原至可用性設定組 | 針對受控磁碟，您可還原磁碟，並使用範本中的可用性設定組選項。<br/><br/> 不支援非受控磁碟。 對於非受控磁碟，請還原磁碟，然後在可用性設定組中建立 VM。
升級至受控 VM 後還原非受控 VM 的備份| 支援。<br/><br/> 您可以還原磁碟，然後建立受控 VM。
在 VM 移轉至受控磁碟之前將 VM 還原至還原點 | 支援。<br/><br/> 您可以還原至非受控磁碟 (預設值)、將還原的磁碟轉換為受控磁碟，然後使用受控磁碟建立 VM。
還原已刪除的 VM。 | 支援。<br/><br/> 您可以從復原點還原 VM。
透過入口網站還原屬於多網域控制站 (DC) 組態的 DC VM | 如果使用 PowerShell 來還原磁碟並建立 VM，則支援此作業。
還原不同虛擬網路中的 VM |支援。<br/><br/> 虛擬網路必須位於相同的訂用帳戶和區域中。

## <a name="vm-compute-support"></a>VM 計算支援

**計算** | **支援**
--- | ---
VM 大小 |至少有 2 個 CPU 核心和 1 GB RAM 的任何 Azure VM 大小。<br/><br/> [深入了解。](../virtual-machines/sizes.md)
備份[可用性設定組](../virtual-machines/availability.md#availability-sets)中的 VM | 支援。<br/><br/> 您無法使用快速建立 VM 的選項來還原可用性設定組中 VM。 在還原 VM 時，您必須還原磁碟並將其用來部署 VM，或還原磁碟並將其用來取代現有的磁碟。
備份使用 [Hybrid Use Benefit (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md) (機器翻譯) 部署的 VM | 支援。
備份在[擴展集](../virtual-machine-scale-sets/overview.md)中部署的 VM |支援。 容錯網域的[協調流程模式](../virtual-machine-scale-sets/orchestration-modes.md)應該設定為2。 不支援可用性設定組。
備份從[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)部署的 vm<br/><br/> (由 Microsoft 或第三方發佈) |支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。 我們不會還原作為 Vm 的 Azure Marketplace Vm，因為這些 Vm 需要購買資訊。 它們只會還原為磁片。
備份從自訂映像 (第三方) 部署的 VM |支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。
備份已移轉至 Azure 的 VM| 支援。<br/><br/> 若要備份 VM，必須在已移轉的機器上安裝 VM 代理程式。
備份多部 VM 一致性 | Azure 備份不會跨多個 Vm 提供資料和應用程式一致性。
使用[診斷設定](../azure-monitor/platform/platform-logs-overview.md) (機器翻譯) 進行備份  | 不支援。 <br/><br/> 如果使用 [[新建]](backup-azure-arm-restore-vms.md#create-a-vm) 選項來觸發透過診斷設定還原 Azure VM，則還原會失敗。
還原區域固定的 VM | 支援 (適用於在 2019 年 1 月之後備份的 VM，以及支援[可用性區域](https://azure.microsoft.com/global-infrastructure/availability-zones/)的 VM)。<br/><br/>目前支援還原至固定在 VM 中的相同區域。 不過，如果該區域不受支援，則還原會失敗。
第 2 代 VM | 支援 <br> Azure 備份支援[第 2 代 VM](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) 的備份和還原。 當這些 Vm 從復原點還原時，就會還原為 [Gen2 vm](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)。
使用鎖定備份 Azure Vm | 未受管理的 Vm 不支援。 <br><br> 受管理的 Vm 支援。
[Spot VM](../virtual-machines/windows/spot-vms.md) | 不支援。 Azure 備份將現成的 Vm 還原為一般的 Azure Vm。

## <a name="vm-storage-support"></a>VM 儲存體支援

**元件** | **支援**
--- | ---
Azure VM 資料磁碟 | 在所有區域中，支援最多32個磁片的 Azure Vm 備份都處於公開預覽狀態。<br><br> 使用非受控磁碟的 Azure VM 或傳統 VM 僅支援備份最多 16 個磁碟。
資料磁碟大小 | 個別磁碟大小最高可達 32 TB，單一 VM 中所有磁碟大小最高可達 256 TB。
儲存體類型 | 標準 HDD、標準 SSD、進階 SSD。
受控磁碟 | 支援。
加密磁碟 | 支援。<br/><br/> 可備份已啟用 Azure 磁碟加密的 Azure VM (無論是否具有 Azure AD 應用程式)。<br/><br/> 已加密的 VM 無法在檔案/資料夾層級復原。 您必須復原整部 VM。<br/><br/> 您可以對已受到 Azure 備份保護的 VM 啟用加密。
已啟用寫入加速器的磁碟 | 不支援。<br/><br/> Azure 備份會在備份期間自動排除啟用寫入加速器 (WA) 的磁碟。 因為它們不會備份，所以您無法從 VM 的復原點還原這些磁片。 <br><br> **重要注意事項**：具有 WA 磁碟的虛擬機器需要網際網路連線才能成功備份 (即使這些磁碟已從備份中排除也一樣。)
備份和還原已刪除重複資料的 VM/磁碟 | Azure 備份不支援重復資料刪除。 如需詳細資訊，請參閱這篇[文章](./backup-support-matrix.md#disk-deduplication-support) <br/> <br/>  -Azure 備份不會跨復原服務保存庫中的 Vm 刪除 <br/> <br/>  -如果在還原期間有 Vm 處於重復資料刪除狀態，則無法還原檔案，因為保存庫無法理解格式。 不過，您可以成功執行完整的 VM 還原。
將磁碟新增至受保護的 VM | 支援。
在受保護的 VM 上調整磁碟大小 | 支援。
共用存放裝置| 不支援使用叢集共用磁碟區 (CSV) 或向外延展檔案伺服器備份 Vm。 CSV 寫入器可能會在備份期間失敗。 還原時，包含 CSV 磁碟區的磁碟可能無法執行。
[共用磁碟](../virtual-machines/windows/disks-shared-enable.md) | 不支援。

## <a name="vm-network-support"></a>VM 網路支援

**元件** | **支援**
--- | ---
網路介面 (NIC) 的數目 | 特定 Azure VM 大小所支援的 NIC 數目上限。<br/><br/> 在還原程序中建立 VM 時，系統會建立 NIC。<br/><br/> 已還原 VM 上的 NIC 數目，會與在 VM 啟用保護時的 NIC 數目相對應。 在啟用保護後移除 NIC 並不會影響計數。
內部/外部負載平衡器 |支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
多個保留的 IP 位址 |支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
具有多個網路介面卡的 VM| 支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
具有公用 IP 位址的 VM| 支援。<br/><br/> 將現有的公用 IP 位址與 NIC 建立關聯，或在還原完成後建立位址，並將其與 NIC 建立關聯。
NIC/子網路上的網路安全性群組 (NSG)。 |支援。
靜態 IP 位址 | 不支援。<br/><br/> 會指派一組動態 IP 位址至從還原點建立的新 VM。<br/><br/> 針對傳統 VM，您無法備份使用保留 IP 位址且未定義端點的 VM。
動態 IP 位址 |支援。<br/><br/> 如果來源 VM 上的 NIC 使用動態 IP 位址，則已還原 VM 上的 NIC 依預設也會使用動態 IP 位址。
Azure 流量管理員| 支援。<br/><br/>如果備份的 VM 位於流量管理員中，請手動將已還原 VM 新增至相同的流量管理員執行個體。
Azure DNS |支援。
自訂 DNS |支援。
透過 HTTP Proxy 建立輸出連線 | 支援。<br/><br/> 不支援已驗證的 Proxy。
虛擬網路服務端點| 支援。<br/><br/> 防火牆和虛擬網路儲存體帳戶設定應允許來自所有網路的存取。

## <a name="vm-security-and-encryption-support"></a>VM 安全性與加密支援

Azure 備份支援傳輸中和待用資料的加密：

對 Azure 的網路流量：

- 從伺服器到復原服務保存庫的備份流量會使用進階加密標準 256 進行加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。
- 備份資料會在復原服務保存庫中以加密格式儲存。
- 只有您擁有可解除鎖定此資料的加密金鑰。 Microsoft 無法解密在任何時間點備份的資料。

  > [!WARNING]
  > 在設定保存庫後，只有您能夠存取加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。

資料安全性：

- 備份 Azure VM 時，您必須在虛擬機器「內部」設定加密。
- Azure 備份支援 Azure 磁碟加密，其會在 Windows 虛擬機器上使用 BitLocker，且在 Linux 虛擬機器上使用 **dm-crypt**。
- 在後端，Azure 備份會使用 [Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，以保護待用資料。

**機器** | **傳輸中** | **待用**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | ![是][green] | ![是][green]
Azure VM | ![是][green] | ![是][green]
使用 DPM 的內部部署/Azure VM | ![是][green] | ![是][green]
使用 MABS 的內部部署/Azure VM | ![是][green] | ![是][green]

## <a name="vm-compression-support"></a>VM 壓縮支援

備份支援壓縮備份流量，如下表摘要說明。 請注意：

- 針對 Azure VM，VM 延伸模組會透過儲存體網路直接從 Azure 儲存體帳戶讀取資料， 不需要壓縮此流量。
- 如果您正在使用 DPM 或 MABS，則可先壓縮資料再將其備份至 DPM/MABS，以節省頻寬。

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮到保存庫 (HTTPS)**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | NA | ![是][green]
Azure VM | NA | NA
使用 DPM 的內部部署/Azure VM | ![是][green] | ![是][green]
使用 MABS 的內部部署/Azure VM | ![是][green] | ![是][green]

## <a name="next-steps"></a>後續步驟

- [備份 Azure VM](backup-azure-arm-vms-prepare.md)。
- [直接備份 Windows 機器](tutorial-backup-windows-server-to-azure.md)，而不使用備份伺服器。
- [設定 MABS](backup-azure-microsoft-azure-backup.md) 以備份至 Azure，然後將工作負載備份至 MABS。
- [設定 DPM](backup-azure-dpm-introduction.md) 以備份至 Azure，然後將工作負載備份至 DPM。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
