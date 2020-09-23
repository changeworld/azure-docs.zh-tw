---
title: 快速入門 - 使用 PowerShell 來備份 VM
description: 在此快速入門中，了解如何使用 Azure PowerShell 模組來備份 Azure 虛擬機器。
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 5e83c599ceed76927f2a313f78c83638d708f1bb
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985131"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>使用 PowerShell 在 Azure 中備份虛擬機器

[Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) 模組用於從命令列或在指令碼中建立和管理 Azure 資源。

[Azure 備份](backup-overview.md)會備份內部部署機器與應用程式，以及 Azure VM。 本文說明如何使用 AZ 模組來備份 Azure VM。 此外，您可以使用 [Azure CLI](quick-backup-vm-cli.md) 或在[Azure 入口網站](quick-backup-vm-portal.md)中備份 VM。

本快速入門能夠在現有的 Azure VM 上進行備份。 如果您需要建立 VM，您可以[使用 Azure PowerShell 來建立 VM](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)。

本快速入門需要 Azure PowerShell AZ 模組 1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>登入並註冊

1. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

    ```powershell
    Connect-AzAccount
    ```

2. 第一次使用 Azure 備份時，您必須使用 [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider) 在您的訂用帳戶中註冊 Azure 復原服務提供者，如下所示：

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

[復原服務保存庫](backup-azure-recovery-services-vault-overview.md)是一個邏輯容器，可儲存受保護資源 (例如 Azure VM) 的備份資料。 當備份作業執行時，它會在復原服務保存庫內建立復原點。 然後您可以使用其中一個復原點，將資料還原到指定的時間點。

當您建立保存庫：

- 針對資源群組和位置，指定您想要備份的資源群組和 VM 的位置。
- 如果您使用[範例指令碼](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)來建立 VM，則資源群組為 **myResourceGroup**、VM 為***myVM**，而資源位於 **WestEurope** 區域。
- 「Azure 備份」會自動處理用於備份資料的儲存體。 保存庫預設會使用[異地備援儲存體 (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)。 異地備援可確保會將備份的資料複寫到與主要區域相距數百英哩的次要 Azure 區域。

現在請建立保存庫：

1. 使用 [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) 來建立保存庫：

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. 使用 [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext) 設定保存庫內容，如下所示：

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. 使用[Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) 變更保存庫的儲存體備援組態 (LRS/GRS)，如下所示：

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > 只有當保存庫中沒有受保護的備份項目時，才可以修改儲存體備援。

## <a name="enable-backup-for-an-azure-vm"></a>啟用 Azure VM 的備份

您可以啟用 Azure VM 的備份功能，並指定備份原則。

- 原則可定義何時執行備份，以及備份所建立的復原點應保留多長的時間。
- 預設保護原則會每天對 VM 執行備份一次，並將建立的復原點保留 30 天。 您可以使用此預設原則來快速保護您的 VM。

請依照下列方式啟用備份：

1. 首先，使用 [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) 設定預設原則：

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. 使用 [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 啟用 VM 備份。 指定原則、資源群組及 VM 名稱。

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>開始備份作業

備份會依據備份原則中指定的排程執行。 您也可以執行隨選備份：

- 第一個初始備份作業會建立完整復原點。
- 在初始備份之後，每個備份作業都會建立增量復原點。
- 增量復原點符合儲存和時間效率，因為它只會傳輸自上次備份後所做的變更。

若要執行隨選備份，請您使用 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)。

- 使用 [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)，指定保留備份資料的保存庫容器。
- 要備份的每個 VM 都會被視為一個項目。 若要開始備份作業，您需使用 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) 來取得 VM 的相關資訊。

執行隨選備份作業，如下所示：

1. 指定容器，取得 VM 資訊，然後執行備份。

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. 因為第一個備份作業會建立完整復原點，所以您可能需要等待長達 20 分鐘。 請監視作業，如下一個程序所述。

## <a name="monitor-the-backup-job"></a>監視備份作業

1. 執行 [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) 以監視作業狀態。

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    輸出會類似下列範例，其顯示的作業為 [進行中]  ︰

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. 當作業狀態為 [已完成]  時，表示 VM 受到保護，並已儲存完整的復原點。

## <a name="clean-up-the-deployment"></a>清除部署

如果您不再需要備份 VM，便可加以清除。

- 如果您想要嘗試還原 VM，請略過清除程序。
- 如果您使用現有的 VM，可以略過最後的 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) Cmdlet，讓資源群組和 VM 留在原處。

停用保護，移除還原點和保存庫。 然後，刪除資源群組和相關聯的 VM 資源，如下所示：

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立復原服務保存庫、啟用 VM 的保護功能，並建立初始復原點。

- [了解如何](tutorial-backup-vm-at-scale.md)在 Azure 入口網站中備份 VM。
- [了解如何](tutorial-restore-disk.md)快速還原的 VM
