---
title: 使用 Azure PowerShell 加密 Azure 擴展集的磁片
description: 了解如何使用 Azure PowerShell 加密 Windows 虛擬機器擴展集中的 VM 執行個體和已連結的磁碟
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: cac2f57ccb5fd5d0aa251533bd4a5dd1179ec058
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069743"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>使用 Azure PowerShell 加密虛擬機器擴展集中的 OS 和連結的資料磁片

Azure PowerShell 模組用於從 PowerShell 命令列或在指令碼中建立和管理 Azure 資源。  本文說明如何使用 Azure PowerShell 來建立和加密虛擬機器擴展集。 如需將 Azure 磁碟加密套用至虛擬機器擴展集的詳細資訊，請參閱 [虛擬機器擴展集的 Azure 磁碟加密](disk-encryption-overview.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>建立針對磁碟加密啟用的 Azure Key Vault

Azure 金鑰保存庫儲存可讓您安全地在應用程式和服務中實作的金鑰和密碼 (Secret 或 Password)。 密碼編譯金鑰會使用軟體保護功能儲存在 Azure 金鑰保存庫中，或者您可以在 FIPS 140-2 第 2 級標準認證的硬體安全性模組 (HSM) 中匯入或產生金鑰。 這些密碼編譯金鑰用來加密及解密連接到 VM 的虛擬磁碟。 您可保留這些密碼編譯金鑰的控制權，並可稽核其使用情況。

使用 [>new-azkeyvault](/powershell/module/az.keyvault/new-azkeyvault)建立 Key Vault。 若要允許 Key Vault 用於磁碟加密，請設定 EnabledForDiskEncryption** 參數。 下列範例也會定義資源群組名稱、Key Vault 名稱和位置的變數。 提供您自己唯一的 Key Vault 名稱：

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>使用現有的 Key Vault

如果您有想要用於磁碟加密的現有 Key Vault，才需要這個步驟。 如果您已在上一節中建立 Key Vault，請略過此步驟。

您可以使用 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy)，在與磁碟加密的擴展集相同之訂用帳戶與區域中，啟用現有的 Key Vault。 在 $vaultName** 變數中定義現有 Key Vault 的名稱，如下所示：


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>建立擴展集

首先，使用 [Get-credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1) 設定虛擬機器執行個體的系統管理員使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

現在使用 [New-AzVmss](/powershell/module/az.compute/new-azvmss) 建立虛擬機器擴展集。 為了將流量散發到個別的虛擬機器執行個體，也會建立負載平衡器。 負載平衡器包含在 TCP 連接埠 80 上散發流量的規則，同時允許 TCP 連接埠 3389 上的遠端桌面流量以及 TCP 連接埠 5985 上的 PowerShell 遠端流量：

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>啟用加密

若要加密擴展集中的虛擬機器執行個體，首先請使用 [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault) 取得 Key Vault URI 和資源識別碼的部分資訊。 然後使用這些變數，以 [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension) 啟動加密程序：


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

出現提示時，輸入 y** 以繼續擴展集虛擬機器執行個體上的磁碟加密程序。

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>使用 KEK 包裝金鑰來啟用加密

加密虛擬機器擴展集時，您也可以使用金鑰加密金鑰來增加安全性。

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  disk-encryption-keyvault 參數值的語法是完整的識別碼字串：</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> key-encryption-key 參數值的語法是 KEK 的完整 URI，如下所示：</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>檢查加密程序

若要檢查磁碟加密的狀態，請使用 [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption)：


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

當虛擬機器執行個體已加密時，EncryptionSummary** 程式碼會報告 ProvisioningState/succeeded**，如下列範例輸出所示：

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>停用加密

如果您不想再使用加密的虛擬機器執行個體磁碟，您可以使用 [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) 停用加密，如下所示：


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>後續步驟

- 在本文中，您使用 Azure PowerShell 來加密虛擬機器擴展集。 您也可以使用 [Azure CLI](disk-encryption-cli.md) 或 [Azure Resource Manager 範本](disk-encryption-azure-resource-manager.md)。
- 如果您想要在佈建其他擴充功能之後套用 Azure 磁碟加密，則可以使用[擴充功能排序](virtual-machine-scale-sets-extension-sequencing.md)。
