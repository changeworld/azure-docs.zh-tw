---
title: 建立和設定 Azure 磁碟加密的金鑰保存庫
description: 本文提供建立和設定金鑰保存庫以搭配使用的步驟 Azure 磁碟加密
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970632"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>建立和設定 Azure 磁碟加密的金鑰保存庫

Azure 磁碟加密使用 Azure Key Vault 來控制及管理磁片加密金鑰和密碼。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../../key-vault/key-vault-secure-your-key-vault.md)。 

> [!WARNING]
> - 如果您先前已使用 Azure 磁碟加密搭配 Azure AD 來加密 VM，則必須繼續使用此選項來加密您的 VM。 如需詳細資訊，請參閱[建立和設定具有 Azure AD （舊版） Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault-aad.md)。

建立和設定與 Azure 磁碟加密搭配使用的金鑰保存庫包含三個步驟：

1. 建立資源群組（如有需要）。
2. 建立金鑰保存庫。 
3. 設定金鑰保存庫的先進存取原則。

下列快速入門將說明這些步驟：

- [使用 Azure CLI 來建立和加密 Linux VM](disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 建立和加密 Linux 虛擬機器](disk-encryption-cli-quickstart.md)

如果您想要的話，也可以產生或匯入金鑰加密金鑰（KEK）。

