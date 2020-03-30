---
title: 創建支援表和佇列的客戶管理金鑰的帳戶
titleSuffix: Azure Storage
description: 瞭解如何創建支援為表和佇列配置客戶管理的金鑰的存儲帳戶。 使用 Azure CLI 或 Azure 資源管理器範本創建依賴于 Azure 存儲加密的帳戶加密金鑰的存儲帳戶。 然後，您可以為帳戶配置客戶管理的金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083563"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>創建支援表和佇列的客戶管理金鑰的帳戶

Azure 存儲在靜態存儲帳戶中加密所有資料。 預設情況下，佇列存儲和表存儲使用限定為服務並由 Microsoft 管理的金鑰。 您還可以選擇使用客戶管理的金鑰來加密佇列或表資料。 要將客戶管理的金鑰用於佇列和表，必須首先創建一個存儲帳戶，該存儲帳戶使用限定到帳戶的加密金鑰，而不是服務。 創建對佇列和表資料使用帳戶加密金鑰的帳戶後，可以使用該存儲帳戶的 Azure 金鑰保存庫配置客戶管理的金鑰。

本文介紹如何創建依賴于限定到該帳戶的金鑰的存儲帳戶。 首次創建帳戶時，Microsoft 使用帳戶金鑰組帳戶中的資料進行加密，Microsoft 管理該金鑰。 隨後，您可以為帳戶配置客戶管理的金鑰，以利用這些優勢，包括提供您自己的金鑰、更新金鑰版本、旋轉金鑰和撤銷存取控制的能力。

## <a name="about-the-feature"></a>關於功能

要創建依賴于佇列和表存儲的帳戶加密金鑰的存儲帳戶，必須首先註冊以將此功能與 Azure 一起使用。 由於容量有限，請注意，可能需要幾個月才能批准訪問請求。

您可以創建一個存儲帳戶，該帳戶依賴于以下區域中的佇列和表存儲的帳戶加密金鑰：

- 美國東部
- 美國中南部
- 美國西部 2  

### <a name="register-to-use-the-account-encryption-key"></a>註冊以使用帳戶加密金鑰

