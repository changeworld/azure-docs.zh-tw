---
title: 使用 PowerShell 備份 Azure 檔
description: 在本文中，瞭解如何使用 Azure 備份服務和 PowerShell 備份 Azure 檔。
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273536"
---
# <a name="back-up-azure-files-with-powershell"></a>使用 PowerShell 備份 Azure 檔

本文介紹如何使用 Azure PowerShell 使用[Azure 備份](backup-overview.md)恢復服務保存庫備份 Azure 檔共用。

本文說明如何：

> [!div class="checklist"]
>
> * 設置 PowerShell 並註冊 Azure 恢復服務提供者。
> * 建立復原服務保存庫。
> * 為 Azure 檔共用配置備份。
> * 運行備份作業。

## <a name="before-you-start"></a>開始之前

* [瞭解有關](backup-azure-recovery-services-vault-overview.md)恢復服務保存庫的更多詳細資訊。
* 閱讀有關備份 Azure[檔共用的](backup-afs.md)預覽功能。
* 查看恢復服務的 PowerShell 物件層次結構。

## <a name="recovery-services-object-hierarchy"></a>復原服務物件階層

物件層次結構總結如下圖。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

查看 Azure 庫中的**Az.恢復服務** [Cmdlet 引用](/powershell/module/az.recoveryservices)。

## <a name="set-up-and-install"></a>設置和安裝

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

設置 PowerShell 如下所示：

1. [下載最新版的 Az PowerShell](/powershell/azure/install-az-ps)。 最低版本需求為 1.0.0。

> [!WARNING]
> 預覽所需的最低 PS 版本為"Az 1.0.0"。 由於 GA 即將更改，所需的最低 PS 版本將為"Az.RecoveryServices 2.6.0"。 將所有現有 PS 版本升級到此版本非常重要。 否則，現有腳本將在 GA 後中斷。 使用以下 PS 命令安裝最小版本

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. 使用此命令查找 Azure 備份電源 Shell Cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 備份、Azure 網站恢復和恢復服務保存庫的別名和 Cmdlet。 下面是您可能會看到的示例。 這不是一個完整的Cmdlet清單。

    ![復原服務 Cmdlet 清單](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用**連接-AzAccount**登錄到 Azure 帳戶。
5. 在顯示的網頁上，系統會提示您輸入帳戶憑據。

    * 或者，您可以將帳戶憑據作為參數包含在帶有 **-憑據**的**Connect-AzAccount** Cmdlet 中。
    * 如果您是代表租戶工作的 CSP 合作夥伴，請使用租戶 ID 或租戶主功能變數名稱將客戶指定為租戶。 例如 **Connect-AzAccount -Tenant** fabrikam.com。

6. 將要使用的訂閱與帳戶關聯，因為帳戶可以有多個訂閱。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 如果您是第一次使用 Azure 備份，請使用 **Register-AzResourceProvider** Cmdlet 利用您的訂用帳戶來註冊 Azure 復原服務提供者。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 檢查器提供者是否成功註冊：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令輸出中，驗證**註冊狀態**是否更改為**已註冊**。 如果沒有，請再次運行**註冊-Az資來源提供者**Cmdlet。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **New-AzResourceGroup** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。

依照下列步驟建立復原服務保存庫。

1. 保存庫放置在資源組中。 如果沒有現有資源組，請使用[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)創建新資源組。 在此示例中，我們在美國西部區域創建一個新資源組。

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. 使用[新恢復服務Vault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) Cmdlet 創建保存庫。 為保存庫指定與用於資源群組相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 指定用於保存庫存儲的冗余類型。

   * 您可以使用[本機備援儲存體](../storage/common/storage-redundancy-lrs.md)或[異地備援儲存體](../storage/common/storage-redundancy-grs.md)。
   * 下面的示例為[設置為](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) **GeoRedundant**的**測試庫**設置 **-備份存儲冗余**選項。

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要檢視訂用帳戶中的所有保存庫，請使用 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)。

```powershell
Get-AzRecoveryServicesVault
```

輸出類似于以下內容。 請注意，提供了關聯的資源組和位置。

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

將保存庫物件存儲在變數中，並設置保存庫上下文。

* 許多 Azure 備份 Cmdlet 需要恢復服務保存庫物件作為輸入，因此將保存庫物件存儲在變數中很方便。
* 保存庫內容是保存庫中受保護的資料類型。 使用[集-Az 恢復服務庫上下文](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)設置它。 設置上下文後，它將應用於所有後續 Cmdlet。

下列範例會設定 **testvault** 的保存庫內容。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>獲取保存庫 ID

我們計畫根據 Azure PowerShell 準則棄用保存庫上下文設置。 相反，您可以存儲或提取保存庫 ID，並將其傳遞給相關命令。 因此，如果您尚未設置保存庫上下文或想要指定為特定保存庫運行的命令，請將保存庫 ID 作為"-vaultID"傳遞給所有相關命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>設定備份原則

備份策略指定備份計畫以及備份復原點應保留多長時間：

* 備份原則至少與一個保留原則相關聯。 保護原則會定義復原點在被刪除之前要保留多久。
* 使用[獲取恢復服務備份保留原則物件](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)查看預設備份策略保留。
* 使用[獲取-Az 恢復服務備份計畫策略物件](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)查看預設備份策略計畫。
* 您可以使用[New-AzRecovery 服務備份保護原則](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)Cmdlet 創建新的備份策略。 輸入計畫和保留原則物件。

