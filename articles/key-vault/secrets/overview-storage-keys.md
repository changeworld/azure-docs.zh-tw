---
title: 使用 Azure Key Vault 和 Azure CLI 管理儲存體帳戶金鑰
description: 儲存體帳戶金鑰可提供對 Azure 儲存體帳戶 Azure Key Vault 和金鑰型存取之間的緊密整合。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7819b62fb92945a409cab0dff926e281620dd80c
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496639"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>使用 Key Vault 和 Azure CLI 管理儲存體帳戶金鑰

Azure 儲存體帳戶使用由帳戶名稱和金鑰組成的認證。 金鑰會自動產生並作為密碼，而不是做為加密金鑰。 Key Vault 會在儲存體帳戶中定期重新產生儲存體帳戶金鑰，並提供共用存取簽章權杖，以供委派存取您儲存體帳戶中的資源。

您可以使用 Key Vault 受控儲存體帳戶金鑰功能來列出（同步）金鑰與 Azure 儲存體帳戶，並定期重新產生（輪替）金鑰。 您可以管理儲存體帳戶和傳統儲存體帳戶的金鑰。

當您使用受控儲存體帳戶金鑰功能時，請考慮下列幾點：

- 回應呼叫端時，絕對不會傳回索引鍵值。
- 只有 Key Vault 應該管理您的儲存體帳戶金鑰。 請勿自行管理金鑰，並避免干擾 Key Vault 進程。
- 只有單一 Key Vault 物件應該管理儲存體帳戶金鑰。 不允許從多個物件進行金鑰管理。
- 您可以要求 Key Vault 來管理具有使用者主體的儲存體帳戶，但不能使用服務主體。
- 僅使用 Key Vault 重新產生金鑰。 不要手動重新產生儲存體帳戶金鑰。

我們建議使用 Azure 儲存體與 Azure Active Directory （Azure AD）的整合，這是 Microsoft 的雲端式身分識別和存取管理服務。 Azure AD 整合適用于[Azure blob 和佇列](../../storage/common/storage-auth-aad.md)，並提供對 Azure 儲存體的 OAuth2 token 型存取（就像 Azure Key Vault）。

Azure AD 可讓您使用應用程式或使用者識別，而不是儲存體帳號憑證來驗證用戶端應用程式。 當您在 Azure 上執行時，可以使用[Azure AD 受控識別](/azure/active-directory/managed-identities-azure-resources/)。 受控識別會移除用戶端驗證的需求，並將認證儲存在或應用程式中。

Azure AD 會使用角色型存取控制（RBAC）來管理授權，Key Vault 也支援這種方式。

## <a name="service-principal-application-id"></a>服務主體應用程式識別碼

Azure AD 租使用者會提供每個已註冊的應用程式與[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)。 服務主體會作為應用程式識別碼，在授權設定期間用於透過 RBAC 存取其他 Azure 資源。

Key Vault 是在所有 Azure AD 租使用者中預先註冊的 Microsoft 應用程式。 Key Vault 會在每個 Azure 雲端中的相同應用程式識別碼下註冊。

| 租用戶 | 雲端 | 應用程式識別碼 |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Prerequisites

若要完成本指南，您必須先執行下列動作：

- [安裝 Azure CLI](/cli/azure/install-azure-cli)。
- [建立金鑰保存庫](quick-create-cli.md)
- [建立 Azure 儲存體帳戶](../../storage/common/storage-account-create.md?tabs=azure-cli)。 儲存體帳戶名稱必須僅使用小寫字母和數位。 名稱的長度必須介於3到24個字元之間。
      