要註冊以將帳戶加密金鑰與佇列或表存儲一起使用，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[電源外殼](#tab/powershell)

要向 PowerShell 註冊，請調用[獲取-AzProviderFeature 命令](/powershell/module/az.resources/get-azproviderfeature)。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要向 Azure CLI 註冊，請調用[az 要素寄存器](/cli/azure/feature#az-feature-register)命令。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

### <a name="check-the-status-of-your-registration"></a>檢查註冊狀態

要檢查佇列或表存儲的註冊狀態，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[電源外殼](#tab/powershell)

要檢查您在 PowerShell 的註冊狀態，請致電[獲取-AzProvider功能](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要檢查 Azure CLI 註冊的狀態，請調用[az 要素](/cli/azure/feature#az-feature-show)命令。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

### <a name="re-register-the-azure-storage-resource-provider"></a>重新註冊 Azure 存儲資來源提供者

註冊獲得批准後，必須重新註冊 Azure 存儲資來源提供者。 使用 PowerShell 或 Azure CLI 重新註冊資來源提供者。

# <a name="powershell"></a>[電源外殼](#tab/powershell)

要使用 PowerShell 重新註冊資來源提供者，請調用[註冊-AzResourceProvider 命令](/powershell/module/az.resources/register-azresourceprovider)。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 重新註冊資來源提供者，請調用[az 提供程式寄存器](/cli/azure/provider#az-provider-register)命令。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>創建使用帳戶加密金鑰的帳戶

您必須配置新的存儲帳戶，以便在創建存儲帳戶時對佇列和表使用帳戶加密金鑰。 創建帳戶後，無法更改加密金鑰的範圍。

存儲帳戶的類型必須為通用 v2。 可以使用 Azure CLI 或 Azure 資源管理器範本創建存儲帳戶並將其配置為依賴帳戶加密金鑰。

> [!NOTE]
> 只能選擇佇列和表存儲來配置，以在創建存儲帳戶時使用帳戶加密金鑰加密資料。 Blob 存儲和 Azure 檔始終使用帳戶加密金鑰來加密資料。

# <a name="powershell"></a>[電源外殼](#tab/powershell)

要使用 PowerShell 創建依賴于帳戶加密金鑰的存儲帳戶，請確保已安裝 Azure PowerShell 模組（版本 3.4.0 或更高版本）。 有關詳細資訊，請參閱安裝[Azure PowerShell 模組](/powershell/azure/install-az-ps)。

接下來，通過調用[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)命令創建通用 v2 存儲帳戶，並具有相應的參數：

- 包括`-EncryptionKeyTypeForQueue`該選項並將其值`Account`設置為使用帳戶加密金鑰加密佇列存儲中的資料。
- 包括`-EncryptionKeyTypeForTable`該選項並將其值`Account`設置為使用帳戶加密金鑰加密表存儲中的資料。

下面的示例演示如何創建為讀取存取異地冗余存儲 （RA-GRS） 配置的通用 v2 存儲帳戶，並使用帳戶加密金鑰加密佇列和表存儲的資料。 請記住將括弧中的預留位置值替換為您自己的值：

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

要使用 Azure CLI 創建依賴于帳戶加密金鑰的存儲帳戶，請確保已安裝 Azure CLI 版本 2.0.80 或更高版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

接下來，通過調用[az 存儲帳戶創建](/cli/azure/storage/account#az-storage-account-create)命令創建具有相應參數，創建通用 v2 存儲帳戶：

- 包括`--encryption-key-type-for-queue`該選項並將其值`Account`設置為使用帳戶加密金鑰加密佇列存儲中的資料。
- 包括`--encryption-key-type-for-table`該選項並將其值`Account`設置為使用帳戶加密金鑰加密表存儲中的資料。

下面的示例演示如何創建為讀取存取異地冗余存儲 （RA-GRS） 配置的通用 v2 存儲帳戶，並使用帳戶加密金鑰加密佇列和表存儲的資料。 請記住將括弧中的預留位置值替換為您自己的值：

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

下面的 JSON 示例創建一個通用 v2 存儲帳戶，該帳戶配置為讀取存取異地冗余存儲 （RA-GRS），並使用帳戶加密金鑰加密佇列和表存儲的資料。 請記住將角度括弧中的預留位置值替換為您自己的值：

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

創建依賴于帳戶加密金鑰的帳戶後，請參閱以下一些文章，以便使用 Azure 金鑰保存庫配置客戶管理的金鑰：

- [使用 Azure 金鑰保存庫配置客戶管理的金鑰](storage-encryption-keys-portal.md)
- [使用 PowerShell 使用 Azure 金鑰保存庫配置客戶管理的金鑰](storage-encryption-keys-powershell.md)
- [使用 Azure CLI 使用 Azure 金鑰保存庫配置客戶管理的金鑰](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>驗證帳戶加密金鑰

要驗證存儲帳戶中的服務是否使用帳戶加密金鑰，請調用 Azure CLI [az 存儲帳戶命令](/cli/azure/storage/account#az-storage-account-show)。 此命令返回一組存儲帳戶屬性及其值。 查找加密屬性`keyType`中每個服務的欄位，並驗證其設置為`Account`。

# <a name="powershell"></a>[電源外殼](#tab/powershell)

要驗證存儲帳戶中的服務是否使用帳戶加密金鑰，請調用[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount)命令。 此命令返回一組存儲帳戶屬性及其值。 查找屬性中`KeyType`每個服務的`Encryption`欄位，並驗證其設置為`Account`。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要驗證存儲帳戶中的服務是否使用帳戶加密金鑰，請調用[az 存儲帳戶](/cli/azure/storage/account#az-storage-account-show)命令。 此命令返回一組存儲帳戶屬性及其值。 查找加密屬性`keyType`中每個服務的欄位，並驗證其設置為`Account`。

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

## <a name="next-steps"></a>後續步驟

- [靜態資料的 Azure 存儲加密](storage-service-encryption.md) 
- [什麼是 Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
