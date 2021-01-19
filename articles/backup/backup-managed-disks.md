---
title: 備份 Azure 受控磁碟
description: 瞭解如何從 Azure 入口網站備份 Azure 受控磁碟。
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 2169e2f44e3ffb2c05c674d633efabed2c531878
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573117"
---
# <a name="back-up-azure-managed-disks-in-preview"></a>在預覽中備份 Azure 受控磁碟 () 

>[!IMPORTANT]
>Azure 磁片備份處於預覽狀態，不含服務等級協定，因此不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 如需區域可用性，請參閱 [支援矩陣](disk-backup-support-matrix.md)。
>
>[填寫此表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以註冊預覽。

本文說明如何從 Azure 入口網站備份 [Azure 受控磁片](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) 。

在本文中，您將學會如何：

- 建立備份保存庫

- 建立備份原則

- 設定 Azure 磁片的備份

- 執行隨選備份作業

如需 Azure 磁片備份區域可用性、支援的案例和限制的詳細資訊，請參閱 [支援矩陣](disk-backup-support-matrix.md)。

## <a name="create-a-backup-vault"></a>建立備份保存庫

備份保存庫是 Azure 中的儲存體實體，可保存 Azure 備份支援之各種較新工作負載的備份資料，例如適用於 PostgreSQL 的 Azure 資料庫伺服器和 Azure 磁片。 備份保存庫可讓您輕鬆地組織您的備份資料，同時將管理額外負荷降到最低。 備份保存庫是以 Azure 的 Azure Resource Manager 模型為基礎，可提供增強的功能來協助保護備份資料的安全。

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com/)。
1. 在 [搜尋] 方塊中輸入 **Backup center** 。
1. 在 [ **服務**] 下，選取 [ **備份中心**]。
1. 在 [ **備份中心** ] 頁面中，選取 [保存 **庫**]。

   ![選取備份中心內的保存庫](./media/backup-managed-disks/backup-center.png)

1. 在 [ **起始：建立保存庫** ] 畫面中，選取 [ **備份保存庫**]，然後 **繼續進行**。

   ![起始：建立保存庫](./media/backup-managed-disks/initiate-create-vault.png)

