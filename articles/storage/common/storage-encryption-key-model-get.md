---
title: 確定存儲帳戶正在使用哪個加密金鑰模型
titleSuffix: Azure Storage
description: 使用 Azure 門戶、PowerShell 或 Azure CLI 檢查如何管理存儲帳戶的加密金鑰。 金鑰可能由 Microsoft（預設）或客戶管理。 客戶管理的金鑰必須存儲在 Azure 金鑰保存庫中。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409845"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>確定存儲帳戶正在使用哪種 Azure 存儲加密金鑰模型

存儲帳戶中的資料由 Azure 存儲自動加密。 Azure 存儲加密提供了兩種在存儲帳戶級別管理加密金鑰的選項：

- **微軟管理的金鑰。** 預設情況下，Microsoft 管理用於加密存儲帳戶的金鑰。
- **客戶管理的金鑰。** 您可以選擇管理存儲帳戶的加密金鑰。 客戶管理的金鑰必須存儲在 Azure 金鑰保存庫中。

此外，您可以在某些 Blob 存儲操作的單個請求級別提供加密金鑰。 在請求上指定加密金鑰時，該金鑰將覆蓋存儲帳戶上處於活動狀態的加密金鑰。 有關詳細資訊，請參閱[在請求 Blob 存儲時指定客戶提供的金鑰](../blobs/storage-blob-customer-provided-key.md)。

有關加密金鑰的詳細資訊，請參閱[靜態資料的 Azure 存儲加密](storage-service-encryption.md)。

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>檢查存儲帳戶的加密金鑰模型

要確定存儲帳戶是使用 Microsoft 管理的金鑰還是客戶管理的金鑰進行加密，請使用以下方法之一。

# <a name="azure-portal"></a>[Azure 門戶](#tab/portal)

要使用 Azure 門戶檢查存儲帳戶的加密模型，請按照以下步驟操作：

1. 在 Azure 入口網站中，瀏覽至您的儲存體帳戶。
1. 選擇 **"加密**"設置並記下該設置。

下圖顯示了使用 Microsoft 管理的金鑰加密的存儲帳戶：

![使用 Microsoft 管理的金鑰加密的帳戶](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

下圖顯示了使用客戶管理的金鑰加密的存儲帳戶：

![顯示 Azure 門戶中加密金鑰設置的螢幕截圖](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[電源外殼](#tab/powershell)

要使用 PowerShell 檢查存儲帳戶的加密模型，請調用[Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount)命令，然後檢查該帳戶的**KeySource**屬性。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

如果**KeySource**屬性的值是`Microsoft.Storage`，則帳戶將使用 Microsoft 管理的金鑰進行加密。 如果**KeySource**屬性的值是`Microsoft.Keyvault`，則帳戶將使用客戶管理的金鑰進行加密。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

要使用 Azure CLI 檢查存儲帳戶的加密模型，請調用[az 存儲帳戶顯示](/cli/azure/storage/account#az-storage-account-show)命令，然後檢查該帳戶的**金鑰源**屬性。

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

如果**keySource**屬性的值是`Microsoft.Storage`，則帳戶將使用 Microsoft 管理的金鑰進行加密。 如果**keySource**屬性的值是`Microsoft.Keyvault`，則帳戶將使用客戶管理的金鑰進行加密。

---

## <a name="next-steps"></a>後續步驟

- [靜態資料的 Azure 存儲加密](storage-service-encryption.md)
- [使用具有 Azure 金鑰保存庫的客戶管理金鑰來管理 Azure 存儲加密](encryption-customer-managed-keys.md)