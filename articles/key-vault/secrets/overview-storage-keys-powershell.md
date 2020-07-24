---
title: Azure Key Vault 受控儲存體帳戶 - PowerShell 版本
description: 受控儲存體帳戶功能可提供 Azure Key Vault 與 Azure 儲存體帳戶之間的完美整合。
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 77cbd5a3c293b137f49a11263580ef45407c6c2b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090462"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>使用 Key Vault 和 Azure PowerShell 管理儲存體帳戶金鑰

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>服務主體應用程式識別碼

Azure AD 租使用者會提供每個已註冊的應用程式與[服務主體](/azure/active-directory/develop/developer-glossary#service-principal-object)。 服務主體會作為應用程式識別碼，在授權設定期間用於透過 RBAC 存取其他 Azure 資源。

Key Vault 是在所有 Azure AD 租使用者中預先註冊的 Microsoft 應用程式。 Key Vault 會在每個 Azure 雲端中的相同應用程式識別碼下註冊。

| 租用戶 | Cloud | 應用程式識別碼 |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公用 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>先決條件

若要完成本指南，您必須先執行下列動作：

- [安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps?view=azps-2.6.0)。
- [建立金鑰保存庫](quick-create-powershell.md)
- [建立 Azure 儲存體帳戶](../../storage/common/storage-account-create.md?tabs=azure-powershell)。 儲存體帳戶名稱必須僅使用小寫字母和數位。 名稱的長度必須介於3到24個字元之間。
      

## <a name="manage-storage-account-keys"></a>管理儲存體帳戶金鑰

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用[disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Cmdlet 來驗證您的 PowerShell 會話。 

```azurepowershell-interactive
Connect-AzAccount
```
如果您有多個 Azure 訂用帳戶，您可以使用[get-azsubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) Cmdlet 加以列出，並指定您想要搭配[set-azcoNtext 指令程式](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)使用的訂用帳戶。 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>設定變數

首先，在下列步驟中設定 PowerShell Cmdlet 所要使用的變數。 請務必更新 <YourResourceGroupName> 、 <YourStorageAccountName> 和 <YourKeyVaultName> 預留位置，並將 $keyVaultSpAppId 設定為 `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` （如上面的[服務主體應用程式識別碼](#service-principal-application-id)中所指定）。

我們也會使用 Azure PowerShell [set-azcoNtext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0)和[new-azstorageaccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) Cmdlet 來取得您的使用者識別碼和 Azure 儲存體帳戶的內容。

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
> 對於傳統儲存體帳戶，請使用「主要」和「次要」進行 $storageAccountKey <br>
> 針對傳統儲存體帳戶，請使用 ' Get-azresource-Name "ClassicStorageAccountName"-ResourceGroupName $resourceGroupName ' 而不是 of'Get-New-azstorageaccount '

### <a name="give-key-vault-access-to-your-storage-account"></a>授與 Key Vault 對儲存體帳戶的存取權

在 Key Vault 可以存取並管理您的儲存體帳戶金鑰之前，您必須為它授權以存取您的儲存體帳戶。 Key Vault 應用程式需要「列出」** 及「重新產生」** 儲存體帳戶金鑰的使用權限。 這些使用權限是透過內建的 RBAC 角色[儲存體帳戶金鑰操作員服務角色](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)來啟用。 

使用 Azure PowerShell [new-azroleassignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) Cmdlet，將此角色指派給 Key Vault 服務主體，並將範圍限制為您的儲存體帳戶。

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
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

如果 Key Vault 在您的儲存體帳戶上已經被新增至該角色，您將會收到「角色指派已存在。」** 錯誤內容。 您也可以使用 Azure 入口網站中該儲存體帳戶的 [存取控制 (IAM)] 頁面來驗證角色指派。  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>將使用者帳戶使用權限授與受控儲存體帳戶

使用 Azure PowerShell [set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) Cmdlet 來更新 Key Vault 存取原則，並將儲存體帳戶許可權授與您的使用者帳戶。

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

請注意，適用於儲存體帳戶的使用權限並不會在該儲存體於 Azure 入口網站中的 [存取原則] 頁面上提供。

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>將受控儲存體帳戶新增到您的 Key Vault 執行個體

使用 Azure PowerShell [AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) Cmdlet，在您的 Key Vault 實例中建立受控儲存體帳戶。 `-DisableAutoRegenerateKey` 參數會指定「不」重新產生儲存體帳戶金鑰。

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

如果您想要 Key Vault 定期重新產生儲存體帳戶金鑰，您可以使用 Azure PowerShell [AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) Cmdlet 來設定重新產生期間。 在此範例中，我們會設定三天的重新產生期間。 當您需要輪替時，Key Vault 重新產生非作用中的金鑰，然後將新建立的金鑰設定為作用中。 只有其中一個金鑰會用來一次發出 SAS 權杖。 這是使用中的金鑰。

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

您也可以要求 Key Vault 產生共用存取簽章權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以對用戶端授與儲存體帳戶中資源的存取權，而不需要共用您的帳戶金鑰。 共用存取簽章可讓您以安全的方式共用儲存體資源，而不會危害您的帳戶金鑰。

本節中的命令會完成下列動作：

- 設定帳戶共用存取簽章定義。 
- 為 Blob、檔案、資料表和佇列服務建立帳戶共用存取簽章權杖。 權杖是針對資源類型服務、容器和物件所建立。 權杖是以擁有權限（透過 HTTPs）和指定的開始和結束日期來建立。
- 在保存庫中設定 Key Vault 管理的儲存體共用存取簽章定義。 定義具有已建立的共用存取簽章權杖的範本 URI。 定義具有共用存取簽章類型 `account` ，且有效期為 N 天。
- 確認已將共用存取簽章儲存在金鑰保存庫中做為密碼。
- 
### <a name="set-variables"></a>設定變數

首先，在下列步驟中設定 PowerShell Cmdlet 所要使用的變數。 請務必更新 <YourStorageAccountName> 和 <YourKeyVaultName> 預留位置。

我們也會使用 Azure PowerShell [AzStorageCoNtext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) Cmdlet 來取得 Azure 儲存體帳戶的內容。

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>建立共用存取簽章權杖

使用 Azure PowerShell AzStorageAccountSASToken Cmdlet 來建立共用存取[簽](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0)章定義。
 
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

使用 Azure PowerShell AzKeyVaultManagedStorageSasDefinition Cmdlet 來建立共用存取[簽](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0)章定義。  您可以將您選擇的名稱提供給 `-Name` 參數。

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>驗證共用存取簽章定義

您可以使用 Azure PowerShell [AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) Cmdlet，確認共用存取簽章定義已儲存在金鑰保存庫中。

首先，在金鑰保存庫中尋找共用存取簽章定義。

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

對應至您 SAS 定義的密碼將具有下列屬性：

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

您現在可以使用[AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) Cmdlet 和 secret `Name` 屬性來查看該秘密的內容。

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

此命令的輸出會顯示您的 SAS 定義字串。


## <a name="next-steps"></a>後續步驟

- [受控儲存體帳戶金鑰範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) \(英文\)
- [Key Vault PowerShell 參考](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
