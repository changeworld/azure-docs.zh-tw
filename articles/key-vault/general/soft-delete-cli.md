---
title: Azure Key Vault - 如何以 CLI 使用虛刪除
description: 以 CLI 程式碼片段進行虛刪除的使用案例範例
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: sudbalas
ms.openlocfilehash: 8f32fcc319c3bffd9089d894c5376268227a0151
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135924"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>如何以 CLI 使用金鑰保存庫虛刪除

Azure Key Vault 的虛刪除功能可復原已刪除的保存庫和保存庫物件。 具體而言，虛刪除解決下列案例：

- 可復原的 Key Vault 刪除支援
- 支援可復原的金鑰保存庫物件刪除；金鑰、密碼和憑證

## <a name="prerequisites"></a>必要條件

- Azure CLI - 如果您沒有為環境進行此設定，請參閱[使用 Azure CLI 管理 Key Vault](manage-with-cli2.md))。

如需 CLI 的 Key Vault 特定參考資訊，請參閱 [Azure CLI Key Vault 參考](https://docs.microsoft.com/cli/azure/keyvault) \(英文\)。

## <a name="required-permissions"></a>所需的權限

Key Vault 作業透過角色型存取控制 (RBAC) 權限來分別管理，如下所示：

| 作業 | 描述 | 使用者權限 |
|:--|:--|:--|
|清單|列出已刪除的金鑰保存庫。|Microsoft.KeyVault/deletedVaults/read|
|復原|還原已刪除的金鑰保存庫。|Microsoft.KeyVault/vaults/write|
|清除|永久移除已刪除的金鑰保存庫和其所有內容。|Microsoft.KeyVault/locations/deletedVaults/purge/action|

如需權限和存取控制的詳細資訊，請參閱[保護您的金鑰保存庫](secure-your-key-vault.md))。

## <a name="enabling-soft-delete"></a>啟用虛刪除

您可啟用「虛刪除」來允許復原已刪除的金鑰保存庫，或金鑰保存庫中儲存的物件。

> [!IMPORTANT]
> 在金鑰保存庫上的啟用「虛刪除」是無法復原的動作。 將虛刪除屬性設定為 "true" 後，就無法加以變更或移除。  

### <a name="existing-key-vault"></a>現有的金鑰保存庫

對於名為 ContosoVault 的現有金鑰保存庫啟用虛刪除，如下所示。 

```azurecli
az keyvault update -n ContosoVault --enable-soft-delete true
```

### <a name="new-key-vault"></a>新的金鑰保存庫

預設會在所有金鑰保存庫上自動啟用虛刪除。 2020 年 12 月 31 日之後，就不能再建立未啟用虛刪除的新金鑰保存庫。

### <a name="verify-soft-delete-enablement"></a>驗證啟用虛刪除

若要驗證金鑰保存庫已啟用虛刪除，請執行 *show* 命令，並尋找「虛刪除已啟用?」 屬性：

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>刪除虛刪除所保護的金鑰保存庫

用以刪除金鑰保存庫行為變更的命令，取決於是否啟用虛刪除。

> [!IMPORTANT]
>如果您針對未啟用虛刪除的金鑰保存庫執行下列命令，您將永久刪除此金鑰保存庫及其所有的內容，並且沒有任何選項可供復原！

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>虛刪除如何保護您的金鑰保存庫

已啟用虛刪除：

- 已刪除的金鑰保存庫會從其資源群組中移除，並放置於與其建立所在位置相關聯的保留命名空間中。 
- 已刪除的物件 (例如金鑰、祕密和憑證) 都無法存取，而且只要其包含金鑰保存庫處於已刪除狀態便無法存取。 
- 系統會保留已刪除金鑰的 DNS 名稱，以免建立同名的金鑰保存庫。  

您可以檢視與您的訂用帳戶建立關聯的已刪除狀態金鑰保存庫，請使用下列命令：

```azurecli
az keyvault list-deleted
```
- 「識別碼」可以用來在復原或清除時識別資源。 
- 「資源識別碼」是此保存庫的原始資源識別碼。 因為此金鑰保存庫目前處於已刪除狀態，所以沒有具有該資源識別碼的資源存在。 
- 如果不採取任何動作，「排定清除日期」就是永久刪除保存庫的時間。 用來計算「排定清除日期」的預設保留期間為 90 天。

## <a name="recovering-a-key-vault"></a>復原金鑰保存庫

若要復原金鑰保存庫，您可指定金鑰保存庫名稱、資源群組和位置。 請記下已刪除之金鑰保存庫的位置和資源群組，因為您需要這些才能進行復原程序。

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

復原金鑰保存庫後，則會使用使用金鑰保存庫的原始資源識別碼建立新的資源。 如果原始資源群組已遭移除，則必須會建立一個同名的資源群組，再嘗試復原。

## <a name="deleting-and-purging-key-vault-objects"></a>刪除並清除金鑰保存庫物件

下列命令會在名為 'ContosoVault' 的金鑰保存庫中，刪除已啟用虛刪除的 'ContosoFirstKey' 金鑰：

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

