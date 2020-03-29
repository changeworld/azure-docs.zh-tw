---
title: 使用 Azure CLI 配置客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure CLI 使用 Azure 金鑰保存庫配置客戶管理的金鑰以進行 Azure 存儲加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6be15b3fdef94c07e70eba7c4234979b5ac62344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061166"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>使用 Azure CLI 使用 Azure 金鑰保存庫配置客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文演示如何使用 Azure CLI 使用客戶管理的金鑰配置 Azure 金鑰保存庫。 若要瞭解如何使用 Azure CLI 創建金鑰保存庫，請參閱[快速入門：使用 Azure CLI 從 Azure 金鑰保存庫設置和檢索機密](../../key-vault/quick-create-cli.md)。

## <a name="assign-an-identity-to-the-storage-account"></a>將標識分配給存儲帳戶

要為存儲帳戶啟用客戶管理的金鑰，請先將系統分配的託管標識分配給存儲帳戶。 您將使用此託管標識授予訪問金鑰保存庫的存儲帳戶許可權。

要使用 Azure CLI 分配託管標識，請調用[az 存儲帳戶更新](/cli/azure/storage/account#az-storage-account-update)。 請記住將括弧中的預留位置值替換為您自己的值。

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

有關使用 Azure CLI 配置系統分配的託管標識的詳細資訊，請參閱使用[Azure CLI 在 Azure VM 上配置 Azure 資源的託管標識](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)。

## <a name="create-a-new-key-vault"></a>創建新金鑰保存庫

用於存儲 Azure 存儲加密的客戶管理金鑰的金鑰保存庫必須啟用兩個金鑰保護設置，**即"虛刪除****"和"不清除**"。 要使用啟用這些設置的 PowerShell 或 Azure CLI 創建新金鑰保存庫，請執行以下命令。 請記住將括弧中的預留位置值替換為您自己的值。

要使用 Azure CLI 創建新金鑰保存庫，請調用[az 金鑰保存庫創建](/cli/azure/keyvault#az-keyvault-create)。 請記住將括弧中的預留位置值替換為您自己的值。

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

要瞭解如何使用 Azure CLI 在現有金鑰保存庫中啟用 **"虛刪除****"和"不清除"，** 請參閱標題為 **"啟用虛刪除**和**啟用清除保護**"的部分，瞭解如何[使用 CLI 使用虛刪除](../../key-vault/key-vault-soft-delete-cli.md)。

## <a name="configure-the-key-vault-access-policy"></a>配置金鑰保存庫訪問策略

接下來，為金鑰保存庫配置訪問策略，以便存儲帳戶具有訪問它的許可權。 在此步驟中，您將使用以前分配給存儲帳戶的託管標識。

要設置金鑰保存庫的訪問策略，請調用[az 金鑰保存庫集策略](/cli/azure/keyvault#az-keyvault-set-policy)。 請記住將括弧中的預留位置值替換為您自己的值。

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
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來，在金鑰保存庫中創建一個金鑰。 要創建金鑰，請調用[az 金鑰庫金鑰創建](/cli/azure/keyvault/key#az-keyvault-key-create)。 請記住將括弧中的預留位置值替換為您自己的值。

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰配置加密

預設情況下，Azure 存儲加密使用 Microsoft 管理的金鑰。 為客戶管理的金鑰配置 Azure 存儲帳戶，並指定要與存儲帳戶關聯的金鑰。

要更新存儲帳戶的加密設定，請調用[az 存儲帳戶更新](/cli/azure/storage/account#az-storage-account-update)，如以下示例所示。 包括參數`--encryption-key-source`並將其設置為`Microsoft.Keyvault`，以啟用存儲帳戶的客戶管理金鑰。 該示例還查詢金鑰保存庫 URI 和最新金鑰版本，這兩個值都需要將金鑰與存儲帳戶關聯。 請記住將括弧中的預留位置值替換為您自己的值。

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

## <a name="update-the-key-version"></a>更新金鑰版本

創建金鑰的新版本時，需要更新存儲帳戶才能使用新版本。 首先，通過調用[az 金鑰保存庫顯示](/cli/azure/keyvault#az-keyvault-show)來查詢金鑰保存庫 URI，並通過調用[az 金鑰庫金鑰清單版本](/cli/azure/keyvault/key#az-keyvault-key-list-versions)查詢金鑰版本。 然後調用[az 存儲帳戶更新](/cli/azure/storage/account#az-storage-account-update)以更新存儲帳戶的加密設定以使用金鑰的新版本，如上一節所示。

## <a name="use-a-different-key"></a>使用其他鍵

要更改用於 Azure 存儲加密的金鑰，請調用[az 存儲帳戶更新](/cli/azure/storage/account#az-storage-account-update)，如[使用客戶管理的金鑰配置加密](#configure-encryption-with-customer-managed-keys)並提供新的金鑰名稱和版本。 如果新金鑰位於其他金鑰保存庫中，則還要更新金鑰保存庫 URI。

## <a name="revoke-customer-managed-keys"></a>撤銷客戶管理的金鑰

如果您認為金鑰可能已洩露，則可以通過刪除金鑰保存庫訪問策略來撤銷客戶管理的金鑰。 要撤銷客戶管理的金鑰，請調用[az 金鑰庫刪除策略](/cli/azure/keyvault#az-keyvault-delete-policy)命令，如以下示例所示。 請記住將括弧中的預留位置值替換為您自己的值，並使用前面示例中定義的變數。

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>禁用客戶管理的金鑰

禁用客戶管理的金鑰時，您的存儲帳戶將再次使用 Microsoft 管理的金鑰進行加密。 要禁用客戶管理的金鑰，請調用[az 存儲帳戶更新](/cli/azure/storage/account#az-storage-account-update)並將`--encryption-key-source parameter``Microsoft.Storage`設置為 ，如以下示例所示。 請記住將括弧中的預留位置值替換為您自己的值，並使用前面示例中定義的變數。

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>後續步驟

- [靜態資料的 Azure 存儲加密](storage-service-encryption.md) 
- [什麼是 Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
