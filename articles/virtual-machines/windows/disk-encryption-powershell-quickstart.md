---
title: 使用 Azure PowerShell 建立和加密 Windows 虛擬機器
description: 在本快速入門中，您會了解如何使用 Azure PowerShell 來建立和加密 Windows 虛擬機器
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: b5e5b44742c85591b913b94e622c76a2aba111ce
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "72245708"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>快速入門：使用 PowerShell 在 Azure 中建立和加密 Windows 虛擬機器

Azure PowerShell 模組用於從 PowerShell 命令列或在指令碼中建立和管理 Azure 資源。 本快速入門會示範如何使用 Azure PowerShell 模組建立 Windows 虛擬機器 (VM)、建立用來儲存加密金鑰的金鑰保存庫以及加密 VM。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器：

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 來建立 Azure 虛擬機器。 您必須提供認證給 Cmdlet。 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

可能需要幾分鐘的時間才能部署好 VM。 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>建立為加密金鑰設定的金鑰保存庫

Azure 磁碟加密會將其加密金鑰儲存在 Azure Key Vault 中。 使用 [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) 建立金鑰保存庫。 若要啟用金鑰保存庫來儲存加密金鑰，請使用 -EnabledForDiskEncryption 參數。

> [!Important]
> 每個金鑰保存庫必須有唯一的名稱。 下列範例會建立名為 *myKV* 的金鑰保存庫，但您必須為自己的金鑰保存庫命名不同名稱。

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>將虛擬機器加密

利用 [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) 將您的 VM 加密。 

Set-AzVmDiskEncryptionExtension 需要金鑰保存庫物件的一些值。 您可以將金鑰保存庫的唯一名稱傳遞至 [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault)，以取得這些值。

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

幾分鐘後處理程序會傳回下列內容：

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

您可以執行 [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus) 以驗證加密程序。

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

啟用加密時，您會在傳回的輸出中看到下列：

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源：

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您會建立虛擬機器、建立為加密金鑰啟用的金鑰保存庫，並加密 VM。  繼續閱讀下一篇文章，以深入了解 IaaS VM 的 Azure 磁碟加密先決條件。

> [!div class="nextstepaction"]
> [Azure 磁碟加密概觀](disk-encryption-overview.md)