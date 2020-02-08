---
title: 使用 PowerShell 備份 Azure 檔案儲存體
description: 在本文中，您將瞭解如何使用 Azure 備份服務和 PowerShell 來備份 Azure 檔案儲存體。
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: a80589fb45937949b3612e12139ab1615bc1620d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086952"
---
# <a name="back-up-azure-files-with-powershell"></a>使用 PowerShell 備份 Azure 檔案儲存體

本文說明如何使用 Azure PowerShell，使用[Azure 備份](backup-overview.md)復原服務保存庫來備份 Azure 檔案儲存體的檔案共用。

本文說明如何：

> [!div class="checklist"]
>
> * 設定 PowerShell，並註冊 Azure 復原服務提供者。
> * 建立復原服務保存庫。
> * 設定 Azure 檔案共用的備份。
> * 執行備份作業。

## <a name="before-you-start"></a>開始之前

* [深入瞭解](backup-azure-recovery-services-vault-overview.md)復原服務保存庫。
* 閱讀[備份 Azure 檔案共用](backup-afs.md)的預覽功能。
* 請參閱復原服務的 PowerShell 物件階層。

## <a name="recovery-services-object-hierarchy"></a>復原服務物件階層

下圖摘要說明物件階層。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

請參閱 Azure 程式庫中的**azurerm.recoveryservices** [Cmdlet 參考](/powershell/module/az.recoveryservices)參考。

## <a name="set-up-and-install"></a>設定並安裝

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

設定 PowerShell，如下所示：

1. [下載最新版的 Az PowerShell](/powershell/azure/install-az-ps)。 最低版本需求為 1.0.0。

> [!WARNING]
> 預覽所需的最小 PS 版本為 ' Az 1.0.0 '。 由於 GA 即將變更，所需的最低 PS 版本將會是 ' Az. Azurerm.recoveryservices 2.6.0 '。 請務必將所有現有的 PS 版本升級至此版本。 否則，現有的腳本會在 GA 之後中斷。 使用下列 PS 命令安裝最低版本

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. 使用下列命令尋找 Azure 備份 PowerShell Cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 備份、Azure Site Recovery 和復原服務保存庫的別名和 Cmdlet。 以下是您可能會看到的範例。 這不是 Cmdlet 的完整清單。

    ![復原服務 Cmdlet 清單](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用 **[Disconnect-azaccount]** 登入您的 Azure 帳戶。
5. 在出現的網頁上，系統會提示您輸入您的帳號憑證。

    * 或者，您可以使用 **-Credential**，將您的帳號憑證當做參數包含在**disconnect-azaccount**指令程式中。
    * 如果您是代表租使用者工作的 CSP 合作夥伴，請使用其 tenantID 或租使用者的主功能變數名稱稱，將客戶指定為租使用者。 例如 **Connect-AzAccount -Tenant** fabrikam.com。

6. 將您想要使用的訂用帳戶與帳戶建立關聯，因為一個帳戶可以有數個訂閱。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 如果您是第一次使用 Azure 備份，請使用 **Register-AzResourceProvider** Cmdlet 利用您的訂用帳戶來註冊 Azure 復原服務提供者。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 確認已成功註冊提供者：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令輸出中，確認 [ **RegistrationState** ] 變更為 [**已註冊**]。 如果不是，請再次執行**register-azresourceprovider** Cmdlet。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **New-AzResourceGroup** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。

依照下列步驟建立復原服務保存庫。

1. 保存庫會放在資源群組中。 如果您沒有現有的資源群組，請建立一個新的[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)。 在此範例中，我們會在美國西部區域中建立新的資源群組。

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. 使用[new-azrecoveryservicesvault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) Cmdlet 來建立保存庫。 為保存庫指定與用於資源群組相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 指定要用於保存庫儲存體的冗余類型。

   * 您可以使用[本機備援儲存體](../storage/common/storage-redundancy-lrs.md)或[異地備援儲存體](../storage/common/storage-redundancy-grs.md)。
   * 下列範例會將**testvault**設定為**異地備援**的[AzRecoveryServicesBackupProperties cmd 設定](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) **-BackupStorageRedundancy**選項。

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要檢視訂用帳戶中的所有保存庫，請使用 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)。

```powershell
Get-AzRecoveryServicesVault
```

輸出如下所示。 請注意，系統會提供相關聯的資源群組和位置。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>設定保存庫內容

將保存庫物件儲存在變數中，並設定保存庫內容。

* 許多 Azure 備份 Cmdlet 都需要復原服務保存庫物件做為輸入，因此將保存庫物件儲存在變數中是很方便的。
* 保存庫內容是保存庫中受保護的資料類型。 將它設定為[set-azrecoveryservicesvaultcoNtext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)。 設定內容之後，它會套用至所有後續的 Cmdlet。

下列範例會設定 **testvault** 的保存庫內容。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保存庫識別碼

我們計畫根據 Azure PowerShell 方針來淘汰保存庫內容設定。 相反地，您可以儲存或提取保存庫識別碼，並將它傳遞至相關的命令。 因此，如果您尚未設定保存庫內容，或想要指定要針對特定保存庫執行的命令，請將保存庫識別碼當做 "-vaultID" 傳遞給所有相關的命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>設定備份原則

備份原則會指定備份的排程，以及應該保留備份復原點的時間長度：

* 備份原則至少與一個保留原則相關聯。 保護原則會定義復原點在被刪除之前要保留多久。
* 使用[AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)來查看預設的備份原則保留期。
* 使用[get-azrecoveryservicesbackupschedulepolicyobject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)來查看預設的備份原則排程。
* 您可以使用[使用 get-azrecoveryservicesbackupprotectionpolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) Cmdlet 來建立新的備份原則。 您可以輸入排程和保留原則物件。

