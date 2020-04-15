---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 6f7f319d2ebb4cd39933addf04f249df02d7819f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314105"
---
## <a name="create-a-resource-group"></a>建立資源群組

*如果您已有資源群組,則可以跳槽到[建立金鑰保管庫](#create-a-key-vault)。*

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

使用[az 組建立](/cli/azure/group?view=azure-cli-latest#az-group-create)資源組建立 Azure CLI 指令、[新 AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell 命令或從[Azure 閘戶](https://portal.azure.com)創建 資源群組。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫

*如果您已經擁有金鑰保管庫,則可以跳到[「設定密鑰保管庫高級存取策略](#set-key-vault-advanced-access-policies)」。*

使用[az 金鑰保管庫建立](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)金鑰保管庫建立 Azure CLI 命令[、New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell 指令[、Azure 門戶](https://portal.azure.com)或[資源管理器樣本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)。

>[!WARNING]
> 密鑰保管庫和 VM 必須位於同一訂閱中。 此外,為了確保加密機密不會跨越區域邊界,Azure 磁碟加密要求密鑰保管庫和 VM 在同一區域中位於同一位置。 創建和使用與要加密的 VM 位於同一訂閱和區域中的密鑰保管庫。 

每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的金鑰保存庫名稱取代 <your-unique-keyvault-name>。

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI 建立金鑰保管庫時,添加"啟用磁碟加密"標誌。

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 Azure PowerShell 建立金鑰保管庫時,添加"啟用的磁碟加密"標誌。

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager 範本

您還可以使用[資源管理器範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)創建金鑰保管庫。

1. 在 Azure 快速入門範本中，按一下 [部署至 Azure]****。
2. 選擇訂閱、資源組、資源組位置、密鑰保管庫名稱、對象 ID、法律術語和協定,然後按一下「**購買**」。 。 


##  <a name="set-key-vault-advanced-access-policies"></a>設定金鑰保存庫進階存取原則

Azure 平台需要存取您金鑰保存庫中的加密金鑰或密碼，讓該資訊可供 VM 用來開機和解密磁碟區。 

如果在創建時未為磁碟加密、部署或範本部署啟用密鑰保管庫(如上一步所示),則必須更新其高級訪問策略。  

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
 使用金鑰保管庫 PowerShell cmdlet[集-AzKeyVault 訪問策略](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)為金鑰保管庫啟用磁碟加密。

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

1. 選擇金鑰保管庫,轉到**存取策略**,然後按下**以顯示進階存取策略**。
2. 選取標示為**為磁碟區加密啟用對 Azure 磁碟加密的存取**的方塊。
3. 視需要選取 [為部署啟用對 Azure 虛擬機器的存取]**** 及/或 [為範本部署啟用對 Azure Resource Manager 的存取]****。 
4. 按一下 [檔案]  。

    ![Azure 金鑰保存庫進階存取原則](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>設定金鑰加密金鑰 (KEK)

如果您想使用金鑰加密金鑰 (KEK) 來為加密金鑰額外添加一層安全性，請將 KEK 新增至金鑰保存庫。 若指定了金鑰加密金鑰，Azure 磁碟加密會先使用該金鑰包裝加密祕密，再寫入 Key Vault。

可以使用 Azure CLI [az 金鑰庫金鑰創建](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令、Azure PowerShell[添加-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 或 Azure[門戶](https://portal.azure.com/)生成新的 KEK。 您必須產生 RSA 金鑰類型;但是,您必須生成 RSA 金鑰類型。Azure 磁碟加密尚不支援使用橢圓曲線鍵。

您可以從本地金鑰管理 HSM 匯入 KEK。 關於詳細資訊,請參閱[金鑰保管庫文件](/azure/key-vault/key-vault-hsm-protected-keys)。

必須對密鑰保管庫 KEK URL 進行版本控制。 Azure 會強制執行設定版本的這項限制。 針對有效的密碼和 KEK URL，請參閱下列範例︰

* 有效機密網址的範例:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 有效的 KEK 網址 範例:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure 磁碟加密不支援將連接埠號碼指定為金鑰保存庫密碼和 KEK URL 的一部分。 如需不支援和支援的金鑰保存庫 URL 範例，請參閱下列範例：

  * 可接受的金鑰保管庫網址:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 無法接受的金鑰保存庫網址:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI [az 金鑰庫建立](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令生成新的 KEK 並將其存儲在密鑰保管庫中。

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

您可以使用 Azure CLI [az 金鑰函式庫金鑰匯入](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)指令匯入私密金鑰:

在這兩種情況下,您將向 Azure CLI [az vm 加密啟用](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable)-- 密鑰加密金鑰參數提供 KEK 的名稱。 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

使用 Azure PowerShell[添加-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) cmdlet 生成新的 KEK 並將其存儲在密鑰保管庫中。

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

您可以使用 Azure PowerShell [az 金鑰庫金鑰匯入](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令導入私密金鑰。

在這兩種情況下,您將向 Azure PowerShell[集-AzVMDisk加密擴展](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0)- 密鑰加密金鑰庫 Id 和 -密鑰加密金鑰 Url 參數提供 KEK 金鑰保管庫的 ID 和 KEK 的 URL。 請注意,此示例假定您對磁碟加密密鑰和 KEK 使用相同的密鑰保管庫。

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
