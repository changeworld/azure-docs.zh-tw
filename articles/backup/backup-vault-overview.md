---
title: 備份保存庫總覽
description: 備份保存庫的總覽。
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: c189997ecc4814917182246b35003649d317ac77
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091282"
---
# <a name="backup-vaults-overview"></a>備份保存庫總覽

本文說明備份保存庫的功能。 備份保存庫是 Azure 中的儲存體實體，可儲存 Azure 備份支援之特定較新工作負載的備份資料。 您可以使用備份保存庫來保存各種 Azure 服務（例如適用於 PostgreSQL 的 Azure 資料庫伺服器和 Azure 備份將支援的較新工作負載）的備份資料。 備份保存庫可讓您輕鬆地組織您的備份資料，同時將管理額外負荷降到最低。 備份保存庫是以 Azure 的 Azure Resource Manager 模型為基礎，可提供下列功能：

- **協助保護備份資料安全的增強功能**：使用備份保存庫，Azure 備份可提供安全性功能來保護雲端備份。 這些安全性功能可確保您能保護您的備份，並安全地將資料復原，即使生產和備份伺服器遭到入侵也一樣。 [深入了解](backup-azure-security-feature.md)

- Azure**角色型存取控制 (AZURE rbac) **： azure rbac 可在 azure 中提供更細緻的存取管理控制。 [Azure 提供各種內建角色](../role-based-access-control/built-in-roles.md)，且 Azure Backup 有三個[內建的角色可用來管理復原點](backup-rbac-rs-vault.md)。 備份保存庫與 Azure RBAC 相容，這會限制對所定義使用者角色集的備份和還原存取權。 [深入了解](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>備份保存庫中的儲存體設定

備份保存庫是一個實體，可儲存一段時間內建立的備份和復原點。 備份保存庫也包含與受保護虛擬機器相關聯的備份原則。

- Azure 備份會自動處理保存庫的儲存體。 當您建立備份保存庫時，請選擇符合您商務需求的儲存體冗余。

- 若要深入瞭解儲存體冗余，請參閱這些有關 [地理](../storage/common/storage-redundancy.md#geo-redundant-storage) 位置和 [本機](../storage/common/storage-redundancy.md#locally-redundant-storage) 冗余的文章。

## <a name="encryption-settings-in-the-backup-vault"></a>備份保存庫中的加密設定

本節將討論可用來將備份保存庫中儲存的備份資料加密的選項。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平臺管理的金鑰來加密備份資料

根據預設，您的所有資料都會使用平臺管理的金鑰進行加密。 您不需要從您的一端採取任何明確的動作，就能啟用此加密。 它適用于所有備份至備份保存庫的工作負載。

## <a name="create-a-backup-vault"></a>建立備份保存庫

備份保存庫是一種管理實體，可儲存一段時間所建立的復原點，並提供介面來執行備份相關的作業。 這些作業包括製作隨選備份、執行還原，以及建立備份原則。

若要建立備份保存庫，請遵循下列步驟。

### <a name="sign-in-to-azure"></a>登入 Azure

在 <https://portal.azure.com> 登入 Azure 入口網站。

### <a name="create-backup-vault"></a>建立備份保存庫

1. 在 [搜尋] 方塊中輸入 **備份保存庫** 。
1. 在 [ **服務**] 下，選取 [ **備份保存庫**]。
1. 在 [ **備份保存庫** ] 頁面中，選取 [ **新增**]。
1. 在 [ **基本]** 索引標籤的 [ **專案詳細資料**] 底下，確認已選取正確的訂用帳戶，然後選擇 [ **建立新** 的資源群組]。 輸入 myResourceGroup  作為名稱。

  ![建立新的資源群組](./media/backup-vault-overview/new-resource-group.png)

1. 在 [**實例詳細資料**] 底下，輸入*MyVault*做為**備份保存庫名稱**，然後選擇您選擇的區域，在此案例中為您**所在地區**的*美國東部*。
1. 現在選擇您的 **儲存體冗余**。 保護保存庫的專案之後，即無法變更儲存體冗余。
1. 如果您使用 Azure 作為主要的備份儲存體端點，建議您繼續使用預設的 **地理區域冗余** 設定。
1. 如果您未使用 Azure 做為主要的備份儲存體端點，則選擇 [本地備援]****，以減少 Azure 儲存體成本。
1. 深入瞭解 [地理](../storage/common/storage-redundancy.md#geo-redundant-storage) 和 [本機](../storage/common/storage-redundancy.md#locally-redundant-storage) 冗余。

  ![選擇儲存體冗余](./media/backup-vault-overview/storage-redundancy.png)

1. 選取頁面底部的 [檢查 + 建立] 按鈕。

    ![選取 [審核] + 建立](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>刪除備份保存庫

本節說明如何刪除備份保存庫。 它包含移除相依性，然後刪除保存庫的指示。

### <a name="before-you-start"></a>開始之前

您無法刪除具有下列任何相依性的備份保存庫：

- 您無法刪除包含受保護資料來源的保存庫 (例如，適用于于 postgresql 伺服器的 Azure 資料庫) 。
- 您無法刪除包含備份資料的保存庫。

如果您嘗試在不移除相依性的情況下刪除保存庫，您將會遇到下列錯誤訊息：

>因為保存庫中有現有的備份實例或備份原則，所以無法刪除備份保存庫。 刪除保存庫中存在的所有備份實例和備份原則，然後嘗試刪除保存庫。

### <a name="proper-way-to-delete-a-vault"></a>刪除保存庫的適當方式

>[!WARNING]
下列作業是破壞性的作業，無法復原。 所有與受保護伺服器相關聯的備份資料和備份專案都會永久刪除。 請謹慎進行。

若要適當地刪除保存庫，您必須依照下列循序執行步驟：

- 您必須檢查以確認是否有任何受保護的專案：
  - 移至左側導覽列中的 [ **備份實例** ]。 您必須先刪除此處列出的所有專案。

完成這些步驟之後，您可以繼續刪除保存庫。

### <a name="delete-the-backup-vault"></a>刪除備份保存庫

當保存庫中沒有任何專案時，請選取保存庫儀表板上的 [ **刪除** ]。 您會看到確認文字，詢問您是否要刪除保存庫。

![刪除保存庫](./media/backup-vault-overview/delete-vault.png)

1. 選取 **[是]** 以確認您要刪除保存庫。 保存庫已刪除。 入口網站會回到 [ **新增** 服務] 功能表。

## <a name="monitor-and-manage-the-backup-vault"></a>監視和管理備份保存庫

本節說明如何使用備份保存庫 **總覽** 儀表板來監視和管理您的備份保存庫。 [總覽] 窗格包含兩個磚： **作業** 和 **實例**。

![概觀儀表板](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>管理備份實例

在 [ **工作** ] 磚中，您可以取得備份保存庫中所有備份和還原相關作業的摘要說明。 選取此磚中的任何數位，可讓您針對特定資料來源類型、作業類型和狀態，查看更多作業的相關資訊。

![備份實例](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>管理備份作業

在 [ **備份實例** ] 圖格中，您可以取得備份保存庫中所有備份實例的摘要顯示。 選取此磚中的任何數位，可讓您針對特定資料來源類型和保護狀態，查看有關備份實例的詳細資訊。

![備份作業](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>後續步驟

- [在 Azure 于 postgresql 資料庫上設定備份](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
