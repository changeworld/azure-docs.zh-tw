---
title: 管理帳戶存取金鑰
titleSuffix: Azure Storage
description: 瞭解如何查看、管理及輪替您的儲存體帳戶存取金鑰。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.openlocfilehash: 4ade2c2e60373298eecf4e85df7fffeae4f45207
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82176614"
---
# <a name="manage-storage-account-access-keys"></a>管理儲存體帳戶存取金鑰

您建立儲存體帳戶時，Azure 會產生兩個 512 位元儲存體帳戶存取金鑰。 這些金鑰可用來透過共用金鑰授權，授權存取您儲存體帳戶中的資料。

Microsoft 建議您使用 Azure Key Vault 來管理您的存取金鑰，並定期輪替並重新產生您的金鑰。 使用 Azure Key Vault 可讓您輕鬆地輪替金鑰，而不會中斷您的應用程式。 您也可以手動旋轉金鑰。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>查看帳戶存取金鑰

您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來查看並複製您的帳戶存取金鑰。 Azure 入口網站也會為您的儲存體帳戶提供可供複製的連接字串。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要從 Azure 入口網站查看並複製儲存體帳戶存取金鑰或連接字串：

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的儲存體帳戶。
1. 在 [**設定**] 底下，選取 [**存取金鑰**]。 您的帳戶存取金鑰隨即出現，此外也會顯示每個金鑰的完整連接字串。
1. 找出 [ **key1**] 底下的機**碼**值，然後按一下 [**複製**] 按鈕以複製帳戶金鑰。
1. 或者，您也可以複製整個連接字串。 尋找 [金鑰1]**** 下方的 [連接字串]**** 值，然後按一下 [複製]**** 按鈕來複製連接字串。

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="顯示如何在 Azure 入口網站中查看存取金鑰的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 取出您的帳戶存取金鑰，請呼叫[AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey)命令。

下列範例會抓取第一個索引鍵。 若要取出第二個索引鍵，請使用， `Value[1]` 而不是 `Value[0]` 。 請記得以您自己的值取代括弧中的預留位置值。

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 列出您的帳戶存取金鑰，請呼叫[az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list)命令，如下列範例所示。 請記得以您自己的值取代括弧中的預留位置值。 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

您可以使用兩個金鑰的其中一個來存取 Azure 儲存體，但一般來說，使用第一個金鑰是很好的作法，並保留在輪替金鑰時使用第二個金鑰的方式。

若要查看或讀取帳戶的存取金鑰，使用者必須是服務管理員，或必須獲指派包含**storageAccounts/listkeys/action**的 RBAC 角色。 包含此動作的一些內建 RBAC 角色是**擁有**者、**參與者**和**儲存體帳戶金鑰操作員服務角色**角色。 如需服務系統管理員角色的詳細資訊，請參閱[傳統訂用帳戶管理員角色、AZURE RBAC 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。 如需 Azure 儲存體內建角色的詳細資訊，請參閱 azure [RBAC 的 azure 內建角色](../../role-based-access-control/built-in-roles.md#storage)中的**儲存體**一節。

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>使用 Azure Key Vault 來管理您的存取金鑰

Microsoft 建議使用 Azure Key Vault 來管理及輪替您的存取金鑰。 您的應用程式可以安全地存取 Key Vault 中的金鑰，因此您可以避免將它們與應用程式的程式碼一起儲存。 如需使用 Key Vault 進行金鑰管理的詳細資訊，請參閱下列文章：

- [使用 Azure Key Vault 和 PowerShell 來管理儲存體帳戶金鑰](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [使用 Azure Key Vault 和 Azure CLI 管理儲存體帳戶金鑰](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>手動輪替存取金鑰

Microsoft 建議您定期輪替存取金鑰，以協助保護您的儲存體帳戶。 可能的話，請使用 Azure Key Vault 來管理您的存取金鑰。 如果您不是使用 Key Vault，就必須手動輪替金鑰。

由於指派兩個存取金鑰，因此您可以旋轉金鑰。 擁有兩個金鑰可確保您的應用程式在整個過程中都保有 Azure 儲存體的存取權。

> [!WARNING]
> 重新產生存取金鑰會影響相依於儲存體帳戶金鑰的應用程式或 Azure 服務。 使用帳戶金鑰來存取儲存體帳戶的任何用戶端必須更新並使用新的金鑰，包括媒體服務、雲端、桌面和行動應用程式，以及 Azure 儲存體的圖形化使用者介面應用程式，例如 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要在 Azure 入口網站中輪替儲存體帳戶存取金鑰：

1. 更新應用程式程式碼中的連接字串，以參考儲存體帳戶的次要存取金鑰。
1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的儲存體帳戶。
1. 在 [**設定**] 底下，選取 [**存取金鑰**]。
1. 若要重新產生儲存體帳戶的主要存取金鑰，請選取主要存取金鑰旁的 [**重新**產生] 按鈕。
1. 更新程式碼中的連接字串，以參考新的主要存取金鑰。
1. 以同樣的方式重新產生次要存取金鑰。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 輪替儲存體帳戶存取金鑰：

1. 更新應用程式程式碼中的連接字串，以參考儲存體帳戶的次要存取金鑰。
1. 呼叫[AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey)命令以重新產生主要存取金鑰，如下列範例所示：

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. 更新程式碼中的連接字串，以參考新的主要存取金鑰。
1. 以同樣的方式重新產生次要存取金鑰。 若要重新產生次要金鑰，請使用 `key2` 做為索引鍵名稱，而不是 `key1` 。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 旋轉您的儲存體帳戶存取金鑰：

1. 更新應用程式程式碼中的連接字串，以參考儲存體帳戶的次要存取金鑰。
1. 呼叫[az storage account keys 更新](/cli/azure/storage/account/keys#az-storage-account-keys-renew)命令以重新產生主要存取金鑰，如下列範例所示：

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. 更新程式碼中的連接字串，以參考新的主要存取金鑰。
1. 以同樣的方式重新產生次要存取金鑰。 若要重新產生次要金鑰，請使用 `key2` 做為索引鍵名稱，而不是 `key1` 。

---

> [!NOTE]
> Microsoft 建議同一時間在您的所有應用程式中僅使用其中一個金鑰。 如果您在某些地方使用金鑰 1 並在其他地方使用金鑰 2，您就無法在沒有部分應用程式遺失存取的情況下輪換您的金鑰。

若要輪替帳戶的存取金鑰，使用者必須是服務管理員，或必須獲指派包含**storageAccounts/regeneratekey/action**的 RBAC 角色。 包含此動作的一些內建 RBAC 角色是**擁有**者、**參與者**和**儲存體帳戶金鑰操作員服務角色**角色。 如需服務系統管理員角色的詳細資訊，請參閱[傳統訂用帳戶管理員角色、AZURE RBAC 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。 如需 Azure 儲存體內建 RBAC 角色的詳細資訊，請參閱 azure [RBAC 的 azure 內建角色](../../role-based-access-control/built-in-roles.md#storage)中的**儲存體**一節。

## <a name="next-steps"></a>後續步驟

- [Azure 儲存體帳戶概觀](storage-account-overview.md)
- [建立儲存體帳戶](storage-account-create.md)
