---
title: 加密 Azure 中 Windows VM 上的磁片
description: 使用 Azure PowerShell 將 Windows VM 上的虛擬磁碟加密以增強安全性
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 096fe779c077424b01df3ead5965cb799866a03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033536"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>將 Windows VM 上的虛擬磁碟加密
如需強化虛擬機器 (VM) 安全性與法規遵循，可以將 Azure 中的虛擬磁碟加密。 磁碟會使用 Azure Key Vault 中受保護的密碼編譯金鑰進行加密。 您可控制這些密碼編譯金鑰，並可稽核其使用情況。 本文說明如何使用 Azure PowerShell 將 Windows VM 上的虛擬磁碟加密。 您也可以[加密Linux虛擬機器](../linux/disk-encryption-overview.md)。

 

## <a name="overview-of-disk-encryption"></a>磁碟加密概觀
Windows VM 上的虛擬磁碟在待用時是使用 BitLocker 進行加密。 將 Azure 中的虛擬磁碟加密完全免費。 密碼編譯金鑰會使用軟體保護功能儲存在 Azure Key Vault 中，或者您可以在 FIPS 140-2 第 2 級標準認證的硬體安全性模組 (HSM) 中匯入或產生金鑰。 密碼編譯金鑰會用來加密及解密連結到 VM 的虛擬磁碟。 您可保有這些密碼編譯金鑰的控制權，並可稽核其使用情況。 

將 VM 加密的程序如下所示：

1. 在 Azure 金鑰保存庫中建立密碼編譯金鑰。
1. 將密碼編譯金鑰設定為可用於磁碟加密。
1. 為您的虛擬磁碟啟用磁碟加密。
1. 系統會從 Azure Key Vault 要求所需的密碼編譯金鑰。
1. 虛擬磁碟會使用所提供的密碼編譯金鑰進行加密。


## <a name="requirements-and-limitations"></a>需求與限制

磁碟加密支援的案例和需求：

* 從 Azure Marketplace 映像或自訂 VHD 映像在新的 Windows VM 上啟用加密。
* 在 Azure 中現有的 Windows VM 上啟用加密。
* 在使用儲存空間設定的 Windows VM 上啟用加密。
* 在 Windows VM 的 OS 和資料磁碟機上停用加密。
* 標準層 VM，例如 A、D、DS、G 及 GS 系列 VM。

    > [!NOTE]
    > 所有資源 (包括金鑰保存庫、儲存體帳戶、VM) 必須位於相同的 Azure 區域和訂用帳戶。

下列案例目前不支援磁碟加密︰

* 基本層 VM。
* 使用傳統部署模型建立的 VM。
* 在已經加密的 VM 上更新密碼編譯金鑰。
* 與內部部署金鑰管理服務整合。


## <a name="create-an-azure-key-vault-and-keys"></a>建立 Azure Key Vault 和金鑰
開始之前，請確定已安裝最新版的 Azure PowerShell 模組。 有關詳細資訊，請參閱[如何安裝和配置 Azure PowerShell](/powershell/azure/overview)。 在下列命令範例中，將所有的範例參數取代為您自己的名稱、位置和金鑰值，例如 *myResourceGroup*、*myKeyVault*、*myVM*等等。

建立 Azure 金鑰保存庫的第一個步驟是儲存您的密碼編譯金鑰。 Azure Key Vault 可儲存讓您安全地在應用程式和服務中實作的金鑰、祕密或密碼。 針對虛擬磁碟加密，您將需要建立 Key Vault 來儲存用來將虛擬磁碟加密或解密的密碼編譯金鑰。 

請使用 [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider) \(英文\) 來啟用您 Azure 訂用帳戶內的 Azure Key Vault 提供者，然後使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) \(英文\) 來建立資源群組。 下列範例會在 *美國東部* 位置建立名為 *myResourceGroup* 的資源群組：

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

持有密碼編譯金鑰和相關聯計算資源 (例如儲存體和 VM) 的 Azure Key Vault 本身，必須都位於相同的區域中。 請使用 [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) \(英文\) 來建立 Azure Key Vault，然後啟用 Key Vault 以搭配磁碟加密使用。 針對 *keyVaultName* 指定一個唯一的 Key Vault 名稱，如下所示︰

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

您可以使用軟體或硬體安全性模型 (HSM) 保護功能來儲存密碼編譯金鑰。  標準的 Key Vault 只會儲存軟體保護的金鑰。 使用 HSM 需要額外付費的進階 Key Vault。 若要建立進階 Key Vault，請在上一個步驟中新增 *-Sku "Premium"* 參數。 下列範例會使用軟體保護的金鑰，因為我們建立了標準金鑰保存庫。 

在兩種保護模型中，Azure 平台都必須獲得存取權，才能在 VM 開機時要求密碼編譯金鑰來將虛擬磁碟解密。 請使用 [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) 在您的 Key Vault 中建立密碼編譯金鑰。 下列範例會建立一個名為 *myKey* 的金鑰：

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器
為了測試加密程序，讓我們使用 [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) \(英文\) 建立一個 VM。 下列範例會使用 *Windows Server 2016 Datacenter* 映像來建立一個名為 *myVM* 的 VM。 出現提供認證的提示時，請輸入要用於 VM 的使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>將虛擬機器加密
使用 Azure Key Vault 金鑰，利用 [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) \(英文\) 來將您的 VM 加密。 下列範例會擷取所有金鑰資訊，然後加密名為 *myVM* 的 VM：

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

接受提示以繼續進行 VM 加密。 VM 會在此程序中重新啟動。 當加密程序完成且已重新啟動 VM 之後，使用 [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) \(英文\) 來檢閱加密狀態：

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

輸出類似於下列範例：

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>後續步驟
* 如需有關管理 Azure Key Vault 的詳細資訊，請參閱[為虛擬機器設定 Key Vault](key-vault-setup.md)。
* 如需有關磁碟加密 (例如準備加密的自訂 VM 以上傳至 Azure) 的詳細資訊，請參閱 [Azure 磁碟加密](../../security/fundamentals/encryption-overview.md)。
