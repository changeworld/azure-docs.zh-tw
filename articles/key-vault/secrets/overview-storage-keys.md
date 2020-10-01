---
title: 使用 Azure Key Vault 和 Azure CLI 管理儲存體帳戶金鑰
description: 儲存體帳戶金鑰提供 Azure Key Vault 與 Azure 儲存體帳戶的金鑰型存取之間的完美整合。
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 26e1852058383ef1e4cc4b3b604e1bdc79d60e14
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612178"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>使用 Key Vault 和 Azure CLI 管理儲存體帳戶金鑰

Azure 儲存體帳戶會使用由帳戶名稱和金鑰組成的認證。 該金鑰是由系統自動產生，並作為密碼使用，比單純的密碼編譯金鑰具有更多功能。 Key Vault 會在儲存體帳戶中定期重新產生儲存體帳戶金鑰，並提供共用存取簽章權杖，以便委派存取您儲存體帳戶中的資源。

您可以使用 Key Vault 受控儲存體帳戶金鑰功能列出 (同步) 金鑰與 Azure 儲存體帳戶，以及定期重新產生 (輪替) 金鑰。 您可以管理儲存體帳戶和傳統儲存體帳戶的金鑰。

使用受控儲存體帳戶金鑰功能時，請考慮以下幾點：

- 系統永遠不會傳回金鑰值以回應呼叫者。
- 只能允許 Key Vault 管理您的儲存體帳戶金鑰。 請勿自行管理金鑰，並避免干擾 Key Vault 程序。
- 只有單一 Key Vault 物件方能管理儲存體帳戶金鑰。 不允許從多個物件進行金鑰管理。
- 只能使用 Key Vault 重新產生金鑰。 不要手動重新產生儲存體帳戶金鑰。

建議使用 Azure 儲存體與 Azure Active Directory (Azure AD) 整合，這是 Microsoft 的雲端式身分識別和存取管理服務。 Azure AD 整合適用於 [Azure Blob 和佇列](../../storage/common/storage-auth-aad.md)，並提供對 Azure 儲存體的 OAuth2 權杖型存取 (如同 Azure Key Vault)。

Azue AD 可讓您使用應用程式或使用者身分識別來驗證用戶端應用程式，而非使用儲存體帳戶認證。 在 Azure 上執行時，可以使用 [Azure AD 受控識別](/azure/active-directory/managed-identities-azure-resources/)。 受控識別能移除用戶端驗證，以及使用應用程式儲存認證或將認證儲存於應用程式中的需求。

Azure AD 會使用同時也受 Key Vault 支援的角色型存取控制 (RBAC) 來管理授權。

## <a name="service-principal-application-id"></a>服務主體的應用程式識別碼

Azure AD 租用戶會為每個已註冊的應用程式提供一個[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)。 服務主體會作為應用程式識別碼，在授權設定期間用來透過 RBAC 存取其他 Azure 資源。

Key Vault 是 Microsoft 應用程式，已在所有 Azure AD 租用戶中預先註冊。 Key Vault 會在每個 Azure 雲端中的相同應用程式識別碼下註冊。

| 租用戶 | 雲端 | 應用程式識別碼 |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>必要條件

若要完成本指南，您必須：

- [安裝 Azure CLI](/cli/azure/install-azure-cli)。
- [建立金鑰保存庫](quick-create-cli.md)
- [建立 Azure 儲存體帳戶](../../storage/common/storage-account-create.md?tabs=azure-cli)。 儲存體帳戶名稱必須只能使用小寫字母和數字。 名稱長度必須介於 3 到 24 個字元之間。
      
