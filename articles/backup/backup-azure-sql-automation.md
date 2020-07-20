---
title: Azure VM 備份中的 SQL DB & 透過 PowerShell 還原
description: 使用 Azure 備份和 PowerShell 來備份和還原 Azure Vm 中的 SQL 資料庫。
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 862455175497fe5496c7eea459c32772074671ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255138"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>使用 PowerShell 備份和還原 Azure Vm 中的 SQL 資料庫

本文說明如何使用 Azure PowerShell，在 Azure VM 中使用[Azure 備份](backup-overview.md)復原服務保存庫來備份和復原 SQL DB。

本文說明如何：

> [!div class="checklist"]
>
> * 設定 PowerShell，並註冊 Azure 復原服務提供者。
> * 建立復原服務保存庫。
> * 在 Azure VM 內設定 SQL DB 的備份。
> * 執行備份作業。
> * 還原已備份的 SQL DB。
> * 監視備份和還原作業。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

* [深入瞭解](backup-azure-recovery-services-vault-overview.md)復原服務保存庫。
* 請參閱在[Azure vm 中備份 SQL db](backup-azure-sql-database.md#before-you-start)的功能功能。
* 請參閱復原服務的 PowerShell 物件階層。

### <a name="recovery-services-object-hierarchy"></a>復原服務物件階層

下圖摘要說明物件階層。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

請參閱 Azure 程式庫中的**azurerm.recoveryservices** [Cmdlet 參考](/powershell/module/az.recoveryservices)參考。

### <a name="set-up-and-install"></a>設定並安裝

設定 PowerShell，如下所示：

1. [下載最新版的 Az PowerShell](/powershell/azure/install-az-ps)。 所需的最低版本為1.5.0。

2. 使用下列命令尋找 Azure 備份 PowerShell Cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 請參閱 Azure 備份和復原服務保存庫的別名和 Cmdlet。 以下是您可能會看到的範例。 這不是 Cmdlet 的完整清單。

    ![復原服務 Cmdlet 清單](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用 **[Disconnect-azaccount]** 登入您的 Azure 帳戶。
5. 在出現的網頁上，系統會提示您輸入您的帳號憑證。

    * 或者，您可以使用 **-Credential**，將您的帳號憑證當做參數包含在**disconnect-azaccount**指令程式中。
    * 如果您是為租使用者工作的 CSP 合作夥伴，請使用其 tenantID 或租使用者的主功能變數名稱稱，將客戶指定為租使用者。 例如 **Connect-AzAccount -Tenant** fabrikam.com。

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

依照下列步驟建立復原服務保存庫。

復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **New-AzResourceGroup** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。

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
    * 下列範例會將**testvault**設定為**異地備援**的[AzRecoveryServicesBackupProperty cmd 設定](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) **-BackupStorageRedundancy**選項。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要檢視訂用帳戶中的所有保存庫，請使用 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)。

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
* 保存庫內容是保存庫中受保護的資料類型。 將它設定為[set-azrecoveryservicesvaultcoNtext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)。 設定內容之後，它會套用至所有後續的 Cmdlet。

下列範例會設定 **testvault** 的保存庫內容。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保存庫識別碼

我們計畫根據 Azure PowerShell 方針來淘汰保存庫內容設定。 相反地，您可以儲存或提取保存庫識別碼，並將它傳遞至相關的命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>設定備份原則

備份原則會指定備份的排程，以及應該保留備份復原點的時間長度：

* 備份原則至少與一個保留原則相關聯。 保護原則會定義復原點在被刪除之前要保留多久。
* 使用[AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)來查看預設的備份原則保留期。
* 使用[get-azrecoveryservicesbackupschedulepolicyobject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)來查看預設的備份原則排程。
* 您可以使用[使用 get-azrecoveryservicesbackupprotectionpolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) Cmdlet 來建立新的備份原則。 您可以輸入排程和保留原則物件。

根據預設，開始時間是在排程原則物件中定義。 使用下列範例，將開始時間變更為所需的開始時間。 所需的開始時間也應該是 UTC。 下列範例假設每日備份所需的開始時間為 01:00 AM UTC。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 您只需要提供30分鐘倍數的開始時間。 在上述範例中，它只能是 "01:00:00" 或 "02:30:00"。 開始時間不能為 "01:15:00"

下列範例會將排程原則和保留原則儲存在變數中。 然後，它會使用這些變數做為新原則的參數（**NewSQLPolicy**）。 **NewSQLPolicy**會採用每日的「完整」備份，保留180天，並每隔2小時進行一次記錄備份

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

針對 Azure VM 備份和 Azure 檔案共用，備份服務可以連接到這些 Azure Resource Manager 資源，並提取相關的詳細資料。 由於 SQL 是 Azure VM 內的應用程式，因此備份服務需要存取應用程式的許可權，並提取必要的詳細資料。 若要這麼做，您必須使用復原服務保存庫來「*註冊*」包含 SQL 應用程式的 Azure VM。 一旦向保存庫註冊 SQL VM，您就只能保護該保存庫的 SQL Db。 使用[AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS Cmdlet 來註冊 VM。

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

此命令將會傳回此資源的「備份容器」，而狀態將會是「已註冊」

> [!NOTE]
> 如果未指定 force 參數，系統會要求使用者以「您要停用此容器的保護」文字來確認。 請忽略此文字，並說「Y」以確認。 這是已知的問題，我們正努力移除強制參數的文字和需求。

### <a name="fetching-sql-dbs"></a>正在提取 SQL Db

註冊完成後，備份服務就能夠列出 VM 內所有可用的 SQL 元件。 若要查看所有尚未備份至此保存庫的 SQL 元件，請使用[AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS Cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

輸出會顯示所有向此保存庫註冊的 SQL Vm 中，專案類型和 ServerName 皆未受保護的 SQL 元件。 您可以藉由傳遞 '-Container ' 參數，或使用 ' Name ' 和 ' ServerName ' 搭配 ItemType 旗標來到達唯一的 SQL 專案，進一步篩選特定的 SQL VM。

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>設定備份

既然我們已經有必要的 SQL DB 和需要備份的原則，我們可以使用[enable-azrecoveryservicesbackupprotection 指令程式](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)來設定此 SQL DB 的備份。

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

此命令會等到設定備份完成，並傳回下列輸出。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>正在提取新的 SQL Db

註冊電腦之後，備份服務將會提取可用 Db 的詳細資料。 如果使用者稍後將 SQL Db/SQL 實例新增至已註冊的電腦，則需要手動觸發備份服務，以執行全新的「查詢」，以再次取得所有未受保護的 Db （包括新增的資料庫）。 在 SQL VM 上使用[Backup-azrecoveryservicesbackupitem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS Cmdlet 來執行全新的查詢。 此命令會等到作業完成。 稍後使用[AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS Cmdlet 來取得最新未受保護的 SQL 元件清單

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

提取相關的可保護專案之後，請依照[上一節](#configuring-backup)中的指示啟用備份。
如果您不想要手動偵測新的 Db，他們可以選擇 start-autoprotection [，如下所述。](#enable-autoprotection)

## <a name="enable-autoprotection"></a>啟用 start-autoprotection

使用者可以設定備份，如此一來，未來新增的所有資料庫都會以特定原則自動受到保護。 若要啟用 start-autoprotection，請使用[AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS Cmdlet。

由於此指示是用來備份所有未來的資料庫，因此作業是在 SQLInstance 層級完成。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

一旦指定了 start-autoprotection 意圖，在機器上進行查詢以提取新增的資料庫，每8小時就會成為排定的背景工作。

## <a name="restore-sql-dbs"></a>還原 SQL Db

Azure 備份可以還原在 Azure Vm 上執行 SQL Server 資料庫，如下所示：

* 使用交易記錄備份還原到特定的日期或時間（到第二個）。 Azure 備份會自動決定適當的完整差異備份，以及根據所選時間還原所需的記錄備份鏈。
* 還原特定的完整或差異備份，以還原到特定的復原點。

在還原 SQL Db 之前，請先檢查[這裡](restore-sql-database-azure-vm.md#prerequisites)所述的必要條件。

首先，使用[Backup-azrecoveryservicesbackupitem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS Cmdlet 來提取相關的備份 SQL DB。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>提取相關的還原時間

如上所述，使用者可以將已備份的 SQL DB 還原至完整/差異複本，**或**還原至記錄時間點。

#### <a name="fetch-distinct-recovery-points"></a>提取不同的復原點

使用[AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0)來提取已備份 SQL DB 的相異（完整/差異）復原點。

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

輸出類似于下列範例

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

如果使用者想要將資料庫還原到特定的時間點，請使用[AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS Cmdlet。 此 Cmdlet 會傳回日期清單，代表該 SQL 備份專案之未中斷、連續記錄鏈的開始和結束時間。 所需的時間點應在此範圍內。

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

輸出會類似下列範例。

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

上述輸出表示使用者可以還原到顯示的開始時間和結束時間之間的任何時間點。 時間為 UTC。 在上方所示的範圍內，依據 PS 來建立任何時間點。

> [!NOTE]
> 選取要還原的記錄時間點時，使用者不需要指定要還原資料庫的起始點，亦即完整備份。 Azure 備份服務會負責整個復原計畫，也就是要選擇的完整備份、要套用的記錄備份等。

### <a name="determine-recovery-configuration"></a>判斷復原設定

若為 SQL DB 還原，則支援下列還原案例。

* 以另一個復原點的資料覆寫備份的 SQL DB-OriginalWorkloadRestore
* 將 SQL DB 還原為相同 SQL 實例中的新資料庫-AlternateWorkloadRestore
* 將 SQL DB 還原為另一個 SQL VM 中另一個 SQL 實例的新資料庫-AlternateWorkloadRestore
* 將 SQL DB 還原為 .bak 檔案-RestoreAsFiles

在提取相關的復原點（相異或記錄時間點）之後，請使用[AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS Cmdlet，根據所需的復原方案來提取復原設定物件。

#### <a name="original-workload-restore"></a>原始工作負載還原

若要使用復原點的資料來覆寫已備份的資料庫，只要指定正確的旗標和相關的復原點，如下列範例所示。

##### <a name="original-restore-with-distinct-recovery-point"></a>具有不同復原點的原始還原

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>具有記錄時間點的原始還原

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>替代工作負載還原

> [!IMPORTANT]
> 已備份的 SQL DB 只能還原至另一個 SQLInstance 的新資料庫，並在 Azure VM ' 已註冊到此保存庫中。

如上所述，如果目標 SQLInstance 位於另一個 Azure VM 中，請確定它已[註冊到此保存庫](#registering-the-sql-vm)，且相關的 SQLInstance 顯示為可保護的專案。

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

然後只傳遞相關的復原點，以正確的旗標來設定目標 SQL 實例，如下所示。

##### <a name="alternate-restore-with-distinct-recovery-point"></a>具有不同復原點的替代還原

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>具有記錄時間點的替代還原

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>還原為檔案

若要將備份資料還原為 .bak 檔案，而不是資料庫，請選擇 [**還原成**檔案] 選項。 已備份的 SQL DB 可以還原到任何已向此保存庫註冊的目標 VM。

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>以不同的復原點還原成檔案

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>從最新完整的記錄時間點還原成檔案

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>從指定的完整還原為具有記錄時間點的檔案

如果您想要提供應用於還原的特定完整，請使用下列命令：

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

從[AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS Cmdlet 取得的最終復原點設定物件具有 restore 的所有相關資訊，如下所示。

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

您可以編輯已還原的資料庫名稱、OverwriteWLIfpresent、NoRecoveryMode 和 targetPhysicalPath 欄位。 取得目標檔案路徑的更多詳細資料，如下所示。

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

將相關 PS 屬性設定為字串值，如下所示。

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
> 請確定最後的復原設定物件具有所有必要和適當的值，因為還原作業會以 config 物件為基礎。

### <a name="restore-with-relevant-configuration"></a>使用相關設定進行還原

取得並驗證相關的復原設定物件之後，請使用 Backup-azrecoveryservicesbackupitem PS Cmdlet 來啟動還原[程式](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0)。

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

還原作業會傳回要追蹤的工作。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>管理 SQL 備份

### <a name="on-demand-backup"></a>隨選備份

啟用資料庫的備份之後，使用者也可以使用[Backup-azrecoveryservicesbackupitem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS Cmdlet 來觸發資料庫的隨選備份。 下列範例會在啟用壓縮的 SQL DB 上觸發完整備份，並保留60天的完整備份。

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

如果輸出遺失，或您想要取得相關的作業識別碼，請從 Azure 備份服務[取得作業清單](#track-azure-backup-jobs)，然後加以追蹤及其詳細資料。

### <a name="change-policy-for-backup-items"></a>變更備份專案的原則

使用者可以將備份專案的原則從 Policy1 變更為 Policy2。 若要切換已備份專案的原則，請提取相關的原則和備份專案，並使用[AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)命令搭配備份專案作為參數。

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

若要編輯現有的原則，請使用[使用 get-azrecoveryservicesbackupprotectionpolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-3.8.0)命令。

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```
經過一段時間之後，請檢查備份作業，以追蹤任何失敗。 如果有，您必須修正問題。 然後，使用**FixForInconsistentItems**參數重新執行 [編輯原則] 命令，以重試編輯先前操作失敗的所有備份專案的原則。

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>重新註冊 SQL Vm

> [!WARNING]
> 嘗試重新註冊之前，請務必閱讀這[份檔](backup-sql-server-azure-troubleshoot.md#re-registration-failures)，以瞭解失敗的徵兆和原因

若要觸發 SQL VM 的重新註冊，請提取相關的備份容器，並將它傳遞給 register Cmdlet。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>停止保護

#### <a name="retain-data"></a>保留資料

如果使用者希望停止保護，他們可以使用[Enable-azrecoveryservicesbackupprotection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS Cmdlet。 這會停止已排程的備份，但備份的資料直到立即保留為止。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>刪除備份資料

若要在保存庫中完全移除儲存的備份資料，只需將 '-RemoveRecoveryPoints ' 旗標/參數新增至 [停用][保護命令](#retain-data)即可。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>停用自動保護

如果在 SQLInstance 上設定 start-autoprotection，則使用者可以使用[disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS Cmdlet 來停用它。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>取消註冊 SQL VM

如果 SQL server 的所有 Db 都不再[受到保護，而且沒有備份資料存在](#delete-backup-data)，使用者就可以從這個保存庫取消註冊 sql VM。 只有使用者可以保護另一個保存庫的 Db。 使用[AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS Cmdlet 來取消註冊 SQL VM。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>追蹤 Azure 備份作業

請務必注意，Azure 備份只會在 SQL 備份中追蹤使用者觸發的作業。 已排程的備份（包括記錄備份）不會顯示在入口網站/powershell 中。 不過，如果有任何排程工作失敗，則會產生[備份警示](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)，並顯示在入口網站中。 [使用 Azure 監視器](backup-azure-monitoring-use-azuremonitor.md)來追蹤所有排程工作和其他相關資訊。

使用者可以使用在非同步作業（例如備份）[輸出](#on-demand-backup)中傳回的 JobID，追蹤隨選/使用者觸發的作業。 使用[AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS Cmdlet 來追蹤作業及其詳細資料。

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

若要從 Azure 備份服務取得隨選作業及其狀態的清單，請使用[AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS Cmdlet。 下列範例會傳回所有進行中的 SQL 作業。

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

若要取消進行中的作業，請使用[AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS Cmdlet。

## <a name="managing-sql-always-on-availability-groups"></a>管理 SQL Always On 可用性群組

若為 SQL Always On 可用性群組，請務必註冊可用性群組（AG）的[所有節點](#registering-the-sql-vm)。 完成所有節點的註冊之後，SQL 可用性群組物件會以邏輯方式建立在可保護的專案底下。 SQL AG 底下的資料庫會列為 ' Backup-sqldatabase '。 節點會顯示為獨立實例，而它們底下的預設 SQL 資料庫也會列為 SQL 資料庫。

例如，假設 SQL AG 有兩個節點： ' SQL-伺服器-0 ' 和 ' sql-伺服器 1 '，以及1個 SQL AG DB。 一旦註冊這兩個節點後，如果使用者列出可保護[的專案](#fetching-sql-dbs)，就會列出下列元件

* SQL AG 物件-可保護專案類型為 Remove-sqlavailabilitygroup
* SQL AG 資料庫可保護的專案類型為 Backup-sqldatabase
* sql-伺服器-0-可保護的專案類型為 SQLInstance
* sql-伺服器-1-可保護的專案類型為 SQLInstance
* 任何預設 SQL Db （master、model、msdb），位於 SQL-伺服器-0-可保護的專案類型為 Backup-sqldatabase
* 任何預設 SQL Db （master、model、msdb），其下的 sql-伺服器-1-可保護的專案類型為 Backup-sqldatabase

當[備份容器列出](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)時，sql-server-0、sql-server-1 也會列為 "AzureVMAppContainer"。

只要提取相關的資料庫，即可[啟用備份](#configuring-backup)，而[隨選備份](#on-demand-backup)和[還原 PS Cmdlet](#restore-sql-dbs)則相同。
