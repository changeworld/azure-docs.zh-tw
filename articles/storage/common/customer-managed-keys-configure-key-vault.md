---
title: 使用儲存在 Azure Key Vault 中客戶管理的金鑰來設定加密
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure 入口網站、PowerShell 或 Azure CLI，以 Azure Key Vault 儲存在客戶管理的金鑰來設定 Azure 儲存體加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0991992a6138d263dfb4d200c9555a8d53366d70
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994404"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>使用儲存在 Azure Key Vault 中客戶管理的金鑰來設定加密

Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以管理自己的金鑰。 客戶管理的金鑰必須儲存在 Azure Key Vault 或 Key Vault 受控硬體安全性模型 (HSM)  (preview) 。

本文說明如何使用 Azure 入口網站、PowerShell 或 Azure CLI，以使用儲存在金鑰保存庫中的客戶管理金鑰來設定加密。 若要瞭解如何使用儲存在受控 HSM 中客戶管理的金鑰來設定加密，請參閱 [使用儲存在 Azure Key Vault 受控 hsm (preview) 中的客戶管理金鑰來設定加密 ](customer-managed-keys-configure-key-vault-hsm.md)。

> [!NOTE]
> Azure Key Vault 和 Azure Key Vault 受控 HSM 支援相同的 Api 和管理介面進行設定。

## <a name="configure-a-key-vault"></a>設定金鑰保存庫

您可以使用新的或現有的金鑰保存庫來儲存客戶管理的金鑰。 儲存體帳戶與金鑰保存庫必須位於相同區域，但可位於不同的訂用帳戶中。

使用客戶管理的金鑰搭配 Azure 儲存體加密需要針對金鑰保存庫啟用虛刪除和清除保護。 當您建立新的金鑰保存庫，且無法停用時，預設會啟用虛刪除。 當您建立金鑰保存庫或建立金鑰保存庫之後，您可以啟用清除保護。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要瞭解如何使用 Azure 入口網站來建立金鑰保存庫，請參閱 [快速入門：使用 Azure 入口網站建立金鑰保存庫](../../key-vault/general/quick-create-portal.md)。 當您建立金鑰保存庫時，請選取 [ **啟用清除保護**]，如下圖所示。

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="顯示如何在建立金鑰保存庫時啟用清除保護的螢幕擷取畫面":::

