---
title: Azure Key Vault 受控儲存體帳戶 - PowerShell 版本
description: 託管儲存帳戶功能在 Azure 密鑰保管庫和 Azure 儲存帳戶之間提供無縫整合。
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: f8c526148e37ba1b716aafd32dcc3f242358f1eb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427779"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>使用金鑰保管庫和 Azure PowerShell 管理儲存帳戶金鑰

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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

- [安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps?view=azps-2.6.0)。
- [建立金鑰保存庫](quick-create-powershell.md)
- [建立 Azure 儲存帳戶](../../storage/common/storage-account-create.md?tabs=azure-powershell)。 存儲帳戶名稱只能使用小寫字母和數位。 名稱的長度必須介於 3 到 24 個字元之間。
      

## <a name="manage-storage-account-keys"></a>管理預取帳戶金鑰

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用[連接-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet 驗證您的 PowerShell 作業階段。 

```azurepowershell-interactive
Connect-AzAccount
```
如果您有多個 Azure 訂閱,則可以使用[獲取-Az訂閱](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)cmdlet 列出它們,並指定要與[Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet 一起使用的訂閱。 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>設定變數

首先,在以下步驟中將 PowerShell cmdlet 使用的變數設置為使用。 請務必更新<YourResourceGroupName><YourStorageAccountName>、<YourKeyVaultName>和 占位符,並將$keyVaultSpAppId設置為`cfa8b339-82a2-471a-a3c9-0fc0be7a4093`(如上文在[服務主體應用程式 ID](#service-principal-application-id)中指定)。

我們還將使用 Azure PowerShell[獲取-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0)和[Get-AzStorage 帳戶](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0)cmdlet 來取得使用者 ID 和 Azure 儲存帳戶的上下文。

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>授金鑰保存儲存帳號存取權限

在 Key Vault 可以存取並管理您的儲存體帳戶金鑰之前，您必須為它授權以存取您的儲存體帳戶。 Key Vault 應用程式需要「列出」** 及「重新產生」** 儲存體帳戶金鑰的使用權限。 這些使用權限是透過內建的 RBAC 角色[儲存體帳戶金鑰操作員服務角色](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)來啟用。 

使用此 Azure PowerShell[新-AzRoleAssign](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet 將此角色分配給密鑰保管庫服務主體,將範圍限制到存儲帳戶。

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

如果 Key Vault 在您的儲存體帳戶上已經被新增至該角色，您將會收到「角色指派已存在。」** 的錯誤。 您也可以使用 Azure 入口網站中該儲存體帳戶的 [存取控制 (IAM)] 頁面來驗證角色指派。  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>將使用者帳戶使用權限授與受控儲存體帳戶

使用 Azure PowerShell[集-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) cmdlet 更新金鑰保管庫存取策略並將儲存帳戶許可權授予使用者帳戶。

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

請注意，適用於儲存體帳戶的使用權限並不會在該儲存體於 Azure 入口網站中的 [存取原則] 頁面上提供。

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>將受控儲存體帳戶新增到您的 Key Vault 執行個體

使用 Azure PowerShell[添加-AzKeyVault 託管儲存帳戶](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0)cmdlet 在金鑰保管庫實例中創建託管存儲帳戶。 `-DisableAutoRegenerateKey` 參數會指定「不」重新產生儲存體帳戶金鑰。

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

如果希望密鑰保管庫定期重新生成存儲帳戶密鑰,則可以使用 Azure PowerShell[添加-AzKeyVault 託管存儲帳戶](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0)cmdlet 來設置再生週期。 在此示例中,我們將再生周期設置為三天。 三天後,金鑰保管庫將重新生成"key2"並將活動密鑰從"key2"交換到"key1"。

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

## <a name="shared-access-signature-tokens"></a>分享存取簽署權杖

您還可以要求金鑰保管庫生成共享訪問簽名權杖。 共用存取簽章可提供您儲存體帳戶中資源的委派存取。 您可以授予用戶端對儲存帳戶中的資源的訪問許可權,而無需共用您的帳戶密鑰。 共享訪問簽名為您提供了一種安全的方式來分享存儲資源,而不會危及您的帳戶密鑰。

本節中的指令完成以下操作:

- 設置帳戶共用訪問簽名定義。 
- 為 Blob、檔案、表和佇列服務創建帳戶共用訪問簽名權杖。 為資源類型服務、容器和物件創建權杖。 令牌使用所有許可權、Htcrit以及指定的開始日期和結束日期創建。
- 在保管庫中設置密鑰保管庫託管存儲共享訪問簽名定義。 該定義具有所創建的共享訪問簽名權杖的範本 URI。 該定義具有共用訪問簽名類型`account`,有效期為 N 天。
- 驗證共享訪問簽名是否作為機密保存在密鑰保管庫中。
- 
### <a name="set-variables"></a>設定變數

首先,在以下步驟中將 PowerShell cmdlet 使用的變數設置為使用。 請務必更新<YourStorageAccountName><YourKeyVaultName>和占位符。

我們還將使用 Azure PowerShell[新儲存上下文](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0)cmdlet 來獲取 Azure 儲存帳戶的上下文。

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>建立分享存取簽署權杖

使用 Azure PowerShell[新儲存帳戶 AsToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) cmdlet 創建共享存取簽名定義。
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
$sasToken的值將與此類似。

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>產生分享存取簽章定義

使用 Azure PowerShell[集-AzKeyVault 託管儲存Sas定義](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0)cmdlet 創建共用存取簽名定義。  您可以為`-Name`參數提供您選擇的名稱。

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>驗證共享存取簽章定義

您可以使用 Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) cmdlet 驗證共享存取簽名定義是否存儲在金鑰保管庫中。

首先,在密鑰保管庫中查找共享訪問簽名定義。

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

與 SAS 定義對應的機密將具有以下屬性:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

您現在可以使用[Get-AzKeyVault 秘密](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)cmdlet 和`Name`機密屬性來查看該機密的內容。

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

此指令的輸出將顯示您的 SAS 定義字串。


## <a name="next-steps"></a>後續步驟

- [受控儲存體帳戶金鑰範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) \(英文\)
- [Key Vault PowerShell 參考](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
