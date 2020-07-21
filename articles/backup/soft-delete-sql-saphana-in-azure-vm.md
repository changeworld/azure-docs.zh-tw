---
title: Azure VM 中的 SQL server 虛刪除和 Azure VM 工作負載中的 SAP Hana
description: 瞭解 Azure VM 中的 SQL server 虛刪除和 Azure VM 工作負載中的 SAP Hana 如何讓備份更加安全。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c0eaedea2d5428376befaade42f87348cf84e7bc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538185"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Azure VM 中的 SQL server 虛刪除和 Azure VM 工作負載中的 SAP Hana

Azure 備份現在會針對 Azure VM 中的 SQL server 和 Azure VM 工作負載中的 SAP Hana 提供虛刪除。 這是已支援的[Azure 虛擬機器虛刪除案例](soft-delete-virtual-machines.md)以外的其他功能。

虛[刪除](backup-azure-security-feature-cloud.md)是一項安全性功能，可協助保護備份資料，即使在刪除後也是如此。 使用虛刪除時，即使惡意執行者刪除資料庫的備份（或不小心刪除備份資料），備份資料仍會保留14個額外的天數。 這允許復原該備份專案，而不會遺失資料。 在「虛刪除」狀態中，此額外保留14天的備份資料不會對客戶產生任何費用。

>[!NOTE]
>啟用訂用帳戶的預覽之後，就無法只針對 SQL server 或 SAP Hana Db 停用虛刪除，同時讓相同保存庫中的虛擬機器能夠使用該功能。 您可以建立個別的保存庫，以進行細微的控制。

## <a name="steps-to-enroll-in-preview"></a>在預覽版中註冊的步驟

1. 登入您的 Azure 帳戶。

   ```powershell
   Login-AzureRmAccount
   ```

2. 選取您想要在預覽版中註冊的訂用帳戶：

   ```powershell
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```

3. 向預覽計畫註冊此訂用帳戶：

   ```powershell
   Register-AzureRMProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

4. 等待30分鐘，讓訂用帳戶註冊到預覽中。

5. 若要檢查狀態，請執行下列 Cmdlet：

   ```powershell
   Get-AzureRmProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

6. 一旦訂用帳戶顯示為已註冊，請執行下列命令：

   ```powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

>[!NOTE]
>每當在已啟用虛刪除的訂用帳戶下建立新的保存庫/保存庫時，就必須重新執行下列命令，以啟用新建立之保存庫的功能。<BR>
> `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>使用 Azure 入口網站在 Azure VM 中進行 SQL server 的虛刪除

>[!NOTE]
>這些指示也適用于 Azure VM 中的 SAP Hana。

1. 若要刪除 SQL server 中資料庫的備份資料，必須停止備份。 在 [Azure 入口網站中，移至您的復原服務保存庫，移至 [備份] 專案，然後選擇 [**停止備份**]。

   ![停止備份](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. 在下列視窗中，您將會獲得刪除或保留備份資料的選項。 如果您選擇 [**刪除備份資料**]，資料庫備份將不會永久刪除。 相反地，備份資料會保留14天的「虛刪除」狀態。 這項刪除作業會延後到第15天，一開始就會將資料庫的備份狀態通知給使用者。

   ![刪除備份資料](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. 在這14天的復原服務保存庫中，虛刪除的專案旁邊會出現紅色的「虛刪除」圖示。

   ![虛刪除的專案](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. 若要還原虛刪除的資料庫，必須先將它取消刪除。 若要取消刪除，請選擇虛刪除的資料庫，然後選取 [取消**刪除**] 選項。

   ![刪除資料庫](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   將會出現一個警告，指出如果選擇 [取消刪除]，將會取消刪除資料庫的所有還原點，並可執行還原作業。 備份專案會保留在「停止保護並保留資料」狀態，並暫停備份並保留備份資料，且不會有備份原則生效。

   ![取消刪除警告](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. 此時，您也可以針對所選的虛刪除備份專案，選取 [**還原**] 來還原資料。

   ![還原 VM](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. 在取消刪除程式完成之後，狀態會回到 [停止備份並保留資料]，然後您可以選擇 [**繼續備份**]。 [**繼續備份**] 作業會將備份專案帶回 [作用中] 狀態，並與定義備份和保留排程的使用者所選取的備份原則相關聯。

   ![繼續備份](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>使用 Azure PowerShell 在 VM 中進行 SQL server 的虛刪除

>[!NOTE]
>使用 Azure PowerShell 進行虛刪除所需的 Az. Azurerm.recoveryservices 版本是最低2.2.0。 使用 `Install-Module -Name Az.RecoveryServices -Force` 來取得最新版本。

使用 Azure PowerShell 的步驟順序與上面所述的 Azure 入口網站相同。

### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure PowerShell 刪除備份專案

使用[Enable-azrecoveryservicesbackupprotection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PS Cmdlet 刪除備份專案。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

備份專案的**DeleteState**會從**NotDeleted**變更為**ToBeDeleted**。 備份資料將會保留14天。 如果您想要還原刪除作業，則應該執行復原-刪除。

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure PowerShell 復原刪除作業

首先，提取處於虛刪除狀態（也就是即將刪除）的相關備份專案。

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

然後，使用[復原-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PS Cmdlet 執行復原刪除作業。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

備份專案的**DeleteState**會還原為**NotDeleted**。 但仍會停止保護。 繼續備份以重新啟用保護。

## <a name="how-to-disable-soft-delete"></a>如何停用虛刪除

不建議停用這項功能。 如果您打算將受保護的專案移至新的保存庫，且在刪除和重新保護之前，不能等待14天（例如在測試環境中），您應該考慮停用虛刪除的唯一情況。如需有關如何停用虛刪除的指示，請參閱[啟用和停](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)用虛刪除。

## <a name="next-steps"></a>後續步驟

- 閱讀虛[刪除的相關常見問題](backup-azure-security-feature-cloud.md#frequently-asked-questions)
- 閱讀[Azure 備份中的所有安全性功能](security-overview.md)
