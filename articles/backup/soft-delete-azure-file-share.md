---
title: Azure 檔案共用的意外刪除保護
description: 瞭解如何虛刪除可以防止意外刪除您的 Azure 檔案共用。
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179907"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>使用 Azure 備份意外刪除 Azure 檔案共用的保護

為保護網路攻擊或意外刪除的情況，當您為個別儲存體帳戶中的任何檔案共用設定備份時，會為儲存體帳戶中的所有檔案共用啟用虛 [刪除](../storage/files/storage-files-prevent-file-share-deletion.md) 。 使用虛刪除時，即使惡意執行者刪除檔案共用，檔案共用的內容和復原點 (快照集) 至少會保留14天，以允許在不遺失資料的情況下復原檔案共用。  標準和高階儲存體帳戶支援虛刪除，而且所有裝載已備份檔案共用的儲存體帳戶 Azure 備份都會啟用此設定。

下列流程圖顯示在儲存體帳戶中啟用檔案共用的虛刪除時，備份專案的不同步驟和狀態：

 ![虛刪除流程圖](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>何時會針對我的儲存體帳戶中的檔案共用啟用虛刪除？

當您第一次為儲存體帳戶中的任何檔案共用設定備份時，Azure 備份服務會針對個別儲存體帳戶中的所有檔案共用啟用虛刪除。

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>我是否可以設定在刪除檔案共用後，快照集和還原點會在虛刪除狀態中保留的天數？

是，您可以根據您的需求設定保留期限。 [本檔](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) 說明設定保留期限的步驟。 針對具有已備份檔案共用的儲存體帳戶，最小保留設定應為14天。

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Azure 備份重設我的保留設定，因為我已將其設定為少於14天？

從安全性的觀點來看，我們建議使用已備份檔案共用的儲存體帳戶至少保留14天。 因此，在每個備份工作執行時，如果 Azure 備份將設定識別為少於14天，則會將設定重設為14天。

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>保留期間所產生的成本為何？

在虛刪除期間，受保護的實例成本和快照儲存體成本將保持不變。  此外，您會依標準檔案共用的一般費率，以及 premium 檔案共用的快照儲存費率，支付所使用容量的費用。

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>當我的資料處於虛刪除狀態時，是否可以執行還原作業？

您必須先取消刪除虛刪除的檔案共用，才能執行還原作業。 取消刪除作業會將檔案共用帶到備份狀態，讓您可以還原到任何時間點。 若要瞭解如何取消刪除您的檔案共用，請流覽 [此連結](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) ，或參閱取消 [刪除檔案共用腳本](./scripts/backup-powershell-script-undelete-file-share.md)。

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>如何清除至少有一個受保護檔案共用之儲存體帳戶中檔案共用的資料？

如果您在儲存體帳戶中至少有一個受保護的檔案共用，這表示會為該帳戶中的所有檔案共用啟用虛刪除，且您的資料會在刪除作業之後保留14天。 但是，如果您想要立即清除資料而不想保留資料，請遵循下列步驟：

1. 如果您已在啟用虛刪除時刪除檔案共用，則會先從檔案 [入口網站](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) 或使用 [取消刪除檔案共用] [腳本](./scripts/backup-powershell-script-undelete-file-share.md)刪除檔案共用。
2. 依照 [本檔](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)中所述的步驟，停用儲存體帳戶中檔案共用的虛刪除。
3. 現在刪除您想要立即清除其內容的檔案共用。

>[!NOTE]
>您應該在下次排定的備份作業執行時，對儲存體帳戶中受保護的檔案共用執行步驟2。 由於每次執行備份作業時，它會針對儲存體帳戶中的所有檔案共用重新啟用虛刪除。

>[!WARNING]
>停用步驟2中的虛刪除之後，對檔案共用執行的任何刪除作業都是永久刪除作業。 因此，如果您在停用虛刪除之後不小心刪除備份的檔案共用，將會遺失所有的快照集，而且無法復原您的資料。

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>在檔案共用的虛刪除設定內容中，當我取消註冊儲存體帳戶時，Azure 備份會有哪些變更？

在取消註冊時，Azure 備份會檢查檔案共用的保留週期設定，如果超過14天或少於14天，則會保留原樣。 但是，如果保留期為14天，我們會將它視為 Azure 備份啟用，因此我們會在取消註冊程式期間停用虛刪除。 如果您想要在保留保留設定的情況下取消註冊儲存體帳戶，請在完成取消註冊之後，從 [儲存體帳戶] 窗格再次加以啟用。 您可以參考 [此連結](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) 以取得設定步驟。

## <a name="next-steps"></a>後續步驟

了解如何[從 Azure 入口網站備份 Azure 檔案共用](backup-afs.md)
