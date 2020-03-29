---
title: 建立及設定適用於 Azure 磁碟加密的金鑰保存庫
description: 本文提供了創建和配置金鑰保存庫以與 Azure 磁片加密一起使用的步驟
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970632"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>建立及設定適用於 Azure 磁碟加密的金鑰保存庫

Azure 磁片加密使用 Azure 金鑰保存庫來控制和管理磁片加密金鑰和機密。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../../key-vault/key-vault-secure-your-key-vault.md)。 

> [!WARNING]
> - 如果以前使用 Azure 磁片加密與 Azure AD 一起加密 VM，則必須繼續使用此選項對 VM 進行加密。 有關詳細資訊[，請參閱使用 Azure AD 創建和配置 Azure 磁片加密的金鑰保存庫（上一版本](disk-encryption-key-vault-aad.md)）。

創建和配置用於 Azure 磁片加密的金鑰保存庫涉及三個步驟：

1. 如果需要，創建資源組。
2. 創建金鑰保存庫。 
3. 設置金鑰保存庫高級訪問策略。

這些步驟在以下快速入門中進行了說明：

- [使用 Azure CLI 來建立和加密 Linux VM](disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 建立和加密 Linux 虛擬機器](disk-encryption-cli-quickstart.md)

如果您願意，還可以生成或導入金鑰加密金鑰 （KEK）。

> [!Note]
> 本文中的步驟在[Azure 磁片加密先決條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)和 Azure[磁片加密先決條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)中自動執行。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連接到 Azure

