---
title: 使用 Azure 金鑰保存庫和 Azure CLI 管理存儲帳戶金鑰
description: 存儲帳戶金鑰提供 Azure 金鑰保存庫和對 Azure 存儲帳戶的基於金鑰的訪問之間的無縫集成。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 104f3423b07eaa3269ffccc054cd2f779bbdabf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199813"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>使用金鑰保存庫和 Azure CLI 管理存儲帳戶金鑰

Azure 存儲帳戶使用包含帳戶名稱和金鑰的憑據。 金鑰是自動生成的，用作密碼，而不是加密金鑰。 金鑰保存庫通過將存儲帳戶金鑰存儲為[金鑰保存庫機密](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)來管理存儲帳戶金鑰。 

可以使用金鑰保存庫託管存儲帳戶金鑰功能列出（同步）具有 Azure 存儲帳戶的金鑰，並定期重新生成（旋轉）金鑰。 您可以管理存儲帳戶和經典存儲帳戶的金鑰。

使用託管存儲帳戶金鑰功能時，請考慮以下幾點：

- 從不返回鍵值以回應調用方。
- 只有金鑰保存庫應管理您的存儲帳戶金鑰。 不要自己管理金鑰，以免干擾金鑰保存庫進程。
- 只有一個金鑰保存庫物件應管理存儲帳戶金鑰。 不允許來自多個物件的金鑰管理。
- 您可以請求金鑰保存庫使用使用者主體管理存儲帳戶，但不能使用服務主體。
- 僅使用金鑰保存庫重新生成金鑰。 不要手動重新產生儲存體帳戶金鑰。

我們建議將 Azure 存儲集成與 Azure 活動目錄 （Azure AD）（Microsoft 基於雲的標識和訪問管理服務）集成。 Azure AD 集成可用於[Azure blob 和佇列](../storage/common/storage-auth-aad.md)，並提供基於 OAuth2 的基於 Windows 存儲的訪問（就像 Azure 金鑰保存庫一樣）。

Azure AD 允許您使用應用程式或使用者標識（而不是存儲帳戶憑據）對用戶端應用程式進行身份驗證。 在 Azure 上運行時，可以使用[Azure AD 託管標識](/azure/active-directory/managed-identities-azure-resources/)。 託管標識消除了對用戶端身份驗證和在應用程式中存儲憑據的需要。

Azure AD 使用基於角色的存取控制 （RBAC） 來管理授權，金鑰保存庫也支援授權。

## <a name="service-principal-application-id"></a>服務主體應用程式 ID

Azure AD 租戶為每個已註冊的應用程式提供[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)。 服務主體用作應用程式 ID，在授權設置期間用於通過 RBAC 訪問其他 Azure 資源。

金鑰保存庫是在所有 Azure AD 租戶中預先註冊的 Microsoft 應用程式。 金鑰保存庫在每個 Azure 雲中的同一應用程式 ID 下註冊。

| 租用戶 | Cloud | 應用程式識別碼 |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Prerequisites

要完成本指南，您必須首先執行以下操作：

- [安裝 Azure CLI](/cli/azure/install-azure-cli)。
- [創建金鑰保存庫](quick-create-cli.md)
- [創建 Azure 存儲帳戶](../storage/common/storage-account-create.md?tabs=azure-cli)。 存儲帳戶名稱只能使用小寫字母和數位。 名稱的長度必須介於 3 到 24 個字元之間。
      