在金鑰保存庫啟用虛刪除的情況下，已刪除的金鑰仍會看似已刪除，例外情況是當您明確地列出或擷取已刪除的金鑰時。 對於已刪除狀態的金鑰，大部分作業會失敗，只除了列出、復原或清除已刪除的金鑰時例外。 

例如，若要要求列出金鑰保存庫中的已刪除金鑰，請使用下列命令：

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>轉換狀態 

當您刪除金鑰保存庫中的金鑰，並且已啟用虛刪除時，可能需要幾秒鐘的時間讓轉換完成。 在此轉換期間，可能會出現金鑰不在使用中狀態或已刪除狀態。 

### <a name="using-soft-delete-with-key-vault-objects"></a>對金鑰保存庫物件使用虛刪除

就像金鑰保存庫，已刪除的金鑰、祕密或憑證仍會維持已刪除狀態長達 90 天，除非加以復原或清除。

#### <a name="keys"></a>索引鍵

若要復原虛刪除的金鑰：

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

若要永久刪除 (也稱為清除) 虛刪除的金鑰：

> [!IMPORTANT]
> 清除金鑰會永久刪除它，而且無法復原！ 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

**復原**和**清除**動作本身的權限已在金鑰保存庫的存取原則中建立關聯。 若要讓使用者或服務主體能夠執行**復原**或**清除**動作，他們必須具有該金鑰或祕密的個別權限。 根據預設，當 'all' 捷徑用於授與所有權限時，**清除**不會新增到金鑰保存庫的存取原則。 您必須明確授與**清除**權限。 

#### <a name="set-a-key-vault-access-policy"></a>設定金鑰保存庫存取原則

下列命令授與 user@contoso.com 對 *ContosoVault* 中的金鑰執行數種作業的權限，這其中包括**清除**：

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> 如果您有現有的金鑰保存庫，且剛剛啟用虛刪除，您可能沒有**復原**和**清除**權限。

#### <a name="secrets"></a>密碼

就像金鑰，祕密會以其自己的命令進行管理：

- 刪除名為 SQLPassword 的密碼： 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- 列出金鑰保存庫中的所有已刪除密碼： 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- 復原已刪除狀態的密碼： 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- 清除已刪除狀態的密碼： 

  > [!IMPORTANT]
  > 清除祕密會將它永久刪除，而且無法復原！ 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>清除虛刪除所保護的金鑰保存庫

> [!IMPORTANT]
> 清除金鑰保存庫或其內含的物件之一，就會永久刪除它，這表示無法復原！

清除函式用來永久刪除金鑰保存庫物件或整個金鑰保存庫，也就是先前虛刪除的項目。 如上一節所示範，儲存在已啟用虛刪除功能的金鑰保存庫中的物件可能經歷多個狀態：

- **作用中**：刪除之前。
- **虛刪除**：刪除之後，能夠列出並復原回到作用中狀態。
- **永久刪除**：清除之後，無法復原。

同樣的作法也適用於金鑰保存庫。 若要永久刪除虛刪除的金鑰保存庫及其內容，您必須清除金鑰保存庫本身。

### <a name="purging-a-key-vault"></a>清除金鑰保存庫

清除金鑰保存庫後，它的整個內容 (包括金鑰、祕密和憑證) 都會永久刪除。 若要清除虛刪除的金鑰保存庫，請使用 `az keyvault purge` 命令。 您可以使用命令 `az keyvault list-deleted` 找到訂用帳戶的已刪除金鑰保存庫的位置。

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>所需的清除權限
- 若要清除已刪除的金鑰保存庫，使用者需要執行 *Microsoft.KeyVault/locations/deletedVaults/purge/action* 作業的 RBAC 權限。 
- 若要列出已刪除的金鑰保存庫，使用者需要執行 *Microsoft.KeyVault/deletedVaults/read* 作業的 RBAC 權限。 
- 根據預設，只有訂用帳戶管理員擁有這些權限。 

### <a name="scheduled-purge"></a>排定的清除

列出已刪除的金鑰保存庫物件，也會顯示它們排定要由金鑰保存庫清除的時間。 如果不採取任何動作，「排定清除日期」指出何時會永久刪除金鑰保存庫物件。 根據預設，已刪除的金鑰保存庫物件的保留期限為 90 天。

>[!IMPORTANT]
>已清除的保存庫物件，由其「排定清除日期」欄位觸發，會永久刪除。 它無法復原！

## <a name="enabling-purge-protection"></a>啟用清除保護

開啟清除保護時，必須等到 90 天的保留期間過後，才能清除處於已刪除狀態的保存庫或物件。 這類保存庫或物件仍可復原。 此功能可加強確保在保留期間已過之前，一律無法永久刪除保存庫或物件。

只有在已啟用虛刪除功能的情況下，才可以啟用清除保護。 不支援停用清除保護。

若要在建立保存庫時開啟虛刪除和清除保護，請使用 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 命令：

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

若要將清除保護新增至現有的保存庫 (已啟用虛刪除)，請使用 [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) 命令：

```azurecli
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>其他資源

- 如需 Key Vault 的虛刪除功能概觀，請參閱 [Azure Key Vault 虛刪除概觀](soft-delete-overview.md))。
- 如需 Azure Key Vault 使用方式的一般概觀，請參閱[什麼是 Azure Key Vault？](overview.md))。

