---
title: 使用 PowerShell 還原 Azure 檔案儲存體
description: 在本文中，您將瞭解如何使用 Azure 備份服務和 PowerShell 來還原 Azure 檔案儲存體。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 23de6e1e352a1b72d1435fc65d6a59ab37d838b0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538542"
---
# <a name="restore-azure-files-with-powershell"></a>使用 PowerShell 還原 Azure 檔案儲存體

本文說明如何使用 Azure PowerShell，從[Azure 備份](backup-overview.md)服務所建立的還原點還原整個檔案共用或特定檔案。

您可以還原整個檔案共用或共用上的特定檔案。 您可以還原至原始位置或替代位置。

> [!WARNING]
> 請確定 PS 版本已升級為 AFS 備份的 ' Az. Azurerm.recoveryservices 2.6.0 ' 的最低版本。 如需詳細資訊，請參閱概述這項變更需求[一節](backup-azure-afs-automation.md#important-notice-backup-item-identification)。

>[!NOTE]
>Azure 備份現在支援使用 PowerShell，將多個檔案或資料夾還原到原始或替代位置。 請參閱檔的[這一節](#restore-multiple-files-or-folders-to-original-or-alternate-location)，以瞭解作法。

## <a name="fetch-recovery-points"></a>擷取復原點

使用[AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint)來列出已備份專案的所有復原點。

在下列腳本中：

* 變數 **$rp**是過去七天內所選備份專案的復原點陣列。
* 陣列是以相反時間順序排序，最新復原點位於索引 **0**。
* 使用標準 PowerShell 陣列索引來挑選復原點。
* 在範例中，**$rp[0]** 會選取最新的復原點。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

輸出如下所示。

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

選取相關的復原點之後，您可以將檔案共用或檔案還原至原始位置或替代位置。

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>將 Azure 檔案共用還原至替代位置

使用[backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem)還原至選取的復原點。 指定這些參數以識別替代位置：

* **TargetStorageAccountName**：要還原備份內容的目標儲存體帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **TargetFileShareName**：要還原備份內容的目標儲存體帳戶內的檔案共用。
* **TargetFolder**：要還原資料的檔案共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **ResolveConflict**：如果與還原的資料發生衝突，則為指令。 可接受的值為 **Overwrite** 或 **Skip**。

使用參數執行 Cmdlet，如下所示：

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

此命令會傳回具識別碼可供追蹤的作業，如下列範例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>將 Azure 檔案還原至替代位置

使用[backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem)還原至選取的復原點。 指定這些參數以識別替代位置，並唯一識別您想要還原的檔案。

* **TargetStorageAccountName**：要還原備份內容的目標儲存體帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **TargetFileShareName**：要還原備份內容的目標儲存體帳戶內的檔案共用。
* **TargetFolder**：要還原資料的檔案共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **SourceFilePath**：檔案共用中要還原之檔案的絕對路徑，以字串表示。 此路徑與 **Get-AzStorageFile** PowerShell Cmdlet 中使用的路徑相同。
* **SourceFileType**：是否已選取目錄或檔案。 接受**目錄**或**檔案**。
* **ResolveConflict**：如果與還原的資料發生衝突，則為指令。 可接受的值為 **Overwrite** 或 **Skip**。

其他參數（SourceFilePath 和 SourceFileType）僅與您要還原的個別檔案相關聯。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

此命令會傳回具有要追蹤之識別碼的作業，如上一節所示。

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>將 Azure 檔案共用和檔案還原至原始位置

當您還原到原始位置時，您不需要指定與目的地和目標相關的參數。 僅必須提供 **ResolveConflict**。

### <a name="overwrite-an-azure-file-share"></a>覆寫 Azure 檔案共用

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>覆寫 Azure 檔案

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>將多個檔案或資料夾還原到原始或替代位置

藉由將您想要還原之所有檔案或資料夾的路徑，傳遞為**MultipleSourceFilePath**參數的值，以使用[Restore-backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem)命令。

### <a name="restore-multiple-files"></a>還原多個檔案

在下列腳本中，我們正嘗試還原*FileSharePage.png*和*MyTestFile.txt*檔案。

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>還原多個目錄

在下列腳本中，我們正嘗試還原*zrs1_restore*和*還原*目錄。

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

如果您想要將多個檔案或資料夾還原至替代位置，請使用上述腳本，方法是指定目標位置相關的參數值，如上面的將[Azure 檔案還原至替代位置](#restore-an-azure-file-to-an-alternate-location)中所述。

## <a name="next-steps"></a>後續步驟

[瞭解如何](restore-afs.md)還原 Azure 入口網站中的 Azure 檔案儲存體。
