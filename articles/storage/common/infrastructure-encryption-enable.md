---
title: 建立已啟用基礎結構加密的儲存體帳戶，以進行資料的雙重加密
titleSuffix: Azure Storage
description: 需要更高層級保證其資料安全的客戶也可以在 Azure 儲存體基礎結構層級啟用256位 AES 加密。 啟用基礎結構加密時，儲存體帳戶中的資料會使用兩個不同的加密演算法和兩個不同的金鑰來加密兩次。
services: storage
author: tamram
ms.service: storage
ms.date: 07/08/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: edeb184af1c1260a456ed3de7064805526629de8
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225151"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>建立已啟用基礎結構加密的儲存體帳戶，以進行資料的雙重加密

Azure 儲存體會使用256位 AES 加密（可用的最強區塊密碼之一），在服務層級自動加密儲存體帳戶中的所有資料，且符合 FIPS 140-2 規範。 需要更高層級保證其資料安全的客戶也可以在 Azure 儲存體基礎結構層級啟用256位 AES 加密。 啟用基礎結構加密時，儲存體帳戶中的資料會在服務層級進行兩次加密一次， &mdash; 而在基礎結構層級上 &mdash; 使用兩個不同的加密演算法和兩個不同的金鑰一次。 Azure 儲存體資料的雙重加密可防範其中一個加密演算法或金鑰可能遭到入侵的案例。 在此案例中，額外一層加密會繼續保護您的資料。

服務層級加密支援使用 Microsoft 管理的金鑰或客戶管理的金鑰搭配 Azure Key Vault。 基礎結構層級加密依賴 Microsoft 管理的金鑰，而且一律使用個別的金鑰。 如需有關使用 Azure 儲存體加密進行金鑰管理的詳細資訊，請參閱[關於加密金鑰管理](storage-service-encryption.md#about-encryption-key-management)。

若要對您的資料進行雙重加密，您必須先建立針對基礎結構加密所設定的儲存體帳戶。 本文說明如何建立可啟用基礎結構加密的儲存體帳戶。

## <a name="about-the-feature"></a>關於功能

若要建立已啟用基礎結構加密的儲存體帳戶，您必須先註冊，才能在 Azure 中使用此功能。 由於容量有限，請注意，可能需要幾個月的時間，才會核准要求的存取權。

您可以在下欄區域中，建立已啟用基礎結構加密的儲存體帳戶：

- 美國東部
- 美國中南部
- 美國西部 2

### <a name="register-to-use-infrastructure-encryption"></a>註冊以使用基礎結構加密

若要註冊使用 Azure 儲存體的基礎結構加密，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要向 PowerShell 註冊，請呼叫[AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature)命令。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要向 Azure CLI 註冊，請呼叫[az feature register](/cli/azure/feature#az-feature-register)命令。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

### <a name="check-the-status-of-your-registration"></a>檢查註冊的狀態

若要檢查基礎結構加密的註冊狀態，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 檢查註冊的狀態，請呼叫[AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature)命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 檢查註冊的狀態，請呼叫[az feature](/cli/azure/feature#az-feature-show)命令。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
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

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>建立已啟用基礎結構加密的帳戶

您必須在建立帳戶時，將儲存體帳戶設定為使用基礎結構加密。 建立帳戶之後，無法啟用或停用基礎結構加密。

儲存體帳戶的類型必須是一般用途 v2。 您可以建立儲存體帳戶，並將它設定為使用 PowerShell、Azure CLI 或 Azure Resource Manager 範本來啟用基礎結構加密。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 來建立已啟用基礎結構加密的儲存體帳戶，請確定您已安裝[Az. Storage PowerShell 模組](https://www.powershellgallery.com/packages/Az.Storage)（2.2.0 或更新版本）。 如需詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps) (英文)。

接下來，呼叫[new-azstorageaccount](/powershell/module/az.storage/new-azstorageaccount)命令來建立一般用途 v2 儲存體帳戶。 包含 `-RequireInfrastructureEncryption` 啟用基礎結構加密的選項。

下列範例示範如何建立一般用途 v2 儲存體帳戶，其已設定為讀取權限異地冗余儲存體 (GRS) ，並已啟用基礎結構加密來進行資料的雙重加密。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 來建立已啟用基礎結構加密的儲存體帳戶，請確定您已安裝 Azure CLI 版本2.8.0 或更新版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

接下來，藉由呼叫[az storage account create](/cli/azure/storage/account#az-storage-account-create)命令來建立一般用途 v2 儲存體帳戶，並包含 `--require-infrastructure-encryption option` 以啟用基礎結構加密。

下列範例示範如何建立一般用途 v2 儲存體帳戶，其已設定為讀取權限異地冗余儲存體 (GRS) ，並已啟用基礎結構加密來進行資料的雙重加密。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[範本](#tab/template)

下列 JSON 範例會建立一般用途 v2 儲存體帳戶，其設定為讀取權限異地冗余儲存體 (GRS) ，並已啟用基礎結構加密來進行資料的雙重加密。 請記得以您自己的值取代括弧中的預留位置值：

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>確認已啟用基礎結構加密

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要確認已針對儲存體帳戶啟用基礎結構加密，請呼叫[new-azstorageaccount](/powershell/module/az.storage/get-azstorageaccount)命令。 此命令會傳回一組儲存體帳戶屬性和其值。 抓取 `RequireInfrastructureEncryption` 屬性中的欄位 `Encryption` ，並確認其設定為 `True` 。

下列範例會抓取屬性的值 `RequireInfrastructureEncryption` 。 請記得以您自己的值取代角括弧中的預留位置值：

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要確認已針對儲存體帳戶啟用基礎結構加密，請呼叫[az storage account show](/cli/azure/storage/account#az-storage-account-show)命令。 此命令會傳回一組儲存體帳戶屬性和其值。 尋找 `requireInfrastructureEncryption` 屬性中的欄位 `encryption` ，並確認其設定為 `true` 。

下列範例會抓取屬性的值 `requireInfrastructureEncryption` 。 請記得以您自己的值取代角括弧中的預留位置值：

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
- [搭配 Azure Key Vault 使用客戶管理的金鑰來管理 Azure 儲存體加密](encryption-customer-managed-keys.md)