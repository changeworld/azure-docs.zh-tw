---
title: 建立已啟用基礎結構加密的儲存體帳戶，以進行資料的雙重加密
titleSuffix: Azure Storage
description: 需要更高等級保證其資料安全的客戶，也可以在 Azure 儲存體基礎結構層級啟用256位 AES 加密。 啟用基礎結構加密時，儲存體帳戶中的資料會使用兩個不同的加密演算法和兩個不同的金鑰來加密兩次。
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 612ba18ba71a22ad6c346b26008e688195c1d1e4
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746572"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>建立已啟用基礎結構加密的儲存體帳戶，以進行資料的雙重加密

Azure 儲存體會使用256位 AES 加密（可用的最強區塊編碼器之一），自動加密儲存體帳戶中的所有資料，且符合 FIPS 140-2 規範。 需要更高等級保證其資料安全的客戶，也可以在 Azure 儲存體基礎結構層級啟用256位 AES 加密。 啟用基礎結構加密時，儲存體帳戶中的資料會在服務層級進行兩次加密一次， &mdash; 並在基礎結構層級進行一次加密， &mdash; 並具有兩個不同的加密演算法和兩個不同的金鑰 Azure 儲存體資料的雙重加密可防止其中一個加密演算法或金鑰可能遭到入侵的案例。 在此案例中，額外的加密層級會繼續保護您的資料。

服務層級加密支援使用 Microsoft 管理的金鑰或客戶管理的金鑰搭配 Azure Key Vault 或 Key Vault 受控硬體安全性模型 (HSM)  (preview) 。 基礎結構層級的加密相依于 Microsoft 管理的金鑰，而且一律使用個別的金鑰。 如需有關使用 Azure 儲存體加密進行金鑰管理的詳細資訊，請參閱 [關於加密金鑰管理](storage-service-encryption.md#about-encryption-key-management)。

若要對您的資料進行雙向加密，您必須先建立針對基礎結構加密設定的儲存體帳戶。 本文說明如何建立可啟用基礎結構加密的儲存體帳戶。

## <a name="register-to-use-infrastructure-encryption"></a>註冊以使用基礎結構加密

若要建立已啟用基礎結構加密的儲存體帳戶，您必須先註冊，才能使用 PowerShell 或 Azure CLI 來搭配 Azure 使用這項功能。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要向 PowerShell 註冊，請呼叫 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 命令。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

若要使用 PowerShell 檢查註冊的狀態，請呼叫 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

註冊經過核准之後，您必須重新註冊 Azure 儲存體資源提供者。 若要使用 PowerShell 重新註冊資源提供者，請呼叫 [>register-azresourceprovider](/powershell/module/az.resources/register-azresourceprovider) 命令。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要向 Azure CLI 註冊，請呼叫 [az feature register](/cli/azure/feature#az-feature-register) 命令。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

若要檢查 Azure CLI 的註冊狀態，請呼叫 [az feature](/cli/azure/feature#az-feature-show) 命令。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

註冊經過核准之後，您必須重新註冊 Azure 儲存體資源提供者。 若要使用 Azure CLI 重新註冊資源提供者，請呼叫 [az provider register](/cli/azure/provider#az-provider-register) 命令。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[範本](#tab/template)

N/A

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>建立已啟用基礎結構加密的帳戶

您必須在建立帳戶時，將儲存體帳戶設定為使用基礎結構加密。 儲存體帳戶的類型必須是一般用途 v2。

建立帳戶之後，無法啟用或停用基礎結構加密。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 PowerShell 來建立啟用基礎結構加密的儲存體帳戶，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至 [ **儲存體帳戶** ] 頁面。
1. 選擇 [ **新增] 按鈕以** 新增一般用途 v2 儲存體帳戶。
1. 在 [ **Advanced** ] 索引標籤上，找出 **基礎結構** 加密，然後選取 [ **啟用** ]。
1. 選取 [ **審核 + 建立** ] 以完成建立儲存體帳戶。

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="顯示如何在建立帳戶時啟用基礎結構加密的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 來建立啟用基礎結構加密的儲存體帳戶，請確定您已安裝 [Az PowerShell 模組](https://www.powershellgallery.com/packages/Az.Storage)（版本2.2.0 或更新版本）。 如需詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps) (英文)。

接下來，呼叫 [new-azstorageaccount](/powershell/module/az.storage/new-azstorageaccount) 命令以建立一般用途 v2 儲存體帳戶。 包含 `-RequireInfrastructureEncryption` 啟用基礎結構加密的選項。

下列範例示範如何建立一般用途 v2 儲存體帳戶，此帳戶已設定為讀取權限異地冗余儲存體 (GRS 的) ，並已啟用對資料進行雙重加密的基礎結構加密。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 建立已啟用基礎結構加密的儲存體帳戶，請確定您已安裝 Azure CLI 2.8.0 版或更新版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

接下來，呼叫 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令以建立一般用途 v2 儲存體帳戶，並包含 `--require-infrastructure-encryption option` 以啟用基礎結構加密。

下列範例示範如何建立一般用途 v2 儲存體帳戶，此帳戶已設定為讀取權限異地冗余儲存體 (GRS 的) ，並已啟用對資料進行雙重加密的基礎結構加密。 請記得以您自己的值取代括弧中的預留位置值：

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

下列 JSON 範例會建立一般用途 v2 儲存體帳戶，此帳戶已設定為可供讀取權限異地冗余儲存體 (GRS 的) ，並已針對資料的雙重加密啟用基礎結構加密。 請記得以您自己的值取代括弧中的預留位置值：

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

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要確認是否已針對具有 Azure 入口網站的儲存體帳戶啟用基礎結構加密，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在 [ **設定** ] 底下，選擇 [ **加密** ]。

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="顯示如何在建立帳戶時啟用基礎結構加密的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要確認已針對具有 PowerShell 的儲存體帳戶啟用基礎結構加密，請呼叫 [new-azstorageaccount](/powershell/module/az.storage/get-azstorageaccount) 命令。 此命令會傳回一組儲存體帳戶屬性及其值。 取出 `RequireInfrastructureEncryption` 屬性中的欄位 `Encryption` ，並確認它已設定為 `True` 。

下列範例會捕獲屬性的值 `RequireInfrastructureEncryption` 。 請記得將角括弧中的預留位置值取代為您自己的值：

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要確認已針對具有 Azure CLI 的儲存體帳戶啟用基礎結構加密，請呼叫 [az storage account show](/cli/azure/storage/account#az-storage-account-show) 命令。 此命令會傳回一組儲存體帳戶屬性及其值。 尋找 `requireInfrastructureEncryption` 屬性中的欄位 `encryption` ，並確認它已設定為 `true` 。

下列範例會捕獲屬性的值 `requireInfrastructureEncryption` 。 請記得將角括弧中的預留位置值取代為您自己的值：

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
- [Azure 儲存體加密的客戶管理金鑰](customer-managed-keys-overview.md)
