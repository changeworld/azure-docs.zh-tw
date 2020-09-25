---
title: 適用于 Azure 虛擬機器的選擇性磁片備份和還原
description: 在本文中，您將瞭解使用 Azure 虛擬機器備份解決方案的選擇性磁片備份和還原。
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions
ms.openlocfilehash: ce7e53bc740882a819e8a21e3ac95ab47d3b876a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91271370"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>適用于 Azure 虛擬機器的選擇性磁片備份和還原

Azure 備份支援使用虛擬機器備份解決方案，同時備份 VM 中的所有磁片 (作業系統和資料) 。 現在，您可以使用選擇性磁片備份和還原功能，在 VM 中備份資料磁片的子集。 如此可提供有效率且符合成本效益的解決方案，以滿足您的備份和還原需求。 每個復原點只包含備份作業中包含的磁片。 這可讓您在還原作業期間，從指定的復原點還原一部分的磁片。 這適用于從快照集與保存庫的還原。

## <a name="scenarios"></a>案例

此解決方案特別適用于下列案例：

1. 如果您的重要資料只會在一個磁片或一部分的磁片上進行備份，而且不想要備份連接至 VM 的其餘磁片，以將備份儲存體成本降至最低。
2. 如果您有一部分 VM 或資料的備份解決方案。 例如，如果您使用不同的工作負載備份解決方案來備份您的資料庫或資料，而且您想要使用 Azure VM 層級備份來執行其餘的資料或磁片，以使用可用的最佳功能來建立有效率且健全的系統。

您可以使用 PowerShell 或 Azure CLI 設定 Azure VM 的選擇性磁片備份。  使用腳本，您可以使用資料磁片的 LUN 編號來包含或排除資料磁片。  目前，透過 Azure 入口網站設定選擇性磁片備份的功能， **僅限於備份 OS 磁片** 選項。 因此，您可以使用 OS 磁片來設定 Azure VM 的備份，並排除所有附加的資料磁片。

>[!NOTE]
> OS 磁片預設會新增至 VM 備份，且無法排除。

## <a name="using-azure-cli"></a>使用 Azure CLI

確定您使用的是 Az CLI 版本2.0.80 或更高版本。 您可以使用下列命令來取得 CLI 版本：

```azurecli
az --version
```

登入復原服務保存庫和 VM 所在的訂用帳戶識別碼：

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>下列每個命令中都只需要 **resourcegroup** 名稱 (不是對應至保存庫的物件) 。

### <a name="configure-backup-with-azure-cli"></a>使用 Azure CLI 設定備份

在設定保護作業期間，您必須使用**包含**  /  **排除**參數指定磁片清單設定，以提供要在備份中包含或排除之磁片的 LUN 編號。

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

如果 VM 不在與保存庫相同的資源群組中，則 **ResourceGroup** 會參考在其中建立保存庫的資源群組。 提供 VM 識別碼，而不是 VM 名稱，如下所示。

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>使用 Azure CLI 修改已備份 Vm 的保護

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>使用 Azure CLI 在設定備份期間僅備份 OS 磁片

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>使用 Azure CLI 修改保護期間僅備份 OS 磁片

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>使用 Azure CLI 復原磁碟

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>僅還原具有 Azure CLI 的 OS 磁片

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>取得受保護的專案，以取得 Azure CLI 的磁片排除詳細資料

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

另外還有一個新增至受保護專案的 **diskExclusionProperties** 參數，如下所示：

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>使用 Azure CLI 取得備份作業

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

此命令可協助您取得備份磁片和排除磁片的詳細資料，如下所示：

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>使用 Azure CLI 列出復原點

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

這會提供在 VM 中附加和備份的磁片數目資訊。

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>使用 Azure CLI 取得復原點

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

每個復原點都具有所包含和排除磁片的資訊：

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>移除磁片排除設定，並使用 Azure CLI 取得受保護的專案

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

當您執行這些命令時，您會看到 `"diskExclusionProperties": null` 。

## <a name="using-powershell"></a>使用 PowerShell

確定您使用 Azure PowerShell 3.7.0 版或更高版本。

### <a name="enable-backup-with-powershell"></a>使用 PowerShell 啟用備份

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -DiskListSetting "Include"/"Exclude" -DisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>使用 PowerShell 在設定備份期間僅備份 OS 磁片

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>取得使用 PowerShell 以修改保護傳遞的備份專案物件

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

您必須將上面取得的 **$item** 物件傳遞至下列 Cmdlet 中的 **– item** 參數。

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>使用 PowerShell 修改已備份 Vm 的保護

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Include"/"Exclude" -DisksList[Strings]   -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>使用 PowerShell 在修改保護期間僅備份 OS 磁片

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>使用 PowerShell 重設磁片排除設定

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Reset" -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>使用 PowerShell 還原選擇性磁片

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>使用 PowerShell 只還原 OS 磁片

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>使用 Azure 入口網站

使用 Azure 入口網站，您可以從 [VM 備份詳細資料] 窗格和 [備份作業詳細資料] 窗格中，查看包含和排除的磁片。  在還原期間，當您選取要從中還原的復原點時，可以在該復原點中查看備份的磁片。

您可以從 VM 備份詳細資料窗格，在入口網站中查看虛擬機器的包含和排除磁片：