## <a name="manage-storage-account-keys"></a>管理存儲帳戶金鑰

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用[az 登錄](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令對 Azure CLI 會話進行身份驗證。

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>授予金鑰保存庫對存儲帳戶的存取權限

使用 Azure CLI [az 角色指派創建](/cli/azure/role/assignment?view=azure-cli-latest)命令為金鑰保存庫授予訪問存儲帳戶。 提供以下參數值的命令：

- `--role`：傳遞"存儲帳戶金鑰操作員服務角色"RBAC 角色。 此角色限制存儲帳戶的訪問範圍。 對於經典存儲帳戶，請改為傳遞"經典存儲帳戶金鑰操作員服務角色"。
- `--assignee-object-id`：傳遞值"93c27d83-f79b-4cb2-8dd4-4aa716542e74"，這是 Azure 公共雲中金鑰保存庫的物件識別碼。 （要獲取 Azure 政府雲中金鑰保存庫的物件識別碼，請參閱[服務主體應用程式 ID](#service-principal-application-id)。
- `--scope`：傳遞您的存儲帳戶資源識別碼，該 ID`/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`在 表單中。 要查找訂閱 ID，請使用 Azure CLI az 帳戶清單命令;使用 Azure CLI [az 帳戶清單](/cli/azure/account?view=azure-cli-latest#az-account-list)命令。"要查找存儲帳戶名稱和存儲帳戶資源組，請使用 Azure CLI [az 存儲帳戶清單](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>創建金鑰保存庫託管存儲帳戶

 使用 Azure CLI [az 金鑰保存庫存儲](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add)命令創建金鑰保存庫託管存儲帳戶。 將再生週期設置為 90 天。 90 天后，金鑰保存庫將`key1`組建活動金鑰並從`key2`轉換到`key1`。 `key1`然後標記為活動鍵。 提供以下參數值的命令：

- `--vault-name`：傳遞金鑰保存庫的名稱。 要查找金鑰保存庫的名稱，請使用 Azure CLI [az 金鑰保存庫清單](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list)命令。
- `-n`：傳遞存儲帳戶的名稱。 要查找存儲帳戶的名稱，請使用 Azure CLI [az 存儲帳戶清單](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。
- `--resource-id`：傳遞您的存儲帳戶資源識別碼，該 ID`/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`在 表單中。 要查找訂閱 ID，請使用 Azure CLI az 帳戶清單命令;使用 Azure CLI [az 帳戶清單](/cli/azure/account?view=azure-cli-latest#az-account-list)命令。"要查找存儲帳戶名稱和存儲帳戶資源組，請使用 Azure CLI [az 存儲帳戶清單](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>共用訪問簽名權杖

您還可以要求金鑰保存庫生成共用訪問簽名權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以授予用戶端對存儲帳戶中的資源的存取權限，而無需共用您的帳戶金鑰。 共用訪問簽名為您提供了一種安全的方式來共用存儲資源，而不會危及您的帳戶金鑰。

本節中的命令完成以下操作：

- 設置帳戶共用訪問簽名定義`<YourSASDefinitionName>`。 定義設置在金鑰保存庫中`<YourStorageAccountName>``<YourKeyVaultName>`的金鑰保存庫託管存儲帳戶上。
- 為 Blob、檔、表和佇列服務創建帳戶共用訪問簽名權杖。 為資源類型服務、容器和物件創建權杖。 權杖使用擁有權限、HTTPs 以及指定的開始日期和結束日期創建。
- 在保存庫中設置金鑰保存庫託管存儲共用訪問簽名定義。 該定義具有所創建的共用訪問簽名權杖的範本 URI。 該定義具有共用訪問簽名類型`account`，有效期為 N 天。
- 驗證共用訪問簽名是否作為機密保存在金鑰保存庫中。

### <a name="create-a-shared-access-signature-token"></a>創建共用訪問簽名權杖

使用 Azure CLI [az 存儲帳戶生成 sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)命令創建共用訪問簽名定義。 此操作需要 和`storage``setsas`許可權。


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
操作成功運行後，複製輸出。

```console
"se=2020-01-01&sp=***"
```

此輸出將在下一步中傳遞給`--template-id`參數。

### <a name="generate-a-shared-access-signature-definition"></a>生成共用訪問簽名定義

使用 Azure CLI [az 金鑰庫存儲 sas 定義創建](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create)命令，將輸出從上一步`--template-id`傳遞到參數，以創建共用訪問簽名定義。  您可以為`-n`參數提供您選擇的名稱。

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>驗證共用訪問簽名定義

您可以使用 Azure CLI [az 金鑰庫金鑰庫金鑰清單](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)和[az 金鑰庫金鑰顯示](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)命令驗證共用訪問簽名定義是否存儲在金鑰保存庫中。

首先，使用[az 金鑰庫金鑰庫金鑰清單](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)命令在金鑰保存庫中查找共用訪問簽名定義。

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

與 SAS 定義對應的機密將具有以下屬性：

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

現在，您可以使用[az keyvault 機密顯示](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)命令和`id`屬性來查看該機密的內容。

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

此命令的輸出將顯示 SAS 定義字串為`value`。


## <a name="next-steps"></a>後續步驟

- 詳細瞭解[金鑰、機密和證書](https://docs.microsoft.com/rest/api/keyvault/)。
- 查看 Azure[金鑰庫團隊博客上](https://blogs.technet.microsoft.com/kv/)的文章。
- 請參閱[az 金鑰庫存儲](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)參考文檔。
