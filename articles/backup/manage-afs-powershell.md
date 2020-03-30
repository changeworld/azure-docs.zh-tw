---
title: 使用 PowerShell 管理 Azure 檔共用備份
description: 瞭解如何使用 PowerShell 管理和監視由 Azure 備份服務備份的 Azure 檔共用。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083174"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>使用 PowerShell 管理 Azure 檔共用備份

本文介紹如何使用 Azure PowerShell 管理和監視由 Azure 備份服務備份的 Azure 檔共用。

> [!WARNING]
> 確保 PS 版本升級到 AFS 備份的"Az.恢復服務 2.6.0"的最低版本。 有關詳細資訊，請參閱概述此更改要求[的部分](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups)。

## <a name="modify-the-protection-policy"></a>修改保護原則

要更改用於備份 Azure 檔共用的策略，請使用[啟用-AzRecoveryServices 備份保護](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)。 指定相關的備份項和新的備份策略。

下列範例會將 **testAzureFS** 保護原則從 **dailyafs** 變更為 **monthlyafs**。

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>追蹤備份和還原作業

按需備份和還原操作返回作業以及 ID，如[運行按需備份](backup-azure-afs-automation.md#trigger-an-on-demand-backup)時所示。 使用[獲取-AzRecovery 服務備份作業詳細資訊](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0)Cmdlet 跟蹤作業進度和詳細資訊。

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>停止保護檔案共用

有兩種方式可停止保護 Azure 檔案共用︰

* 停止所有將來的備份作業並*刪除*所有復原點
* 停止所有將來的備份作業，但*離開*復原點

由於 Azure 備份創建的基礎快照將保留，因此將存儲中存儲復原點可能存在成本。 但是，離開復原點的好處是，如果需要，您可以稍後還原檔共用。 有關離開復原點的成本的資訊，請參閱[定價詳細資訊](https://azure.microsoft.com/pricing/details/storage/files/)。 如果選擇刪除所有復原點，則無法還原檔共用。

## <a name="stop-protection-and-retain-recovery-points"></a>停止保護並保留復原點

要在保留資料時停止保護，請使用[禁用-AzRecovery 服務備份保護](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)Cmdlet。

以下示例停止對*afsfileshare*檔共用的保護，但保留所有復原點：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

輸出中的作業 ID 屬性對應于備份服務為"停止保護"操作創建的作業的作業 ID。 要跟蹤作業的狀態，請使用[獲取-AzRecovery 服務備份作業](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)Cmdlet。

## <a name="stop-protection-without-retaining-recovery-points"></a>停止保護而不保留復原點

要停止保護而不保留復原點，請使用[禁用-AzRecoveryServices 備份保護](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)Cmdlet 並添加 **-刪除復原點**參數。

以下示例停止保護*afsfileshare*檔共用而不保留復原點：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>後續步驟

[瞭解如何](manage-afs-backup.md)在 Azure 門戶中管理 Azure 檔共用備份。
