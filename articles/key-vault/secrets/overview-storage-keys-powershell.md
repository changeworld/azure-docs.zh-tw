---
title: Azure Key Vault 受控儲存體帳戶 - PowerShell 版本
description: 受控儲存體帳戶功能可在 Azure Key Vault 與 Azure 儲存體帳戶之間提供完美整合。
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 50fbaf5092e793369daaa71fc7364dfd406e03b3
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444889"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>使用 Key Vault 和 Azure PowerShell 管理儲存體帳戶金鑰

Azure 儲存體帳戶會使用由帳戶名稱和金鑰組成的認證。 該金鑰是由系統自動產生，並作為密碼使用，比單純的密碼編譯金鑰具有更多功能。 Key Vault 會在儲存體帳戶中定期重新產生儲存體帳戶金鑰，並提供共用存取簽章權杖，以便委派存取您儲存體帳戶中的資源。

您可以使用 Key Vault 受控儲存體帳戶金鑰功能列出 (同步) 金鑰與 Azure 儲存體帳戶，以及定期重新產生 (輪替) 金鑰。 您可以管理儲存體帳戶和傳統儲存體帳戶的金鑰。

使用受控儲存體帳戶金鑰功能時，請考慮以下幾點：

- 系統永遠不會傳回金鑰值以回應呼叫者。
- 只能允許 Key Vault 管理您的儲存體帳戶金鑰。 請勿自行管理金鑰，並避免干擾 Key Vault 程序。
- 只有單一 Key Vault 物件方能管理儲存體帳戶金鑰。 不允許從多個物件進行金鑰管理。
- 只能使用 Key Vault 重新產生金鑰。 不要手動重新產生儲存體帳戶金鑰。

建議使用 Azure 儲存體與 Azure Active Directory (Azure AD) 整合，這是 Microsoft 的雲端式身分識別和存取管理服務。 Azure AD 整合適用於 [Azure Blob 和佇列](../../storage/common/storage-auth-aad.md)，並提供對 Azure 儲存體的 OAuth2 權杖型存取 (如同 Azure Key Vault)。

Azue AD 可讓您使用應用程式或使用者身分識別來驗證用戶端應用程式，而非使用儲存體帳戶認證。 在 Azure 上執行時，可以使用 [Azure AD 受控識別](../../active-directory/managed-identities-azure-resources/index.yml)。 受控識別能移除用戶端驗證，以及使用應用程式儲存認證或將認證儲存於應用程式中的需求。

Azure AD 會使用同時也受 Key Vault 支援的 Azure 角色型存取控制 (Azure RBAC) 來管理授權。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>服務主體的應用程式識別碼

