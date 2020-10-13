---
title: 備份 Azure 檔案服務常見問題集
description: 在本文中，您可以針對如何使用 Azure 備份服務來保護 Azure 檔案共用的常見問題，找到相關解答。
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: e2b6afb25e189ee2848f25c0ba59d843baf37090
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940830"
---
# <a name="questions-about-backing-up-azure-files"></a>有關備份 Azure 檔案服務的問題

本文提供有關備份 Azure 檔案服務的常見問題解答。 在某些答案中，有具有完整資訊的文章連結。 您也可以在 [Microsoft 問與答頁面](/answers/topics/azure-backup.html)中張貼有關 Azure 備份服務的問題，以供討論。

若要快速掃描本文中的各節內容，在**本文**下使用右方連結。

## <a name="configuring-the-backup-job-for-azure-files"></a>設定 Azure 檔案服務的備份作業

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>為何看不到某些我想要保護且包含有效 Azure 檔案共用的儲存體帳戶？

請參閱 [Azure 檔案共用備份的支援矩陣](azure-file-share-support-matrix.md)，以確定儲存體帳戶屬於其中一種支援的儲存體帳戶類型。 也有可能是因為您所尋找的儲存體帳戶已受保護，或註冊至另一個保存庫。 請從保存庫[取消註冊儲存體帳戶](manage-afs-backup.md#unregister-a-storage-account)，以探索其他保存庫中用於保護的儲存體帳戶。

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>為何我在嘗試設定備份時，在儲存體帳戶中看不到某些 Azure 檔案共用？

請確認 Azure 檔案共用是否已在相同的復原服務保存庫中受到保護，或是否已於近期刪除。

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>我可以保護已連線到 Azure 檔案同步中同步群組的檔案共用嗎？

是。 保護已連線至同步群組的 Azure 檔案共用，是已啟用的功能。

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>當您嘗試備份檔案共用時，我選取了一個儲存體帳戶來探索其中的檔案共用。 不過，我並未加以保護。 如何使用任何其他保存庫來保護這些檔案共用？

嘗試備份，並選取 [儲存體帳戶] 以探索其中的檔案共用時，向保存庫註冊此 [儲存體帳戶]。 如果您選擇使用不同的保存庫來保護檔案共用，從這個保存庫[取消註冊](manage-afs-backup.md#unregister-a-storage-account)所選的儲存體帳戶。

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>為什麼我無法變更保存庫來設定檔案共用的備份？

如果儲存體帳戶已向保存庫註冊，或使用保存庫保護儲存體帳戶中的其他檔案共用，則您不會獲得變更的選項。 儲存體帳戶中的所有檔案共用只能由相同的保存庫保護。 如果您想要變更保存庫，您必須 [停止保護儲存體帳戶中的所有檔案共用](manage-afs-backup.md#stop-protection-on-a-file-share) 的連線保存庫、 [取消註冊](manage-afs-backup.md#unregister-a-storage-account) 儲存體帳戶，然後選擇不同的保存庫以進行保護。

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>可以將保存庫變更為我備份檔案共用的保存庫嗎？

是。 不過，您必須從已連線的保存庫[停止保護檔案共用](manage-afs-backup.md#stop-protection-on-a-file-share)，[取消註冊](manage-afs-backup.md#unregister-a-storage-account)這個儲存體帳戶，然後從不同的保存庫加以保護。

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>可以將相同儲存體帳戶中的兩個不同檔案共用放入不同的保存庫中保護？

否。 一個儲存體帳戶中的所有檔案共用只能由相同的保存庫保護。

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>如果我的備份因已達最大限制的錯誤而失敗，該如何處理？

在任何時間點，檔案共用最多可以有 200 個快照集。 如您的原則所定義，此限制包含 Azure 備份所產生的快照集。 如果您的備份在達到此限制之後開始失敗，請刪除隨選快照集，讓後續備份可成功執行。

## <a name="restore"></a>還原

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>已刪除的 Azure 檔案共用是否可以復原？

如果檔案共用處於已虛刪除的狀態，您必須先取消刪除檔案共用以執行還原作業。 取消刪除作業會使檔案共用處於作用中狀態，讓您可以還原到任何時間點。 若要瞭解如何取消刪除您的檔案共用，請流覽 [此連結](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) ，或參閱取消 [刪除檔案共用腳本](./scripts/backup-powershell-script-undelete-file-share.md)。 如果檔案共用永久刪除，您將無法還原內容和快照集。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>如果我停止保護 Azure 檔案共用，是否可以從備份還原？

是。 如果您在停止保護時選擇 [保留備份資料]，則您可以從所有現有的還原點進行還原。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果我取消進行中的還原作業，會發生什麼事？

如果取消進行中的還原作業，還原程序會停止，而所有在取消前還原的檔案會留在所設定的目的地 (原始或替代位置)，不需要回復。

## <a name="manage-backup"></a>管理備份

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>我是否可以使用 PowerShell 來設定/管理/還原 Azure 檔案共用的備份？

是。 請參閱[這裡](backup-azure-afs-automation.md)的詳細文件。

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>我可以存取 Azure 備份所產生的快照集並加以裝載嗎？

在入口網站、PowerShell 或 CLI 中檢視快照集，即可存取 Azure Backup 所產生的所有快照集。 若要深入了解 Azure 檔案共用快照集，請參閱 [Azure 檔案的共用快照集概觀](../storage/files/storage-snapshots-files.md)。

### <a name="what-happens-after-i-move-a-backed-up-file-share-to-a-different-subscription"></a>將備份的檔案共用移至不同的訂用帳戶之後會發生什麼事？

一旦檔案共用移至不同的訂用帳戶之後，Azure 備份就會將它視為新的檔案共用。 這些是建議的步驟：
 
案例：假設您在訂用帳戶*S1*中有檔案共用*FS1* ，並使用*V1*保存庫加以保護。 現在您想要將檔案共用移至訂用帳戶 *S2*。
 
1.  將所需的儲存體帳戶和檔案共用 (FS1) 移至不同的訂用帳戶 (S2) 。
2.  在 V1 保存庫中，觸發 FS1 的「停止保護」和「刪除資料」作業。
3.  從 V1 保存庫將裝載 FS1 的儲存體帳戶取消註冊。
4.  使用 S2 訂用帳戶中的保存庫 (V2) 重新設定 FS1 的備份（現在已移至 S2）。 
 
請注意，使用 V2 重新設定備份之後，將不會再由 Azure 備份管理以 V1 取得的快照。 因此，您必須根據您的需求手動刪除這些快照集。

### <a name="can-i-move-my-backed-up-file-share-to-a-different-resource-group"></a>我可以將已備份的檔案共用移至不同的資源群組嗎？
 
是，您可以將已備份的檔案共用移至不同的資源群組。 不過，您將需要重新設定檔案共用的備份，因為 Azure 備份會將它視為新的資源。 此外，在資源群組移動之前建立的快照集將不會再由 Azure 備份管理。 因此，您必須根據您的需求手動刪除這些快照集。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>我可以為備份設定的保留期上限為何？

如需最大保留期的詳細資訊，請參閱[支援矩陣](azure-file-share-support-matrix.md)。 如果您在設定備份原則時輸入了保留值，Azure 備份將會執行快照集數目的即時計算。 與您已定義的保留值對應的快照集數目一旦超過 200，入口網站就會顯示一則警告，要求您調整保留值。 如此，您任何的檔案共用在任何時間點都不會超過 Azure 檔案儲存體所支援的快照集數目上限。

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>當我修改 Azure 檔案共用的備份原則，從「每日原則」切換至「GFS 原則」時，現有的復原點和快照集會受到什麼影響？

當您將每日備份原則修改為 GFS 原則 (新增每週/每月/每年保留期) 時，其行為如下所示：

- **保留**：如果您在修改原則時新增了每週/每月/每年保留期，則未來在排程備份中建立的所有復原點，都會根據新的原則進行標記。 所有現有的復原點仍會被視為每日復原點，因此不會標記為每週/每月/每年。

- **快照集和復原點清除**：

  - 如果延長了每日保留期，則現有復原點的到期日將會根據新原則中設定的每日保留值進行更新。
  - 如果縮減了每日保留期，則會根據新原則中設定的每日保留值，將現有的復原點和快照集標示為要刪除，然後加以刪除。

以下是其運作方式的範例：

#### <a name="existing-policy-p1"></a>現有原則 [P1]

|保留期類型 |排程 |保留  |
|---------|---------|---------|
|每日    |    每天晚上 8 點    |  100 天       |

#### <a name="new-policy-modified-p1"></a>新原則 [修改過的 P1]

| 保留期類型 | 排程                       | 保留 |
| -------------- | ------------------------------ | --------- |
| 每日          | 每天晚上 9 點              | 50 天   |
| 每週         | 星期日晚上 9 點              | 3 週   |
| 每月        | 上星期一晚上 9 點         | 1 個月   |
| 每年         | 一月第 3 個星期日的晚上 9 點 | 4 年   |

#### <a name="impact"></a>影響

1. 現有復原點的到期日將會根據新原則的每日保留值進行調整，也就是 50 天。 因此，任何早於 50 天的復原點都會標示為要刪除。

2. 現有的復原點不會根據新的原則標記為每週/每月/每年。

3. 所有後續的備份都會根據新的排程進行觸發，也就是在晚上 9 點。

4. 後續所有復原點的到期日，都將以新原則為準。

>[!NOTE]
>原則變更只會影響到在排程備份作業執行時建立的復原點。 對於隨選備份，保留期會取決於進行備份時所指定的 [保留截止日期] 值。

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>修改現有的 GFS 原則時，對現有的復原點會有何影響？

在檔案共用上套用新的原則時，後續所有的排程備份都將根據修改過的原則中所設定的排程來執行。  所有現有復原點的保留期都會根據設定的新保留值進行調整。 因此，如果延長保留期，現有的復原點將會根據新原則標示為要保留。 如果縮短保留期，則會將復原點標示為要在下次清除作業中清除，然後加以刪除。

以下是其運作方式的範例：

#### <a name="existing-policy-p2"></a>現有原則 [P2]

| 保留期類型 | 排程           | 保留 |
| -------------- | ------------------ | --------- |
| 每日          | 每天晚上 8 點 | 50 天   |
| 每週         | 星期一晚上 8 點  | 3 週   |

#### <a name="new-policy-modified-p2"></a>新原則 [修改過的 P2]

| 保留期類型 | 排程               | 保留 |
| -------------- | ---------------------- | --------- |
| 每日          | 每天晚上 9 點     | 10 天   |
| 每週         | 星期一晚上 9 點      | 2 週   |
| 每月        | 上星期一晚上 9 點 | 2 個月  |

#### <a name="impact-of-change"></a>變更的影響

1. 現有每日復原點的到期日將會根據新的每日保留值進行調整，也就是 10 天。 因此，任何早於 10 天的每日復原點都將刪除。

2. 現有每週復原點的到期日將會根據新的每週保留值進行調整，也就是兩週。 因此，任何早於兩週的每週復原點都將刪除。

3. 每月復原點只會根據新的原則設定在未來的備份中建立。

4. 後續所有復原點的到期日，都將以新原則為準。

>[!NOTE]
>原則變更只會影響到在排程備份中建立的復原點。 對於隨選備份，保留期會取決於進行備份時所指定的 [保留截止日期] 值。

## <a name="next-steps"></a>後續步驟

- [針對備份 Azure 檔案共用時發生的問題進行疑難排解](troubleshoot-azure-files.md)
