---
title: 教學課程：使用 PowerShell 進行多個 Azure VM 備份
description: 本教學課程會詳細說明如何使用 Azure PowerShell 將多個 Azure VM 備份至復原服務保存庫。
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 33090a0156d147fee5ab362b284ec503039617c4
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181726"
---
# <a name="back-up-azure-vms-with-powershell"></a>使用 PowerShell 來備份 Azure VM

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本教學課程說明如何使用 PowerShell 來部署[Azure 備份](backup-overview.md)復原服務保存庫，以備份多個 Azure VM。  

在本教學課程中，您將了解如何：

> [!div class="checklist"]
>
> * 建立復原服務保存庫並設定保存庫內容。
> * 定義備份原則
> * 套用備份原則以保護多部虛擬機器
> * 觸發受保護虛擬機器的隨選備份作業。在備份 (或保護) 虛擬機器之前，您必須先完成[先決條件](backup-azure-arm-vms-prepare.md)來備妥保護 VM 的環境。

> [!IMPORTANT]
> 本教學課程假設您已經建立資源群組和 Azure 虛擬機器。

## <a name="sign-in-and-register"></a>登入並註冊

1. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

    ```powershell
    Connect-AzAccount
    ```

2. 第一次使用「Azure 備份」時，您必須使用 [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider) 在您的訂用帳戶中註冊「Azure 復原服務」提供者。 如果您已經註冊，請略過此步驟。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

[復原服務保存庫](backup-azure-recovery-services-vault-overview.md)是一個邏輯容器，可儲存受保護資源 (例如 Azure VM) 的備份資料。 當備份作業執行時，它會在復原服務保存庫內建立復原點。 然後您可以使用其中一個復原點，將資料還原到指定的時間點。

* 在本教學課程中，您需在與所要備份之 VM 相同的資源群組和位置中建立保存庫。
* 「Azure 備份」會自動處理用於備份資料的儲存體。 保存庫預設會使用[異地備援儲存體 (GRS)](../storage/common/storage-redundancy.md)。 異地備援可確保會將備份的資料複寫到與主要區域相距數百英哩的次要 Azure 區域。

請依照下列方式建立保存庫：

1. 使用 [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) 來建立保存庫。 指定所要備份之 VM 的資源群組名稱和位置。

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. 許多 Azure 備份 Cmdlet 都需要將復原服務保存庫物件當做輸入。 基於這個理由，將備份復原服務保存庫物件儲存在變數中會是方便的做法。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. 使用 [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext) 來設定保存庫內容。

   * 保存庫內容是保存庫中受保護的資料類型。
   * 內容設定之後，即適用於所有後續的 Cmdlet

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>備份 Azure VM

備份會依據備份原則中指定的排程執行。 當您建立復原服務保存庫時，它會隨附預設的保護和保留原則。

* 預設保護原則會每天在指定時間觸發備份作業一次。
* 預設保留原則會將每日復原點保留 30 天。

為了在本教學課程中啟用並備份 Azure VM，我們會執行下列操作：

1. 使用 [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer) 來指定保存庫中裝載您備份資料的容器。
2. 每個要備份的 VM 都是一個項目。 若要開始備份作業，您需使用 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) 來取得 VM 的相關資訊。
3. 使用 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) 來執行隨選備份。
    * 第一個初始備份作業會建立完整復原點。
    * 在初始備份之後，每個備份作業都會建立增量復原點。
    * 增量復原點符合儲存和時間效率，因為它只會傳輸自上次備份後所做的變更。

請依照下列方式啟用並執行備份：

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>疑難排解

如果您在備份虛擬機器時遇到問題，請檢閱這篇[疑難排解文章](backup-azure-vms-troubleshoot.md)。

### <a name="deleting-a-recovery-services-vault"></a>刪除復原服務保存庫

如果您需要刪除保存庫，請先刪除保存庫中的復原點，再將保存庫取消註冊，如下所示：

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>後續步驟

* [檢閱](backup-azure-vms-automation.md)使用 PowerShell 來備份和還原 Azure VM 的更詳細逐步解說。
* [管理及監視 Azure VM 備份](backup-azure-manage-vms.md)
* [還原 Azure VM](backup-azure-arm-restore-vms.md)
