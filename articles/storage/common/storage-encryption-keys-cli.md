---
title: 使用 Azure CLI 設定客戶管理的金鑰
titleSuffix: Azure Storage
description: 瞭解如何使用 Azure CLI 使用 Azure 密鑰保管庫配置客戶管理的密鑰以進行 Azure 儲存加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 796e3b3f46bc83b776826baf6e078c696eda543b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456766"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>使用 Azure CLI 使用 Azure 金鑰保管庫設定客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文展示如何使用 Azure CLI 使用客戶管理的密鑰配置 Azure 密鑰保管庫。 若要瞭解如何使用 Azure CLI 建立金鑰保管庫,請參閱[快速入門:使用 Azure CLI 從 Azure 金鑰保存庫設定與檢索機密](../../key-vault/secrets/quick-create-cli.md)。

## <a name="assign-an-identity-to-the-storage-account"></a>將識別配置給儲存帳戶

要為存儲帳戶啟用客戶管理的密鑰,請先將系統分配的託管標識分配給存儲帳戶。 您將使用此託管標識授予存取金鑰保管庫的儲存帳戶許可權。

要使用 Azure CLI 分配託管識別,請呼叫[az 儲存帳戶更新](/cli/azure/storage/account#az-storage-account-update)。 請記住將括弧中的占位符值替換為您自己的值。

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

有關使用 Azure CLI 設定系統分配的託管識別的詳細資訊,請參閱使用[Azure CLI 在 Azure VM 上設定 Azure 資源的託管識別](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)。

## <a name="create-a-new-key-vault"></a>建立新金鑰保存庫

用於存儲 Azure 儲存加密的客戶管理金鑰的金鑰保管庫必須啟用兩個金鑰保護設置,**即「軟刪除****」和「不清除**」 。 要使用啟用這些設置的 PowerShell 或 Azure CLI 創建新密鑰保管庫,請執行以下命令。 請記住將括弧中的占位符值替換為您自己的值。

要使用 Azure CLI 建立新金鑰保存,請呼叫[az 金鑰保存 。](/cli/azure/keyvault#az-keyvault-create) 請記住將括弧中的占位符值替換為您自己的值。

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

要瞭解如何使用 Azure CLI 在現有金鑰保存式庫中啟用 **「軟刪除****」與「不清除」** 請參考標題為 **「啟用軟刪除**與**開啟清除保護**」的部分,瞭解如何[使用 CLI 使用軟刪除](../../key-vault/general/soft-delete-cli.md)。

## <a name="configure-the-key-vault-access-policy"></a>設定金鑰保存庫存取原則

接下來,為金鑰保管庫配置訪問策略,以便存儲帳戶具有訪問它的許可權。 在此步驟中,您將使用以前分配給存儲帳戶的託管標識。

要設定金鑰保存庫的存取政策,請呼叫[az 金鑰保管庫集原則](/cli/azure/keyvault#az-keyvault-set-policy)。 請記住將括弧中的占位符值替換為您自己的值。

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

## <a name="create-a-new-key"></a>建立新的金鑰

接下來,在密鑰保管庫中創建一個密鑰。 要建立金鑰,請呼叫[az 金鑰庫金鑰建立](/cli/azure/keyvault/key#az-keyvault-key-create)。 請記住將括弧中的占位符值替換為您自己的值。

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

Azure 儲存加密僅支援 2048 位 RSA 和 RSA-HSM 密鑰。 關於金鑰的詳細資訊,請參考[「關於 Azure 金鑰保管庫金鑰、機密和憑證」](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)的**金鑰保管庫金鑰**。

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

默認情況下,Azure 儲存加密使用Microsoft管理的密鑰。 為客戶管理的密鑰配置 Azure 儲存帳戶,並指定要與存儲帳戶關聯的金鑰。

要更新存儲帳戶的加密設置,請調用[az 儲存帳戶更新](/cli/azure/storage/account#az-storage-account-update),如以下範例所示。 包括參數`--encryption-key-source`並將其設置`Microsoft.Keyvault`為 ,以啟用儲存帳戶的客戶管理金鑰。 該示例還查詢金鑰保管庫 URI 和最新密鑰版本,這兩個值都需要將密鑰與存儲帳戶關聯。 請記住將括弧中的占位符值替換為您自己的值。

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

建立金鑰的新版本時,需要更新存儲帳戶才能使用新版本。 首先,通過調用[az 密鑰保管庫顯示](/cli/azure/keyvault#az-keyvault-show)來查詢密鑰保管庫 URI,並通過調用[az 密鑰庫金鑰清單版本](/cli/azure/keyvault/key#az-keyvault-key-list-versions)查詢密鑰版本。 然後調用[az 儲存帳戶更新](/cli/azure/storage/account#az-storage-account-update)以更新存儲帳戶的加密設置以使用密鑰的新版本,如上一節所示。

## <a name="use-a-different-key"></a>使用其他鍵

要更改用於 Azure 儲存加密的金鑰,請呼叫[az 儲存帳戶更新](/cli/azure/storage/account#az-storage-account-update),如[使用客戶管理的金鑰設定加密](#configure-encryption-with-customer-managed-keys)並提供新的金鑰名稱和版本。 如果新密鑰位於其他密鑰保管庫中,則還要更新密鑰保管庫URI。

## <a name="revoke-customer-managed-keys"></a>復原客戶管理的金鑰

如果您認為密鑰可能已洩露,則可以通過刪除密鑰保管庫訪問策略來撤銷客戶管理的金鑰。 要復原客戶管理的金鑰,請呼叫[az 金鑰庫刪除策略](/cli/azure/keyvault#az-keyvault-delete-policy)命令,如以下範例所示。 請記住將括弧中的占位符值替換為您自己的值,並使用前面示例中定義的變數。

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>關閉客戶管理的金鑰

禁用客戶管理的密鑰時,您的儲存帳戶將再次使用 Microsoft 管理的密鑰進行加密。 要禁用客戶管理的密鑰,請調用[az 儲存帳戶更新](/cli/azure/storage/account#az-storage-account-update)並將`--encryption-key-source parameter``Microsoft.Storage`設置為 ,如以下範例所示。 請記住將括弧中的占位符值替換為您自己的值,並使用前面示例中定義的變數。

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>後續步驟

- [靜態資料的 Azure 儲存加密](storage-service-encryption.md) 
- [什麼是 Azure 金鑰保存嗎](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
