---
title: 常見問題-備份 Azure Vm
description: 在本文中，您會找到有關使用 Azure 備份服務來備份 Azure Vm 的常見問題解答。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 268146430a71cca4e620381ec4ce8666f808fefd
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190891"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>常見問題-備份 Azure Vm

本文會回答有關使用 [Azure 備份](./backup-overview.md) 服務來備份 Azure vm 的常見問題。

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>建立虛擬機器映射時，可以針對備份啟用哪些 VM 映射？

當您建立 VM 時，可以針對執行 [支援的作業系統](backup-support-matrix-iaas.md#supported-backup-actions)的 vm 啟用備份。

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>VM 成本中包含備份成本嗎？

不正確。 備份成本與 VM 的成本不同。 深入瞭解 [Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>若要啟用 VM 的備份，需要哪些許可權？

如果您是 VM 參與者，您可以在 VM 上啟用備份。 如果您使用的是自訂角色，您需要下列許可權才能在 VM 上啟用備份：

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

如果您的復原服務保存庫和 VM 有不同的資源群組，請確定您在復原服務保存庫的資源群組中具有寫入權限。  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>隨選備份作業是否會使用與排定備份相同的保留排程？

不正確。 指定隨選備份作業的保留範圍。 根據預設，若從入口網站觸發，則會保留 30 天。

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>我在最近一些 VM 上啟用了 Azure 磁碟加密。 我的備份是否會繼續運作？

提供存取 Key Vault Azure 備份的許可權。 在 PowerShell 中指定權限，如 [Azure 備份 PowerShell](backup-azure-vms-automation.md) 文件的**啟用備份**一節中所述。

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>我已將 VM 磁碟遷移至受控磁碟。 我的備份是否會繼續運作？

是，備份會順利運作。 不需要重新設定任何項目。

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>為什麼我在 [設定備份] 精靈中看不到我的 VM？

此精靈只會列出與保存庫位於相同區域且尚未備份的 VM。

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>我的 VM 關閉了。 隨選或排程的備份工作會運作嗎？

是。 備份會在機器關機時執行。 復原點會標示為「當機時保持一致」。

### <a name="can-i-cancel-an-in-progress-backup-job"></a>我是否可以取消進行中的備份作業？

是。 您可以在 **拍攝快照** 集狀態中取消備份作業。 如果正在從快照集傳輸資料，則無法取消作業。

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>我啟用了 Azure 備份 (服務所建立之資源群組的鎖定，例如 `AzureBackupRG_<geo>_<number>`) 。 我的備份是否會繼續運作？

如果您鎖定 Azure 備份服務所建立的資源群組，則備份會開始失敗，因為最大限制為18個還原點。

移除鎖定，並清除該資源群組中的還原點集合，讓未來的備份成功。 [請遵循下列步驟](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) 來移除還原點集合。

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Azure 備份是否支援標準 SSD 受控磁片？

是，Azure 備份支援 [標準 SSD 受控磁片](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)。

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>我們可以備份具有已啟用寫入加速器 (WA) 之磁碟的 VM 嗎？

您無法在已啟用 WA 的磁碟上建立快照集。 不過，Azure 備份服務可以從備份中排除已啟用 WA 的磁碟。

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>我的 VM 具有寫入加速器 (WA) 磁碟並已安裝 SAP HANA。 該如何備份？

Azure 備份無法備份已啟用 WA 的磁碟，但可從備份中排除該磁碟。 不過，備份將不會提供資料庫一致性，因為系統不會備份已啟用 WA 之磁碟的相關資訊。 如果您想要作業系統磁碟備份以及未啟用 WA 的磁碟備份，您可以使用此設定來備份磁碟。

Azure 備份為具有15分鐘 RPO 的 SAP Hana 資料庫提供串流備份解決方案。 它是由 SAP 認證，以提供利用 SAP Hana 的原生 Api 的原生備份支援。 深入瞭解 [如何在 Azure vm 中備份 SAP Hana 資料庫](./sap-hana-db-about.md)。

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>我在 VM 備份原則中所設定的排程備份時間，我可以預期的最大延遲為何？

排定的備份將會在排程備份時間的2小時內觸發。 例如，如果 100 Vm 的備份開始時間是在上午2:00 進行排程，則在最新的所有 100 Vm 上，會有 4:00 AM 的備份作業正在進行中。 如果排定的備份因中斷而暫停並繼續或重試，則備份可以在此排程的兩小時時間範圍以外啟動。

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>每日備份點的最小允許保留範圍為何？

Azure 虛擬機器備份原則支援最小保留範圍，最多7天到9999天。 任何修改現有 VM 備份原則的時間若少於7天，將需要更新以符合7天的最低保留範圍。

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>如果我變更我的 VM 或我的 VM 資源群組的名稱案例，會發生什麼事？

如果您將大小寫 (變更為 VM 或 VM 資源群組的上限或較低) ，備份專案名稱的案例將不會變更。 不過，這是預期的 Azure 備份行為。 案例變更不會出現在備份專案中，但會在後端更新。

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>我可以備份或還原附加至 VM 的選擇性磁片嗎？

Azure 備份現在支援使用 Azure 虛擬機器備份解決方案進行選擇性磁碟備份和還原。 如需詳細資訊，請參閱 [適用于 Azure vm 的選擇性磁片備份與還原](selective-disk-backup-restore.md)。

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

還原程式維持不變。 如果復原點是 VM 具有非受控磁片時的時間點，您可以將 [磁片還原為未受管理](tutorial-restore-disk.md#unmanaged-disks-restore)。 如果 VM 具有受控磁片，則您可以將 [磁片還原為受控磁片](tutorial-restore-disk.md#managed-disk-restore)。 然後您可以 [從這些磁片建立 VM](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)。

[深入了解](backup-azure-vms-automation.md#restore-an-azure-vm)如何在 PowerShell 中執行此動作。

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>如果還原無法建立 VM，還原中包含的磁片會發生什麼事？

在受控 VM 還原的事件中，即使 VM 建立失敗，仍然會復原磁碟。

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>我可以還原已刪除的 VM 嗎？

是。 即使您刪除 VM，您也可以移至保存庫中對應的備份專案，並從復原點還原。

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>如何? 將 VM 還原至相同的可用性設定組？

針對受控磁片 Azure Vm，在還原為受控磁片時，會在範本中提供選項來啟用還原至可用性設定組。 此範本具有稱為**可用性設定組**的輸入參數。

### <a name="how-do-we-get-faster-restore-performances"></a>我們該如何取得更快速的還原效能？

「[立即還原](backup-instant-restore-capability.md)」功能可協助從快照集進行更快速的備份和立即還原。

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>當我們變更已加密 VM 的金鑰保存庫設定時，會發生什麼事？

在您變更已加密 VM 的金鑰保存庫設定後，備份將會繼續使用一組新的詳細資料。 不過，在變更之前從復原點還原之後，您必須先還原金鑰保存庫中的秘密，才能建立 VM。 如需詳細資訊，請參閱這篇[文章](./backup-azure-restore-key-secret.md)。

秘密/金鑰變換之類的作業不需要此步驟，而且可以在還原之後使用相同的金鑰保存庫。

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>因為 VM 與網域控制站的關聯性中斷，我可以在還原後存取 VM 嗎？

是，因為 VM 與網域控制站的關聯性中斷，所以您會在還原後存取 VM。 如需詳細資訊，請參閱這篇[文章](./backup-azure-arm-restore-vms.md#post-restore-steps)

## <a name="manage-vm-backups"></a>管理 VM 備份

### <a name="what-happens-if-i-modify-a-backup-policy"></a>如果我修改備份原則，會發生什麼事？

VM 會使用已修改或新的原則中的排程和保留期設定來備份。

- 如果延長保留期，就會標示現有的復原點，並依據新原則加以保留。
- 如果縮短保留期，則會標示復原點，以便在下次清除作業中加以剪除然後刪除。

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>如何將 Azure 備份所備份的 VM 移至不同的資源群組？

1. 暫時停止備份並保留備份資料。
2. 若要移動以 Azure 備份設定的虛擬機器，請執行下列步驟：

   1. 尋找虛擬機器的位置。
   2. 尋找具有下列命名模式的資源群組： `AzureBackupRG_<location of your VM>_1` 。 例如， *AzureBackupRG_westus2_1*
   3. 在 [Azure 入口網站中，勾選 [ **顯示隱藏的類型**]。
   4. 尋找類型為 **Microsoft. Compute/restorePointCollections** 且具有命名模式的資源 `AzureBackup_<name of your VM that you're trying to move>_###########` 。
   5. 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。
   6. 刪除作業完成之後，您就可以移動虛擬機器。

3. 將 VM 移至目標資源群組。
4. 繼續備份。

您可以從移動作業之前所建立的可用還原點來還原 VM。

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>將 VM 移至不同的資源群組之後，會發生什麼事？

一旦將 VM 移至不同的資源群組，就會是新的 VM，就 Azure 備份而言。

將 VM 移至新的資源群組之後，您可以在相同的保存庫或不同的保存庫中重新保護 VM。 由於這是 Azure 備份的新 VM，因此您將會個別計費。

如果需要，舊版 VM 的還原點將可供還原。 如果您不需要此備份資料，您可以使用 [刪除資料] 來停止保護舊的 VM。

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>可以與相同備份原則建立關聯的 Vm 數目是否有限制？

是，有 100 Vm 的限制，可以從入口網站與相同的備份原則建立關聯。 針對超過100個 Vm，我們建議您建立多個具有相同排程或不同排程的備份原則。

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>如何才能查看備份的保留設定？

目前，您可以根據指派給 VM 的備份原則，在 (VM) 層級的備份專案上，查看保留設定。 

若要查看備份的保留設定，其中一種方式是在 [Azure 入口網站] 中流覽至 VM 的 [備份專案] [儀表板](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms#view-vms-on-the-dashboard) 。 按一下其備份原則的連結，可協助您查看與 VM 相關聯的每日、每週、每月和每年保留點的保留期間。

您也可以使用 [ [備份瀏覽器](https://docs.microsoft.com/azure/backup/monitor-azure-backup-with-backup-explorer) ]，在單一畫面中查看所有 vm 的保留設定。 流覽至任何復原服務保存庫中的備份瀏覽器，移至 [ **備份專案** ] 索引標籤，然後選取 [Advanced View]，以查看每個 VM 的詳細保留資訊。  