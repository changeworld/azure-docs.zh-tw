---
title: 虛擬機器的虛刪除
description: 瞭解虛刪除虛擬機器如何讓備份更加安全。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: ba00b235ea70bcc2dabbd5a91a3f7003f9bbed49
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82765769"
---
# <a name="soft-delete-for-virtual-machines"></a>虛擬機器的虛刪除

Vm 的虛刪除可保護 Vm 的備份不會遭到意外刪除。 即使在刪除備份之後，它們仍會以虛刪除狀態保留14個額外的天數。

> [!NOTE]
> 虛刪除只會保護已刪除的備份資料。 如果 VM 在沒有備份的情況下刪除，虛刪除功能將不會保留資料。 所有資源都應該使用 Azure 備份來保護，以確保完整的復原能力。
>

## <a name="supported-regions"></a>支援區域

美國中西部、東亞、加拿大中部、加拿大東部、法國中部、法國南部、韓國中部、南韓南部、英國南部、英國西部、澳大利亞東部、澳大利亞東部、北歐、美國西部、West 美國2、美國中部、南部東亞、美國中北部、美國中南部、日本東部、日本西部、印度南部、印度中部、印度西部、美國東部2，目前支援虛刪除、瑞士北部、瑞士西部、挪威西部、挪威東部和所有國家地區。

## <a name="soft-delete-for-vms-using-azure-portal"></a>使用 Azure 入口網站的 Vm 虛刪除

1. 若要刪除 VM 的備份資料，必須停止備份。 在 Azure 入口網站中，移至您的復原服務保存庫，以滑鼠右鍵按一下備份專案，然後選擇 [**停止備份**]。

   ![Azure 入口網站備份專案的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在下列視窗中，您將會獲得刪除或保留備份資料的選項。 如果您選擇 [**刪除備份資料**]，然後**停止備份**，則不會永久刪除 VM 備份。 相反地，備份資料會保留14天的「虛刪除」狀態。 如果選擇 [**刪除備份資料**]，則會將刪除電子郵件警示傳送至設定的電子郵件識別碼，通知使用者14天保留備份資料的延伸保留期。 此外，第12天會傳送電子郵件警示，通知您有兩天的時間來 resurrect 已刪除的資料。 刪除作業會延後到15天后，將會進行永久刪除，並傳送最終的電子郵件警示來通知永久刪除資料。

   ![[停止備份] 畫面 Azure 入口網站的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在這14天的復原服務保存庫中，虛刪除的 VM 旁會出現紅色的「虛刪除」圖示。

   ![Azure 入口網站的螢幕擷取畫面，VM 處於虛刪除狀態](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 如果保存庫中有任何虛刪除的備份專案，就無法在該時間刪除保存庫。 請在永久刪除備份專案之後嘗試刪除保存庫，而且保存庫中沒有任何已虛刪除狀態的專案。

4. 若要還原已虛刪除的 VM，則必須先將它取消刪除。 若要取消刪除，請選擇虛刪除的 VM，然後選取 [取消**刪除**] 選項。

   ![Azure 入口網站、取消刪除 VM 的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   將會出現一個警告，指出如果選擇 [取消刪除]，將會取消刪除 VM 的所有還原點，並可執行還原作業。 VM 將會保留在「停止保護並保留資料」狀態，並暫停備份並保留備份資料，且不會有任何備份原則生效。

   ![Azure 入口網站的螢幕擷取畫面，確認刪除 VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   此時，您也可以從選擇的還原點選取 [**還原 vm** ] 來還原 vm。  

   ![Azure 入口網站，Restore VM 選項的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 只有在使用者執行**繼續備份**作業之後，垃圾收集行程才會執行並清除過期的復原點。

5. 在取消刪除程式完成之後，狀態會回到 [停止備份並保留資料]，然後您可以選擇 [**繼續備份**]。 [**繼續備份**] 作業會將備份專案帶回 [作用中] 狀態，並與定義備份和保留排程的使用者所選取的備份原則相關聯。

   ![[繼續備份] 選項 Azure 入口網站的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>使用 Azure PowerShell 的 Vm 虛刪除

> [!IMPORTANT]
> 使用 Azure PS 進行虛刪除所需的 Az. Azurerm.recoveryservices 版本是 min 2.2.0。 使用 ```Install-Module -Name Az.RecoveryServices -Force``` 來取得最新版本。

如上 Azure 入口網站所述，使用 Azure PowerShell 時，步驟的順序也相同。

### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure PowerShell 刪除備份專案

使用[Enable-azrecoveryservicesbackupprotection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS Cmdlet 刪除備份專案。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

備份專案的 ' DeleteState ' 會從 ' NotDeleted ' 變更為 ' ToBeDeleted '。 備份資料將會保留14天。 如果您想要還原刪除作業，則應該執行復原-刪除。

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure PowerShell 復原刪除作業

首先，提取處於虛刪除狀態（也就是即將刪除）的相關備份專案。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然後，使用[復原-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS Cmdlet 執行復原刪除作業。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

備份專案的 ' DeleteState ' 將會還原為 ' NotDeleted '。 但仍會停止保護。 [繼續備份](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items)以重新啟用保護。

## <a name="soft-delete-for-vms-using-rest-api"></a>使用 REST API 的 Vm 虛刪除

- 如[這裡](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述，使用 REST API 刪除備份。
- 如果使用者希望復原這些刪除作業，請參閱[這裡](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)所述的步驟。

## <a name="how-to-disable-soft-delete"></a>如何停用虛刪除

不建議停用這項功能。 如果您打算將受保護的專案移至新的保存庫，且在刪除和重新保護之前，不能等待14天（例如在測試環境中），您應該考慮停用虛刪除的唯一情況。如需有關如何停用虛刪除的指示，請參閱[啟用和停](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)用虛刪除。

## <a name="next-steps"></a>後續步驟

- 閱讀虛[刪除的相關常見問題](backup-azure-security-feature-cloud.md#frequently-asked-questions)
- 閱讀[Azure 備份中的所有安全性功能](security-overview.md)