## <a name="manage-storage-account-keys"></a>管理儲存體帳戶金鑰

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用 [az login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令驗證您的 Azure CLI 工作階段。

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>對儲存體帳戶提供 Key Vault 存取權

使用 Azure CLI [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest) 命令，授與 Key Vault 存取您的儲存體帳戶。 將下列參數值提供給命令：

- `--role`:傳遞「儲存體帳戶金鑰操作員服務角色」Azure 角色。 此角色會將存取範圍限制為您的儲存體帳戶。 若是傳統帳戶類型，請傳遞「傳統儲存體帳戶金鑰操作員服務角色」。
- `--assignee`:傳遞 "https://vault.azure.net" 值，這是 Azure 公用雲端中 Key Vault 的 URL。 (針對 Azure Goverment 雲端，請改用 '--asingee-object-id'，請參閱[服務主體應用程式識別碼](#service-principal-application-id)。)
- `--scope`:傳遞您的儲存體帳戶資源識別碼，其格式為 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`。 若要尋找您的訂用帳戶識別碼，請使用 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) 命令；若要尋找您的儲存體帳戶名稱和儲存體帳戶資源群組，請使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 命令。

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>將使用者帳戶使用權限授與受控儲存體帳戶

使用 Azure CLI [az keyvault-set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Cmdlet 來更新 Key Vault 存取原則，並將儲存體帳戶權限授與您的使用者帳戶。

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

請注意，適用於儲存體帳戶的使用權限並不會在該儲存體於 Azure 入口網站中的 [存取原則] 頁面上提供。
### <a name="create-a-key-vault-managed-storage-account"></a>建立 Key Vault 受控儲存體帳戶

 使用 Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) 命令來建立 Key Vault 受控儲存體帳戶。 設定 90 天的重新產生期間。 需要輪替時，KeyVault 會重新產生非作用中的金鑰，然後將新建立的金鑰設定為作用中。 只有其中一個金鑰會用來在任何時間簽發 SAS 權杖，這就是使用中的金鑰。 將下列參數值提供給命令：

- `--vault-name`:傳遞金鑰保存庫的名稱。 若要尋找金鑰保存庫的名稱，請使用 Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) 命令。
- `-n`:傳遞儲存體帳戶的名稱。 若要尋找儲存體帳戶的名稱，請使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 命令。
- `--resource-id`:傳遞您的儲存體帳戶資源識別碼，其格式為 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`。 若要尋找您的訂用帳戶識別碼，請使用 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) 命令；若要尋找您的儲存體帳戶名稱和儲存體帳戶資源群組，請使用 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 命令。
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>共用存取簽章權杖

您也可以要求 Key Vault 產生共用存取簽章權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以對用戶端授與儲存體帳戶中資源的存取權，而不必共用帳戶金鑰。 共用存取簽章可讓您安全地共用儲存體資源，而不會將帳戶金鑰外洩。

本節中的命令會完成下列動作：

- 設定帳戶共用存取簽章定義 `<YourSASDefinitionName>`。 定義會在金鑰保存庫 `<YourKeyVaultName>`中的 Key Vault 受控儲存體帳戶 `<YourStorageAccountName>` 上設定。
- 為 Blob、檔案、資料表和佇列服務建立帳戶共用存取簽章權杖。 權杖是針對資源類型服務、容器和物件所建立。 權杖是針對所有權限、透過 https 和指定的開始和結束日期來建立。
- 在保存庫中設定 Key Vault 受控儲存體共用存取簽章定義。 定義具有已建立的共用存取簽章權杖的範本 URI。 定義的共用存取簽章類型為 `account`，且有效期為 N 天。
- 確認已將共用存取簽章儲存在金鑰保存庫中作為祕密。

### <a name="create-a-shared-access-signature-token"></a>建立共用存取簽章權杖

使用 Azure CLI [az storage account generate-sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) 命令來建立共用存取簽章定義。 這項作業必須有 `storage` 和 `setsas` 權限。


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
作業執行成功之後，請複製輸出。

```console
"se=2020-01-01&sp=***"
```

在下一個步驟中，會將此輸出傳遞給 `--template-uri` 參數。

### <a name="generate-a-shared-access-signature-definition"></a>產生共用存取簽章定義

使用 Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) 命令，將上一個步驟的輸出傳遞至 `--template-uri` 參數，以建立共用存取簽章定義。  您可以將選擇的名稱提供給 `-n` 參數。

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>驗證共用存取簽章定義

您可以使用 Azure CLI [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 和 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令，確認共用存取簽章定義已儲存在金鑰保存庫中。

首先，使用 [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 命令，尋找金鑰保存庫中的共用存取簽章定義。

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

對應至您 SAS 定義的祕密將具有下列屬性：

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

您現在可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令和 `id` 屬性來檢視祕密的內容。

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

此命令的輸出會將您的 SAS 定義字串顯示為 `value`。


## <a name="next-steps"></a>後續步驟

- 深入了解[金鑰、祕密與憑證](https://docs.microsoft.com/rest/api/keyvault/)。
- 請參閱 [Azure Key Vault 小組部落格](https://blogs.technet.microsoft.com/kv/)的文章。
- 請參閱 [az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) 參考文件。
