---
title: 常見問題-備份 Azure Vm
description: 在本文中，探索使用 Azure 備份服務備份 Azure Vm 的常見問題解答。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: e6e14209a8df7160d103cb036d38c9fee29b34dd
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296058"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>常見問題-備份 Azure Vm

本文提供使用 [Azure 備份](./backup-overview.md) 服務來備份 Azure vm 的常見問題解答。

## <a name="backup"></a>備份

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>當我建立 VM 映射時，可以啟用這些映射以進行備份嗎？

當您建立 VM 時，您可以針對執行 [受支援作業系統](backup-support-matrix-iaas.md#supported-backup-actions)的 vm 啟用備份。

### <a name="why-initial-backup-is-taking-lot-of-time-to-complete"></a>為何初始備份需要大量的時間才能完成？

初始備份一律是完整備份，而且會取決於資料的大小和處理備份的時間。 <br>
若要改善備份效能，請參閱 [備份最佳做法](./backup-azure-vms-introduction.md#best-practices); [備份考慮](./backup-azure-vms-introduction.md#backup-and-restore-considerations) 和 [備份效能](./backup-azure-vms-introduction.md#backup-performance)<br>
雖然增量備份的備份時間總計小於 24 小時，但是可能不適合第一次備份。

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>VM 成本中是否包含備份成本？

否。 備份成本與 VM 的成本分開。 深入瞭解 [Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>啟用 VM 備份需要哪些許可權？

如果您是 VM 參與者，您可以在 VM 上啟用備份。 如果您是使用自訂角色，則需要下列許可權，才能在 VM 上啟用備份：

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

如果您的復原服務保存庫和 VM 有不同的資源群組，請確定您在復原服務保存庫的資源群組中有寫入權限。  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>隨選備份作業是否會使用與排定備份相同的保留排程？

否。 指定隨選備份作業的保留範圍。 根據預設，若從入口網站觸發，則會保留 30 天。

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>我在最近一些 VM 上啟用了 Azure 磁碟加密。 我的備份是否會繼續運作？

提供 Azure 備份存取 Key Vault 的許可權。 在 PowerShell 中指定權限，如 [Azure 備份 PowerShell](backup-azure-vms-automation.md) 文件的 **啟用備份** 一節中所述。

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>我已將 VM 磁碟遷移至受控磁碟。 我的備份是否會繼續運作？

是，備份會順利運作。 不需要重新設定任何項目。

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>為什麼我在 [設定備份] 精靈中看不到我的 VM？

此精靈只會列出與保存庫位於相同區域且尚未備份的 VM。

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>我的 VM 關閉了。 隨選或排程的備份工作會運作嗎？

是。 備份會在機器關機時執行。 復原點會標示為「當機時保持一致」。

### <a name="can-i-cancel-an-in-progress-backup-job"></a>我是否可以取消進行中的備份作業？

是。 您可以在 **拍攝快照** 集狀態中取消備份作業。 如果正在從快照集傳輸資料，則無法取消作業。

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>我已啟用 Azure 備份 Service (所建立資源群組的鎖定，例如 `AzureBackupRG_<geo>_<number>`) 。 我的備份是否會繼續運作？

如果您鎖定 Azure 備份服務所建立的資源群組，則備份會開始失敗，因為有最多18個還原點的限制。

移除鎖定，並清除該資源群組中的還原點集合，讓未來的備份成功。 [請遵循下列步驟](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) 來移除還原點集合。

### <a name="i-have-a-lock-at-the-resource-group-level-that-contains-all-the-resources-related-to-my-virtual-machine-will-my-backup-work"></a>我有資源群組層級的鎖定，其中包含與我的虛擬機器相關的所有資源。 我的備份是否能運作？

Azure 備份會以儲存 ResourcePointCollections 物件的格式來建立個別的資源群組 `AzureBackupRG_<geo>_<number>` 。 因為此資源群組是由服務所擁有，所以鎖定會導致備份失敗。 鎖定只能套用至客戶建立的資源群組。

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Azure 備份是否支援標準 SSD 受控磁片？

是，Azure 備份支援 [標準 SSD 受控磁片](https://docs.microsoft.com/azure/virtual-machines/disks-types#standard-ssd)。

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>我們可以備份具有已啟用寫入加速器 (WA) 之磁碟的 VM 嗎？

快照集只能在已啟用 WA 且不是 OS 磁片的資料磁片上取得。 因此，只有已啟用 WA 的資料磁片可以受到保護。

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>我的 VM 具有寫入加速器 (WA) 磁碟並已安裝 SAP HANA。 該如何備份？

Azure 備份可以備份已啟用 WA 的資料磁片。 不過，備份不會提供資料庫一致性。

Azure 備份針對 RPO 為15分鐘的 SAP Hana 資料庫提供串流備份解決方案。 它是經過 SAP 認證的 Backint，可利用 SAP Hana 的原生 Api 來提供原生備份支援。 深入瞭解 [如何在 Azure vm 中備份 SAP Hana 資料庫](./sap-hana-db-about.md)。

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>我在我的 VM 備份原則中設定的排程備份時間，我在備份開始時間所預期的最大延遲為何？

排定的備份將會在排程備份時間的2小時內觸發。 例如，如果 100 Vm 的備份開始時間排定在上午2:00，4:00 則在最新的所有 100 Vm 上，將會有其備份工作正在進行中。 如果排定的備份因為中斷而暫停，並已恢復或重試，則備份可以在此排程的兩小時時間內啟動。

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>每日備份點的最小允許保留範圍為何？

Azure 虛擬機器備份原則支援最小保留範圍（最多7天），最多可達9999天。 任何不到七天的現有 VM 備份原則修改都需要更新，以符合七天的最小保留範圍。

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>如果我變更 VM 或我的 VM 資源群組的名稱案例，會發生什麼事？

如果您將案例 (變更為 VM 或 VM 資源群組的上限或下限) ，則備份專案名稱的大小寫不會變更。 不過，這是預期的 Azure 備份行為。 案例變更不會出現在備份專案中，但會在後端更新。

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>我可以備份或還原連接至 VM 的選擇性磁片嗎？

Azure 備份現在支援使用 Azure 虛擬機器備份解決方案進行選擇性磁碟備份和還原。 如需詳細資訊，請參閱 [Azure vm 的選擇性磁片備份和還原](selective-disk-backup-restore.md)。

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>如果在備份期間發生租使用者變更時，是否保留受控識別？

如果發生 [租使用者變更](/azure/devops/organizations/accounts/change-azure-ad-connection) ，您就必須停用並重新啟用 [受控](../active-directory/managed-identities-azure-resources/overview.md) 識別，才能讓備份再次運作。

## <a name="restore"></a>還原

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>如何決定是否只要還原磁碟，還是要還原整個 VM？

將 VM 還原視為 Azure VM 的快速建立選項。 此選項會變更磁片名稱、磁片所使用的容器、公用 IP 位址和網路介面名稱。 變更會在建立 VM 時維護唯一的資源。 VM 不會新增至可用性設定組。

如果您想要，您可以使用還原磁碟選項：

- 自訂所建立的 VM。 例如，變更大小。
- 新增備份時不存在的設定。
- 控制所建立資源的命名慣例。
- 將 VM 新增至可用性設定組。
- 新增任何其他必須使用 PowerShell 或範本設定的設定。

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>升級至受控磁碟之後，可以還原非受控 VM 磁碟的備份嗎？

是，您可以使用在將磁碟從非受控磁碟遷移至受控磁碟之前所擷取的備份。

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>如何將 VM 還原至將 VM 遷移至受控磁碟之前的還原點？

還原程式會維持不變。 如果復原點是 VM 擁有非受控磁片時的時間點，您可以將 [磁片還原為非受控](tutorial-restore-disk.md#unmanaged-disks-restore)磁片。 如果 VM 具有受控磁片，則您可以將 [磁片還原為受控磁片](tutorial-restore-disk.md#managed-disk-restore)。 然後，您可以 [從這些磁片建立 VM](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)。

[深入了解](backup-azure-vms-automation.md#restore-an-azure-vm)如何在 PowerShell 中執行此動作。

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>如果還原無法建立 VM，則還原中包含的磁片會發生什麼事？

在受控 VM 還原的事件中，即使 VM 建立失敗，仍會復原磁碟。

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>我可以還原已刪除的 VM 嗎？

是。 即使您刪除 VM，您也可以移至保存庫中對應的備份專案，然後從復原點還原。

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>如何? 將 VM 還原至相同的可用性設定組？

針對受控磁片 Azure Vm，在還原為受控磁片時，可在範本中提供選項，藉以啟用還原至可用性設定組。 此範本具有稱為 **可用性設定組** 的輸入參數。

### <a name="how-do-we-get-faster-restore-performances"></a>我們該如何取得更快速的還原效能？

[立即還原](backup-instant-restore-capability.md) 功能可協助您更快速地從快照集進行備份和立即還原。

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>當我們變更加密 VM 的金鑰保存庫設定時，會發生什麼事？

變更加密 VM 的金鑰保存庫設定之後，備份將繼續使用新的一組詳細資料。 不過，在變更之前從復原點還原之後，您必須先還原金鑰保存庫中的密碼，才能從該復原點建立 VM。 如需詳細資訊，請參閱這篇[文章](./backup-azure-restore-key-secret.md)。

秘密/金鑰變換之類的作業不需要此步驟，而且可以在還原後使用相同的金鑰保存庫。

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>是否可以在還原後存取 VM，因為 VM 與網域控制站的關聯性中斷？

是的，您可以在還原後存取 VM，因為 VM 與網域控制站之間的關聯性中斷。 如需詳細資訊，請參閱這篇[文章](./backup-azure-arm-restore-vms.md#post-restore-steps)

### <a name="why-restore-operation-is-taking-long-time-to-complete"></a>為何還原作業需要很長的時間才能完成？

總還原時間取決於每秒的輸入/輸出作業 (IOPS) 和儲存體帳戶的輸送量。 如果目標儲存體帳戶與其他應用程式讀取和寫入作業一起載入，則總還原時間可能會受到影響。 若要改善還原作業，請選取未與其他應用程式資料一起載入的儲存體帳戶。

## <a name="manage-vm-backups"></a>管理 VM 備份

### <a name="what-happens-if-i-modify-a-backup-policy"></a>如果我修改備份原則，會發生什麼事？

VM 會使用已修改或新的原則中的排程和保留期設定來備份。

- 如果延長保留期，就會標示現有的復原點，並依據新原則加以保留。
- 如果縮短保留期，則會標示復原點，以便在下次清除作業中加以剪除然後刪除。

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>如何將 Azure 備份所備份的 VM 移至不同的資源群組？

1. 暫時停止備份並保留備份資料。
2. 若要移動使用 Azure 備份設定的虛擬機器，請執行下列步驟：

   1. 尋找虛擬機器的位置。
   2. 尋找具有下列命名模式的資源群組： `AzureBackupRG_<location of your VM>_1` 。 例如， *AzureBackupRG_westus2_1*
   3. 在 [Azure 入口網站中，選取 [ **顯示隱藏的類型**]。
   4. 尋找具有命名模式的 **restorePointCollections** 類型的資源 `AzureBackup_<name of your VM that you're trying to move>_###########` 。
   5. 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。
   6. 刪除作業完成之後，您就可以移動虛擬機器。

3. 將 VM 移至目標資源群組。
4. 繼續備份。

您可以從移動作業之前所建立的可用還原點來還原 VM。

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>將 VM 移至不同的資源群組後，會發生什麼事？

一旦將 VM 移至不同的資源群組，就會成為 Azure 備份所關注的新 VM。

將 VM 移至新的資源群組之後，您可以在相同的保存庫或不同的保存庫中重新保護 VM。 由於這是 Azure 備份的新 VM，因此您將會另外支付費用。

如果需要，舊的 VM 還原點將可供還原。 如果您不需要此備份資料，您可以使用刪除資料來停止保護舊的 VM。

### <a name="is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy"></a>可以與相同備份原則建立關聯的 Vm 數目是否有限制？

是，有100個 Vm 的限制，可與入口網站中的相同備份原則建立關聯。 建議您針對100以上的 Vm，以相同的排程或不同排程建立多個備份原則。

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>如何查看我的備份的保留設定？

目前，您可以根據指派給 VM 的備份原則，在 (VM) 層級的備份專案上，查看保留設定。

若要查看備份的保留設定，其中一種方式是在 Azure 入口網站中流覽至 VM 的 [備份專案] [儀表板](./backup-azure-manage-vms.md#view-vms-on-the-dashboard) 。 選取備份原則的連結，可協助您查看與 VM 相關聯的每日、每週、每月和每年保留點的保留時間。

您也可以使用 [Backup Explorer](./monitor-azure-backup-with-backup-explorer.md) 來查看單一窗格內所有 vm 的保留設定。 從任何復原服務保存庫流覽至 Backup Explorer，移至 [ **備份專案** ] 索引標籤，然後選取 [Advanced View] 以查看每個 VM 的詳細保留資訊。