本文中的步驟可以使用[Azure CLI、Azure](/cli/azure/) [PowerShell Az 模組](/powershell/azure/overview)或 Azure[門戶](https://portal.azure.com)完成。 

當門戶可通過瀏覽器訪問時，Azure CLI 和 Azure PowerShell 需要本地安裝;但是，Azure CLI 和 Azure PowerShell 需要本地安裝。請參閱[Linux 的 Azure 磁片加密：安裝工具](disk-encryption-linux.md#install-tools-and-connect-to-azure)的詳細資訊。

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

在使用 Azure CLI 或 Azure PowerShell 之前，必須首先連接到 Azure 訂閱。 為此，請[使用 Azure CLI 登錄](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)、使用 Azure [Powershell 登錄](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在提示時向 Azure 門戶提供憑據。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>建立資源群組

*如果您已有資源組，則可以跳槽到[創建金鑰保存庫](#create-a-key-vault)。*

資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

使用[az 組創建](/cli/azure/group?view=azure-cli-latest#az-group-create)資源組創建 Azure CLI 命令、[新 AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell 命令或從[Azure 門戶](https://portal.azure.com)創建 資源組。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>建立金鑰保存庫

*如果您已經擁有金鑰保存庫，則可以跳到["設置金鑰保存庫高級訪問策略](#set-key-vault-advanced-access-policies)"。*

使用[az 金鑰保存庫創建](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)金鑰保存庫創建 Azure CLI 命令[、New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell 命令[、Azure 門戶](https://portal.azure.com)或[資源管理器範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)。

>[!WARNING]
> 為了確保加密機密不會跨越區域邊界，Azure 磁片加密要求金鑰保存庫和 VM 在同一區域中位於同一位置。 創建和使用與要加密的 VM 位於同一區域的金鑰保存庫。 

每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的金鑰保存庫名稱取代 <your-unique-keyvault-name>。

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI 創建金鑰保存庫時，添加"啟用磁片加密"標誌。

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 Azure PowerShell 創建金鑰保存庫時，添加"啟用的磁片加密"標誌。

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager 範本

您還可以使用[資源管理器範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)創建金鑰保存庫。

1. 在 Azure 快速入門範本中，按一下 [部署至 Azure]****。
2. 選擇訂閱、資源組、資源組位置、金鑰保存庫名稱、物件識別碼、法律術語和協定，然後按一下"**購買**"。 


##  <a name="set-key-vault-advanced-access-policies"></a>設定金鑰保存庫進階存取原則

Azure 平台需要存取您金鑰保存庫中的加密金鑰或密碼，讓該資訊可供 VM 用來開機和解密磁碟區。 

如果在創建時未為磁片加密、部署或範本部署啟用金鑰保存庫（如上一步所示），則必須更新其高級訪問策略。  

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
 使用金鑰保存庫 PowerShell Cmdlet[集-AzKeyVault 訪問策略](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)為金鑰保存庫啟用磁片加密。

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

1. 選擇金鑰保存庫，轉到**訪問策略**，然後按一下**以顯示高級訪問策略**。
2. 選取標示為**為磁碟區加密啟用對 Azure 磁碟加密的存取**的方塊。
3. 視需要選取 [為部署啟用對 Azure 虛擬機器的存取]**** 及/或 [為範本部署啟用對 Azure Resource Manager 的存取]****。 
4. 按一下 [儲存]****。

    ![Azure 金鑰保存庫進階存取原則](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>設置金鑰加密金鑰 （KEK）

如果您想使用金鑰加密金鑰 (KEK) 來為加密金鑰額外添加一層安全性，請將 KEK 新增至金鑰保存庫。 若指定了金鑰加密金鑰，Azure 磁碟加密會先使用該金鑰包裝加密祕密，再寫入 Key Vault。

可以使用 Azure CLI [az 金鑰庫金鑰創建](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令、Azure PowerShell[添加-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) Cmdlet 或 Azure[門戶](https://portal.azure.com/)生成新的 KEK。 您必須生成 RSA 金鑰類型;但是，您必須生成 RSA 金鑰類型。Azure 磁片加密尚不支援使用橢圓曲線鍵。

您可以從本地金鑰管理 HSM 導入 KEK。 有關詳細資訊，請參閱[金鑰保存庫文檔](../../key-vault/key-vault-hsm-protected-keys.md)。 

必須對金鑰保存庫 KEK URL 進行版本控制。 Azure 會強制執行設定版本的這項限制。 針對有效的密碼和 KEK URL，請參閱下列範例︰

* 有效機密 URL 的示例：*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 有效的 KEK URL 示例：*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure 磁碟加密不支援將連接埠號碼指定為金鑰保存庫密碼和 KEK URL 的一部分。 如需不支援和支援的金鑰保存庫 URL 範例，請參閱下列範例：

  * 可接受的金鑰保存庫 URL：*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 不可接受的金鑰保存庫 URL：*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI [az 金鑰庫創建](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)命令生成新的 KEK 並將其存儲在金鑰保存庫中。

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

.您可以使用 Azure CLI [az 金鑰庫金鑰導入](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令導入私密金鑰：

在這兩種情況下，您將向 Azure CLI [az vm 加密啟用](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable)-- 金鑰加密金鑰參數提供 KEK 的名稱。 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

使用 Azure PowerShell[添加-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) Cmdlet 生成新的 KEK 並將其存儲在金鑰保存庫中。

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

您可以使用 Azure PowerShell [az 金鑰庫金鑰導入](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)命令導入私密金鑰。

在這兩種情況下，您將向 Azure PowerShell[集-AzVMDisk加密擴展](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0)- 金鑰加密金鑰庫 Id 和 -金鑰加密金鑰 Url 參數提供 KEK 金鑰保存庫的 ID 和 KEK 的 URL。 請注意，此示例假定您對磁片加密金鑰和 KEK 使用相同的金鑰保存庫。

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>後續步驟

- [Azure 磁片加密先決條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁片加密先決條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- 在 Linux VM 上瞭解[Azure 磁片加密方案](disk-encryption-linux.md)
- 瞭解如何排除[Azure 磁片加密的故障](disk-encryption-troubleshooting.md)
- 閱讀[Azure 磁片加密示例腳本](disk-encryption-sample-scripts.md)
