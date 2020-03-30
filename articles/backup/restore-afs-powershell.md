---
title: 使用 PowerShell 還原 Azure 檔
description: 在本文中，瞭解如何使用 Azure 備份服務和 PowerShell 還原 Azure 檔。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086818"
---
# <a name="restore-azure-files-with-powershell"></a>使用 PowerShell 還原 Azure 檔

本文介紹如何從 Azure[備份](backup-overview.md)服務使用 Azure Powershell 創建的還原點還原整個檔共用或特定檔。

您可以還原共用上的整個檔共用或特定檔。 您可以還原到原始位置或備用位置。

> [!WARNING]
> 確保 PS 版本升級到 AFS 備份的"Az.恢復服務 2.6.0"的最低版本。 有關詳細資訊，請參閱概述此更改要求[的部分](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups)。

## <a name="fetch-recovery-points"></a>擷取復原點

使用[獲取恢復服務備份復原點](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0)列出備份項的所有復原點。

在以下腳本中：

* 變數 **$rp**是過去七天所選備份項的復原點陣列。
* 陣列是以相反時間順序排序，最新復原點位於索引 **0**。
* 使用標準 PowerShell 陣列索引來挑選復原點。
* 在範例中，**$rp[0]** 會選取最新的復原點。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

輸出類似于以下內容。

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

選擇相關復原點後，您將檔共用或檔案還原到原始位置或備用位置。

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>將 Azure 檔共用還原到備用位置

使用[還原-Az 恢復服務備份專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0)還原到選定的復原點。 指定這些參數以標識備用位置：

* **目標存儲帳戶名稱**：還原備份內容的存儲帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **TargetFileShareName**：檔共用在目標存儲帳戶中還原備份的內容。
* **目的檔案夾**：還原資料的檔共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **解決衝突**：如果與還原的資料發生衝突，則說明。 可接受的值為 **Overwrite** 或 **Skip**。

使用參數運行 Cmdlet，如下所示：

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

此命令會傳回具識別碼可供追蹤的作業，如下列範例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>將 Azure 檔案還原到備用位置

使用[還原-Az 恢復服務備份專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0)還原到選定的復原點。 指定這些參數以標識備用位置，並唯一標識要還原的檔。

* **目標存儲帳戶名稱**：還原備份內容的存儲帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **TargetFileShareName**：檔共用在目標存儲帳戶中還原備份的內容。
* **目的檔案夾**：還原資料的檔共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **SourceFilePath**：檔的絕對路徑，將在檔共用中作為字串還原。 此路徑與 **Get-AzStorageFile** PowerShell Cmdlet 中使用的路徑相同。
* **源檔案類型**：是否選擇了目錄或檔。 接受**目錄**或**檔**。
* **解決衝突**：如果與還原的資料發生衝突，則說明。 可接受的值為 **Overwrite** 或 **Skip**。

其他參數（原始檔案路徑和源檔案類型）僅與要還原的各個檔相關。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

此命令返回具有要跟蹤 ID 的作業，如上一節所示。

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>將 Azure 檔共用和檔案還原到原始位置

還原到原始位置時，不需要指定與目標和目標相關的參數。 僅必須提供 **ResolveConflict**。

#### <a name="overwrite-an-azure-file-share"></a>覆寫 Azure 檔案共用

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>覆寫 Azure 檔案

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>後續步驟

[瞭解如何](restore-afs.md)在 Azure 門戶中還原 Azure 檔。