> [!Note]
> 本文中的步驟會在[Azure 磁碟加密必要條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)中自動化，並[Azure 磁碟加密必要條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連接至 Azure

您可以使用[Azure CLI](/cli/azure/)、 [Azure PowerShell Az 模組](/powershell/azure/overview)或[Azure 入口網站](https://portal.azure.com)來完成這篇文章中的步驟。 

雖然可以透過瀏覽器存取入口網站，但 Azure CLI 和 Azure PowerShell 需要本機安裝;如需詳細資訊，請參閱[適用于 Linux 的 Azure 磁碟加密：安裝工具](disk-encryption-linux.md#install-tools-and-connect-to-azure)。

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用 Azure CLI 或 Azure PowerShell 之前，您必須先連接到您的 Azure 訂用帳戶。 若要這麼做，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)、[使用 Azure Powershell 登入](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在出現提示時提供您的認證給 Azure 入口網站。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>建立資源群組

*如果您已經有資源群組，您可以跳到[建立金鑰保存庫](#create-a-key-vault)。*

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

使用[az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI 命令、 [remove-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell 命令，或從[Azure 入口網站](https://portal.azure.com)建立資源群組。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫

*如果您已經有金鑰保存庫，您可以跳到[設定金鑰保存庫的先進存取原則](#set-key-vault-advanced-access-policies)。*

使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI 命令、 [AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell 命令、 [Azure 入口網站](https://portal.azure.com)或[Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)來建立金鑰保存庫。

>[!WARNING]
> 為了確保加密秘密不會跨越區域界限，Azure 磁碟加密需要 Key Vault 和 Vm 共置於相同區域中。 建立並使用與要加密的 Vm 位於相同區域的 Key Vault。 

每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的金鑰保存庫名稱取代 <your-unique-keyvault-name>。

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI 建立金鑰保存庫時，請新增「--已啟用磁片加密」旗標。

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 Azure PowerShell 建立金鑰保存庫時，請新增 "-EnabledForDiskEncryption" 旗標。

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager 範本

您也可以使用[Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)來建立金鑰保存庫。

1. 在 Azure 快速入門範本中，按一下 [部署至 Azure]。
2. 選取訂用帳戶、資源群組、資源群組位置、Key Vault 名稱、物件識別碼、法律條款及協定，然後按一下 [**購買**]。 


##  <a name="set-key-vault-advanced-access-policies"></a>設定金鑰保存庫的先進存取原則

Azure 平台需要存取您金鑰保存庫中的加密金鑰或密碼，讓該資訊可供 VM 用來開機和解密磁碟區。 

如果您未在建立時啟用金鑰保存庫進行磁片加密、部署或範本部署（如前一個步驟所示），您必須更新其先進的存取原則。  

### <a name="azure-cli"></a>Azure CLI

使用 [az keyvault update](/cli/azure/keyvault#az-keyvault-update) 啟用金鑰保存庫的磁碟加密。 

 - **針對磁碟加密啟用 Key Vault：** 需要 Enabled-for-disk-encryption。 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **視需要針對部署啟用 Key Vault：** 可讓 Microsoft.Compute 資源提供者在資源建立期間 (例如，在建立虛擬機器時) 參考了這個金鑰保存庫的情況下，從這個金鑰保存庫擷取祕密。

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **視需要針對範本部署啟用 Key Vault：** 允許 Resource Manager 從保存庫擷取祕密。
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 使用金鑰保存庫 PowerShell Cmdlet[設定-set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)來啟用金鑰保存庫的磁片加密。

  - **針對磁碟加密啟用 Key Vault：** Azure 磁碟加密需要 EnabledForDiskEncryption。
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **視需要針對部署啟用 Key Vault：** 可讓 Microsoft.Compute 資源提供者在資源建立期間 (例如，在建立虛擬機器時) 參考了這個金鑰保存庫的情況下，從這個金鑰保存庫擷取祕密。

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **視需要針對範本部署啟用 Key Vault：** 可讓 Azure Resource Manager 在範本部署參考了這個金鑰保存庫的情況下，從這個金鑰保存庫取得祕密。

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure 入口網站

1. 選取您的金鑰保存庫，移至 [**存取原則**]，然後**按一下以顯示 [高級存取原則**]。
2. 選取標示為**為磁碟區加密啟用對 Azure 磁碟加密的存取**的方塊。
3. 視需要選取 [為部署啟用對 Azure 虛擬機器的存取] 及/或 [為範本部署啟用對 Azure Resource Manager 的存取]。 
4. 按一下 [檔案]。

    ![Azure 金鑰保存庫進階存取原則](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>設定金鑰加密金鑰（KEK）

如果您想使用金鑰加密金鑰 (KEK) 來為加密金鑰額外添加一層安全性，請將 KEK 新增至金鑰保存庫。 若指定了金鑰加密金鑰，Azure 磁碟加密會先使用該金鑰包裝加密祕密，再寫入 Key Vault。

您可以使用 Azure CLI [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令、Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) Cmdlet 或[AZURE 入口網站](https://portal.azure.com/)來產生新的 KEK。 您必須產生 RSA 金鑰類型;Azure 磁碟加密還不支援使用橢圓曲線鍵。

您可以改為從內部部署金鑰管理 HSM 匯入 KEK。 如需詳細資訊，請參閱 [Key Vault 文件](../../key-vault/key-vault-hsm-protected-keys.md)。 

您的金鑰保存庫 KEK Url 必須進行版本設定。 Azure 會強制執行設定版本的這項限制。 針對有效的密碼和 KEK URL，請參閱下列範例︰

* 有效的秘密 URL 範例： *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 有效 KEK URL 的範例： *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure 磁碟加密不支援將連接埠號碼指定為金鑰保存庫密碼和 KEK URL 的一部分。 如需不支援和支援的金鑰保存庫 URL 範例，請參閱下列範例：

  * 可接受的金鑰保存庫 URL： *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 無法接受的金鑰保存庫 URL： *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令來產生新的 KEK，並將它儲存在您的金鑰保存庫中。

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

.您可以改為使用 Azure CLI [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令匯入私密金鑰：

不論是哪一種情況，您都會將 KEK 的名稱提供給 Azure CLI [az vm encryption enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --key-encryption-key 參數。 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

使用 Azure PowerShell [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) Cmdlet 來產生新的 KEK，並將它儲存在您的金鑰保存庫中。

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

您可以改為使用 Azure PowerShell [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令匯入私密金鑰。

不論是哪一種情況，您都會將 KEK 金鑰保存庫的識別碼和 KEK 的 URL 提供給 Azure PowerShell [AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId 和-KeyEncryptionKeyUrl 參數。 請注意，此範例假設您使用相同的金鑰保存庫來存放磁片加密金鑰和 KEK。

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密必要條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密必要條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- 瞭解[Linux vm 上 Azure 磁碟加密案例](disk-encryption-linux.md)
- 瞭解如何[疑難排解 Azure 磁碟加密](disk-encryption-troubleshooting.md)
- 閱讀[Azure 磁碟加密範例腳本](disk-encryption-sample-scripts.md)