![從備份的詳細資料窗格中查看包含和排除的磁片](./media/selective-disk-backup-restore/backup-details.png)

您可以在這裡從 [作業詳細資料] 窗格中，查看備份中包含和排除的磁片：

![從作業詳細資料窗格中查看包含和排除的磁片](./media/selective-disk-backup-restore/job-details.png)

當您選取要從中還原的復原點時，您可以在這裡查看備份的磁片：

![在還原期間查看備份的磁片](./media/selective-disk-backup-restore/during-restore.png)

透過 Azure 入口網站設定 VM 的選擇性磁片備份體驗 **僅限備份 OS 磁片** 選項。 若要在已備份的 VM 上使用選擇性磁片備份，或針對 VM 的特定資料磁片進行先進的包含或排除，請使用 PowerShell 或 Azure CLI。

>[!NOTE]
>如果資料跨磁片進行，請確定備份中包含所有的相依磁片。 如果您未備份磁片區中的所有相依磁片，還原期間將不會建立包含某些非備份磁片的磁片區。

### <a name="backup-os-disk-only-in-the-azure-portal"></a>只有 Azure 入口網站中的備份作業系統磁片

當您使用 Azure 入口網站啟用備份時，您可以選擇 [ **僅備份 OS 磁片** ] 選項。 因此，您可以使用 OS 磁片來設定 Azure VM 的備份，並排除所有附加的資料磁片。

![僅設定 OS 磁片的備份](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>使用 Azure REST API

您可以使用幾個選取的磁片來設定 Azure VM 備份，也可以修改現有 VM 的保護，以包含/排除幾個磁片[（如下所述）。](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)

## <a name="selective-disk-restore"></a>選擇性磁片還原

選擇性磁片還原是在您啟用選擇性磁片備份功能時所能取得的新增功能。 您可以使用這項功能，從復原點中備份的所有磁片還原選擇性磁片。 它更有效率，而且在您知道需要還原哪一個磁片的情況下，可節省時間。

- 預設會在 VM 備份和還原中包含 OS 磁片，而且無法排除。
- 只有在啟用磁片排除功能之後建立的復原點才支援選擇性磁片還原。
- 使用磁片 **排除設定的** 備份只支援 **磁片還原** 選項。 在此情況下，不支援**VM 還原**或**取代現有**的還原選項。

![還原作業期間無法使用還原 VM 和取代現有的選項](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>限制

傳統虛擬機器和加密的虛擬機器不支援選擇性磁片備份功能。 因此，使用 Azure 磁碟加密 (ADE 加密的 Azure Vm 會) 使用 BitLocker 加密 Windows VM，且不支援適用于 Linux Vm 的 dm crypt 功能。

針對已啟用選擇性磁片備份功能的 VM，不支援用來 **建立新 vm** 和 **取代現有** vm 的還原選項。

Azure VM 備份目前不支援具有 ultra 磁片的 Vm 或連接到它們的共用磁片。 在這種情況下，選擇性磁片備份無法用於在這種情況下，這會排除磁片並備份 VM。

## <a name="billing"></a>計費

Azure 虛擬機器備份會遵循現有的定價模型，其詳細說明請見 [此處](https://azure.microsoft.com/pricing/details/backup/)。

只有當您選擇使用 [**僅 Os 磁片**] 選項進行備份時，才會針對 os 磁片計算**受保護的實例 (PI) 成本**。  如果您設定備份並至少選取一個資料磁片，則會針對所有連接至 VM 的磁片計算 PI 成本。 **備份儲存體成本** 只會根據包含的磁片計算，因此您可以節省儲存體成本。 一律會為 VM 中的所有磁片計算**快照集成本**， (包含和排除的磁片) 。

如果您已選擇跨區域還原 (CRR) 功能，則在排除磁片之後，會將 [CRR 定價](https://azure.microsoft.com/pricing/details/backup/) 套用至備份儲存體成本。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>受保護的實例 (PI) 成本如何針對 Windows 和 Linux 中的 OS 磁片備份計算？

PI 成本是根據 VM) 大小的實際 (來計算。

- 針對 Windows：已使用的空間計算是以儲存作業系統 (磁片磁碟機為基礎，通常是 C： ) 。
- 針對 Linux：已使用的空間計算是以裝載根檔案系統 (/) 的裝置為基礎。

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>我只設定 OS 磁片備份，為什麼所有磁片都會發生快照集？

選擇性的磁片備份功能可讓您藉由強化屬於備份一部分的內含磁片來節省備份保存庫儲存體成本。 不過，會針對所有連接至 VM 的磁片取得快照集。 因此，一律會為 VM 中的所有磁片計算快照集成本， (包含和排除的磁片) 。 如需詳細資訊，請參閱 [帳單](#billing)。

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>我無法藉由將 ultra 磁片或連接至 VM 的共用磁片排除，來設定 Azure 虛擬機器的備份

選擇性磁片備份功能是 Azure 虛擬機器備份解決方案的最上層提供的功能。 Azure VM 備份目前不支援具有 ultra 磁片的 Vm 或連接到它們的共用磁片。

## <a name="next-steps"></a>後續步驟

- [Azure VM 備份的支援矩陣](backup-support-matrix-iaas.md) \(部分機器翻譯\)
- [常見問題-備份 Azure Vm](backup-azure-vm-backup-faq.md)
