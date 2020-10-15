---
title: PowerShell 指令碼範例 - 備份 Azure VM
description: 在本文中，您將了解如何使用 Azure PowerShell 指令碼範例來備份 Azure 虛擬機器。
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 6e3af44284acdc96f6f0197c4153c6f7538570d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069458"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>使用 PowerShell 備份已加密的 Azure 虛擬機器

此指令碼會針對已加密的 Azure 虛擬機器，建立具有異地備援儲存體 (GRS) 的復原服務保存庫。 預設保護原則會套用到保存庫。 此原則會產生虛擬機器的每日備份，並將每個備份保留 30 天。 此指令碼也會觸發虛擬機器的初始復原點，並將該復原點保留 365 天。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | 建立復原服務保存庫來儲存備份。 |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | 在復原服務保存庫上設定備份儲存體屬性。 |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| 在復原服務保存庫中，使用排程原則和保留原則來建立保護原則。 |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | 在 Key Vault 上設定權限，以授與服務主體對加密金鑰的存取權。 |
| [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | 使用指定的備份保護原則來啟用項目的備份。 |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| 修改現有的備份保護原則。 |
| [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | 針對未繫結到備份排程的受保護 Azure 備份項目，開始進行備份。 |
| [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | 等待 Azure 備份作業完成。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/new-azureps-module-az)。
