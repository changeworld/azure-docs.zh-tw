---
title: 關於 Azure 磁片備份的常見問題
description: 取得有關 Azure 磁片備份的常見問題的解答
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 3ef18a7d178075194e24889477768583f05f0cdd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734557"
---
# <a name="frequently-asked-questions-about-azure-disk-backup-in-preview"></a>有關 Azure 磁片備份 (預覽版的常見問題) 

>[!IMPORTANT]
>Azure 磁片備份處於預覽狀態，不含服務等級協定，因此不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 如需區域可用性，請參閱 [支援矩陣](disk-backup-support-matrix.md)。
>
>[填寫此表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以註冊預覽。

本文將回答有關 Azure 磁片備份的常見問題。 如需 [Azure 磁片備份](disk-backup-overview.md) 區域可用性、支援的案例和限制的詳細資訊，請參閱 [支援矩陣](disk-backup-support-matrix.md)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>如果使用 Azure 虛擬機器備份來備份相同磁片，我可以使用 Azure 磁片備份解決方案來備份磁片嗎？

Azure 備份提供使用磁片備份和 [AZURE VM 備份](backup-azure-vms-introduction.md) 解決方案來備份受控磁片的並存支援。 當您需要一天的應用程式一致的虛擬機器備份，以及更頻繁的 OS 磁片備份，或較頻繁的 OS 磁片備份或特定的資料磁片（當機而不影響生產應用程式效能）時，這會很有用。

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>如何? 尋找我用來設定磁片備份的快照集資源群組？

在 [ **備份實例** ] 畫面中，您可以在 [ **基本** ] 區段中找到 [快照集資源群組] 欄位。 您可以從備份中心或備份保存庫搜尋和選取對應磁片的備份實例。

![Snapshot 資源群組欄位](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>什麼是快照集資源群組？

Azure 磁片備份提供受控磁片的操作層備份。 也就是說，在排程和隨選備份作業期間建立的快照集會儲存在訂用帳戶內的資源群組中。 Azure 備份提供立即還原，因為增量快照集會儲存在您的訂用帳戶中。 此資源群組稱為快照集資源群組。 如需詳細資訊，請參閱 [設定備份](backup-managed-disks.md#configure-backup)。

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>快照集資源群組為何必須與要備份的磁片位於相同的訂用帳戶中？

您無法為該磁片訂用帳戶以外的特定磁片建立增量快照集。 因此，請選擇與要備份之磁片相同的訂用帳戶中的資源群組。 深入瞭解受控磁片的累加 [式快照](../virtual-machines/disks-incremental-snapshots.md#restrictions) 集。

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>為什麼我需要提供角色指派才能設定備份、執行排程備份和隨選備份，以及還原作業？

Azure 磁片備份使用最低許可權方法來探索、保護和還原您訂用帳戶中的受控磁片。 為了達成此目的，Azure 備份使用 [備份保存庫](backup-vault-overview.md) 的受控識別來存取其他 Azure 資源。 系統指派的受控識別會限制為每個資源一個，並系結到此資源的生命週期。 您可以使用 Azure 角色型存取控制 (Azure RBAC) ，將許可權授與受控識別。 受控識別是特殊類型的服務主體，只可搭配 Azure 資源使用。 深入了解[受控識別](../active-directory/managed-identities-azure-resources/overview.md)。 根據預設，備份保存庫不會有許可權存取要備份的磁片、建立週期性快照集、在保留期限後刪除快照集，以及從備份復原磁碟。 藉由明確授與角色指派給備份保存庫的受控識別，您就可以控制對訂用帳戶上資源的版權管理。

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>備份原則為何會限制保留期限？

Azure 磁片備份使用增量快照集，其限制為每個磁片200個快照集。 為了讓您在排程的備份之外進行隨選備份，備份原則會將備份總數限制為180。 深入瞭解受控磁片的 [增量快照](../virtual-machines/disks-incremental-snapshots.md#restrictions) 集。

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>每小時和每日備份頻率在備份原則中的運作方式為何？

Azure 磁片備份每天提供多個備份。 如果您需要更頻繁的備份，請選擇 **每小時** 備份頻率。 備份會根據選取的 **時間** 間隔進行排程。 例如，如果您選取 **每4小時**，則會大約每4小時進行備份，讓備份在一天內平均分散。 如果備份一天的時間足夠，請選擇 **每日** 備份頻率。 在 [每日備份頻率] 中，您可以指定要執行備份的當日時間。 請務必注意，一天中的時程表示備份開始時間，而不是備份完成的時間。 完成備份作業所需的時間取決於各種因素，包括連續備份之間的流失率。 不過，Azure 磁片備份是無代理程式備份，它會使用不會影響生產應用程式效能的累加 [式快照](../virtual-machines/disks-incremental-snapshots.md) 集。

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>為什麼備份保存庫的冗余設定不適用於 (快照集資源群組) 的作業層中儲存的備份？

Azure 備份使用受控磁片的 [增量快照](../virtual-machines/disks-incremental-snapshots.md#restrictions) 集，其只會在標準 HDD 儲存體上的上一個快照集之後，將差異變更儲存至磁片，不論父磁片的儲存體類型為何。 為了更可靠，增量快照集會儲存在區域冗余儲存體 (ZRS) 預設為支援 ZRS 的區域。 目前，Azure 磁片備份支援不會將備份複製到備份保存庫儲存體之受控磁片的操作備份。 因此，備份保存庫的備份儲存體冗余設定不會套用至復原點。

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>我可以使用備份中心來設定備份，以及管理 Azure 磁片的備份實例嗎？

是，Azure 磁片備份已整合到 [備份中心](backup-center-overview.md)，可在 azure 中提供 **單一整合的管理體驗** ，讓企業能夠大規模管理、監視、操作及分析備份。 您也可以使用備份保存庫來備份、還原及管理保存庫中受保護的備份實例。

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>為什麼我需要建立備份保存庫，而不使用復原服務保存庫？

備份保存庫是 Azure 中的儲存體實體，可儲存 Azure 備份支援之特定較新工作負載的備份資料。 您可以使用備份保存庫來保存各種 Azure 服務（例如適用於 PostgreSQL 的 Azure 資料庫伺服器、Azure 磁片和 Azure 備份將支援的較新工作負載）的備份資料。 備份保存庫可讓您輕鬆地組織您的備份資料，同時將管理額外負荷降到最低。 若要深入瞭解，請參閱 [備份保存庫](./backup-vault-overview.md) 。

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>磁片可以備份，且備份保存庫位於不同的訂用帳戶嗎？

是，要備份的來源受控磁片，以及備份保存庫可以位於不同的訂用帳戶中。

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>磁片可以備份，且備份保存庫位於不同的區域嗎？

否，目前要備份的來源受控磁片和備份保存庫必須位於相同的區域。

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>我可以將磁片還原至不同的訂用帳戶嗎？

是的，您可以將磁片還原至不同的訂用帳戶，而不是進行備份的來源受控磁片。

### <a name="can-i-back-up-multiple-disks-together"></a>我可以同時備份多個磁片嗎？

否，不支援連接到虛擬機器的多個磁片的時間點快照集。 如需詳細資訊，請參閱「 [設定備份](backup-managed-disks.md#configure-backup) 」，若要深入瞭解限制，請參閱 [支援矩陣](disk-backup-support-matrix.md)。

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>有哪些選項可以跨多個訂用帳戶備份磁片？

目前，使用 Azure 入口網站設定磁片的備份，最多可有20個來自相同訂用帳戶的磁片。

### <a name="what-is-a-target-resource-group"></a>什麼是目標資源群組？

在還原作業期間，您可以選擇要將磁片還原至其中的訂用帳戶和資源群組。 Azure 備份將會從所選資源群組中的復原點建立新的磁片。 這稱為目標資源群組。 請注意，備份保存庫的受控識別需要目標資源群組上的角色指派，才能順利執行還原作業。 如需詳細資訊，請參閱 [還原檔](restore-managed-disks.md)。

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>在備份和還原作業期間，Azure 備份所使用的許可權為何？

以下是在要備份的 **磁片** 上指派的「**磁片備份讀取器**」角色中使用的動作：

"Microsoft. Compute/磁片/read"

"Microsoft. Compute/磁片/beginGetAccess/action"

以下是在 **快照集資源群組** 上指派的 **磁片快照參與者** 角色中使用的動作：

"Microsoft. Compute/快照/刪除"

"Microsoft. 計算/快照/寫入"

"Microsoft. Compute/快照/read"

"Microsoft. Storage/storageAccounts/write"

"Microsoft. Storage/storageAccounts/read"

"Microsoft. Storage/storageAccounts/delete"

"Microsoft .Resources/訂閱/resourceGroups/read"

"Microsoft. Storage/storageAccounts/listkeys/action"

"Microsoft. Compute/快照/beginGetAccess/action"

"Microsoft. Compute/快照/呼叫 endgetaccess/action"

"Microsoft. Compute/磁片/beginGetAccess/action"

以下是在 **目標資源群組** 上指派的 **磁片還原操作員** 角色中使用的動作：

"Microsoft. Compute/磁片/write"

"Microsoft. Compute/磁片/read"

"Microsoft .Resources/訂閱/resourceGroups/read"

>[!NOTE]
>這些角色的許可權可能會在未來變更，根據 Azure 備份服務所加入的功能而定。

## <a name="next-steps"></a>後續步驟

- [Azure 磁碟備份支援矩陣](disk-backup-support-matrix.md)