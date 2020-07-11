---
title: 建立支援資料表和佇列之客戶管理金鑰的帳戶
titleSuffix: Azure Storage
description: 瞭解如何建立儲存體帳戶，以支援為數據表和佇列設定客戶管理的金鑰。 使用 Azure CLI 或 Azure Resource Manager 範本，建立依賴帳戶加密金鑰進行 Azure 儲存體加密的儲存體帳戶。 接著，您可以設定帳戶的客戶管理金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: bf7d72e6f16605827b55e3a460a9b28010842d2f
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86220825"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>建立支援資料表和佇列之客戶管理金鑰的帳戶

Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，佇列儲存體和資料表儲存體會使用範圍為服務且由 Microsoft 管理的金鑰。 您也可以選擇使用客戶管理的金鑰來加密佇列或資料表資料。 若要使用客戶管理的金鑰搭配佇列和資料表，您必須先建立一個儲存體帳戶，以使用範圍限定在帳戶的加密金鑰，而不是服務。 建立使用佇列和資料表資料之帳戶加密金鑰的帳戶之後，您可以使用該儲存體帳戶的 Azure Key Vault 來設定客戶管理的金鑰。

本文說明如何建立儲存體帳戶，其依賴以帳戶為範圍的金鑰。 第一次建立帳戶時，Microsoft 會使用帳戶金鑰來加密帳戶中的資料，而 Microsoft 會管理金鑰。 接著，您可以為帳戶設定客戶管理的金鑰，以利用這些權益，包括提供您自己的金鑰、更新金鑰版本、輪替金鑰，以及撤銷存取控制等功能。

## <a name="about-the-feature"></a>關於功能

若要建立的儲存體帳戶會依賴佇列和資料表儲存體的帳戶加密金鑰，您必須先註冊，才能在 Azure 中使用此功能。 由於容量有限，請注意，可能需要幾個月的時間，才會核准要求的存取權。

您可以針對下欄區域中的佇列和表格儲存體，建立依賴帳戶加密金鑰的儲存體帳戶：

- 美國東部
- 美國中南部
- 美國西部 2  

### <a name="register-to-use-the-account-encryption-key"></a>註冊以使用帳戶加密金鑰

