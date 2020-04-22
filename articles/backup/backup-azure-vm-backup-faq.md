---
title: 常見問題解答 ─ 備份 Azure VM
description: 在本文中,瞭解有關使用 Azure 備份服務備份 Azure VM 的常見問題的解答。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: accfc57055f70254814c889de875f5360878bcd9
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757462"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>常見問題 - 備份 Azure VM

本文回答了有關使用[Azure 備份](backup-introduction-to-azure-backup.md)服務備份 Azure VM 的常見問題。

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>創建時可以啟用哪些 VM 映射進行備份?

創建 VM 時,可以為運行[受支援的作業系統的](backup-support-matrix-iaas.md#supported-backup-actions)VM 啟用備份。

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>備份成本是否包含在 VM 成本中?

否。 備份成本與 VM 成本不同。 瞭解有關[Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)的更多。

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>為 VM 啟用備份需要哪些許可權?

如果您是 VM 參與者,則可以在 VM 上啟用備份。 如果使用自訂角色,則需要以下許可權才能在 VM 上啟用備份:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

如果恢復服務保管庫和 VM 具有不同的資源組,請確保在恢復服務保管庫的資源組中具有寫入許可權。  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>隨選備份作業是否會使用與排定備份相同的保留排程？

否。 指定按需備份作業的保留範圍。 根據預設，若從入口網站觸發，則會保留 30 天。

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>我在最近一些 VM 上啟用了 Azure 磁碟加密。 我的備份是否會繼續運作？

為 Azure 備份提供訪問密鑰保管庫的許可權。 在 PowerShell 中指定權限，如 [Azure 備份 PowerShell](backup-azure-vms-automation.md) 文件的**啟用備份**一節中所述。

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>我已將 VM 磁碟遷移至受控磁碟。 我的備份是否會繼續運作？

是，備份會順利運作。 不需要重新設定任何項目。

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>為什麼我在 [設定備份] 精靈中看不到我的 VM？

此精靈只會列出與保存庫位於相同區域且尚未備份的 VM。

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>我的 VM 關閉了。 隨選或排程的備份工作會運作嗎？

是。 備份會在機器關機時執行。 復原點會標示為「當機時保持一致」。

### <a name="can-i-cancel-an-in-progress-backup-job"></a>我是否可以取消進行中的備份作業？

是。 您可以在 **「拍攝快照」** 狀態下取消備份作業。 如果正在從快照集傳輸資料，則無法取消作業。

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>我啟用了 Azure 備份服務創建的資源組的鎖(`AzureBackupRG_<geo>_<number>`例如。 我的備份是否會繼續運作？

如果鎖定 Azure 備份服務創建的資源組,備份將開始失敗,因為最大限制為 18 個還原點。

刪除鎖,並從該資源組清除還原點集合,以使將來的備份成功。 [按照以下步驟](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal)刪除還原點集合。

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Azure 備份是否支援標準 SSD 管理的磁碟?

是的,Azure 備份支援[標準 SSD 託管磁碟](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)。

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>我們可以備份具有已啟用寫入加速器 (WA) 之磁碟的 VM 嗎？

您無法在已啟用 WA 的磁碟上建立快照集。 不過，Azure 備份服務可以從備份中排除已啟用 WA 的磁碟。

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>我的 VM 具有寫入加速器 (WA) 磁碟並已安裝 SAP HANA。 該如何備份？

Azure 備份無法備份已啟用 WA 的磁碟，但可從備份中排除該磁碟。 不過，備份將不會提供資料庫一致性，因為系統不會備份已啟用 WA 之磁碟的相關資訊。 如果您想要作業系統磁碟備份以及未啟用 WA 的磁碟備份，您可以使用此設定來備份磁碟。

Azure 備份為 SAP HANA 資料庫提供了 15 分鐘的流式處理備份解決方案。 它由 SAP 認證,可利用 SAP HANA 的本機 API 提供本機備份支援。 [瞭解有關在 Azure VM 中備份 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-about)的資訊。

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>從 VM 備份策略中設置的計劃備份時間開始,備份開始時間中的最大延遲是多少?

計劃的備份將在計劃備份時間的 2 小時內觸發。 例如,如果 100 個 VM 的備份開始時間安排在淩晨 2:00,那麼最晚 4:00 時,所有 100 個 VM 都將進行備份作業。 如果計劃備份因中斷而暫停並恢復或重試,則備份可以在計劃兩小時視窗之外啟動。

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>每日備份點的最小允許保留範圍是多少?

Azure 虛擬機器備份策略支援從 7 天到 9999 天的最低保留範圍。 對現有 VM 備份策略的任何修改少於 7 天都需要更新,以滿足 7 天的最短保留範圍。

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>如果我更改 VM 或 VM 資源組的名稱,會發生什麼情況?

如果將 VM 或 VM 資源組的情況(更改為上部或下部),則備份項名稱的情況不會更改。 但是,這是預期的 Azure 備份行為。 案例更改不會顯示在備份項中,但在後端更新。

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>我可以備份或還原連接到 VM 的選擇性磁碟嗎?

Azure 備份現在支援使用 Azure 虛擬機器備份解決方案進行選擇性磁碟備份和還原。

如今,Azure 備份支援使用虛擬機器備份解決方案一起備份 VM 中的所有磁碟(作業系統和資料)。 使用排除磁碟功能,您可以選擇從 VM 中的許多資料磁碟備份一個或多個數據磁碟。 這為備份和恢復需求提供了高效且經濟高效的解決方案。 每個恢復點都包含備份操作中包含的磁碟的數據,這進一步允許您在還原操作期間從給定恢復點還原磁碟子集。 這適用於從快照和保管庫還原。

要註冊預覽版,請寫信給我們:AskAzureBackupTeam@microsoft.com

## <a name="restore"></a>還原

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>如何決定是否只要還原磁碟，還是要還原整個 VM？

將 VM 還原視為 Azure VM 的快速建立選項。 此選項更改磁碟名稱、磁碟使用的容器、公共 IP 位址和網路介面名稱。 變更會在建立 VM 時維護唯一的資源。 VM 不會新增至可用性設定組。

如果您想要，您可以使用還原磁碟選項：

- 自訂所建立的 VM。 例如,更改大小。
- 添加備份時不存在的配置設置。
- 控制所建立資源的命名慣例。
- 將 VM 新增至可用性設定組。
- 新增任何其他必須使用 PowerShell 或範本設定的設定。

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>升級至受控磁碟之後，可以還原非受控 VM 磁碟的備份嗎？

是，您可以使用在將磁碟從非受控磁碟遷移至受控磁碟之前所擷取的備份。

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>如何將 VM 還原至將 VM 遷移至受控磁碟之前的還原點？

還原過程保持不變。 如果恢復點是 VM 具有非託管磁碟時的時間點,則可以[將磁碟還原為非託管](tutorial-restore-disk.md#unmanaged-disks-restore)磁碟。 如果 VM 具有託管磁碟,則可以[將磁碟還原為託管磁碟](tutorial-restore-disk.md#managed-disk-restore)。 然後,可以從[這些磁碟創建 VM。](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)

[深入了解](backup-azure-vms-automation.md#restore-an-azure-vm)如何在 PowerShell 中執行此動作。

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>我可以還原已刪除的 VM 嗎？

是。 即使您刪除了 VM,也可以轉到保管庫中的相應備份項並從恢復點進行還原。

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>如何將 VM 還原到相同的可用性集?

對於託管磁碟 Azure VM,透過在範本中提供一個選項,同時還原為託管磁碟,從而啟用還原到可用性集。 此範本具有稱為**可用性設定組**的輸入參數。

### <a name="how-do-we-get-faster-restore-performances"></a>我們該如何取得更快速的還原效能？

[即時還原](backup-instant-restore-capability.md)功能有助於加快備份速度,並快速從快照進行恢復。

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>當我們更改加密 VM 的密鑰保管庫設置時會發生什麼情況?

更改加密 VM 的金鑰保管庫設置後,備份將繼續使用新的詳細資訊集。 但是,在更改之前從恢復點還原後,您必須還原密鑰保管庫中的秘密,然後才能從該密鑰保管庫中創建 VM。 有關詳細資訊,請參閱[本文](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret)。

機密/密鑰滾動等操作不需要此步驟,並且還原後可以使用相同的 KeyVault。

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>由於 VM 與域控制器的關係斷開,我是否可以在還原後訪問 VM?

是,由於 VM 與域控制器的關係斷開,您訪問 VM 後進行還原。 有關詳細資訊,請參閱[本文](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>管理 VM 備份

### <a name="what-happens-if-i-modify-a-backup-policy"></a>如果我修改備份原則，會發生什麼事？

VM 會使用已修改或新的原則中的排程和保留期設定來備份。

- 如果延長保留期，就會標示現有的復原點，並依據新原則加以保留。
- 如果縮短保留期，則會標示復原點，以便在下次清除作業中加以剪除然後刪除。

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>如何將 Azure 備份所備份的 VM 移至不同的資源群組？

1. 暫時停止備份並保留備份資料。
2. 將 VM 移至目標資源群組。
3. 在同一保管庫中重新啟用備份。

您可以從移動作業之前所建立的可用還原點來還原 VM。

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>可以與同一備份策略關聯的 VM 數量是否有限制?

是的,有 100 個 VM 的限制可以從門戶連接到同一備份策略。 我們建議,對於 100 多個 VM,創建多個具有相同計劃或不同計劃的備份策略。
