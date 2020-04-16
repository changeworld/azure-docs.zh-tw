---
title: 使用 Azure 金鑰保存管理和 Azure CLI 管理儲存帳戶金鑰
description: 存儲帳戶金鑰提供 Azure 密鑰保管庫和對 Azure 儲存帳戶的基於金鑰的訪問之間的無縫整合。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 0b855584ef6efef574e8264f3cead79000a51b13
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432004"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>使用金鑰保存者與 Azure CLI 管理儲存帳戶金鑰

Azure 儲存帳戶使用包含帳戶名稱和密鑰的憑據。 金鑰是自動生成的,用作密碼,而不是加密密鑰。 密鑰保管庫通過將存儲帳戶密鑰存儲為密鑰保管庫機密來管理存儲帳戶金鑰。 

可以使用密鑰保管庫託管存儲帳戶密鑰功能列出(同步)具有 Azure 儲存帳戶的密鑰,並定期重新生成(旋轉)密鑰。 您可以管理儲存帳戶和經典存儲帳戶的密鑰。

使用託管儲存帳戶金鑰功能時,請考慮以下幾點:

- 從不返回鍵值以回應調用方。
- 只有金鑰保管庫應管理您的儲存帳戶金鑰。 不要自己管理密鑰,以免干擾密鑰保管庫進程。
- 只有一個密鑰保管庫物件應管理存儲帳戶密鑰。 不允許來自多個物件的密鑰管理。
- 您可以請求金鑰保管庫使用使用者主體管理存儲帳戶,但不能使用服務主體。
- 僅使用密鑰保管庫重新生成密鑰。 不要手動重新產生儲存體帳戶金鑰。

我們建議將 Azure 儲存整合與 Azure 活動目錄 (Azure AD)(Microsoft 基於雲端的標識和存取管理服務)整合。 Azure AD 整合可用於[Azure blob 和佇列](../../storage/common/storage-auth-aad.md),並提供基於 OAuth2 的基於 Windows 儲存的存取(就像 Azure 金鑰保管庫一樣)。

Azure AD 允許您使用應用程式或用戶識別(而不是存儲帳戶認證)對用戶端應用程式進行身份驗證。 在 Azure 上執行時,可以使用[Azure AD 託管識別](/azure/active-directory/managed-identities-azure-resources/)。 託管標識消除了對用戶端身份驗證和在應用程式中存儲憑據的需要。

Azure AD 使用基於角色的存取控制 (RBAC) 來管理授權,密鑰保管庫也支援授權。

## <a name="service-principal-application-id"></a>服務主體應用程式識別碼

Azure AD 租戶為每個已註冊的應用程式提供[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)。 服務主體用作應用程式 ID,在授權設置期間用於通過 RBAC 訪問其他 Azure 資源。

密鑰保管庫是在所有 Azure AD 租戶中預先註冊的 Microsoft 應用程式。 密鑰保管庫在每個 Azure 雲中的同一應用程式 ID 下註冊。

| 租用戶 | 雲端 | 應用程式識別碼 |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Prerequisites

要完成本指南,您必須首先執行以下操作:

- [安裝 Azure CLI](/cli/azure/install-azure-cli)。
- [建立金鑰保存庫](quick-create-cli.md)
- [建立 Azure 儲存帳戶](../../storage/common/storage-account-create.md?tabs=azure-cli)。 存儲帳戶名稱只能使用小寫字母和數位。 名稱的長度必須介於 3 到 24 個字元之間。
      