若要註冊以使用帳戶加密金鑰搭配佇列或表格儲存體，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要向 PowerShell 註冊，請呼叫[AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要向 Azure CLI 註冊，請呼叫[az feature register](/cli/azure/feature#az-feature-register)命令。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

### <a name="check-the-status-of-your-registration"></a>檢查註冊的狀態

若要檢查佇列或資料表儲存體的註冊狀態，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 檢查註冊的狀態，請呼叫[AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 檢查註冊的狀態，請呼叫[az feature](/cli/azure/feature#az-feature-show)命令。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

### <a name="re-register-the-azure-storage-resource-provider"></a>重新註冊 Azure 儲存體資源提供者

在您的註冊核准之後，您必須重新註冊 Azure 儲存體資源提供者。 使用 PowerShell 或 Azure CLI 重新註冊資源提供者。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 重新註冊資源提供者，請呼叫[register-azresourceprovider](/powershell/module/az.resources/register-azresourceprovider)命令。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 重新註冊資源提供者，請呼叫[az provider register](/cli/azure/provider#az-provider-register)命令。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>建立使用帳戶加密金鑰的帳戶

您必須設定新的儲存體帳戶，以便在建立儲存體帳戶時，針對佇列和資料表使用帳戶加密金鑰。 建立帳戶之後，就無法變更加密金鑰的範圍。

儲存體帳戶的類型必須是一般用途 v2。 您可以建立儲存體帳戶，並將它設定為依賴帳戶加密金鑰，方法是使用 Azure CLI 或 Azure Resource Manager 範本。

> [!NOTE]
> 只有在建立儲存體帳戶時，才可以選擇性地將佇列和資料表儲存體設定為使用帳戶加密金鑰來加密資料。 Blob 儲存體和 Azure 檔案儲存體一律使用帳戶加密金鑰來加密資料。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 來建立依賴帳戶加密金鑰的儲存體帳戶，請確定您已安裝 Azure PowerShell 模組（3.4.0 或更新版本）。 如需詳細資訊，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

接下來，使用適當的參數呼叫[new-azstorageaccount](/powershell/module/az.storage/new-azstorageaccount)命令，以建立一般用途 v2 儲存體帳戶：

- 包含 `-EncryptionKeyTypeForQueue` 選項，並將其值設定為 `Account` ，以使用帳戶加密金鑰來加密佇列儲存體中的資料。
- 包含 `-EncryptionKeyTypeForTable` 選項，並將其值設定為 `Account` ，以使用帳戶加密金鑰來加密資料表儲存體中的資料。

下列範例示範如何建立一般用途 v2 儲存體帳戶，其設定為讀取權限異地多餘儲存體 (GRS) ，並使用帳戶加密金鑰來加密佇列和資料表儲存體的資料。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 來建立依賴帳戶加密金鑰的儲存體帳戶，請確定您已安裝 Azure CLI 版2.0.80 或更新版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

接下來，使用適當的參數來呼叫[az storage account create](/cli/azure/storage/account#az-storage-account-create)命令，以建立一般用途 v2 儲存體帳戶：

- 包含 `--encryption-key-type-for-queue` 選項，並將其值設定為 `Account` ，以使用帳戶加密金鑰來加密佇列儲存體中的資料。
- 包含 `--encryption-key-type-for-table` 選項，並將其值設定為 `Account` ，以使用帳戶加密金鑰來加密資料表儲存體中的資料。

下列範例示範如何建立一般用途 v2 儲存體帳戶，其設定為讀取權限異地多餘儲存體 (GRS) ，並使用帳戶加密金鑰來加密佇列和資料表儲存體的資料。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[範本](#tab/template)

下列 JSON 範例會建立一般用途 v2 儲存體帳戶，其設定為讀取權限異地多餘儲存體 (GRS) ，並使用帳戶加密金鑰來加密佇列和資料表儲存體的資料。 請記得以您自己的值取代角括弧中的預留位置值：

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

建立依賴帳戶加密金鑰的帳戶之後，請參閱下列其中一篇文章，使用 Azure Key Vault 設定客戶管理的金鑰：

- [使用 Azure 入口網站以 Azure Key Vault 設定客戶管理的金鑰](storage-encryption-keys-portal.md)
- [使用 PowerShell 以 Azure Key Vault 設定客戶管理的金鑰](storage-encryption-keys-powershell.md)
- [使用 Azure CLI 以 Azure Key Vault 設定客戶管理的金鑰](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>確認帳戶加密金鑰

若要確認儲存體帳戶中的服務是否使用帳戶加密金鑰，請呼叫 Azure CLI [az storage account](/cli/azure/storage/account#az-storage-account-show)命令。 此命令會傳回一組儲存體帳戶屬性和其值。 在 `keyType` [加密] 屬性中尋找每個服務的欄位，並確認其設定為 `Account` 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要確認儲存體帳戶中的服務是否使用帳戶加密金鑰，請呼叫[new-azstorageaccount](/powershell/module/az.storage/get-azstorageaccount)命令。 此命令會傳回一組儲存體帳戶屬性和其值。 在 `KeyType` 屬性中尋找每個服務的欄位 `Encryption` ，並確認其設定為 `Account` 。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要確認儲存體帳戶中的服務是否使用帳戶加密金鑰，請呼叫[az storage account show](/cli/azure/storage/account#az-storage-account-show)命令。 此命令會傳回一組儲存體帳戶屬性和其值。 在 `keyType` [加密] 屬性中尋找每個服務的欄位，並確認其設定為 `Account` 。

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md) 
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