預設情況下，在計畫策略物件中定義開始時間。 使用以下示例將開始時間更改為所需的開始時間。 所需的開始時間也應為 UTC。 下面的示例假定每日備份所需的開始時間為 01：00 UTC AM。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 您只需提供 30 分鐘倍數的開始時間。 在上面的示例中，它只能是"01：00：00"或"02：30：00"。 開始時間不能為"01：15：00"

下列範例會將排程原則和保留原則儲存在變數中。 然後，它使用這些變數作為新策略 **（NewAFSPolicy） 的**參數。 **NewAFSPolicy** 會每日進行備份，並保留 30 天。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

輸出類似于以下內容。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>啟用備份

定義備份策略後，可以使用 該策略啟用 Azure 檔共用的保護。

### <a name="retrieve-a-backup-policy"></a>檢索備份策略

您可以使用[獲取-AzRecovery 服務備份保護原則](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)獲取相關策略物件。 使用此 Cmdlet 獲取特定策略，或查看與工作負載類型關聯的策略。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>檢索工作負載類型的策略

下面的示例檢索工作負荷類型**AzureFiles**的策略。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

輸出類似于以下內容。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell 中 **BackupTime** 欄位的時區是國際標準時間 (UTC)。 當備份時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。

### <a name="retrieve-a-specific-policy"></a>檢索特定策略

下列原則會擷取名為 **dailyafs** 的備份原則。

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>啟用備份並應用策略

啟用[-AzRecovery 服務備份保護啟用保護](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)。 策略與保存庫關聯後，將根據策略計畫觸發備份。

下面的示例支援保護存儲帳戶**測試 StorageAcct**中的 Azure 檔共用**測試 AzureFileShare，** 以及策略**每日afs。**

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

此命令會一直等到設定保護作業完成，並提供如下所示的輸出。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>重要通知 - AFS 備份的備份項標識

本節概述了為準備 GA 而 AFS 備份的一個重要更改。

在為 AFS 啟用備份時，使用者將客戶友好的檔共用名稱稱作為機構名稱和備份項的創建。 備份項的"名稱"是 Azure 備份服務創建的唯一識別碼。 通常識別碼涉及方便使用名稱。 但是，要處理虛刪除的重要方案，其中檔共用可以被刪除，另一個檔共用可以使用相同的名稱創建，Azure 檔共用的唯一標識現在將是一個 ID 而不是客戶友好的名稱。 為了知道每個專案的唯一標識/名稱，只需使用備份管理類型和工作負載```Get-AzRecoveryServicesBackupItem```類型的相關篩選器運行該命令，獲取所有相關項，然後觀察返回的 PS 物件/回應中的名稱欄位。 始終建議列出項，然後從"名稱"欄位中檢索它們的唯一名稱以進行回應。 使用此值使用"名稱"參數篩選項。 否則，使用"易記名稱"參數檢索具有客戶易記名稱/識別碼的專案。

> [!WARNING]
> 確保 PS 版本升級到 AFS 備份的"Az.恢復服務 2.6.0"的最低版本。 使用此版本，"易記名稱"篩選器可用於```Get-AzRecoveryServicesBackupItem```命令。 將 Azure 檔共用名稱稱傳遞給易記名稱參數。 如果將 Azure 檔共用名稱稱傳遞給"名稱"參數，此版本將引發一條警告，以將此易記名稱傳遞給易記名稱參數。 不安裝此最小版本可能會導致現有腳本失敗。 使用以下命令安裝 PS 的最小版本。

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>觸發隨選備份

使用[備份-AzRecovery 服務備份專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0)為受保護的 Azure 檔共用運行按需備份。

1. 使用[Get-AzRecoveryServices 備份容器](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)從保存庫中保存備份資料的容器中檢索存儲帳戶。
2. 要啟動備份作業，您需要獲取有關 Azure 檔共用的資訊，包括[獲取服務備份項](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)。
3. 使用 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) 來執行隨選備份。

運行按需備份，如下所示：

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

### <a name="using-on-demand-backups-to-extend-retention"></a>使用按需備份擴展保留

按需備份可用於將快照保留 10 年。 計畫程式可用於運行具有所選保留的按需 PowerShell 腳本，從而每週、每月或每年定期拍攝快照。 拍攝常規快照時，請參閱使用 Azure[備份按需備份的限制](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share)。

如果要查找示例腳本，可以使用 Azure 自動化 Runbook 參考 GitHub<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>（） 上的示例腳本，該運行簿使您能夠定期計畫備份，並保留備份甚至長達 10 年。

> [!WARNING]
> 確保 PS 版本升級到自動化 Runbook 中 AFS 備份的"Az.RecoveryServices 2.6.0"的最低版本。 您必須將舊的"AzureRM"模組替換為"Az"模組。 使用此版本，"易記名稱"篩選器可用於```Get-AzRecoveryServicesBackupItem```命令。 將 azure 檔共用名稱稱傳遞給易記名稱參數。 如果將 azure 檔共用名稱稱傳遞給"名稱"參數，此版本將引發一條警告，以將此易記名稱傳遞給易記名稱參數。

## <a name="next-steps"></a>後續步驟

[瞭解如何](backup-afs.md)在 Azure 門戶中備份 Azure 檔。