## <a name="manage-storage-account-keys"></a>管理儲存體帳戶金鑰

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用[az login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令來驗證您的 Azure CLI 會話。

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>授與 Key Vault 對儲存體帳戶的存取權

使用 Azure CLI [az role 指派 create](/cli/azure/role/assignment?view=azure-cli-latest)命令，讓 Key Vault 存取您的儲存體帳戶。 提供命令下列參數值：

- `--role`：傳遞「儲存體帳戶金鑰操作員服務角色」 RBAC 角色。 此角色會將存取範圍限制為您的儲存體帳戶。 若為傳統儲存體帳戶，請改為傳遞「傳統儲存體帳戶金鑰操作員服務角色」。
- `--assignee`：傳遞值 " https://vault.azure.net "，這是 Azure 公用雲端中 Key Vault 的 url。 （針對 Azure Goverment 雲端，請改用 [--asingee-物件識別碼]，請參閱[服務主體應用程式識別碼](#service-principal-application-id)）。
- `--scope`：傳遞您的儲存體帳戶資源識別碼，其格式為 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` 。 若要尋找您的訂用帳戶識別碼，請使用 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list)命令;若要尋找您的儲存體帳戶名稱和儲存體帳戶資源群組，請使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>將使用者帳戶使用權限授與受控儲存體帳戶

使用 Azure CLI [az keyvault-set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Cmdlet 來更新 Key Vault 存取原則，並將儲存體帳戶許可權授與您的使用者帳戶。

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

請注意，適用於儲存體帳戶的使用權限並不會在該儲存體於 Azure 入口網站中的 [存取原則] 頁面上提供。
### <a name="create-a-key-vault-managed-storage-account"></a>建立 Key Vault 受控儲存體帳戶

 使用 Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add)命令來建立 Key Vault 受控儲存體帳戶。 設定90天的重新產生期間。 當您需要輪替時，KeyVault 會重新產生非作用中的金鑰，然後將新建立的金鑰設定為作用中。 只有其中一個金鑰會用來在任何時間發出 SAS 權杖，這是使用中的金鑰。 提供命令下列參數值：

- `--vault-name`：傳遞金鑰保存庫的名稱。 若要尋找金鑰保存庫的名稱，請使用 Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list)命令。
- `-n`：傳遞您的儲存體帳戶名稱。 若要尋找儲存體帳戶的名稱，請使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。
- `--resource-id`：傳遞您的儲存體帳戶資源識別碼，其格式為 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` 。 若要尋找您的訂用帳戶識別碼，請使用 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list)命令;若要尋找您的儲存體帳戶名稱和儲存體帳戶資源群組，請使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)命令。
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>共用存取簽章權杖

您也可以要求 Key Vault 產生共用存取簽章權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以對用戶端授與儲存體帳戶中資源的存取權，而不需要共用您的帳戶金鑰。 共用存取簽章可讓您以安全的方式共用儲存體資源，而不會危害您的帳戶金鑰。

本節中的命令會完成下列動作：

- 設定帳戶共用存取簽章定義 `<YourSASDefinitionName>` 。 定義會在金鑰保存庫中的 Key Vault 受控儲存體帳戶上設定 `<YourStorageAccountName>` `<YourKeyVaultName>` 。
- 為 Blob、檔案、資料表和佇列服務建立帳戶共用存取簽章權杖。 權杖是針對資源類型服務、容器和物件所建立。 權杖是以擁有權限（透過 HTTPs）和指定的開始和結束日期來建立。
- 在保存庫中設定 Key Vault 管理的儲存體共用存取簽章定義。 定義具有已建立的共用存取簽章權杖的範本 URI。 定義具有共用存取簽章類型 `account` ，且有效期為 N 天。
- 確認已將共用存取簽章儲存在金鑰保存庫中做為密碼。

### <a name="create-a-shared-access-signature-token"></a>建立共用存取簽章權杖

使用 Azure CLI [az storage account 產生-sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)命令來建立共用存取簽章定義。 這種作業需要 `storage` 和 `setsas` 許可權。


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
作業執行成功之後，請複製輸出。

```console
"se=2020-01-01&sp=***"
```

在下一個步驟中，此輸出將會是傳遞給 `--template-uri` 參數的。

### <a name="generate-a-shared-access-signature-definition"></a>產生共用存取簽章定義

使用 Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create)命令，將上一個步驟的輸出傳遞至 `--template-uri` 參數，以建立共用存取簽章定義。  您可以將您選擇的名稱提供給 `-n` 參數。

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>驗證共用存取簽章定義

您可以使用 Azure CLI [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)和[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)命令，確認已將共用存取簽章定義儲存在金鑰保存庫中。

首先，使用[az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)命令，尋找金鑰保存庫中的共用存取簽章定義。

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

對應至您 SAS 定義的密碼將具有下列屬性：

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

您現在可以使用[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)命令和 `id` 屬性來查看該密碼的內容。

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

此命令的輸出會將您的 SAS 定義字串顯示為 `value` 。


## <a name="next-steps"></a>後續步驟

- 深入瞭解[金鑰、秘密和憑證](https://docs.microsoft.com/rest/api/keyvault/)。
- 請參閱[Azure Key Vault 小組 blog](https://blogs.technet.microsoft.com/kv/)的文章。
- 請參閱[az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)參考檔 \ （英文 \）。