Azure AD 租用戶會為每個已註冊的應用程式提供一個[服務主體](../../active-directory/develop/developer-glossary.md#service-principal-object)。 服務主體會作為應用程式識別碼，在授權設定期間用來透過 Azure RBAC 存取其他 Azure 資源。

Key Vault 是 Microsoft 應用程式，已在所有 Azure AD 租用戶中預先註冊。 Key Vault 會在每個 Azure 雲端中的相同應用程式識別碼下註冊。

| 租用戶 | 雲端 | 應用程式識別碼 |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>必要條件

若要完成本指南，您必須：

- [安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。
- [建立金鑰保存庫](quick-create-powershell.md)
- [建立 Azure 儲存體帳戶](../../storage/common/storage-account-create.md?tabs=azure-powershell)。 儲存體帳戶名稱必須只能使用小寫字母和數字。 名稱長度必須介於 3 到 24 個字元之間。


## <a name="manage-storage-account-keys"></a>管理儲存體帳戶金鑰

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 來驗證您的 PowerShell 工作階段。

```azurepowershell-interactive
Connect-AzAccount
```
如果您有多個 Azure 訂用帳戶，可以使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) Cmdlet 列出，並使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 指定要使用的訂用帳戶。

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>設定變數

首先，在下列步驟中設定 PowerShell Cmdlet 所要使用的變數。 請務必更新 "YourResourceGroupName", "YourStorageAccountName", and "YourKeyVaultName" 預留位置，並將 $keyVaultSpAppId 設定為 `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (如以上[服務主體應用程式識別碼](#service-principal-application-id)中所指定)。

我們也會使用 Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext) 和 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) Cmdlet 取得您的使用者識別碼和 Azure 儲存體帳戶的內容。

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> 對於傳統儲存體帳戶，請針對 $storageAccountKey 使用「主要」和「次要」 <br>
> 針對傳統儲存體帳戶，請使用 'Get-AzResource -Name "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName'，而不要使用 'Get-AzStorageAccount'

### <a name="give-key-vault-access-to-your-storage-account"></a>對儲存體帳戶提供 Key Vault 存取權

在 Key Vault 可以存取並管理您的儲存體帳戶金鑰之前，您必須為它授權以存取您的儲存體帳戶。 Key Vault 應用程式需要「列出」及「重新產生」儲存體帳戶金鑰的使用權限。 這些使用權限是透過 Azure 內建的角色[儲存體帳戶金鑰操作員服務角色](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role)來啟用。

使用 Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) Cmdlet，將此角色指派給 Key Vault 服務主體，並將範圍限制在您的儲存體帳戶。

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

角色指派成功時，您應該會看到類似於以下範例的輸出：

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

如果 Key Vault 在您的儲存體帳戶上已經被新增至該角色，您將會收到「角色指派已存在。」 錯誤內容。 您也可以使用 Azure 入口網站中該儲存體帳戶的 [存取控制 (IAM)] 頁面來驗證角色指派。

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>將使用者帳戶使用權限授與受控儲存體帳戶

使用 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Cmdlet 來更新 Key Vault 存取原則，並將儲存體帳戶權限授與您的使用者帳戶。

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

請注意，適用於儲存體帳戶的使用權限並不會在該儲存體於 Azure 入口網站中的 [存取原則] 頁面上提供。

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>將受控儲存體帳戶新增到您的 Key Vault 執行個體

使用 Azure PowerShell [Add-AzKeyVaultManagedStorageAccoun](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) Cmdlet，在您的 Key Vault 執行個體中建立受控儲存體帳戶。 `-DisableAutoRegenerateKey` 參數會指定「不」重新產生儲存體帳戶金鑰。

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

在不搭配金鑰重新產生成功新增儲存體帳戶時，您應該會看到類似於以下範例的輸出：

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

### <a name="enable-key-regeneration"></a>啟用金鑰重新產生

如果希望 Key Vault 定期重新產生儲存體帳戶金鑰，您可以使用 Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) Cmdlet 來設定重新產生期間。 在此範例中，我們會設定為期三天的重新產生期間。 需要輪替時，Key Vault 會重新產生非作用中的金鑰，然後將新建立的金鑰設定為作用中。 只有其中一個金鑰會用來在任何時間簽發 SAS 權杖。 這就是使用中的金鑰。

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

在搭配金鑰重新產生成功新增儲存體帳戶時，您應該會看到類似於以下範例的輸出：

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

## <a name="shared-access-signature-tokens"></a>共用存取簽章權杖

您也可以要求 Key Vault 產生共用存取簽章權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以對用戶端授與儲存體帳戶中資源的存取權，而不必共用帳戶金鑰。 共用存取簽章可讓您安全地共用儲存體資源，而不會將帳戶金鑰外洩。

本節中的命令會完成下列動作：

- 設定帳戶共用存取簽章定義。
- 為 Blob、檔案、資料表和佇列服務建立帳戶共用存取簽章權杖。 權杖是針對資源類型服務、容器和物件所建立。 權杖是針對所有權限、透過 https 和指定的開始和結束日期來建立。
- 在保存庫中設定 Key Vault 受控儲存體共用存取簽章定義。 定義具有已建立的共用存取簽章權杖的範本 URI。 定義的共用存取簽章類型為 `account`，且有效期為 N 天。
- 確認已將共用存取簽章儲存在金鑰保存庫中作為祕密。
-
### <a name="set-variables"></a>設定變數

首先，在下列步驟中設定 PowerShell Cmdlet 所要使用的變數。 請務必更新 <YourStorageAccountName> 和 <YourKeyVaultName> 預留位置。

我們也會使用 Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) Cmdlet 取得您的 Azure 儲存體帳戶內容。

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>建立共用存取簽章權杖

使用 Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken) Cmdlet 來建立共用存取簽章定義。

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
$SasToken 的值看起來會像這樣。

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>產生共用存取簽章定義

使用 Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) Cmdlet 來建立共用存取簽章定義。  您可以將選擇的名稱提供給 `-Name` 參數。

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>驗證共用存取簽章定義

您可以使用 Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) 命令，確認共用存取簽章定義已儲存在金鑰保存庫中。

首先，在金鑰保存庫中尋找共用存取簽章定義。

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

對應至您 SAS 定義的祕密將具有下列屬性：

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

您現在可以使用 [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) Cmdlet 和祕密 `Name` 屬性來檢視祕密的內容。

```azurepowershell-interactive
Write-Host (Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>).SecretValue | ConvertFrom-SecureString -AsPlainText
```

此命令的輸出會顯示您的 SAS 定義字串。


## <a name="next-steps"></a>後續步驟

- [受控儲存體帳戶金鑰範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) \(英文\)
- [Key Vault PowerShell 參考](/powershell/module/az.keyvault/#key_vault)