---
title: Azure VM 備份中的 SQL DB &通過 PowerShell 還原
description: 使用 Azure 備份和 PowerShell 在 Azure VM 中備份和還原 SQL 資料庫。
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131818"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>使用 PowerShell 在 Azure VM 中備份和還原 SQL 資料庫

本文介紹如何使用 Azure PowerShell 使用[Azure 備份](backup-overview.md)恢復服務保存庫在 Azure VM 中備份和恢復 SQL DB。

本文說明如何：

> [!div class="checklist"]
>
> * 設置 PowerShell 並註冊 Azure 恢復服務提供者。
> * 建立復原服務保存庫。
> * 在 Azure VM 中配置 SQL DB 的備份。
> * 運行備份作業。
> * 還原備份的 SQL DB。
> * 監視備份和恢復作業。

## <a name="before-you-start"></a>開始之前

* [瞭解有關](backup-azure-recovery-services-vault-overview.md)恢復服務保存庫的更多詳細資訊。
* 閱讀有關在 Azure [VM 中備份 SQL DB](backup-azure-sql-database.md#before-you-start)的功能。
* 查看恢復服務的 PowerShell 物件層次結構。

### <a name="recovery-services-object-hierarchy"></a>復原服務物件階層

物件層次結構總結如下圖。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

查看 Azure 庫中的**Az.恢復服務** [Cmdlet 引用](/powershell/module/az.recoveryservices)。

### <a name="set-up-and-install"></a>設置和安裝

設置 PowerShell 如下所示：

1. [下載最新版的 Az PowerShell](/powershell/azure/install-az-ps)。 所需的最小版本為 1.5.0。

2. 使用此命令查找 Azure 備份電源 Shell Cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 備份和恢復服務保存庫的別名和 Cmdlet。 下面是您可能會看到的示例。 這不是一個完整的Cmdlet清單。

    ![復原服務 Cmdlet 清單](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用**連接-AzAccount**登錄到 Azure 帳戶。
5. 在顯示的網頁上，系統會提示您輸入帳戶憑據。

    * 或者，您可以將帳戶憑據作為參數包含在帶有 **-憑據**的**Connect-AzAccount** Cmdlet 中。
    * 如果您是為租戶工作的 CSP 合作夥伴，請使用租戶 ID 或租戶主功能變數名稱將客戶指定為租戶。 例如 **Connect-AzAccount -Tenant** fabrikam.com。

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

依照下列步驟建立復原服務保存庫。

復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **New-AzResourceGroup** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。

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

輸出類似于以下內容。 提供了關聯的資源組和位置。

```output
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

我們計畫根據 Azure PowerShell 準則棄用保存庫上下文設置。 相反，您可以存儲或提取保存庫 ID，並將其傳遞給相關命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>設定備份原則

備份策略指定備份計畫以及備份復原點應保留多長時間：

* 備份原則至少與一個保留原則相關聯。 保護原則會定義復原點在被刪除之前要保留多久。
* 使用[獲取恢復服務備份保留原則物件](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)查看預設備份策略保留。
* 使用[獲取-Az 恢復服務備份計畫策略物件](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)查看預設備份策略計畫。
* 您可以使用[New-AzRecovery 服務備份保護原則](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)Cmdlet 創建新的備份策略。 輸入計畫和保留原則物件。

預設情況下，在計畫策略物件中定義開始時間。 使用以下示例將開始時間更改為所需的開始時間。 所需的開始時間也應為 UTC。 下面的示例假定每日備份所需的開始時間為 01：00 UTC AM。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 您只需提供 30 分鐘倍數的開始時間。 在上面的示例中，它只能是"01：00：00"或"02：30：00"。 開始時間不能為"01：15：00"

下列範例會將排程原則和保留原則儲存在變數中。 然後，它使用這些變數作為新策略 **（NewSQLPolicy）** 的參數。 **NewSQLPolicy**每天進行"完整"備份，保留 180 天，每 2 小時進行一次記錄備份

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

輸出類似于以下內容。

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>啟用備份

### <a name="registering-the-sql-vm"></a>註冊 SQL VM

對於 Azure VM 備份和 Azure 檔共用，備份服務可以連接到這些 Azure 資源管理器資源並獲取相關詳細資訊。 由於 SQL 是 Azure VM 中的應用程式，因此備份服務需要許可權才能訪問應用程式並獲取必要的詳細資訊。 為此，您需要使用恢復服務保存庫 *"註冊"* 包含 SQL 應用程式的 Azure VM。 使用保存庫註冊 SQL VM 後，只能保護 SQL DB 到該保存庫。 使用[註冊-Az 恢復服務備份容器](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0)PS Cmdlet 註冊 VM。

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

該命令將返回此資源的"備份容器"，狀態將"註冊"

> [!NOTE]
> 如果未給出力參數，則要求使用者用文本"是否要禁用此容器的保護"進行確認。 請忽略此文本，並說"Y"以確認。 這是一個已知問題，我們正在努力刪除文本和力參數的要求。

### <a name="fetching-sql-dbs"></a>正在提取 SQL DB

註冊完成後，備份服務將能夠列出 VM 中的所有可用 SQL 元件。 要查看尚未備份到此保存庫的所有 SQL 元件，請使用[Get-AzRecoveryServices 備份可保護專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0)PS Cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

輸出將顯示所有使用專案類型和伺服器名稱註冊到此保存庫的 SQL VM 中未受保護的 SQL 元件。 您可以通過傳遞"-容器"參數或使用"名稱"和"伺服器名稱"以及 ItemType 標誌的組合來進一步篩選到特定的 SQL VM，以得出唯一的 SQL 項。

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>設定備份

現在，我們擁有所需的 SQL DB 及其需要備份的策略，我們可以使用[啟用-AzRecoveryServicesBackup 保護](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)Cmdlet 來配置此 SQL DB 的備份。

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

該命令等待配置備份完成並返回以下輸出。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>獲取新的 SQL DB

註冊電腦後，備份服務將獲取當時可用的 DB 的詳細資訊。 如果使用者稍後將 SQL DB/SQL 實例添加到已註冊的電腦，則需要手動觸發備份服務以執行新的"查詢"，以便再次獲取所有未受保護的 DB（包括新添加的 DB）。 使用 SQL VM 上的[初始化-AzRecovery 服務備份專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0)PS Cmdlet 執行新的查詢。 該命令等待操作完成。 稍後使用[獲取-AzRecovery 服務備份可保護專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0)PS Cmdlet 獲取最新未受保護的 SQL 元件清單

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

獲取相關可保護項後，按照[上一節](#configuring-backup)中的指示啟用備份。
如果不想手動檢測新的 DB，他們可以選擇自動保護，[如下所述](#enable-autoprotection)。

## <a name="enable-autoprotection"></a>啟用自動保護

使用者可以配置備份，以便將來添加的所有 DB 都將自動受到特定策略的保護。 要啟用自動保護，請使用[啟用-Az 恢復服務備份自動保護](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0)PS Cmdlet。

由於該指令是備份所有將來的 DB，因此該操作在 SQLA 級別完成。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

給出自動保護意圖後，對機器的查詢以獲取新添加的 DB 將每 8 小時作為計畫背景工作進行。

## <a name="restore-sql-dbs"></a>還原 SQL DB

Azure 備份可以還原在 Azure VM 上運行的 SQL Server 資料庫，如下所示：

* 通過使用事務記錄備份還原到特定日期或時間（第二個）。 Azure 備份會自動確定根據所選時間還原所需的適當完全差異備份和記錄備份鏈。
* 還原特定的完整備份或差異備份以還原到特定復原點。

在還原 SQL DB 之前，請檢查[此處](restore-sql-database-azure-vm.md#prerequisites)提到的先決條件。

首先使用[獲取-Az 恢復服務備份專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0)PS Cmdlet 獲取相關的備份 SQL DB。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>獲取相關還原時間

如上所述，使用者可以將備份的 SQL DB 還原為完整/差異副本**或**到日誌時間點。

#### <a name="fetch-distinct-recovery-points"></a>獲取不同的復原點

使用[Get-Az 恢復服務備份復原點](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0)為備份的 SQL DB 獲取不同的（完整/差異）復原點。

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

輸出類似于以下示例

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

使用"復原點Id"篩選器或陣列篩選器獲取相關復原點。

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>獲取時間點復原點

如果使用者希望將資料庫還原到特定時間點，請使用[獲取-Az 恢復服務備份修復記錄鏈](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0)PS Cmdlet。 Cmdlet 返回日期清單，該日期清單表示該 SQL 備份項的不間斷連續日誌鏈的開始和結束時間。 所需的時間點應在此範圍內。

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

輸出將類似于以下示例。

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

上述輸出意味著使用者可以在顯示的開始時間和結束時間之間恢復到任何時間點。 時間以 UTC 表示。 在 PS 中構造上述範圍內的任何時間點。

> [!NOTE]
> 選擇用於還原的日誌點時，使用者無需指定起點，即從中還原資料庫的完整備份。 Azure 備份服務將負責整個恢復計畫，即選擇要選擇的完全備份、要應用的記錄備份等。

### <a name="determine-recovery-configuration"></a>確定恢復配置

對於 SQL DB 還原，支援以下還原方案。

* 用來自另一個復原點的資料覆蓋備份的 SQL DB - 原始工作負載還原
* 在同一 SQL 實例中將 SQL DB 還原為新資料庫 - 備用工作負載還原
* 在另一個 SQL VM 中的另一個 SQL 實例中將 SQL DB 還原為新資料庫 - 備用工作負載還原
* 將 SQL DB 還原為 .bak 檔 - 還原AsFiles

獲取相關復原點（不同或日誌時間點）後，使用[Get-AzRecoveryServices 備份工作負載恢復 Config](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS Cmdlet 根據所需的恢復計畫獲取恢復設定物件。

#### <a name="original-workload-restore"></a>原始工作負載恢復

要使用復原點的資料覆蓋備份的資料庫，只需指定正確的標誌和相關復原點，如下例所示。

##### <a name="original-restore-with-distinct-recovery-point"></a>具有不同復原點的原始還原

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>使用時間日誌點進行原始還原

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>備用工作負載還原

> [!IMPORTANT]
> 備份的 SQL DB 可以作為新資料庫還原到另一個 SQLInstance，在 Azure VM 中"註冊"到此保存庫。

如上所述，如果目標 SQLA 實例位於另一個 Azure VM 中，請確保將其[註冊為此保存庫](#registering-the-sql-vm)，並且相關的 SQLInstance 顯示為可保護項。

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

然後，只需傳遞相關復原點，使用右標誌定位 SQL 實例，如下所示。

##### <a name="alternate-restore-with-distinct-recovery-point"></a>具有不同復原點的備用還原

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>使用時間日誌點進行備用還原

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>恢復為檔

要將備份資料還原為 .bak 檔而不是資料庫，請選擇"**還原為檔**"選項。 備份的 SQL DB 可以還原到註冊到此保存庫的任何目標 VM。

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>還原為具有不同復原點的檔

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>使用最新的完整日誌點還原為檔

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>使用指定的完整日誌點還原為檔

如果要提供應用於還原的特定完整，請使用以下命令：

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

從[Get-AzRecoveryServices 備份工作負載恢復 Config](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS Cmdlet 獲取的最終復原點設定物件包含所有還原相關資訊，如下所示。

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

您可以編輯還原的資料庫名稱、覆蓋 WLIf 存在、無復原模式和目標實體路徑欄位。 獲取目的檔案路徑的更多詳細資訊，如下所示。

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

將相關的 PS 屬性設置為字串值，如下所示。

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> 確保最終恢復設定物件具有所有必要和正確的值，因為還原操作將基於設定物件。

### <a name="restore-with-relevant-configuration"></a>使用相關配置進行還原

獲取並驗證相關恢復 Config 物件後，使用[還原-Az 恢復服務備份專案](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0)PS Cmdlet 啟動還原過程。

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

還原操作返回要跟蹤的作業。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>管理 SQL 備份

### <a name="on-demand-backup"></a>按需備份

為資料庫啟用備份後，使用者還可以使用[備份-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS Cmdlet 為資料庫觸發按需備份。 以下示例在啟用壓縮的 SQL DB 上觸發完整備份，並且完整備份應保留 60 天。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

按需備份命令返回要跟蹤的作業。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

如果輸出丟失或想要獲取相關的作業 ID，請從 Azure 備份服務[獲取作業清單](#track-azure-backup-jobs)，然後跟蹤它及其詳細資訊。

### <a name="change-policy-for-backup-items"></a>更改備份項的策略

使用者可以修改現有策略或將備份項的策略從策略 1 更改為策略 2。 要切換備份項的策略，請獲取相關策略並備份項，並使用啟用[-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)命令，並將備份項作為參數。

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

該命令等待配置備份完成並返回以下輸出。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>重新註冊 SQL VM

> [!WARNING]
> 在嘗試重新註冊之前，請確保閱讀[本文檔](backup-sql-server-azure-troubleshoot.md#re-registration-failures)以瞭解故障症狀和原因

要觸發 SQL VM 的重新註冊，請獲取相關的備份容器並將其傳遞給寄存器 Cmdlet。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>停止保護

#### <a name="retain-data"></a>保留資料

如果使用者希望停止保護，他們可以使用[禁用-AzRecoveryServices 備份保護](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)PS Cmdlet。 這將停止計畫備份，但備份到現在的資料將永久保留。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>刪除備份資料

為了完全刪除保存庫中存儲的備份資料，只需將"-刪除復原點"標誌/開關添加到["禁用"保護命令](#retain-data)。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>禁用自動保護

如果在 SQLA 實例上配置了自動保護，則使用者可以使用[禁用-AzRecoveryServicesBackup 自動保護](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0)PS Cmdlet 禁用它。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>取消註冊 SQL VM

如果 SQL 伺服器的所有 DB[不再受到保護，並且不存在備份資料](#delete-backup-data)，則使用者可以從該保存庫取消註冊 SQL VM。 只有這樣，使用者才能將 DB 保護到另一個保存庫。 使用[取消註冊-Az 恢復服務備份容器](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0)PS Cmdlet 取消註冊 SQL VM。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>跟蹤 Azure 備份作業

請務必注意，Azure 備份僅跟蹤 SQL 備份中使用者觸發的作業。 計畫備份（包括記錄備份）在門戶/電源shell 中不可見。 但是，如果任何計畫作業失敗，將生成[備份警報](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)並在門戶中顯示。 [使用 Azure 監視器](backup-azure-monitoring-use-azuremonitor.md)跟蹤所有計劃作業和其他相關資訊。

使用者可以使用在非同步作業（如備份）的[輸出](#on-demand-backup)中返回的 JobID 跟蹤按需/使用者觸發的操作。 使用[獲取-Az恢復服務備份作業細節](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail)PS Cmdlet 來跟蹤作業及其詳細資訊。

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

要從 Azure 備份服務獲取按需作業及其狀態的清單，請使用[獲取-AzRecoveryService 備份作業](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0)PS Cmdlet。 下面的示例返回所有正在進行的 SQL 作業。

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

要取消正在進行的作業，請使用[停止-Az 恢復服務備份作業](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0)PS Cmdlet。

## <a name="managing-sql-always-on-availability-groups"></a>始終在可用性組上管理 SQL

對於 SQL 始終打開可用性組，請確保註冊可用性組 （AG）[的所有節點](#registering-the-sql-vm)。 完成所有節點的註冊後，SQL 可用性組物件將邏輯地在可保護項下創建。 SQL AG 下的資料庫將列為"SQL 資料庫"。 這些節點將顯示為獨立實例，它們下的預設 SQL 資料庫也將列為 SQL 資料庫。

例如，假設 SQL AG 具有兩個節點："sql-server-0"和"sql-server-1"和 1 個 SQL AG 資料庫。 註冊這兩個節點後，如果使用者[列出可保護的專案](#fetching-sql-dbs)，它將列出以下元件

* SQL AG 物件 - 可保護的項類型作為 SQL 可用性組
* SQL AG 資料庫 - 可保護的項類型作為 SQL 資料庫
* sql-server-0 - 可保護的項類型作為 SQLInstance
* sql 伺服器-1 - 可保護的項類型作為 SQLInstance
* sql-server-0 下的任何預設 SQL DB（主資料庫、模型、msdb） - 可保護的項類型作為 SQL 資料庫
* sql-server-1 下的任何預設 SQL DB（主資料庫、模型、msdb） - 可保護的項類型作為 SQL 資料庫

sql-server-0，sql-server-1 也將列為"AzureVMApp 容器"，當[列出備份容器](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)時。

只需獲取相關的 SQL 資料庫[即可啟用備份](#configuring-backup)，按需[備份](#on-demand-backup)和恢復[PS Cmdlet](#restore-sql-dbs)是相同的。
