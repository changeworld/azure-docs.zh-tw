---
title: 使用 Azure CLI 來設定客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure CLI，以 Azure 儲存體加密的 Azure Key Vault 來設定客戶管理的金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: fcb4636263843143e685de2e3d2a27bf87cc5a90
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137402"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>使用 Azure CLI 以 Azure Key Vault 設定客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用 Azure CLI，以客戶管理的金鑰設定 Azure Key Vault。 若要瞭解如何使用 Azure CLI 建立金鑰保存庫，請參閱[快速入門：使用 Azure CLI 從 Azure Key Vault 設定和取出秘密](../../key-vault/quick-create-cli.md)。

## <a name="assign-an-identity-to-the-storage-account"></a>將身分識別指派給儲存體帳戶

若要為您的儲存體帳戶啟用客戶管理的金鑰，請先將系統指派的受控識別指派給儲存體帳戶。 您將使用此受控識別來授與儲存體帳戶存取金鑰保存庫的許可權。

若要使用 Azure CLI 指派受控識別，請呼叫[az storage account update](/cli/azure/storage/account#az-storage-account-update)。 請記得以您自己的值取代括弧中的預留位置值。

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

如需有關使用 Azure CLI 設定系統指派的受控識別的詳細資訊，請參閱[使用 Azure CLI 在 AZURE VM 上設定 azure 資源的受控](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)識別。

## <a name="create-a-new-key-vault"></a>建立新的金鑰保存庫

您用來儲存 Azure 儲存體加密客戶管理金鑰的金鑰保存庫，必須啟用兩個金鑰保護設定： [虛**刪除**] 和 [不要**清除**]。 若要在啟用這些設定的情況下使用 PowerShell 或 Azure CLI 建立新的金鑰保存庫，請執行下列命令。 請記得以您自己的值取代括弧中的預留位置值。

若要使用 Azure CLI 建立新的金鑰保存庫，請呼叫[az keyvault create](/cli/azure/keyvault#az-keyvault-create)。 請記得以您自己的值取代括弧中的預留位置值。

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

若要瞭解如何使用 Azure CLI 在現有的金鑰保存庫上啟用「虛**刪除**」和「不要**清除**」，請參閱[如何搭配使用虛刪除與 CLI](../../key-vault/key-vault-soft-delete-cli.md)中的 < 啟用虛**刪除**和**啟用清除保護**的章節。

## <a name="configure-the-key-vault-access-policy"></a>設定 key vault 存取原則

接下來，設定金鑰保存庫的存取原則，讓儲存體帳戶有權存取它。 在此步驟中，您將使用先前指派給儲存體帳戶的受控識別。

若要設定金鑰保存庫的存取原則，請呼叫[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy)。 請記得以您自己的值取代括弧中的預留位置值。

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

接下來，在金鑰保存庫中建立金鑰。 若要建立金鑰，請呼叫[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)。 請記得以您自己的值取代括弧中的預留位置值。

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

根據預設，Azure 儲存體加密會使用 Microsoft 管理的金鑰。 為客戶管理的金鑰設定您的 Azure 儲存體帳戶，並指定要與儲存體帳戶建立關聯的金鑰。

若要更新儲存體帳戶的加密設定，請呼叫[az storage account update](/cli/azure/storage/account#az-storage-account-update)，如下列範例所示。 包含 `--encryption-key-source` 參數，並將它設定為 `Microsoft.Keyvault`，以啟用儲存體帳戶的客戶管理金鑰。 此範例也會查詢金鑰保存庫 URI 和最新的金鑰版本，這兩個值都需要用來將金鑰與儲存體帳戶建立關聯。 請記得以您自己的值取代括弧中的預留位置值。

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

當您建立新版本的金鑰時，您必須更新儲存體帳戶，才能使用新版本。 首先，藉由呼叫 az [keyvault show](/cli/azure/keyvault#az-keyvault-show)，並針對金鑰版本查詢金鑰保存庫 URI，方法是呼叫[az keyvault key list-版本](/cli/azure/keyvault/key#az-keyvault-key-list-versions)。 然後呼叫[az storage account update](/cli/azure/storage/account#az-storage-account-update)來更新儲存體帳戶的加密設定，以使用新版本的金鑰，如上一節所示。

## <a name="use-a-different-key"></a>使用不同的金鑰

若要變更 Azure 儲存體加密所使用的金鑰，請呼叫[az Storage account update](/cli/azure/storage/account#az-storage-account-update) ，如使用[客戶管理的金鑰設定加密](#configure-encryption-with-customer-managed-keys)中所示，並提供新的金鑰名稱和版本。 如果新的金鑰位於不同的金鑰保存庫中，請同時更新金鑰保存庫 URI。

## <a name="revoke-customer-managed-keys"></a>撤銷客戶管理的金鑰

如果您認為金鑰可能遭到入侵，您可以藉由移除 key vault 存取原則來撤銷客戶管理的金鑰。 若要撤銷客戶管理的金鑰，請呼叫[az keyvault delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy)命令，如下列範例所示。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>停用客戶管理的金鑰

當您停用客戶管理的金鑰時，您的儲存體帳戶會再次使用 Microsoft 管理的金鑰進行加密。 若要停用客戶管理的金鑰，請呼叫[az storage account update](/cli/azure/storage/account#az-storage-account-update) ，並將 `--encryption-key-source parameter` 設定為 `Microsoft.Storage`，如下列範例所示。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md) 
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