若要在現有的金鑰保存庫上啟用清除保護，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至您的金鑰保存庫。
1. 在 [ **設定**] 底下，選擇 [ **屬性**]。
1. 在 [ **清除保護** ] 區段中，選擇 [ **啟用清除保護**]。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 建立新的金鑰保存庫，請安裝2.0.0 版或更新版本的 [Az. KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell 模組。 然後呼叫 [>new-azkeyvault](/powershell/module/az.keyvault/new-azkeyvault) 來建立新的金鑰保存庫。 使用2.0.0 版和更新版本的 Az. KeyVault 模組時，當您建立新的金鑰保存庫時，預設會啟用虛刪除。

下列範例會建立新的金鑰保存庫，同時啟用虛刪除和清除保護。 請記得以您自己的值取代括弧中的預留位置值。

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

若要瞭解如何使用 PowerShell 在現有的金鑰保存庫上啟用清除保護，請參閱 [如何搭配 powershell 使用虛刪除](../../key-vault/general/soft-delete-powershell.md)。

接下來，將系統指派的受控識別指派給您的儲存體帳戶。 您將使用此受控識別來授與儲存體帳戶存取金鑰保存庫的許可權。 如需系統指派的受控識別的詳細資訊，請參閱 [什麼是適用于 Azure 資源的受控識別？](../../active-directory/managed-identities-azure-resources/overview.md)。

若要使用 PowerShell 指派受控識別，請呼叫 [Set-new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount)：

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

最後，設定金鑰保存庫的存取原則，讓儲存體帳戶有權存取該金鑰保存庫。 在此步驟中，您將使用先前指派給儲存體帳戶的受控識別。

若要設定金鑰保存庫的存取原則，請呼叫 [set->set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)：

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 建立新的金鑰保存庫，請呼叫 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

若要瞭解如何使用 Azure CLI 在現有的金鑰保存庫上啟用清除保護，請參閱 [如何搭配 CLI 使用虛刪除](../../key-vault/general/soft-delete-cli.md)。

接下來，將系統指派的受控識別指派給儲存體帳戶。 您將使用此受控識別來授與儲存體帳戶存取金鑰保存庫的許可權。 如需系統指派的受控識別的詳細資訊，請參閱 [什麼是適用于 Azure 資源的受控識別？](../../active-directory/managed-identities-azure-resources/overview.md)。

若要使用 Azure CLI 指派受控識別，請呼叫 [az storage account update](/cli/azure/storage/account#az-storage-account-update)：

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

最後，設定金鑰保存庫的存取原則，讓儲存體帳戶有權存取該金鑰保存庫。 在此步驟中，您將使用先前指派給儲存體帳戶的受控識別。

若要設定金鑰保存庫的存取原則，請呼叫 [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy)：

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>新增金鑰

接下來，在金鑰保存庫中新增金鑰。

Azure 儲存體加密支援2048、3072和4096大小的 RSA 和 RSA-HSM 金鑰。 如需有關金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)的**Key Vault 金鑰**。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要瞭解如何使用 Azure 入口網站來新增金鑰，請參閱 [快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出索引鍵](../../key-vault/keys/quick-create-portal.md)。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 新增金鑰，請呼叫 [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 新增金鑰，請呼叫 [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)。 請記得以您自己的值取代括弧中的預留位置值。

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

接下來，設定您的 Azure 儲存體帳戶以搭配 Azure Key Vault 使用客戶管理的金鑰，然後指定要與儲存體帳戶建立關聯的金鑰。

當您使用客戶管理的金鑰來設定加密時，您可以選擇在相關聯的金鑰保存庫中有新版本可用時，自動更新 Azure 儲存體加密所使用的金鑰版本。 或者，您可以明確地指定要用於加密的金鑰版本，直到手動更新金鑰版本為止。

> [!NOTE]
> 若要輪替金鑰，請在 Azure Key Vault 中建立新版本的金鑰。 Azure 儲存體不會在 Azure Key Vault 中處理金鑰的輪替，因此您必須手動輪替金鑰，或建立函式以依排程輪替。

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>設定加密以自動更新金鑰版本

Azure 儲存體可以自動更新用來加密的客戶管理金鑰，以使用最新的金鑰版本。 在 Azure Key Vault 中輪替客戶管理的金鑰時，Azure 儲存體會自動開始使用最新版本的金鑰進行加密。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站中的金鑰版本自動更新來設定客戶管理的金鑰，請遵循下列步驟：

1. 瀏覽至儲存體帳戶。
1. 在儲存體帳戶的 [設定]**** 刀鋒視窗上，按一下 [加密]****。 選取 [ **客戶管理的金鑰** ] 選項，如下圖所示。

    ![顯示加密選項的入口網站螢幕擷取畫面](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. 選擇 [從 Key Vault 選取]**** 選項。
1. 選取 [ **選取金鑰保存庫與金鑰**]。
1. 選取包含您要使用之金鑰的金鑰保存庫。
1. 選取金鑰保存庫中的金鑰。

   ![顯示如何選取金鑰保存庫和金鑰的螢幕擷取畫面](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. 儲存您的變更。

當您指定金鑰之後，Azure 入口網站會指出已啟用金鑰版本的自動更新，並顯示目前用於加密的金鑰版本。

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="螢幕擷取畫面，顯示已啟用金鑰版本的自動更新":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 自動更新金鑰版本來設定客戶管理的金鑰，請安裝2.0.0 或更新版本的 [Az](https://www.powershellgallery.com/packages/Az.Storage) 模組。

若要自動更新客戶管理金鑰的金鑰版本，請在您針對儲存體帳戶使用客戶管理的金鑰設定加密時，省略金鑰版本。 呼叫 [new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) 以更新儲存體帳戶的加密設定，如下列範例所示，並包含 **-KeyvaultEncryption** 選項，以針對儲存體帳戶啟用客戶管理的金鑰。

請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 的金鑰版本自動更新來設定客戶管理的金鑰，請安裝 [Azure CLI 版本 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) 或更新版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

若要自動更新客戶管理金鑰的金鑰版本，請在您針對儲存體帳戶使用客戶管理的金鑰設定加密時，省略金鑰版本。 呼叫 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 以更新儲存體帳戶的加密設定，如下列範例所示。 包含 `--encryption-key-source` 參數並將它設定為， `Microsoft.Keyvault` 以針對帳戶啟用客戶管理的金鑰。

請記得以您自己的值取代括弧中的預留位置值。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>設定加密以手動更新金鑰版本

如果您想要手動更新金鑰版本，請在使用客戶管理的金鑰設定加密時，明確指定版本。 在此情況下，Azure 儲存體不會在金鑰保存庫中建立新版本時自動更新金鑰版本。若要使用新的金鑰版本，您必須手動更新 Azure 儲存體加密所使用的版本。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站中的金鑰版本手動更新來設定客戶管理的金鑰，請指定金鑰 URI，包括版本。 若要將金鑰指定為 URI，請遵循下列步驟：

1. 若要在 Azure 入口網站中找出金鑰 URI，請流覽至您的金鑰保存庫，然後選取 [ **金鑰** ] 設定。 選取所需的金鑰，然後按一下金鑰以查看其版本。 選取金鑰版本以查看該版本的設定。
1. 複製 [ **金鑰識別碼** ] 欄位的值，以提供 URI。

    ![顯示 key vault 金鑰 URI 的螢幕擷取畫面](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. 在儲存體帳戶的 [ **加密金鑰** ] 設定中，選擇 [ **輸入金鑰 URI** ] 選項。
1. 將您複製的 URI 貼到 [ **金鑰 URI** ] 欄位中。 從 URI 中省略金鑰版本，以啟用金鑰版本的自動更新。

   ![顯示如何輸入金鑰 URI 的螢幕擷取畫面](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. 指定包含金鑰保存庫的訂用帳戶。
1. 儲存您的變更。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用手動更新金鑰版本來設定客戶管理的金鑰，請在設定儲存體帳戶的加密時，明確提供金鑰版本。 呼叫 [new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) 以更新儲存體帳戶的加密設定，如下列範例所示，並包含 **-KeyvaultEncryption** 選項，以針對儲存體帳戶啟用客戶管理的金鑰。

請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

當您手動更新金鑰版本時，您必須將儲存體帳戶的加密設定更新為使用新版本。 首先，呼叫 [AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) 取得最新版本的金鑰。 然後呼叫 [new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) ，將儲存體帳戶的加密設定更新為使用新版本的金鑰，如先前範例所示。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用手動更新金鑰版本來設定客戶管理的金鑰，請在設定儲存體帳戶的加密時，明確提供金鑰版本。 呼叫 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 以更新儲存體帳戶的加密設定，如下列範例所示。 包含 `--encryption-key-source` 參數並將它設定為， `Microsoft.Keyvault` 以針對帳戶啟用客戶管理的金鑰。

請記得以您自己的值取代括弧中的預留位置值。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

當您手動更新金鑰版本時，您必須將儲存體帳戶的加密設定更新為使用新版本。 首先，藉由呼叫 [az keyvault show](/cli/azure/keyvault#az-keyvault-show)來查詢金鑰保存庫 URI，並藉由呼叫 [az keyvault key list-](/cli/azure/keyvault/key#az-keyvault-key-list-versions)version 來查詢金鑰版本。 然後呼叫 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 更新儲存體帳戶的加密設定，以使用新版本的金鑰，如先前範例所示。

---

## <a name="change-the-key"></a>變更金鑰

您可以隨時變更 Azure 儲存體加密所使用的金鑰。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要變更 Azure 入口網站的金鑰，請遵循下列步驟：

1. 流覽至您的儲存體帳戶，並顯示 **加密** 設定。
1. 選取金鑰保存庫，然後選擇新的金鑰。
1. 儲存您的變更。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 來變更金鑰，請呼叫 New-azstorageaccount，如[使用客戶管理的金鑰進行加密](#configure-encryption-with-customer-managed-keys)[設定](/powershell/module/az.storage/set-azstorageaccount)，並提供新的金鑰名稱和版本。 如果新的金鑰位於不同的金鑰保存庫中，則您也必須更新金鑰保存庫 URI。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要變更 Azure CLI 的金鑰，請呼叫 [az storage account update](/cli/azure/storage/account#az-storage-account-update) ，如 [使用客戶管理的金鑰設定加密](#configure-encryption-with-customer-managed-keys) ，以及提供新的金鑰名稱和版本。 如果新的金鑰位於不同的金鑰保存庫中，則您也必須更新金鑰保存庫 URI。

---

## <a name="revoke-customer-managed-keys"></a>撤銷客戶管理的金鑰

撤銷客戶管理的金鑰會移除儲存體帳戶與金鑰保存庫之間的關聯。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站撤銷客戶管理的金鑰，請停用金鑰，如 [停用客戶管理的金鑰](#disable-customer-managed-keys)中所述。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

您可以藉由移除金鑰保存庫存取原則來撤銷客戶管理的金鑰。 若要使用 PowerShell 撤銷客戶管理的金鑰，請呼叫 [>set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) 命令，如下列範例所示。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以藉由移除金鑰保存庫存取原則來撤銷客戶管理的金鑰。 若要使用 Azure CLI 撤銷客戶管理的金鑰，請呼叫 [az keyvault delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) 命令，如下列範例所示。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>停用客戶管理的金鑰

當您停用客戶管理的金鑰時，您的儲存體帳戶會再次使用 Microsoft 管理的金鑰進行加密。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要在 Azure 入口網站中停用客戶管理的金鑰，請遵循下列步驟：

1. 流覽至您的儲存體帳戶，並顯示 **加密** 設定。
1. 取消選取 [ **使用您自己的金鑰** ] 設定旁的核取方塊。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 停用客戶管理的金鑰，請使用選項呼叫 [Set-new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) `-StorageEncryption` ，如下列範例所示。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 停用客戶管理的金鑰，請呼叫 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 並將設定 `--encryption-key-source parameter` 為 `Microsoft.Storage` ，如下列範例所示。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>下一步

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
- [Azure 儲存體加密的客戶管理金鑰](customer-managed-keys-overview.md)
- [使用儲存在 Azure Key Vault 受控 HSM (preview) 中的客戶管理金鑰來設定加密 ](customer-managed-keys-configure-key-vault-hsm.md)
