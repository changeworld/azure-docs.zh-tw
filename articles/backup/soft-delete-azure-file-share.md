---
title: 意外刪除 Azure 檔案共用的保護
description: 瞭解如何進行虛刪除，以保護您的 Azure 檔案共用不會遭到意外刪除。
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 0ec2d3bf84aed19b608a92b6f21cd1674ba5b7cf
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282699"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>使用 Azure 備份意外刪除 Azure 檔案共用的保護

若要針對網路攻擊或意外刪除提供保護，當您為個別儲存體帳戶中的任何檔案共用設定備份時，會針對儲存體帳戶中的所有檔案共用啟用虛[刪除](../storage/files/storage-files-prevent-file-share-deletion.md)。 使用虛刪除時，即使惡意執行者刪除檔案共用，檔案共用的內容和復原點（快照集）至少會保留14個額外的天數，允許復原不會遺失資料的檔案共用。  標準和高階儲存體帳戶支援虛刪除，而且設定是針對所有裝載備份檔案共用的儲存體帳戶 Azure 備份啟用。

下列流程圖顯示針對儲存體帳戶中的檔案共用啟用虛刪除時，備份專案的不同步驟和狀態：

 ![虛刪除流程圖表](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>何時會針對我的儲存體帳戶中的檔案共用啟用虛刪除？

當您第一次為儲存體帳戶中的任何檔案共用設定備份時，Azure 備份服務會針對個別儲存體帳戶中的所有檔案共用啟用虛刪除。

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>我可以在刪除檔案共用之後，設定將快照集和還原點保留在虛刪除狀態的天數嗎？

是，您可以根據您的需求設定保留週期。 [本檔](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal)說明設定保留期限的步驟。 針對具有備份檔案共用的儲存體帳戶，最小保留設定應為14天。

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Azure 備份重設我的保留設定，因為我將其設定為少於14天？

從安全性的觀點來看，我們建議針對具有備份檔案共用的儲存體帳戶，保留14天的最小值。 因此，在每個備份作業執行時，如果 Azure 備份將設定識別為少於14天，則會將其重設為14天。

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>保留期間所產生的成本為何？

在虛刪除期間，受保護的實例成本和快照儲存成本會保持不變。  此外，系統會以標準檔案共用的一般費率，以及高階檔案共用的快照集儲存體費率，向您收取已使用容量的費用。

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>當我的資料處於虛刪除狀態時，可以執行還原作業嗎？

您必須先取消刪除虛刪除的檔案共用，以執行還原作業。 取消刪除作業會將檔案共用帶入已備份的狀態，您可以在其中還原至任何時間點。 若要瞭解如何取消刪除您的檔案共用，請造訪[此連結](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)，或參閱取消[刪除檔案共用腳本](./scripts/backup-powershell-script-undelete-file-share.md)。

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>如何清除至少有一個受保護檔案共用之儲存體帳戶中的檔案共用資料？

如果您在儲存體帳戶中至少有一個受保護的檔案共用，這表示該帳戶中的所有檔案共用都已啟用虛刪除，而且您的資料會在刪除作業之後保留14天。 但是，如果您想要立即清除資料，而不想要保留它，請遵循下列步驟：

1. 如果您已在啟用虛刪除時刪除檔案共用，則會先從檔案[入口網站](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)或使用 [取消刪除檔案共用][腳本](./scripts/backup-powershell-script-undelete-file-share.md)，將檔案共用取消刪除。
2. 依照[這份](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)檔中所述的步驟，停用儲存體帳戶中檔案共用的虛刪除。
3. 現在刪除您想要立即清除其內容的檔案共用。

>[!NOTE]
>您應該先執行步驟2，再針對儲存體帳戶中受保護的檔案共用執行下一個排定的備份工作。 因為每次執行備份作業時，都會針對儲存體帳戶中的所有檔案共用重新啟用虛刪除。

>[!WARNING]
>停用步驟2中的虛刪除之後，針對檔案共用執行的任何刪除作業都是永久的刪除作業。 這表示如果您在停用虛刪除之後不小心刪除備份的檔案共用，將會遺失所有快照集，而且將無法復原您的資料。

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>在檔案共用的虛刪除設定內容中，當我取消註冊儲存體帳戶時 Azure 備份執行哪些變更？

在取消註冊時，Azure 備份會檢查檔案共用的保留期限設定，如果超過14天或少於14天，則會保留為不保持。 不過，如果保留時間為14天，我們會將它視為已 Azure 備份啟用，因此我們會在取消註冊程式期間停用虛刪除。 如果您想要取消註冊儲存體帳戶，同時保持保留設定的狀態，請在完成取消註冊之後，從 [儲存體帳戶] 窗格再次啟用它。 如需設定步驟，您可以參考[此連結](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)。

## <a name="next-steps"></a>後續步驟

了解如何[從 Azure 入口網站備份 Azure 檔案共用](backup-afs.md)
