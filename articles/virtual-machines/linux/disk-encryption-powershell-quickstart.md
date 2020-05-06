---
title: 使用 Azure PowerShell 建立和加密 Linux 虛擬機器
description: 在本快速入門中，您會了解如何使用 Azure PowerShell 來建立和加密 Linux 虛擬機器
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: a2cb8919ac0752c42f22e064d6201c7120fbc9b6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "78970541"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 在 Azure 中建立和加密 Linux VM

Azure PowerShell 模組用於從 PowerShell 命令列或在指令碼中建立和管理 Azure 資源。 本快速入門會示範如何使用 Azure PowerShell 模組建立 Linux 虛擬機器 (VM)、建立用來儲存加密金鑰的 Key Vault 以及加密 VM。 本快速入門會從 Canonical 和 VM Standard_D2S_V3 大小使用 Ubuntu 16.04 LTS 市集映像。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器：

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 建立 Azure 虛擬機器，並將您在上方建立的 VM 設定物件傳給它。

```powershell-interactive
$cred = = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest -Size Standard_D2S_V3
```

可能需要幾分鐘的時間才能部署好 VM。 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>建立為加密金鑰設定的金鑰保存庫

Azure 磁碟加密會將其加密金鑰儲存在 Azure Key Vault 中。 使用 [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) 建立金鑰保存庫。 若要啟用金鑰保存庫來儲存加密金鑰，請使用 -EnabledForDiskEncryption 參數。

> [!Important]
> 每個金鑰保存庫都必須有在 Azure 中唯一的名稱。 在下列範例中，請將 <your-unique-keyvault-name> 取代為您選擇的名稱。

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>將虛擬機器加密

利用 [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) 將您的 VM 加密。 

Set-AzVmDiskEncryptionExtension 需要金鑰保存庫物件的一些值。 您可以將金鑰保存庫的唯一名稱傳遞至 [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault)，以取得這些值。

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

幾分鐘後處理程序會傳回下列內容：

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

您可以執行 [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus) 以驗證加密程序。

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

啟用加密時，您會在傳回的輸出中看到下列：

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您會建立虛擬機器、建立為加密金鑰啟用的金鑰保存庫，並加密 VM。  繼續閱讀下一篇文章，以深入了解 Linux VM 的 Azure 磁碟加密。

> [!div class="nextstepaction"]
> [Azure 磁碟加密概觀](disk-encryption-overview.md)