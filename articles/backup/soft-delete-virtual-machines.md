---
title: 虛擬機器的虛刪除
description: 瞭解虛擬機器虛刪除如何讓備份更安全。
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022236"
---
# <a name="soft-delete-for-virtual-machines"></a>虛擬機器的虛刪除

Vm 的虛刪除會保護 Vm 的備份免于非預期的刪除。 即使在刪除備份之後，它們仍會以虛刪除狀態保留14個額外的天數。

> [!NOTE]
> 虛刪除只會保護已刪除的備份資料。 如果在沒有備份的情況下刪除 VM，虛刪除功能將不會保留資料。 所有資源都應該使用 Azure 備份來保護，以確保完整的復原能力。
>

## <a name="supported-regions"></a>支援區域

在美國中西部、東亞、加拿大中部、加拿大東部、法國中部、法國南部、韓國中部、南韓南部、英國南部、歐洲、澳大利亞東部、澳大利亞東南部、歐洲北部、美國西部、美國中南部、美國中部、日本東部、日本西部、印度南部、印度中部、印度西部、美國東部2中，都支援虛刪除、瑞士北部、瑞士西部、挪威西部、挪威東部和所有國家地區。

## <a name="soft-delete-for-vms-using-azure-portal"></a>使用 Azure 入口網站的 Vm 虛刪除

1. 若要刪除 VM 的備份資料，必須停止備份。 在 Azure 入口網站中，移至您的復原服務保存庫，以滑鼠右鍵按一下備份專案，然後選擇 [ **停止備份**]。

   ![Azure 入口網站備份專案的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在下一個視窗中，您可以選擇刪除或保留備份資料。 如果您選擇 [ **刪除備份資料** ]，然後 **停止備份**，將不會永久刪除 VM 備份。 相反地，備份資料將會保留14天的虛刪除狀態。 如果選擇 [ **刪除備份資料** ]，則會將 [刪除電子郵件] 警示傳送至設定的電子郵件識別碼，通知使用者有14天的備份資料保留期保留。 此外，第12天會傳送電子郵件警示，通知您有兩天 resurrect 已刪除的資料。 刪除會延後到15天后，永久刪除將會進行，而最後傳送的電子郵件警示則會通知您永久刪除資料。

   ![Azure 入口網站的 [停止備份] 畫面的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在這14天的復原服務保存庫中，虛刪除的 VM 旁邊會顯示紅色的「虛刪除」圖示。

   ![Azure 入口網站的螢幕擷取畫面，以虛刪除狀態的 VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 如果保存庫中有任何虛刪除的備份專案，則在該時間無法刪除保存庫。 請在永久刪除備份專案之後嘗試刪除保存庫，而且保存庫中沒有任何已虛刪除狀態的專案。

4. 若要還原虛刪除的 VM，必須先將它取消刪除。 若要取消刪除，請選擇虛刪除的 VM，然後選取 [取消 **刪除**] 選項。

   ![Azure 入口網站的螢幕擷取畫面，將 VM 取消刪除](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   將會出現一個視窗警告，如果選擇了 [取消刪除]，將會取消刪除 VM 的所有還原點，並可執行還原作業。 VM 將會保留為「停止保護但保留資料」狀態，且備份已暫停，且備份資料永遠保留，且沒有備份原則有效。

   ![Azure 入口網站的螢幕擷取畫面，確認取消刪除 VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   此時，您也可以從所選的還原點選取 [ **還原 vm** ]，以還原 vm。  

   ![Azure 入口網站的 [還原 VM] 選項的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 只有在使用者執行 **繼續備份** 作業之後，垃圾收集行程才會執行和清除過期的復原點。

5. 當取消刪除程式完成之後，狀態會回到 [停止備份並保留資料]，然後您可以選擇 [ **繼續備份**]。 「 **繼續備份** 」作業會將備份專案帶回 [作用中] 狀態，並與定義備份和保留排程的使用者所選取的備份原則相關聯。

   ![Azure 入口網站的 [繼續備份] 選項的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>使用 Azure PowerShell 的 Vm 虛刪除

> [!IMPORTANT]
> 使用 Azure PowerShell 的虛刪除所需的 Az. Az.recoveryservices 版本最少2.2.0。 使用 ```Install-Module -Name Az.RecoveryServices -Force``` 取得最新版本。

如上述 Azure 入口網站所述，使用 Azure PowerShell 也是相同的步驟順序。

### <a name="delete-the-backup-item-using-azure-powershell"></a>使用 Azure PowerShell 刪除備份專案

使用 [>enable-azrecoveryservicesbackupprotection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell Cmdlet 來刪除備份專案。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

備份專案的 ' DeleteState ' 會從 ' NotDeleted ' 變更為 ' ToBeDeleted '。 備份資料將會保留14天。 如果您想要還原刪除操作，則應該執行復原-刪除。

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>使用 Azure PowerShell 復原刪除操作

首先，提取處於虛刪除狀態的相關備份專案 (也就是要刪除) 。

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

然後，使用 [AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell Cmdlet 來執行復原刪除作業。

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

備份專案的 ' DeleteState ' 將會還原為 ' NotDeleted '。 但是仍會停止保護。 [繼續備份](./backup-azure-vms-automation.md#change-policy-for-backup-items) 以重新啟用保護。

## <a name="soft-delete-for-vms-using-rest-api"></a>使用 REST API 的 Vm 虛刪除

- 使用 [這裡](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)所述的 REST API 來刪除備份。
- 如果您想要復原這些刪除作業，請參閱 [此處](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)所述的步驟。

## <a name="how-to-disable-soft-delete"></a>如何停用虛刪除

不建議停用此功能。 如果您打算將受保護的專案移至新的保存庫，而且在刪除和重新保護 (（例如在測試環境中）之前，您應該考慮停用虛刪除的唯一情況 ) 。如需有關如何停用虛刪除的指示，請參閱 [啟用和停](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)用虛刪除。

## <a name="next-steps"></a>後續步驟

- 閱讀有關[虛刪除的常見問題](backup-azure-security-feature-cloud.md#frequently-asked-questions)
- 閱讀 [Azure 備份中的所有安全性功能](security-overview.md)
