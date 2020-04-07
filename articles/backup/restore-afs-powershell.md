---
title: 使用 PowerShell 還原 Azure 檔案
description: 在本文中,瞭解如何使用 Azure 備份服務和 PowerShell 還原 Azure 檔。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 12bff49bc249b23542534d218b13b517411f461b
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756203"
---
# <a name="restore-azure-files-with-powershell"></a>使用 PowerShell 還原 Azure 檔案

本文介紹如何從 Azure[備份](backup-overview.md)服務使用 Azure PowerShell 創建的還原點還原整個檔共用或特定檔。

您可以還原共用上的整個檔共享或特定檔。 您可以還原到原始位置或備用位置。

> [!WARNING]
> 確保 PS 版本升級到 AFS 備份的「Az.恢復服務 2.6.0」的最低版本。 有關詳細資訊,請參閱概述此更改要求的[部分](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups)。

>[!NOTE]
>Azure 備份現在支援使用 PowerShell 將多個檔案或資料夾還原到原始或備用位置。 請參閱本文檔的[此部分](#restore-multiple-files-or-folders-to-original-or-alternate-location)以瞭解如何。

## <a name="fetch-recovery-points"></a>擷取復原點

使用[獲取恢復服務備份恢復點](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0)列出備份項的所有恢復點。

在以下文稿中:

* 變數 **$rp**是過去七天所選備份項的恢復點陣組。
* 陣列是以相反時間順序排序，最新復原點位於索引 **0**。
* 使用標準 PowerShell 陣列索引來挑選復原點。
* 在範例中，**$rp[0]** 會選取最新的復原點。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

輸出類似於以下內容。

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

選擇相關復原點後,您將檔案共享或檔還原到原始位置或備用位置。

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>將 Azure 檔案分享回復到備用位置

使用[還原-Az 恢復服務備份專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0)還原到選定的恢復點。 指定這些參數以識別的位置:

* **目標存儲帳戶名稱**:還原備份內容的存儲帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **TargetFileShareName**:檔案共用在目標存儲帳戶中還原備份的內容。
* **目標資料夾**:還原資料的檔案共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **解決衝突**:如果與還原的數據發生衝突,則說明。 可接受的值為 **Overwrite** 或 **Skip**。

使用參數執行 cmdlet,如下所示:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

此命令會傳回具識別碼可供追蹤的作業，如下列範例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>將 Azure 檔回復到備用位置

使用[還原-Az 恢復服務備份專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0)還原到選定的恢復點。 指定這些參數以標識備用位置,並唯一標識要還原的檔。

* **目標存儲帳戶名稱**:還原備份內容的存儲帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **TargetFileShareName**:檔案共用在目標存儲帳戶中還原備份的內容。
* **目標資料夾**:還原資料的檔案共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **SourceFilePath**:文件的絕對路徑,將在檔共用中作為字串還原。 此路徑與 **Get-AzStorageFile** PowerShell Cmdlet 中使用的路徑相同。
* **源文件類型**:是否選擇了目錄或檔。 接受**目錄**或**檔案**。
* **解決衝突**:如果與還原的數據發生衝突,則說明。 可接受的值為 **Overwrite** 或 **Skip**。

其他參數(源檔路徑和源文件類型)僅與要還原的各個文件相關。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

此命令返回具有要跟蹤 ID 的作業,如上一節所示。

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>將 Azure 檔案分享與檔案回復到原始位置

還原到原始位置時,不需要指定與目標和目標相關的參數。 僅必須提供 **ResolveConflict**。

### <a name="overwrite-an-azure-file-share"></a>覆寫 Azure 檔案共用

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>覆寫 Azure 檔案

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>將多個檔案或資料夾回復到原始或備用位置

通過傳遞要還原的所有檔的路徑作為**多源檔路徑**參數的值,使用[還原-AzRecovery ServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0)命令。

### <a name="restore-multiple-files"></a>回復多個檔案

在下面的腳本中,我們嘗試還原*FileSharePage.png*和*MyTestFile.txt*檔。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>回復多個目錄

在下面的腳本中,我們嘗試還原*zrs1_restore*和*還原*目錄。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

輸出將類似於：

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

如果要將多個檔或資料夾還原到備用位置,請使用上述文本,指定目標位置相關的參數值,如上文在[將 Azure 檔還原到備用位置](#restore-an-azure-file-to-an-alternate-location)中所述。

## <a name="next-steps"></a>後續步驟

[瞭解如何](restore-afs.md)在 Azure 門戶中還原 Azure 檔。
