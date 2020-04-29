---
title: 備份 Azure 檔案服務常見問題集
description: 在本文中，您可以針對如何使用 Azure 備份服務來保護 Azure 檔案共用的常見問題，找到相關解答。
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105636"
---
# <a name="questions-about-backing-up-azure-files"></a>有關備份 Azure 檔案服務的問題

本文提供有關備份 Azure 檔案服務的常見問題解答。 在某些答案中，有具有完整資訊的文章連結。 您也可以在 [論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中張貼有關 Azure 備份服務的問題。

若要快速掃描本文中的各節內容，在**本文**下使用右方連結。

## <a name="configuring-the-backup-job-for-azure-files"></a>設定 Azure 檔案服務的備份作業

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>為什麼我看不到我想要保護的部分儲存體帳戶，其中包含有效的 Azure 檔案共用？

請參閱[Azure 檔案共用備份的支援矩陣](azure-file-share-support-matrix.md)，以確保儲存體帳戶屬於其中一種支援的儲存體帳戶類型。 您所尋找的儲存體帳戶也可能已受到保護，或已向另一個保存庫註冊。 從[保存庫取消註冊儲存體帳戶](manage-afs-backup.md#unregister-a-storage-account)，以探索其他保存庫中的儲存體帳戶以進行保護。

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>為何我在嘗試設定備份時，在儲存體帳戶中看不到某些 Azure 檔案共用？

請確認 Azure 檔案共用是否已在相同的復原服務保存庫中受到保護，或是否已於近期刪除。

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>我可以保護已連線到 Azure 檔案同步中同步群組的檔案共用嗎？

是。 已啟用保護連接到同步處理群組的 Azure 檔案共用。

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>在嘗試備份檔案共用時，我按了一下 [儲存體帳戶] 來探索其中的檔案共用。 不過，我並沒有保護它們。 如何? 使用任何其他保存庫保護這些檔案共用嗎？

嘗試備份時，選取要在其中探索檔案共用的儲存體帳戶，會向執行此作業的保存庫註冊儲存體帳戶。 如果您選擇使用不同的保存庫來保護檔案共用，請將所選的儲存體帳戶從這個保存庫[取消註冊](manage-afs-backup.md#unregister-a-storage-account)。

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>可以將保存庫變更為我備份檔案共用的保存庫嗎？

是。 不過，您必須從已連線的保存庫[停止檔案共用的保護](manage-afs-backup.md#stop-protection-on-a-file-share)、[取消註冊](manage-afs-backup.md#unregister-a-storage-account)此儲存體帳戶，然後從不同的保存庫加以保護。

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>我可以在一個保存庫中保護多少個 Azure 檔案共用？

您可以針對每個保存庫保護最多50個儲存體帳戶的 Azure 檔案共用。 此外，您在單一保存庫中最多可保護 200 個檔案共用。

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>可以將相同儲存體帳戶中的兩個不同檔案共用放入不同的保存庫中保護？

否。 一個儲存體帳戶中的所有檔案共用只能由相同的保存庫保護。

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>如果我的備份因為已達到最大限制的錯誤而開始失敗，該怎麼辦？

在任何時間點，檔案共用最多可以有 200 個快照集。 如您的原則所定義，此限制包含 Azure 備份所產生的快照集。 如果您的備份在達到限制後開始失敗，請刪除隨選快照集以進行成功的未來備份。

## <a name="restore"></a>還原

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>已刪除的 Azure 檔案共用是否可以復原？

刪除 Azure 檔案共用時，您會看到將刪除的備份清單，並要求確認。 目前無法還原已刪除的 Azure 檔案共用。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>如果我停止保護 Azure 檔案共用，是否可以從備份還原？

是。 如果您在停止保護時選擇 [保留備份資料]****，則您可以從所有現有的還原點進行還原。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果我取消進行中的還原作業，會發生什麼事？

如果取消進行中的還原作業，還原程序會停止，而所有在取消前還原的檔案會留在所設定的目的地 (原始或替代位置)，不需要回復。

## <a name="manage-backup"></a>管理備份

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>我是否可以使用 PowerShell 來設定/管理/還原 Azure 檔案共用的備份？

是。 請參閱[這裡](backup-azure-afs-automation.md)的詳細檔。

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>我可以存取 Azure 備份所建立的快照集並加以掛接嗎？

藉由在入口網站、PowerShell 或 CLI 中查看快照集，即可存取 Azure 備份所建立的所有快照集。 若要深入瞭解 Azure 檔案儲存體共用快照集，請參閱[Azure 檔案儲存體的共用快照集總覽](../storage/files/storage-snapshots-files.md)。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>我可以為備份設定的保留期上限為何？

如需最大保留期的詳細資訊，請參閱[支援矩陣](azure-file-share-support-matrix.md)。 當您在設定備份原則時輸入保留值時，Azure 備份會即時計算快照集數目。 一旦對應至您已定義保留值的快照集數目超過200，入口網站將會顯示一則警告，要求您調整保留值。 如此一來，您就不會超過任何時間點的檔案共用 Azure 檔案儲存體所支援的快照集數目上限限制。

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>變更 Azure 檔案共用的備份原則時，會發生什麼狀況？

在檔案共用上套用新原則後，就會遵循新原則的排程和保留期。 如果延長保留期，會標示現有的復原點，以依據新的原則加以保留。 如果縮短保留期，則會標示現有的復原點，以便在下次清除作業中剪除並刪除。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 備份的其他領域，請參閱其他備份常見問題集：

- [復原服務保存庫常見問題集](backup-azure-backup-faq.md)
- [Azure VM 備份常見問題集](backup-azure-vm-backup-faq.md)
- [Azure 備份代理程式常見問題集](backup-azure-file-folder-backup-faq.md)
