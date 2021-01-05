---
title: 使用 PowerShell 備份和復原 Azure Vm
description: 說明如何使用 Azure 備份搭配 PowerShell 來備份和復原 Azure Vm
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 610049ec14243abb296aef431eb37533c6169817
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797055"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>使用 PowerShell 備份及還原 Azure Vm

本文說明如何使用 PowerShell Cmdlet，在 [Azure 備份](backup-overview.md) 復原服務保存庫中備份和還原 Azure VM。

在本文中，您將了解如何：

> [!div class="checklist"]
>
> * 建立復原服務保存庫並設定保存庫內容。
> * 定義備份原則
> * 套用備份原則以保護多部虛擬機器
> * 觸發受保護虛擬機器的隨選備份作業。在備份 (或保護) 虛擬機器之前，您必須先完成[先決條件](backup-azure-arm-vms-prepare.md)來備妥保護 VM 的環境。

## <a name="before-you-start"></a>開始之前

* [深入瞭解](backup-azure-recovery-services-vault-overview.md) 復原服務保存庫。
* 請[參閱](backup-architecture.md#architecture-built-in-azure-vm-backup)Azure VM 備份的架構、[瞭解](backup-azure-vms-introduction.md)備份程式，以及[審查](backup-support-matrix-iaas.md)支援、限制和必要條件。
* 檢查復原服務的 PowerShell 物件階層。

## <a name="recovery-services-object-hierarchy"></a>復原服務物件階層

下圖摘要說明物件階層。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

請參閱 Azure 文件庫中的 **Az. az.recoveryservices** [Cmdlet 參考](/powershell/module/az.recoveryservices/) 參考。

## <a name="set-up-and-register"></a>設定並註冊

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

開始：

1. [下載最新版本的 PowerShell](/powershell/azure/install-az-ps)

2. 輸入下列命令，以找到可用的 Azure 備份 PowerShell Cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    將顯示 Azure備份、Azure Site Recovery 與復原服務保存庫的別名與 Cmdlet。 下圖是您將看到的範例。 它不是 Cmdlet 的完整清單。

    ![復原服務清單](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. 使用 **Connect-AzAccount** 登入您的 Azure 帳戶。 此 Cmdlet 會開啟網頁，提示您輸入帳戶認證：

    * 或者，您可以使用 **-Credential** 參數，以參數形式將您的帳戶認證加入 **Connect-AzAccount** Cmdlet。
    * 如果您是代表租使用者工作的 CSP 合作夥伴，請使用其 tenantID 或租使用者主功能變數名稱稱將客戶指定為租使用者。 例如： **Connect-disconnect-azaccount-租使用者 "fabrikam.com"**

4. 由於一個帳戶可以有多個訂用帳戶，因此您必須將要使用的訂用帳戶與帳戶建立關聯：

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. 如果您是第一次使用 Azure 備份，就必須使用 **[>register-azresourceprovider](/powershell/module/az.resources/register-azresourceprovider)** 指令程式，向您的訂用帳戶註冊 Azure 復原服務提供者。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 您可以使用下列命令，確認提供者已成功註冊：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    在命令輸出中，**RegistrationState** 應該變更為 **Registered**。 如果不是，請再次執行 **[>register-azresourceprovider](/powershell/module/az.resources/register-azresourceprovider)** Cmdlet。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

下列步驟將引導您完成建立復原服務保存庫。 復原服務保存庫不同於備份保存庫。

1. 復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **[>new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. 使用 [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) Cmdlet 來建立復原服務保存庫。 請務必為保存庫指定與用於資源群組相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 指定要使用的儲存空間備援類型。 您可以使用 [本機冗余儲存體 (LRS) ](../storage/common/storage-redundancy.md#locally-redundant-storage)、 [異地冗余儲存體 (GRS) ](../storage/common/storage-redundancy.md#geo-redundant-storage)或 [區域冗余儲存體 (ZRS) ](../storage/common/storage-redundancy.md#zone-redundant-storage)。 下列範例顯示 *testvault* 的 **-BackupStorageRedundancy** 選項設為 **GeoRedundant** 的情形。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > 許多 Azure 備份 Cmdlet 都需要將復原服務保存庫物件當做輸入。 基於這個理由，將備份復原服務保存庫物件儲存在變數中會是方便的做法。
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要查看訂用帳戶中的所有保存庫，請使用 [>new-azrecoveryservicesvault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)：

```powershell
Get-AzRecoveryServicesVault
```

輸出會類似下列的範例，請注意，會提供相關聯的 ResourceGroupName 與位置。

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>備份 Azure VM

使用復原服務保存庫來保護您的虛擬機器。 在您套用保護之前，請設定保存庫內容 (保存庫中所保護的資料類型)，並確認保護原則。 保護原則是備份工作何時執行，以及每個備份快照之保留時間長度的排程。

### <a name="set-vault-context"></a>設定保存庫內容

在 VM 上啟用保護之前，請使用 [>set-azrecoveryservicesvaultcoNtext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) 設定保存庫內容。 保存庫內容設定之後就會套用至所有後續的 Cmdlet。 下列範例會設定保存庫 *testvault* 的保存庫內容。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保存庫識別碼

我們計畫根據 Azure PowerShell 的指導方針來淘汰保存庫內容設定。 相反地，您可以儲存或提取保存庫識別碼，並將它傳遞至相關的命令。 因此，如果您尚未設定保存庫內容，或想要為特定保存庫指定要執行的命令，請將保存庫識別碼以 "-vaultID" 傳遞給所有相關的命令，如下所示：

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Or

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>修改儲存體複寫設定

使用 [AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) 命令將保存庫的儲存體複寫設定設定為 LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> 只有當保存庫中沒有受保護的備份項目時，才可以修改儲存體備援。

### <a name="create-a-protection-policy"></a>建立保護原則

當您建立復原服務保存庫時，它會隨附預設的保護和保留原則。 預設保護原則會在每天的指定時間觸發備份作業。 預設保留原則會將每日復原點保留 30 天。 您可以使用預設原則來快速地保護 VM，並在之後編輯原則的各種詳細資料。

使用 **[>get-azrecoveryservicesbackupprotectionpolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** 來查看保存庫中可用的保護原則。 您可以使用此 Cmdlet 來取得特定的原則，或檢視與工作負載類型相關聯的原則。 下列範例會取得工作負載類型 AzureVM 的原則。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

輸出類似於下列範例：

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell 中 BackupTime 欄位的時區是 UTC。 不過，當備份時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。
>
>

備份保護原則至少與一個保留原則相關聯。 保護原則會定義復原點在被刪除之前要保留多久。

* 使用 [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) 檢視預設保留原則。
* 同樣地，您可以使用 [>get-azrecoveryservicesbackupschedulepolicyobject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) 取得預設排程原則。
* [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) Cmdlet 會建立 PowerShell 物件來保存備份原則資訊。
* 排程和保留原則物件可當作 New-AzRecoveryServicesBackupProtectionPolicy Cmdlet 的輸入。

根據預設，開始時間是在排程原則物件中定義。 使用下列範例將開始時間變更為所需的開始時間。 所需的開始時間也應為 UTC。 下列範例假設所需的開始時間為每日備份 01:00 AM UTC。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 您只需要在30分鐘的倍數內提供開始時間。 在上述範例中，它只能是 "01:00:00" 或 "02:30:00"。 開始時間不能是 "01:15:00"

下列範例會將排程原則和保留原則儲存在變數中。 這個範例在建立保護原則 *NewPolicy* 時會使用這些變數來定義參數。

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

輸出類似於下列範例：

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>啟用保護。

在定義保護原則之後，您仍然必須對項目啟用此原則。 使用 [>enable-azrecoveryservicesbackupprotection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 啟用保護。 啟用保護需要兩個物件：項目和原則。 一旦原則與保存庫相關聯，備份工作流程將依照原則排程定義的時間觸發。

> [!IMPORTANT]
> 使用 PowerShell 同時啟用多個 Vm 的備份時，請確定單一原則沒有超過100個與其相關聯的 Vm。 這是[建議的最佳做法](./backup-azure-vm-backup-faq.md#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy)。 目前，如果有超過 100 部 VM，但已規劃在未來新增檢查，則 PowerShell 用戶端不會明確封鎖。

下列範例會使用原則 NewPolicy 來對項目 V2VM 啟用保護。 這些範例根據 VM 是否加密以及加密的類型而有所不同。

在 **非加密 Resource Manager VM** 上啟用保護：

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

若要在加密的 VM 上 (使用 BEK 與 KEK 加密) 啟用保護，您必須提供權限讓 Azure 備份服務讀取金鑰保存庫中的金鑰與祕密。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

若要在 **加密的 VM 上 (只使用 BEK 加密)** 啟用保護，您必須提供權限讓 Azure 備份服務讀取金鑰保存庫中的密碼。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> 如果您是使用 Azure Government 雲端，則 `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` 在 [>set-azkeyvaultaccesspolicy 指令程式](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)中使用參數 **ServicePrincipalName** 的值。
>

如果您想要選擇性地備份幾個磁片，並將其他磁片排除在 [這些案例](selective-disk-backup-restore.md#scenarios)中，您可以設定保護並只備份 [此處](selective-disk-backup-restore.md#enable-backup-with-powershell)所述的相關磁片。

## <a name="monitoring-a-backup-job"></a>監視備份工作

您不必透過 Azure 入口網站就可以監視長時間執行的作業，例如備份作業。 若要取得進行中作業的狀態，請使用 [AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) Cmdlet。 此 Cmdlet 會取得特定保存庫 (在保存庫內容中指定) 的備份作業。 下列範例會以陣列形式取得進行中作業的狀態，並將狀態儲存在 $joblist 變數中。

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

輸出類似於下列範例：

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

不要輪詢這些作業以完成-這是不必要的額外程式碼-請使用 [AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) Cmdlet。 此 Cmdlet 會暫停執行，直到工作完成，或達到指定的逾時值為止。

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>管理 Azure VM 備份

### <a name="modify-a-protection-policy"></a>修改保護原則

若要修改保護原則，請使用 [>get-azrecoveryservicesbackupprotectionpolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) 來修改 SchedulePolicy 或 >retentionpolicy 物件。

#### <a name="modifying-scheduled-time"></a>修改排程時間

當您建立保護原則時，預設會指派一個開始時間。 下列範例示範如何修改保護原則的開始時間。

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>修改保留

下列範例會將復原點保留變更為 365 天。

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>正在設定立即還原快照集保留期

> [!NOTE]
> 從 Azure PowerShell 版本1.6.0 開始，您可以使用 PowerShell 來更新原則中的立即還原快照集保留期限

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

預設值會是2。 您最少可以將值設定為1，最大值為5。 針對每週備份原則，期限設定為5，而且無法變更。

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>在快照集保留期間建立 Azure 備份資源群組

> [!NOTE]
> 從 Azure PowerShell 版本3.7.0 開始，您可以建立和編輯建立的資源群組，以便儲存立即的快照集。

若要深入瞭解資源群組建立規則和其他相關的詳細資料，請參閱 [虛擬機器的 Azure 備份資源群組](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) 檔。

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="exclude-disks-for-a-protected-vm"></a>排除受保護 VM 的磁片

Azure VM 備份提供一項功能，可選擇性地排除或包含在 [這些案例](selective-disk-backup-restore.md#scenarios)中很有説明的磁片。 如果虛擬機器已受到 Azure VM 備份的保護，且所有磁片都已備份，您可以修改保護以選擇性地包含或排除磁片，如 [這裡](selective-disk-backup-restore.md#modify-protection-for-already-backed-up-vms-with-powershell)所述。

### <a name="trigger-a-backup"></a>觸發備份

使用 [備份 >backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) 來觸發備份作業。 如果是初始備份，則會是完整備份。 後續的備份會採用增量複本。 下列範例會將 VM 備份保留60天。

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

輸出類似於下列範例：

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell 中 StartTime 和 EndTime 欄位的時區是 UTC。 不過，當時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。
>
>

### <a name="change-policy-for-backup-items"></a>變更備份專案的原則

您可以修改現有的原則，或將備份專案的原則從 Policy1 變更為 Policy2。 若要切換已備份專案的原則，請提取相關的原則和備份專案，並使用 [AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 命令搭配備份專案作為參數。

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

此命令會等到設定備份完成，並傳回下列輸出。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>停止保護

#### <a name="retain-data"></a>保留資料

如果您想要停止保護，您可以使用 [>enable-azrecoveryservicesbackupprotection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell Cmdlet。 這將會停止已排程的備份，但直到現在為止備份的資料會永久保留。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>刪除備份資料

若要完全移除保存庫中儲存的備份資料，請將 '-RemoveRecoveryPoints ' 旗標/切換新增至 [' disable ' 保護命令](#retain-data)。

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>還原 Azure VM

使用 Azure 入口網站來還原 VM 和使用 PowerShell 還原 VM 之間有一個重要的差異。 使用 PowerShell 時，建立磁碟和復原點組態資訊之後，還原作業即完成。 還原作業不會建立虛擬機器。 若要從磁碟建立虛擬機器，請參閱[從還原的磁碟建立 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)一節。 如果您不想還原整個 VM，但想要從 Azure VM 備份還原或復原幾個檔案，請參閱[檔案復原](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)一節。

> [!Tip]
> 還原作業不會建立虛擬機器。
>
>

下圖顯示從 RecoveryServicesVault 到 BackupRecoveryPoint 的物件階層。

![顯示 BackupContainer 的復原服務物件階層](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

若要還原備份資料，請識別已備份的項目和保存時間點資料的復原點。 使用 [restore->backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 將資料從保存庫還原至您的帳戶。

還原 Azure VM 的基本步驟如下︰

* 選取 VM。
* 選擇復原點。
* 還原磁碟。
* 從預存的磁碟建立 VM。

### <a name="select-the-vm-when-restoring-files"></a>在還原檔案時選取 VM () 

若要取得可識別正確備份項目的 PowerShell 物件，請從保存庫中的容器開始，向下深入物件階層。 若要選取代表 VM 的容器，請使用 [>get-azrecoveryservicesbackupcontainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) 指令程式，並使用管道傳送至 [>backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) Cmdlet。

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>在還原檔案時選擇復原點 () 

使用 [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) Cmdlet 來列出備份項目的所有復原點。 接下來選擇要還原的復原點。 如果您不確定要使用哪一個復原點，最好在清單中選擇最新的 RecoveryPointType = AppConsistent 點。

在下列腳本中，變數 **$rp** 是所選備份專案的復原點陣列（從過去七天起算）。 陣列是以相反時間順序排序，最新復原點位於索引 0。 使用標準 PowerShell 陣列索引來挑選復原點。 在範例中，$rp[0] 會選取最新的復原點。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

輸出類似於下列範例：

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>還原磁碟

使用 [>backup-azrecoveryservicesbackupitem 指令程式](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) ，將備份專案的資料和設定還原至復原點。 在您識別復原點之後，請使用它作為 **-RecoveryPoint** 參數的值。 在上述範例中， **$rp [0]** 是要使用的復原點。 在接下來的範例程式碼中，**$rp[0]** 是要用來還原磁碟的復原點。

還原磁碟和設定資訊：

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>還原受控磁碟

> [!NOTE]
> 如果支援的 VM 具有受控磁片，而您想要將其還原為受控磁片，我們引進了 Azure PowerShell RM 模組 v 6.7.0 的功能。 起。
>
>

請提供額外的參數 **TargetResourceGroupName** 以指定將作為受控磁碟還原目的地的 RG。

> [!IMPORTANT]
> 強烈建議使用 **TargetResourceGroupName** 參數來還原受控磁片，因為這樣會大幅改善效能。 如果未指定此參數，則您無法從立即還原功能獲益，而且還原作業將會比較慢。 如果目的是要將受控磁片還原為非受控磁片，則請勿提供此參數，並藉由提供參數來清除意圖 `-RestoreAsUnmanagedDisks` 。 您 `-RestoreAsUnmanagedDisks` 可以從 Azure PowerShell 3.7.0 開始取得參數。 在未來的版本中，必須提供這些參數的任一個，才能進行正確的還原體驗。
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

**VMConfig.JSON** 檔案將會還原至儲存體帳戶，而受控磁碟則會還原至指定的目標 RG。

輸出類似於下列範例：

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

使用 [AzRecoveryServicesBackupJob 指令程式](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) 等候還原作業完成。

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

還原作業完成之後，請使用 AzRecoveryServicesBackupJobDetails 指令 [程式](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) 取得還原作業的詳細資料。 JobDetails 屬性具有重建 VM 所需的資訊。

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

#### <a name="restore-selective-disks"></a>還原選擇性磁片

使用者可以選擇性地還原數個磁片，而不是整個備份組。 提供所需的磁片 Lun 作為參數，只還原它們，而不是整個集合[，如下所述。](selective-disk-backup-restore.md#restore-selective-disks-with-powershell)

> [!IMPORTANT]
> 其中一個必須選擇性地備份磁片，以選擇性地復原磁碟。 [這裡](selective-disk-backup-restore.md#selective-disk-restore)提供更多詳細資料。

還原磁碟之後，請繼續下一節來建立 VM。

## <a name="replace-disks-in-azure-vm"></a>更換 Azure VM 中的磁片

若要取代磁片和設定資訊，請執行下列步驟：

* 步驟1： [復原磁碟](backup-azure-vms-automation.md#restore-the-disks)
* 步驟2： [使用 PowerShell 卸離資料磁片](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* 步驟3： [使用 PowerShell 將資料磁片連結至 WINDOWS VM](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>從還原的磁碟建立 VM

在您還原磁碟之後，使用下列步驟從磁碟建立及設定虛擬機器。

> [!NOTE]
>
> 1. 需要 AzureAz 模組3.0.0 或更高版本。 <br>
> 2. 若要從預存的磁碟建立加密的 VM，您的 Azure 角色必須具備可執行 **Microsoft.KeyVault/vaults/deploy/action** 動作的權限。 如果您的角色沒有此許可權，請使用此動作建立自訂角色。 如需詳細資訊，請參閱 [Azure 自訂角色](../role-based-access-control/custom-roles.md)。 <br>
> 3. 還原磁碟之後，現在即可取得部署範本，您可以直接用來建立新的虛擬機器。 您不需要不同的 PowerShell Cmdlet 來建立已加密/未加密的受控/非受控 Vm。<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>使用部署範本建立 VM

結果工作詳細資料提供可查詢和部署的範本 URI。

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

此範本位於客戶的儲存體帳戶和指定的容器下，因此無法直接存取。 我們需要完整的 URL (以及暫時的 SAS 權杖)，才能存取此範本。

1. 先從 templateBlobURI 中解壓縮範本名稱。 格式如下所述。 您可以使用 PowerShell 中的分割作業，從這個 URL 中解壓縮出最終的範本名稱。

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. 然後可以產生完整的 URL [，如下所述。](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment)

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. 部署範本以建立新的 VM [，如下所述。](../azure-resource-manager/templates/deploy-powershell.md)

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
    ```

### <a name="create-a-vm-using-the-config-file"></a>使用設定檔建立 VM

下節列出使使用「VMConfig」檔案建立虛擬機器所需的步驟。

> [!NOTE]
> 強烈建議您使用上面詳述的部署範本來建立 VM。 本節 (1-6 點) 很快就會淘汰。

1. 查詢工作詳細資料的已還原磁碟內容。

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. 設定 Azure 儲存體內容，並還原為 JSON 組態檔。

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. 使用 JSON 組態檔來建立 VM 組態。

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. 連接作業系統磁碟與資料磁碟。 此步驟提供各種受控和已加密 VM 設定的範例。 使用適合您的 VM 設定的範例。

    * **非受控與未加密的 VM** - 如果是非受控、未加密的 VM，請使用下列範例。

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **含 Azure AD 的非受控已加密 VM (僅限 BEK)** - 對於含 Azure AD 的非受控已加密 VM (僅限使用 BEK 加密)，您需要先將密碼還原至金鑰保存庫，才可以連結磁碟。 如需詳細資訊，請參閱[從 Azure 備份復原點還原已加密的虛擬機器](backup-azure-restore-key-secret.md)。 下列範例示範如何將 OS 和資料磁碟連結至已加密的 VM。 設定 OS 磁碟時，請務必提及相關的 OS 類型。

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **含 Azure AD 的非受控已加密 VM (BEK 和 KEK)** - 對於含 Azure AD 的非受控已加密 VM (使用 BEK 和 KEK 加密)，您需要先將金鑰與密碼還原到金鑰保存庫，才可以連結磁碟。 如需詳細資訊，請參閱[從 Azure 備份復原點還原已加密的虛擬機器](backup-azure-restore-key-secret.md)。 下列範例示範如何將 OS 和資料磁碟連結至已加密的 VM。

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **不含 Azure AD 的非受控已加密 VM (僅限 BEK)** - 對於不含 Azure AD 的非受控已加密 VM (僅限使用 BEK 加密)，如果 **無法使用來源 keyVault/密碼**，會使用 [從 Azure 備份復原點還原未加密的虛擬機器](backup-azure-restore-key-secret.md)中的程序，將密碼還原至金鑰保存庫。 然後執行下列腳本，以在還原的作業系統 blob 上設定加密詳細資料 (此步驟不是資料 blob) 所需的步驟。 可以從還原的 keyVault 擷取 $dekurl。

    只有在無法使用來源 keyVault/密碼時，才需要執行下列腳本。

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    有 **密碼可用**，而且也對於 OS Blob 設定加密詳細資料後，可使用下列指令碼附加磁碟。

    如果來源 keyVault/密碼已可供使用，則不需要執行上述腳本。

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **不含 Azure AD 的非受控已加密 VM (BEK 和 KEK)** - 對於不含 Azure AD 的非受控已加密 VM (僅限使用 BEK 和 KEK 加密)，如果 **無法使用來源 keyVault/金鑰/密碼**，會使用 [從 Azure 備份復原點還原未加密的虛擬機器](backup-azure-restore-key-secret.md)中的程序，將金鑰和密碼還原至金鑰保存庫。 然後執行下列腳本，以在還原的作業系統 blob 上設定加密詳細資料 (此步驟不是資料 blob) 所需的步驟。 可以從還原的 keyVault 擷取 $dekurl 和 $kekurl。

    只有在沒有來源 keyVault/金鑰/密碼可用時，才需要執行下列腳本。

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    有 **金鑰/密碼可用**，而且對於 OS Blob 設定加密詳細資料後，可使用下列指令碼附加磁碟。

    如果來源 keyVault/金鑰/密碼可供使用，則不需要執行上述腳本。

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **受控與未加密的 VM** - 針對受控、未加密的 VM，請連結已還原的受控磁碟。 如需深入的資訊，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

    * **含 Azure AD 的受控已加密 VM (僅限 BEK)** - 對於含 Azure AD 的受控已加密的 VM (僅限使用 BEK 加密)，請連結已還原的受控磁碟。 如需深入的資訊，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

    * **含 Azure AD 的受控已加密 VM (BEK 與 KEK)** - 對於含 Azure AD 的受控已加密的 VM (使用 BEK 與 KEK 加密)，請連結已還原的受控磁碟。 如需深入的資訊，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

    * 未使用 **Azure AD (的受控和加密 vm) 僅** 適用于未使用 BEK 進行 Azure AD (加密的受控、已加密的 vm。如果無法使用來源 **keyVault/密碼**，請使用 [從) 復原點還原非加密虛擬機器](backup-azure-restore-key-secret.md)中的程式，將秘密還原至金鑰保存庫。 然後執行下列腳本，在還原的 OS 磁片上設定加密詳細資料 (此步驟不是資料磁片) 所需的步驟。 可以從還原的 keyVault 擷取 $dekurl。

    只有在無法使用來源 keyVault/密碼時，才需要執行下列腳本。  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    有密碼可用，而且也對於 OS 磁碟設定加密詳細資料後，若要附加已還原的受控磁碟，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

    * 未 **Azure AD (BEK 和 KEK 的受控和加密 vm)** -針對 Azure AD 未使用 BEK (KEK & 加密的受控、已加密的 vm，如果無法使用來源 **keyVault/金鑰/秘密**，請使用 [從) 復原點還原非加密虛擬機器](backup-azure-restore-key-secret.md)中的程式，將金鑰和秘密還原至金鑰保存庫。 然後執行下列腳本，在還原的 OS 磁片上設定加密詳細資料 (此步驟不是) 資料磁片的必要步驟。 可以從還原的 keyVault 擷取 $dekurl 和 $kekurl。

    只有在沒有來源 keyVault/金鑰/密碼可用時，才需要執行下列腳本。

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    有金鑰/密碼可用，而且對於 OS 磁碟設定加密詳細資料後，若要附加已還原的受控磁碟，請參閱[使用 PowerShell 將資料磁碟連結至 Windows VM](../virtual-machines/windows/attach-disk-ps.md)。

5. 設定網路設定。

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. 建立虛擬機器。

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. 推送 ADE 擴充功能。
   如果未推送 ADE 延伸模組，則資料磁片會標示為未加密，因此必須執行下列步驟：

   * **對於含 Azure AD 的 VM** - 使用下列命令，以手動方式啟用資料磁碟的加密  

     **僅限 BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK 和 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **對於不含 Azure AD 的 VM** - 使用下列命令，以手動方式啟用資料磁碟的加密。

     如果在命令執行期間要求 AADClientID，則您需要更新 Azure PowerShell。

     **僅限 BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK 和 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> 請務必手動刪除建立為加密 VM 復原磁碟進程一部分的 JASON 檔案。

## <a name="restore-files-from-an-azure-vm-backup"></a>從 Azure VM 備份還原檔案

除了還原磁碟，您也可以從 Azure VM 備份還原個別檔案。 還原檔案功能提供復原點中所有檔案的存取權。 透過檔案總管管理檔案，就像一般的檔案一樣。

從 Azure VM 備份還原檔案的基本步驟如下：

* 選取 VM
* 選擇復原點
* 掛接復原點的磁碟
* 複製所需的檔案
* 將磁碟取消掛接

### <a name="select-the-vm-when-restoring-the-vm"></a>在還原 VM 時選取 VM () 

若要取得可識別正確備份項目的 PowerShell 物件，請從保存庫中的容器開始，向下深入物件階層。 若要選取代表 VM 的容器，請使用 [>get-azrecoveryservicesbackupcontainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) 指令程式，並使用管道傳送至 [>backup-azrecoveryservicesbackupitem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) Cmdlet。

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>在還原 VM 時選擇復原點 () 

使用 [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) Cmdlet 來列出備份項目的所有復原點。 接下來選擇要還原的復原點。 如果您不確定要使用哪一個復原點，最好在清單中選擇最新的 RecoveryPointType = AppConsistent 點。

在下列腳本中，變數 **$rp** 是所選備份專案的復原點陣列（從過去七天起算）。 陣列是以相反時間順序排序，最新復原點位於索引 0。 使用標準 PowerShell 陣列索引來挑選復原點。 在範例中，$rp[0] 會選取最新的復原點。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

輸出類似於下列範例：

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>掛接復原點的磁碟

使用 [AzRecoveryServicesBackupRPMountScript 指令程式](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) 取得腳本，以掛接復原點的所有磁片。

> [!NOTE]
> 磁碟會以 iSCSI 連接磁碟的形式掛接至執行指令碼所在的機器。 掛接會立即發生，您不會產生任何費用。
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

輸出類似於下列範例：

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

在您要復原檔案的機器上執行此指令碼。 若要執行指令碼，您必須輸入提供的密碼。 連結磁碟之後，使用 Windows 檔案總管瀏覽新的磁碟區與檔案。 如需詳細資訊，請參閱備份 [檔，從 Azure 虛擬機器備份復原](backup-azure-restore-files-from-vm.md)檔案。

### <a name="unmount-the-disks"></a>將磁碟取消掛接

複製所需的檔案之後，請使用 [停用-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) 將磁片卸載。 請務必取消掛接磁碟，以便移除對復原點檔案的存取權。

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>後續步驟

如果您偏好使用 PowerShell 來與 Azure 資源互動，請參閱 PowerShell 文章：[部署和管理 Windows Server 的備份](backup-client-automation.md)。 如果您管理 DPM 備份，請參閱[部署及管理 DPM 的備份](backup-dpm-automation.md)一文。