根據預設，開始時間是在排程原則物件中定義。 使用下列範例，將開始時間變更為所需的開始時間。 所需的開始時間也應該是 UTC。 下列範例假設每日備份所需的開始時間為 01:00 AM UTC。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 您只需要提供30分鐘倍數的開始時間。 在上述範例中，它只能是 "01:00:00" 或 "02:30:00"。 開始時間不能為 "01:15:00"

下列範例會將排程原則和保留原則儲存在變數中。 然後，它會使用這些變數做為新原則的參數（**NewAFSPolicy**）。 **NewAFSPolicy** 會每日進行備份，並保留 30 天。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

輸出如下所示。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>啟用備份

定義備份原則之後，您可以使用原則來啟用 Azure 檔案共用的保護。

### <a name="retrieve-a-backup-policy"></a>取出備份原則

您可以使用[使用 get-azrecoveryservicesbackupprotectionpolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)來提取相關的原則物件。 使用此 Cmdlet 來取得特定原則，或查看與工作負載類型相關聯的原則。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>取得工作負載類型的原則

下列範例會抓取工作負載類型**AzureFiles**的原則。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

輸出如下所示。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell 中 **BackupTime** 欄位的時區是國際標準時間 (UTC)。 當備份時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。

### <a name="retrieve-a-specific-policy"></a>取出特定原則

下列原則會擷取名為 **dailyafs** 的備份原則。

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>啟用備份並套用原則

啟用[enable-azrecoveryservicesbackupprotection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)的保護。 當原則與保存庫建立關聯之後，就會根據原則排程觸發備份。

下列範例會在 [儲存體帳戶] **testStorageAcct**中，使用原則**Dailyafs**來啟用 Azure 檔案共用**testAzureFileShare**的保護。

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

此命令會一直等到設定保護作業完成，並提供如下所示的輸出。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>重要通知-AFS 備份的備份專案識別

本節概述從預覽到 GA 的 AFS 備份的備份專案抓取變更。

啟用 AFS 的備份時，使用者會提供客戶易記的檔案共用名稱作為機構名稱，並建立備份專案。 備份專案的 ' name ' 是 Azure 備份服務所建立的唯一識別碼。 識別碼通常牽涉到使用者易記名稱。 但 Azure 服務在內部唯一識別 azure 檔案共用的方式有了變更。 這表示 AFS 備份的備份專案唯一名稱會是 GUID，而且不會與客戶易記名稱有任何關聯。 若要知道每個專案的唯一名稱，只要以 backupManagementType 和 WorkloadType 的相關篩選準則執行 ```Get-AzRecoveryServicesBackupItem``` 命令，即可取得所有相關的專案，然後觀察傳回的 PS 物件/回應中的名稱欄位。 一律建議您列出專案，然後從 [名稱] 欄位取得回應中的唯一名稱。 使用此值來篩選具有 ' Name ' 參數的專案。 否則，請使用 FriendlyName 參數來抓取具有客戶易記名稱/識別碼的專案。

> [!WARNING]
> 請確定 PS 版本已升級為 AFS 備份的 ' Az. Azurerm.recoveryservices 2.6.0 ' 的最低版本。 使用此版本時，[friendlyName] 篩選準則適用于 ```Get-AzRecoveryServicesBackupItem``` 命令。 將 azure 檔案共用名稱傳遞給 friendlyName 參數。 如果您將 azure 檔案共用名稱傳遞給 ' Name ' 參數，此版本會擲回警告，將此易記名稱傳遞給易記名稱參數。 若未安裝此最低版本，可能會導致現有的腳本失敗。 使用下列命令安裝 PS 的最低版本。

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>觸發隨選備份

使用[備份 backup-azrecoveryservicesbackupitem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0)來執行受保護 Azure 檔案共用的隨選備份。

1. 使用[AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)在保存備份資料的保存庫中，從容器中取出儲存體帳戶。
2. 若要開始備份作業，您可以使用[backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)取得 Azure 檔案共用的相關資訊。
3. 使用 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) 來執行隨選備份。

執行隨選備份，如下所示：

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

此命令會傳回具識別碼可供追蹤的作業，如下列範例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Azure 檔案共用快照用於進行備份時，因此通常在命令傳回此輸出時作業便已完成。

### <a name="using-on-demand-backups-to-extend-retention"></a>使用隨選備份來延長保留期

隨選備份可以用來保留您的快照10年。 排程器可用來以選擇的保留來執行隨選 PowerShell 腳本，因此每週、每月或每年都會定期拍攝快照集。 取得一般快照集時，請參閱使用 Azure 備份進行[隨選備份的限制](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share)。

如果您要尋找範例腳本，您可以使用 Azure 自動化 runbook 參閱 GitHub （<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>）上的範例腳本，讓您定期排程備份並保留最多10年。

> [!WARNING]
> 請確定在您的自動化 runbook 中，PS 版本已升級為 Azurerm.recoveryservices 2.6.0 的最小版本，以進行 AFS 備份。 您必須將舊的 ' AzureRM ' 模組取代為 ' Az ' 模組。 使用此版本時，[friendlyName] 篩選準則適用于 ```Get-AzRecoveryServicesBackupItem``` 命令。 將 azure 檔案共用名稱傳遞給 friendlyName 參數。 如果您將 azure 檔案共用名稱傳遞給 ' Name ' 參數，此版本會擲回警告，將此易記名稱傳遞給易記名稱參數。

## <a name="next-steps"></a>後續步驟

[瞭解](backup-afs.md)Azure 入口網站中的備份 Azure 檔案儲存體。