## <a name="manage-storage-account-keys"></a>管理預取帳戶金鑰

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用[az 登錄](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令對 Azure CLI 會話進行身份驗證。

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>授金鑰保存儲存帳號存取權限

使用 Azure CLI [az 角色分配創建](/cli/azure/role/assignment?view=azure-cli-latest)指令為金鑰保管庫授予存取儲存帳戶。 提供以下參數值的指令:

- `--role`:傳遞"存儲帳戶金鑰操作員服務角色"RBAC 角色。 此角色限制存儲帳戶的訪問範圍。 對於經典存儲帳戶,請改為傳遞"經典存儲帳戶密鑰操作員服務角色"。
- `--assignee-object-id`:傳遞值"93c27d83-f79b-4cb2-8dd4-4aa716542e74",這是 Azure 公共雲中密鑰保管庫的對象 ID。 (要取得 Azure 政府雲中金鑰保管庫的物件 ID,請參考[服務主體應用程式 ID](#service-principal-application-id)。
- `--scope`:傳遞您的儲存帳戶資源 ID,該`/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`ID 在窗體中。 要查找訂閱 ID,請使用 Azure CLI az 帳戶清單命令;使用 Azure CLI [az 帳戶清單](/cli/azure/account?view=azure-cli-latest#az-account-list)命令。"要查找儲存帳戶名稱和儲存帳戶資源組,請使用 Azure CLI [az 儲存帳戶清單](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>建立金鑰保存庫託管儲存帳戶

 使用 Azure CLI [az 金鑰保管庫儲存](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add)命令創建密鑰保管庫託管儲存帳戶。 將再生周期設置為 90 天。 90 天後,金鑰保存到`key1`一個動作金鑰,`key2`並`key1`從轉換到 。 `key1`然後標記為活動鍵。 提供以下參數值的指令:

- `--vault-name`:傳遞金鑰保管庫的名稱。 要查找密鑰保管庫的名稱,請使用 Azure CLI [az 密鑰保管庫清單](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list)命令。
- `-n`:傳遞存儲帳戶的名稱。 要查找儲存帳戶的名稱,請使用 Azure CLI [az 儲存帳戶清單](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。
- `--resource-id`:傳遞您的儲存帳戶資源 ID,該`/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`ID 在窗體中。 要查找訂閱 ID,請使用 Azure CLI az 帳戶清單命令;使用 Azure CLI [az 帳戶清單](/cli/azure/account?view=azure-cli-latest#az-account-list)命令。"要查找儲存帳戶名稱和儲存帳戶資源組,請使用 Azure CLI [az 儲存帳戶清單](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>分享存取簽署權杖

您還可以要求金鑰保管庫生成共享訪問簽名權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以授予用戶端對儲存帳戶中的資源的訪問許可權,而無需共用您的帳戶密鑰。 共享訪問簽名為您提供了一種安全的方式來分享存儲資源,而不會危及您的帳戶密鑰。

本節中的指令完成以下操作:

- 設定帳號分享存取簽章`<YourSASDefinitionName>`定義 。 定義設置在密鑰保管庫中`<YourStorageAccountName>``<YourKeyVaultName>`的密鑰保管庫託管存儲帳戶上。
- 為 Blob、檔案、表和佇列服務創建帳戶共用訪問簽名權杖。 為資源類型服務、容器和物件創建權杖。 令牌使用所有許可權、Htcrit以及指定的開始日期和結束日期創建。
- 在保管庫中設置密鑰保管庫託管存儲共享訪問簽名定義。 該定義具有所創建的共享訪問簽名權杖的範本 URI。 該定義具有共用訪問簽名類型`account`,有效期為 N 天。
- 驗證共享訪問簽名是否作為機密保存在密鑰保管庫中。

### <a name="create-a-shared-access-signature-token"></a>建立分享存取簽署權杖

使用 Azure CLI [az 儲存帳戶生成 sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)命令建立共享存取簽名定義。 此操作需要和`storage``setsas`許可權。


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
操作成功運行後,複製輸出。

```console
"se=2020-01-01&sp=***"
```

此輸出將在下一步中傳遞給`--template-id`參數。

### <a name="generate-a-shared-access-signature-definition"></a>產生分享存取簽章定義

使用 Azure CLI [az 密鑰庫儲存 sas 定義建立](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create)命令`--template-id`,將輸出從上一步 傳遞到參數,以創建共享存取簽名定義。  您可以為`-n`參數提供您選擇的名稱。

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>驗證共享存取簽章定義

您可以使用 Azure CLI [az 金鑰庫金鑰庫金鑰清單](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)和[az 金鑰庫金鑰顯示](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)命令驗證共享存取簽名定義是否儲存在金鑰保管庫中。

首先,使用[az 金鑰庫金鑰庫金鑰清單](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)命令在密鑰保管庫中尋找共享存取簽名定義。

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

與 SAS 定義對應的機密將具有以下屬性:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

現在,您可以使用[az keyvault 機密顯示](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)命令和`id`屬性來查看該機密的內容。

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

這個指令的輸出會顯示 SAS 定義字串`value`為 。


## <a name="next-steps"></a>後續步驟

- 詳細瞭解[金鑰、機密與憑證](https://docs.microsoft.com/rest/api/keyvault/)。
- 查看 Azure[密鑰庫團隊部落格上](https://blogs.technet.microsoft.com/kv/)的文章。
- 請參閱[az 金鑰庫存儲](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)參考文檔。
