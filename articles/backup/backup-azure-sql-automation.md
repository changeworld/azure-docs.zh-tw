---
title: Azure VM 備份中的 SQL DB & 透過 PowerShell 還原
description: 使用 Azure 備份和 PowerShell 來備份和還原 Azure Vm 中的 SQL 資料庫。
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: b355aaa465132e86c636c68552f3d650b51b08f1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004981"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>使用 PowerShell 備份及還原 Azure Vm 中的 SQL 資料庫

本文說明如何使用 Azure PowerShell，以使用 [Azure 備份](backup-overview.md) 復原服務保存庫來備份和復原 Azure VM 中的 SQL DB。

本文說明如何：

> [!div class="checklist"]
>
> * 設定 PowerShell 並註冊 Azure 復原服務提供者。
> * 建立復原服務保存庫。
> * 在 Azure VM 中設定 SQL DB 的備份。
> * 執行備份作業。
> * 還原已備份的 SQL DB。
> * 監視備份和還原作業。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

* [深入瞭解](backup-azure-recovery-services-vault-overview.md) 復原服務保存庫。
* 瞭解在 [Azure vm 中備份 SQL db](backup-azure-sql-database.md#before-you-start)的功能功能。
* 檢查復原服務的 PowerShell 物件階層。

### <a name="recovery-services-object-hierarchy"></a>復原服務物件階層

下圖摘要說明物件階層。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

請參閱 Azure 文件庫中的 **Az. az.recoveryservices** [Cmdlet 參考](/powershell/module/az.recoveryservices) 參考。

### <a name="set-up-and-install"></a>設定和安裝

設定 PowerShell，如下所示：

1. [下載最新版的 Az PowerShell](/powershell/azure/install-az-ps)。 所需的最低版本為1.5.0。

2. 使用下列命令尋找 Azure 備份 PowerShell Cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 請參閱 Azure 備份和復原服務保存庫的別名和 Cmdlet。 以下是您可能會看到的範例。 它不是 Cmdlet 的完整清單。

    ![復原服務 Cmdlet 清單](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用連線 **disconnect-azaccount**來登入您的 Azure 帳戶。
5. 在出現的網頁中，系統會提示您輸入您的帳號憑證。

    * 或者，您可以將您的帳號憑證納入 **disconnect-azaccount** 指令程式中的參數，並搭配 **-Credential**。
    * 如果您是為租使用者工作的 CSP 合作夥伴，請使用其 tenantID 或租使用者主功能變數名稱稱將客戶指定為租使用者。 例如 **Connect-AzAccount -Tenant** fabrikam.com。

6. 將您想要使用的訂用帳戶與帳戶建立關聯，因為帳戶可以有數個訂用帳戶。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 如果您是第一次使用 Azure 備份，請使用 **Register-AzResourceProvider** Cmdlet 利用您的訂用帳戶來註冊 Azure 復原服務提供者。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 確認提供者已成功註冊：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令輸出中，確認 [ **>registrationstate** ] 變更為 [ **已註冊**]。 如果沒有，請再次執行 **>register-azresourceprovider** Cmdlet。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

依照下列步驟建立復原服務保存庫。

復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **New-AzResourceGroup** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。

1. 保存庫會放置在資源群組中。 如果您沒有現有的資源群組，請使用 [新的->new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)建立新的資源群組。 在此範例中，我們會在美國西部區域建立新的資源群組。

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. 使用 [>new-azrecoveryservicesvault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) 指令 Cmdlet 來建立保存庫。 為保存庫指定與用於資源群組相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 指定要用於保存庫儲存體的冗余類型。

    * 您可以使用[本機備援儲存體](../storage/common/storage-redundancy.md)或[異地備援儲存體](../storage/common/storage-redundancy.md)。
    * 下列範例會針對**testvault**設定為**異地備援**的[AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd 設定 **-BackupStorageRedundancy**選項。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要檢視訂用帳戶中的所有保存庫，請使用 [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)。

```powershell
Get-AzRecoveryServicesVault
```

輸出如下所示。 系統會提供相關聯的資源群組和位置。

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

將保存庫物件儲存在變數中，並設定保存庫內容。

* 許多 Azure 備份 Cmdlet 都需要復原服務保存庫物件做為輸入，因此將保存庫物件儲存在變數中是很方便的。
* 保存庫內容是保存庫中受保護的資料類型。 使用 [>set-azrecoveryservicesvaultcoNtext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext)設定。 設定內容之後，它會套用至所有後續的 Cmdlet。

下列範例會設定 **testvault** 的保存庫內容。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保存庫識別碼

我們計畫根據 Azure PowerShell 的指導方針來淘汰保存庫內容設定。 相反地，您可以儲存或提取保存庫識別碼，並將它傳遞至相關的命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>設定備份原則

備份原則會指定備份的排程，以及備份復原點的保留時間長度：

* 備份原則至少與一個保留原則相關聯。 保護原則會定義復原點在被刪除之前要保留多久。
* 使用 [AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject)來查看預設備份原則保留期。
* 使用 [>get-azrecoveryservicesbackupschedulepolicyobject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject)來查看預設的備份原則排程。
* 您可以使用 [>get-azrecoveryservicesbackupprotectionpolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) Cmdlet 來建立新的備份原則。 您可以輸入排程和保留原則物件。

根據預設，開始時間是在排程原則物件中定義。 使用下列範例將開始時間變更為所需的開始時間。 所需的開始時間也應為 UTC。 下列範例假設所需的開始時間為每日備份 01:00 AM UTC。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 您只需要在30分鐘的倍數內提供開始時間。 在上述範例中，它只能是 "01:00:00" 或 "02:30:00"。 開始時間不能是 "01:15:00"。

下列範例會將排程原則和保留原則儲存在變數中。 然後，它會使用這些變數作為新原則 (**NewSQLPolicy**) 的參數。 **NewSQLPolicy** 會採用每日「完整」備份，保留180天，並每隔2小時進行一次記錄備份

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

輸出如下所示。

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>啟用備份

### <a name="registering-the-sql-vm"></a>註冊 SQL VM

針對 Azure VM 備份和 Azure 檔案共用，備份服務可以連線到這些 Azure Resource Manager 資源，並提取相關的詳細資料。 由於 SQL 是 Azure VM 中的應用程式，因此備份服務需要存取應用程式的許可權，並提取必要的詳細資料。 若要這樣做，您必須使用復原服務保存庫來「 *註冊* 」包含 SQL 應用程式的 Azure VM。 使用保存庫註冊 SQL VM 之後，您就只能將 SQL Db 保護到該保存庫。 使用 [>get-azrecoveryservicesbackupcontainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) PowerShell Cmdlet 來註冊 VM。

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

此命令會傳回此資源的「備份容器」，且狀態將會是「已註冊」

> [!NOTE]
> 如果未指定 force 參數，系統會要求您以「是否要停用此容器的保護」文字來確認。 請忽略此文字並說出 "Y" 來確認。 這是已知問題，我們正努力移除文字和 force 參數的需求。

### <a name="fetching-sql-dbs"></a>提取 SQL Db

註冊完成之後，備份服務將能夠列出 VM 內所有可用的 SQL 元件。 若要查看尚未備份至此保存庫的所有 SQL 元件，請使用 [AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell Cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

輸出會顯示所有已向此保存庫註冊的 SQL Vm （具有專案類型和伺服器名稱）的所有未受保護的 SQL 元件。 您可以藉由傳遞 '-Container ' 參數，或使用 ' Name ' 和 ' ServerName ' 的組合以及 ItemType 旗標來取得唯一的 SQL 專案，以進一步篩選至特定的 SQL VM。

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>設定備份

既然我們已有必要的 SQL DB 和需要備份的原則，我們可以使用 [>enable-azrecoveryservicesbackupprotection 指令程式](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 來設定此 sql DB 的備份。

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

此命令會等到設定備份完成，並傳回下列輸出。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>提取新的 SQL Db

註冊機器之後，備份服務將會提取可用資料庫的詳細資料。 如果稍後將 SQL Db 或 SQL 實例新增至已註冊的電腦，您需要手動觸發備份服務，以執行全新的「查詢」以取得 **所有** 未受保護的 db (包括重新新增的資料庫) 。 在 SQL VM 上使用 [>backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) PowerShell Cmdlet，以執行新的查詢。 此命令會等到作業完成為止。 稍後使用 [AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell Cmdlet 來取得最新未受保護的 SQL 元件清單。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

一旦取得相關的可保護專案之後，請依照 [上一節](#configuring-backup)中的指示啟用備份。
如果您不想要手動偵測新的 Db，可以選擇 start-autoprotection [，如下所述。](#enable-autoprotection)

## <a name="enable-autoprotection"></a>啟用 start-autoprotection

您可以設定備份，如此一來，未來新增的所有 Db 都會自動以特定原則保護。 若要啟用 start-autoprotection，請使用 [enable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) PowerShell Cmdlet。

因為指令是要備份所有未來的資料庫，所以作業是在 SQLInstance 層級進行。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

一旦指定了 start-autoprotection 意圖，在機器上查詢以提取新增的資料庫，就會在每8小時的排程背景工作中進行。

## <a name="restore-sql-dbs"></a>還原 SQL Db

Azure 備份可以還原在 Azure Vm 上執行的 SQL Server 資料庫，如下所示：

* 使用交易記錄備份還原至特定日期或時間 (到第二個) 。 Azure 備份會根據選取的時間，自動決定還原所需的適當完整差異備份和記錄備份鏈。
* 還原特定的完整或差異備份，以還原至特定復原點。

請先檢查 [此處](restore-sql-database-azure-vm.md#prerequisites) 所述的必要條件，再還原 SQL db。

首先，使用 [>backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) PowerShell Cmdlet 來提取相關的已備份 SQL DB。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>提取相關的還原時間

如上所述，您可以將備份的 SQL 資料庫還原至完整/差異複本 **或** 記錄時間點。

#### <a name="fetch-distinct-recovery-points"></a>提取相異復原點

使用 [AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) 來為備份的 SQL DB 提取相異 (完整/差異) 復原點。

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

輸出類似下列範例

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

使用 ' RecoveryPointId ' 篩選或陣列篩選器來提取相關的復原點。

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>提取時間點復原點

如果您想要將資料庫還原到特定時間點，請使用 [AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) PowerShell Cmdlet。 指令程式會傳回日期清單，代表該 SQL 備份專案之未中斷、連續記錄鏈的開始和結束時間。 所需的時間點應該在此範圍內。

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

輸出將類似下列範例。

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

上述輸出表示您可以還原至顯示的開始時間和結束時間之間的任何時間點。 時間是以 UTC 為限。 在 PowerShell 中的任何時間點，于上方顯示的範圍內建立任何時間點。

> [!NOTE]
> 選取要還原的記錄檔時間點時，您不需要指定起始點，也就是還原資料庫的完整備份。 Azure 備份服務將負責處理整個復原計畫，也就是要選擇的完整備份、要套用的記錄備份等等。

### <a name="determine-recovery-configuration"></a>判斷恢復設定

在 SQL DB 還原的案例中，支援下列還原案例。

* 使用來自另一個復原點的資料來覆寫備份的 SQL DB-OriginalWorkloadRestore
* 將 SQL DB 還原為相同 SQL 實例中的新資料庫->alternateworkloadrestore
* 在另一個 SQL VM 中將 SQL DB 還原為另一個 SQL 實例中的新資料庫->alternateworkloadrestore
* 將 SQL DB 還原為 .bak 檔案-RestoreAsFiles

將相關的復原點提取 (相異或記錄時間點) 之後，請使用 [AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell Cmdlet，根據所需的復原方案提取復原設定物件。

#### <a name="original-workload-restore"></a>原始工作負載還原

若要使用來自復原點的資料來覆寫已備份的資料庫，只要指定正確的旗標和相關的復原點，如下列範例中所示 (s) 。

##### <a name="original-restore-with-distinct-recovery-point"></a>使用相異復原點的原始還原

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>記錄時間點的原始還原

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>替代工作負載還原

> [!IMPORTANT]
> 已備份的 SQL DB 可在 Azure VM 「已註冊」至此保存庫中，以新的 DB 形式還原至另一個 SQLInstance。

如上所述，如果目標 SQLInstance 位於另一個 Azure VM 中，請確定它 [已註冊到此保存庫](#registering-the-sql-vm) ，而相關的 SQLInstance 會顯示為可保護的專案。

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

然後，只要傳遞相關的復原點，並以正確的旗標來設定目標 SQL 實例，如下所示。

##### <a name="alternate-restore-with-distinct-recovery-point"></a>使用相異復原點的替代還原

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>記錄時間點的替代還原

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>還原為檔案

若要將備份資料還原為 .bak 檔案而非資料庫，請選擇 [ **還原為** 檔案] 選項。 已備份的 SQL DB 可以還原至任何已註冊至此保存庫的目標 VM。

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>以相異復原點還原為檔案

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>從最新的完整版本還原為具有記錄時間點的檔案

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>從指定的完整還原為具有記錄時間點的檔案

如果您想要提供應該用於還原的特定完整版，請使用下列命令：

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

從 [AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell Cmdlet 取得的最終復原點設定物件具有還原的相關資訊，如下所示。

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

您可以編輯還原的資料庫名稱、OverwriteWLIfpresent、NoRecoveryMode 和 targetPhysicalPath 欄位。 取得目標檔案路徑的更多詳細資料，如下所示。

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

將相關的 PowerShell 屬性設定為字串值，如下所示。

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
> 請確定最後的復原設定物件具有所有必要和適當的值，因為還原作業將會以 config 物件為基礎。

### <a name="restore-with-relevant-configuration"></a>使用相關設定進行還原

取得並驗證相關的復原設定物件之後，請使用 >backup-azrecoveryservicesbackupitem PowerShell Cmdlet 來啟動還原 [程式](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 。

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

還原作業會傳回要追蹤的作業。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>管理 SQL 備份

### <a name="on-demand-backup"></a>隨選備份

針對資料庫啟用備份之後，您也可以使用 [>backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) PowerShell Cmdlet 來觸發資料庫的隨選備份。 下列範例會在啟用壓縮的 SQL DB 上觸發完整備份，且應保留60天的完整備份。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

隨選備份命令會傳回要追蹤的作業。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

如果輸出遺失或您想要取得相關的工作識別碼，請從 Azure 備份服務 [取得作業清單](#track-azure-backup-jobs) ，然後追蹤其詳細資料。

### <a name="change-policy-for-backup-items"></a>變更備份專案的原則

您可以將備份專案的原則從 *Policy1* 變更為 *Policy2*。 若要切換已備份專案的原則，請提取相關的原則和備份專案，並使用 [AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 命令搭配備份專案作為參數。

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

此命令會等到設定備份完成，並傳回下列輸出。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>編輯現有的備份原則

若要編輯現有的原則，請使用 [>get-azrecoveryservicesbackupprotectionpolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) 命令。

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

經過一段時間之後，請檢查備份作業以追蹤任何失敗。 如果有，您需要修正問題。 然後使用 **FixForInconsistentItems** 參數重新執行 [編輯原則] 命令，以重試在先前操作失敗的所有備份專案上編輯原則。

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>重新註冊 SQL Vm

> [!WARNING]
> 請務必閱讀這 [份檔](backup-sql-server-azure-troubleshoot.md#re-registration-failures) ，以瞭解失敗的徵兆和原因，然後再嘗試重新註冊

若要觸發重新註冊 SQL VM，請提取相關的備份容器，並將其傳遞至 register Cmdlet。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>停止保護

#### <a name="retain-data"></a>保留資料

如果您想要停止保護，您可以使用 [>enable-azrecoveryservicesbackupprotection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell Cmdlet。 這將會停止已排程的備份，但直到現在為止備份的資料會永久保留。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>刪除備份資料

若要完全移除保存庫中儲存的備份資料，只需將 '-RemoveRecoveryPoints ' 旗標/切換至「停用」 [保護命令](#retain-data)。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>停用自動保護

如果在 SQLInstance 上設定 start-autoprotection，您可以使用 [AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) PowerShell Cmdlet 來停用它。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>取消註冊 SQL VM

如果 SQL server 的所有 Db 都不再 [受到保護，而且沒有備份資料存在](#delete-backup-data)，您可以從這個保存庫取消註冊 sql VM。 只有這樣才能保護資料庫到另一個保存庫。 使用 [>get-azrecoveryservicesbackupcontainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) PowerShell Cmdlet 取消註冊 SQL VM。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>追蹤 Azure 備份作業

請務必注意，Azure 備份只會在 SQL 備份中追蹤使用者觸發的作業。 排程備份 (包括記錄備份) 不會顯示在入口網站或 PowerShell 中。 但是，如果有任何排程工作失敗，則會產生 [備份警示](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) ，並顯示在入口網站中。 [使用 Azure 監視器](backup-azure-monitoring-use-azuremonitor.md) 來追蹤所有排程工作和其他相關資訊。

使用者可以追蹤隨選/使用者觸發的作業，以及在非同步作業 [輸出](#on-demand-backup) （例如備份）中傳回的 JobID。 使用 [AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) PowerShell Cmdlet 來追蹤工作及其詳細資料。

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

若要從 Azure 備份服務取得隨選作業及其狀態的清單，請使用 [AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) PowerShell Cmdlet。 下列範例會傳回所有進行中的 SQL 作業。

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

若要取消進行中的工作，請使用 [AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) PowerShell Cmdlet。

## <a name="managing-sql-always-on-availability-groups"></a>管理 SQL Always On 可用性群組

針對 SQL Always On 可用性群組，請務必註冊可用性群組 (AG) 的 [所有節點](#registering-the-sql-vm) 。 一旦完成所有節點的註冊，就會在可保護的專案下以邏輯方式建立 SQL 可用性群組物件。 SQL AG 底下的資料庫將會列為 ' SQLDatabase '。 節點會顯示為獨立的實例，而這些節點下的預設 SQL 資料庫也會列為 SQL 資料庫。

例如，假設 SQL AG 有兩個節點： *sql-server-0* 和 *sql-server-1* 和 1 sql AG DB。 這兩個節點都註冊之後，如果您列出可保護 [的專案](#fetching-sql-dbs)，它會列出下列元件

* SQL AG 物件可保護的專案類型為 >new-sqlavailabilitygroup
* SQL AG 資料庫-可保護的專案類型為 SQLDatabase
* sql-伺服器-0-可保護的專案類型為 SQLInstance
* sql-server-1-可保護的專案類型為 SQLInstance
* 任何預設 SQL Db (master、model、msdb) 在 SQL-server-0-可保護的專案類型下為 SQLDatabase
* 任何預設 SQL Db (master、model、msdb) 在 SQL-server-1-可保護的專案類型下為 SQLDatabase

當 [列出備份容器](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)時，sql-伺服器-0、sql--1 也會列為 "AzureVMAppContainer"。

只需要提取相關的資料庫以 [啟用備份](#configuring-backup) ， [隨選備份](#on-demand-backup) 和 [還原 PowerShell Cmdlet](#restore-sql-dbs) 會相同。