1. 在 [ **基本** ] 索引標籤中，提供訂用帳戶、資源群組、備份保存庫名稱、區域和備份儲存體冗余。 選取 [ **審核 + 建立**] 繼續進行。 深入瞭解如何 [建立備份保存庫](https://docs.microsoft.com/azure/backup/backup-vault-overview#create-a-backup-vault)。

   ![檢查和建立保存庫](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>建立備份原則

1. 在上一個步驟中建立的 *DemoVault* **備份保存庫** 中，移至 [ **備份原則** ]，然後選取 [ **新增**]。

   ![新增備份原則](./media/backup-managed-disks/backup-policies.png)

1. 在 [ **基本** ] 索引標籤中，提供原則名稱，選取 [ **資料來源類型** ] 作為 [ **Azure 磁片**]。 保存庫已預先填入，並顯示選取的保存庫屬性。

   >[!NOTE]
   > 雖然選取的保存庫可能具有全域冗余設定，但目前的 Azure 磁片備份僅支援快照集資料存放區。 所有備份都會儲存在您訂用帳戶的資源群組中，而不會複製到備份保存庫儲存體。

   ![選取資料來源類型](./media/backup-managed-disks/datasource-type.png)

1. 在 [ **備份原則** ] 索引標籤中，選取 [備份排程頻率]。

   ![選取備份排程頻率](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure 磁片備份每天提供多個備份。 如果您需要更頻繁的備份，請選擇 [ **每小時** 備份頻率]，讓您能夠以每4、6、8或12小時的間隔進行備份。 備份會根據選取的 **時間** 間隔進行排程。 例如，如果您選取 **每4小時**，則會在每隔4小時的間隔內大約進行備份，讓備份在一天內平均分散。 如果一天備份已足夠，請選擇 **每日** 備份頻率。 在 [每日備份頻率] 中，您可以指定執行備份的當日時間。 請務必注意，一天中的時程表示備份開始時間，而不是備份完成的時間。 完成備份作業所需的時間取決於各種因素，包括磁片的大小，以及連續備份之間的流失率。 不過，Azure 磁片備份是使用 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal)的無代理程式備份，這不會影響生產應用程式的效能。

1. 在 [ **備份原則** ] 索引標籤中，選取符合復原點目標的保留設定 (RPO) 需求。

   如果未指定其他保留規則，則會套用預設保留規則。 您可以修改預設保留規則來變更保留期間，但無法刪除。 您可以選取 [新增 **保留規則**] 來新增保留規則。

   ![新增保留規則](./media/backup-managed-disks/add-retention-rule.png)

   您可以挑選每天或每週的 **第一次成功備份** ，並提供要在刪除特定備份之前保留的保留期間。 此選項適用于保留一天或一周的特定備份長時間。 所有其他頻繁的備份可以保留較短的持續時間。

   ![保留設定](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >受控磁碟的 Azure 備份會使用每個磁片最多200個快照集的累加式快照集。 為了讓您在排程的備份之外進行隨選備份，備份原則會將備份總數限制為180。 深入瞭解受控磁片的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) 集。

1. 選取 [ **審核 + 建立**]，完成備份原則的建立。

## <a name="configure-backup"></a>設定備份

備份保存庫會使用受控識別來存取其他 Azure 資源。 若要設定受控磁片的備份，備份保存庫的受控識別需要來源磁片上的一組許可權，以及建立及管理快照集的資源群組。

系統指派的受控識別會限制為每個資源一個，並系結到此資源的生命週期。 您可以使用 Azure 角色型存取控制 (Azure RBAC) ，將許可權授與受控識別。 受控識別是特殊類型的服務主體，只可搭配 Azure 資源使用。 深入瞭解 [受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

若要設定受控磁片的備份，必須具備下列必要條件：

1. 在要備份的來源磁片上，將「 **磁片備份讀取器** 」角色指派給備份保存庫的受控識別。

   1. 移至需要備份的磁片。

   1. 移至 [**存取控制] (IAM)** ，然後選取 [**新增角色指派**]

   1. 在右側的內容窗格中，選取 [**角色**] 下拉式清單中的 [**磁片備份讀取器**]。 選取備份保存庫的受控識別，並 **儲存**。

   >[!TIP]
   >輸入備份保存庫名稱，以選取保存庫的受控識別。

   ![新增磁片備份讀取器角色](./media/backup-managed-disks/disk-backup-reader-role.png)

1. 在 Azure 備份服務建立和管理備份的資源群組上，將「 **磁片快照參與者** 」角色指派給備份保存庫的受控識別。 磁片快照集會儲存在您訂用帳戶內的資源群組中。 若要允許 Azure 備份服務建立、儲存及管理快照集，您必須提供備份保存庫的許可權。

   **選擇用來儲存和管理快照集的資源群組：**

   - 請勿選取與來源磁片相同的資源群組。

   - 作為指導方針，建議您建立專用的資源群組做為 Azure 備份服務所使用的快照集資料存放區。 具有專用的資源群組可限制資源群組的存取權限，以提供安全且易於管理的備份資料。

   - 您可以使用此資源群組，將快照集儲存在要 (或計畫) 備份的多個磁片上。  

   - 您無法為該磁片訂用帳戶以外的特定磁片建立增量快照集。 因此，請選擇與要備份之磁片相同的訂用帳戶中的資源群組。 深入瞭解受控磁片的累加 [式快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) 集。

   若要指派角色，請遵循下列步驟：

   1. 移至資源群組。 例如，資源群組是 *SnapshotRG*，與要備份的磁片位於相同的訂用帳戶中。

   1. 移至 [ **存取控制] (IAM)** ，然後選取 [ **新增角色指派**]。

   1. 在右側的內容窗格中，選取 [**角色**] 下拉式清單中的 [**磁片快照參與者**]。 選取備份保存庫的受控識別，並 **儲存**。

   >[!TIP]
   >輸入備份保存庫名稱，以選取保存庫的受控識別。

   ![新增磁片快照集參與者角色](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. 如果要備份的磁片使用 [客戶管理的金鑰進行加密 (CMK)](https://docs.microsoft.com/azure/virtual-machines/disks-enable-customer-managed-keys-portal)或使用 [以平臺管理的金鑰和客戶管理的金鑰進行雙重加密](https://docs.microsoft.com/azure/virtual-machines/disks-enable-double-encryption-at-rest-portal)，然後在 **磁片加密集** 資源上，將「**讀取** 者」角色許可權指派給備份保存庫的受控識別。

1. 確認備份保存庫的受控識別在來源磁片和資源群組上有一組正確的角色指派，作為快照資料存放區。

   1. 移至 **備份保存庫-> 身分識別** ，然後選取 [ **Azure 角色指派**]。

      ![選取 Azure 角色指派](./media/backup-managed-disks/azure-role-assignments.png)

   1. 確認已正確反映角色、資源名稱和資源類型。

      ![確認角色、資源名稱和資源類型](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >當角色指派在入口網站上正確反映時，可能需要大約15分鐘的時間，才會在備份保存庫的受控識別上套用許可權。

1. 符合必要條件之後，請移至 **備份保存庫-> 總覽** ]，然後選取 [ **備份** ] 開始設定磁片的備份。

   ![選取備份](./media/backup-managed-disks/select-backup.png)

1. 在 [ **基本** ] 索引標籤中，選取 [ **Azure 磁片** ] 作為資料來源類型。

   ![選取 Azure 磁片](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure 備份使用受控磁片的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) 集，這會在標準 HDD 儲存體上的上一個快照集之後，只儲存磁片的差異變更，不論父磁片的儲存體類型為何。 針對額外的可靠性，增量快照集會儲存在區域冗余儲存體 (ZRS) 預設位於支援 ZRS 的區域中。 目前，Azure 磁片備份支援不會將備份複製到備份保存庫儲存體之受控磁片的操作備份。 因此，備份保存庫的備份儲存體冗余設定不會套用至復原點。

1. 在 [ **備份原則** ] 索引標籤中，選擇備份原則。

   ![選擇備份原則](./media/backup-managed-disks/choose-backup-policy.png)

1. 在 [ **資源** ] 索引標籤中，選取 [ **選取 Azure 磁片**]。 在右側的內容窗格中，選取要備份的磁片。

   ![選取要備份的磁片](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >當入口網站可讓您選取多個磁片並設定備份時，每個磁片都是個別的備份實例。 Azure 磁片備份目前僅支援個別磁片的備份。 不支援連接至虛擬磁片之多個磁片的時間點備份。
   >
   >使用入口網站時，您只能選取相同訂用帳戶內的磁片。 如果您有數個要備份的磁片，或磁片分散到不同的訂用帳戶，您可以使用腳本來自動化。
   >
   >如需 Azure 磁片備份區域可用性、支援的案例和限制的詳細資訊，請參閱 [支援矩陣](disk-backup-support-matrix.md)。

1. 選取 **快照集資源群組** ，然後選取 [ **驗證**]。 這是 Azure 備份服務建立和管理備份保存庫增量快照集的資源群組。 受控識別會以必要的角色許可權指派，作為必要條件的一部分。

   ![選取快照集資源群組](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >驗證可能需要幾分鐘才能完成，您才能完成設定備份工作流程。 如果有下列情況，驗證可能會失敗：
   >
   > - 不支援磁片。 請參閱 [支援矩陣](disk-backup-support-matrix.md) 以取得不支援的案例。
   > - 備份保存庫受控識別在要備份的 **磁片** 上沒有有效的角色指派，或是在儲存增量快照集的 **快照集資源群組** 上沒有有效的角色指派。

1. 驗證成功之後，請選取 [ **審核] 並設定** ，以設定所選磁片的備份。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

1. 在上一個步驟中建立的 *DemoVault* **備份保存庫** 中，移至 [ **備份實例** ]，然後選取 [備份實例]。

   ![選取備份實例](./media/backup-managed-disks/select-backup-instance.png)

1. 在 [ **備份實例** ] 畫面中，您會發現：

   - **重要** 資訊包括來源磁片名稱、快照集資源群組，其中會儲存增量快照集、備份保存庫和備份原則。
   - **工作狀態** 顯示備份和還原作業的摘要，以及過去七天內的狀態。
   - 所選時段的 **還原點** 清單。

1. 選取 [ **備份** ] 以起始隨選備份。

   ![選取立即備份](./media/backup-managed-disks/backup-now.png)

1. 選取其中一個與備份原則相關聯的保留規則。 此保留規則將決定此隨選備份的保留期間。 選取 [ **立即備份** ] 以開始備份。

   ![起始備份](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>追蹤備份作業

Azure 備份服務會為已排程的備份建立作業，或者，如果您觸發追蹤的隨選備份作業。 若要查看備份作業狀態：

1. 移至 [ **備份實例** ] 畫面。 它會顯示過去七天內具有作業和狀態的作業儀表板。

   ![作業儀表板](./media/backup-managed-disks/jobs-dashboard.png)

1. 若要查看備份作業的狀態，請選取 [ **全部查看** ]，顯示此備份實例的持續和過去工作。

   ![選取全視圖](./media/backup-managed-disks/view-all.png)

1. 檢查備份和還原作業的清單及其狀態。 從作業清單中選取作業，以查看作業詳細資料。

   ![選取作業以查看詳細資料](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>後續步驟

- [還原 Azure 受控磁碟](restore-managed-disks.md)
