---
title: 對應用程式授與 Azure 金鑰保存庫的存取權限 - Azure Key Vault | Microsoft Docs
description: 了解如何對許多應用程式授與金鑰保存庫的存取權限
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d5086377b0bb7f3ca2ece643f82a4e45156f1955
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78184837"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>使用存取控制原則提供 Key Vault 驗證

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要向 Key Vault 驗證雲端式應用程式，最簡單的方法是使用受控識別；如需詳細資訊，請參閱[使用 App Service 受控識別存取 Azure Key Vault](managed-identity.md)。  如果您要建立內部部署應用程式、進行本機開發，或無法使用受控識別，您可以改為以手動方式註冊服務主體，並使用存取控制原則提供金鑰保存庫的存取權。  

金鑰保存庫最多可支援 1024 個存取原則項目，每個項目分別會將一組不同的權限授與「主體」： 例如，這是[適用於 .NET 的 Azure Key Vault 用戶端程式庫快速入門](quick-create-net.md)中的主控台應用程式存取金鑰保存庫的方式。

如需 Key Vault 存取控制的完整資訊，請參閱 [Azure Key Vault 安全性：身分識別和存取管理](overview-security.md#identity-and-access-management)。 如需 [金鑰、秘密和憑證](about-keys-secrets-and-certificates.md)存取控制的完整資訊，請參閱： 

- [金鑰存取控制](about-keys-secrets-and-certificates.md#key-access-control)
- [祕密存取控制](about-keys-secrets-and-certificates.md#secret-access-control)
- [憑證存取控制](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites

- 一個金鑰保存庫。 您可以使用現有的金鑰保存庫，或依照下列其中一個快速入門中的步驟建立新的金鑰保存庫：
   - [使用 Azure CLI 建立金鑰保存庫](quick-create-cli.md)
   - [使用 Azure PowerShell 建立金鑰保存庫](quick-create-powershell.md)
   - [使用 Azure 入口網站建立金鑰保存庫](quick-create-portal.md)。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/overview)。 或者，您可以使用 [Azure 入口網站](https://portal.azure.com)。

## <a name="grant-access-to-your-key-vault"></a>授與對金鑰保存庫的存取權

每個金鑰保存庫存取原則項目分別會將一組不同的權限授與主體：

- **應用程式** 如果應用程式是雲端式的，您即應改為[使用受控識別來存取 Azure Key Vault](managed-identity.md) (可能的話)
- **Azure AD 群組** 雖然金鑰保存庫僅支援 1024 個存取原則項目，但您可以將多個應用程式和使用者新增至單一 Azure AD 群組，然後再將該群組新增為存取控制原則的單一項目。
- **使用者** **不建議**為使用者授與直接存取金鑰保存庫的權限。 在理想情況下，使用者應先新增至 Azure AD 群組，繼而獲得金鑰保存庫的存取權。 請參閱 [Azure Key Vault 安全性：身分識別和存取管理](overview-security.md#identity-and-access-management)。


### <a name="get-the-objectid"></a>取得 objectID

若要為應用程式、Azure AD 群組或使用者授與金鑰保存庫的存取權，您必須先取得其 objectId。

#### <a name="applications"></a>應用程式

應用程式的 objectId 會與其相關聯的服務主體相對應。 如需服務主體的完整資訊， 請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。 

有兩種方式可取得應用程式的 objectId。  第一種方式是向 Azure Active Directory 註冊您的應用程式。 若要這麼做，請依照[使用 Microsoft 身分識別平台來註冊應用程式](../active-directory/develop/quickstart-register-app.md)快速入門中的步驟操作。 註冊完成時，objectID 會列示為「應用程式 (用戶端) 識別碼」。

第二種方式是在終端機視窗中建立服務主體。 在 Azure CLI 中，使用 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令。

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

objectId 會在輸出中列示為 `clientID`。

在 Azure PowerShell 中，使用 [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) Cmdlet。


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

objectId 會在輸出中列示為 `Id` (而非 `ApplicationId`)。

#### <a name="azure-ad-groups"></a>Azure AD 群組

您可以將多個應用程式和使用者新增至 Azure AD 群組，然後為該群組授與金鑰保存庫的存取權。  如需詳細資訊，請參閱下方的[在 Azure AD 群組中建立及新增成員](#creating-and-adding-members-to-an-azure-ad-group)一節。

若要使用 Azure CLI 尋找 Azure AD 群組的 objectId，請使用 [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) 命令。 由於您的組織中可能會有大量的群組，因此您也應為 `--display-name` 參數提供搜尋字串。

```azurecli-interactive
az ad group list --display-name <search-string>
```
objectId 會在 JSON 中傳回：

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

若要使用 Azure PowerShell 尋找 Azure AD 群組的 objectId，請使用 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) Cmdlet。 由於您的組織中可能會有大量的群組，因此您也可以為 `-SearchString` 參數提供搜尋字串。

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

在輸出中，objectId 會列示為 `Id`：

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>使用者

您也可以將個別使用者新增至金鑰保存庫的存取控制原則。 **我們不建議這麼做。** 我們建議您將使用者新增至 Azure AD 群組，然後在原則上新增該群組。

如果您還是想要使用 Azure CLI 來尋找使用者，請使用 [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) 命令，將使用者的電子郵件地址傳至 `--id` 參數。


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

使用者的 objectId 會在輸出中傳回：

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

若要使用 Azure PowerShell 來尋找使用者，請使用 [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) Cmdlet，並將使用者的電子郵件地址傳至 `-UserPrincipalName` 參數。

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

使用者的 objectId 會在輸出中傳回：`Id`。

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>對主體授與金鑰保存庫的存取權

現在您已有主體的 objectID，接下來即可為金鑰保存庫建立存取原則，讓它據以獲得金鑰和密碼的取得、列出、設定和刪除權限，以及您想要的任何其他權限。

在 Azure CLI 中，此作業可藉由將 objectId 傳至 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令來完成。

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

在 Azure PowerShell 中，此作業可藉由將 objectId 傳至 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) Cmdlet 來完成。 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>在 Azure AD 群組中建立及新增成員

您可以建立 Azure AD 群組、將應用程式和使用者新增至該群組，然後為該群組授與金鑰保存庫的存取權。  這可讓您將多個應用程式新增至金鑰保存庫作為單一存取原則項目，而無須為使用者授與直接存取金鑰保存庫的權限 (我們不鼓勵這麼做)。 如需詳細資訊，請參閱[使用 Azure Active Directory 群組來管理應用程式和資源的存取權](../active-directory/fundamentals/active-directory-manage-groups.md)。

### <a name="additional-prerequisites"></a>其他先決條件

除了[上述必要條件](#prerequisites)以外，您還需要在 Azure Active Directory 租用戶中建立/編輯群組的權限。 如果您沒有權限，則可能需要連絡 Azure Active Directory 系統管理員。

如果您想要使用 PowerShell，您也將需要 [Azure AD PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>建立 Azure Active Directory 群組

使用 Azure CLI [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) 命令或 Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) Cmdlet 來建立新的 Azure Active Directory 群組。


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

無論採用哪種方式，均請記下新建立的群組 GroupId，因為您在下列步驟中將會用到。

### <a name="find-the-objectids-of-your-applications-and-users"></a>尋找應用程式和使用者的 objectid

您可以在 Azure CLI 中使用 [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) 命令並搭配 `--show-mine` 參數來尋找應用程式的 objectid。

```azurecli-interactive
az ad sp list --show-mine
```

在 Azure PowerShell 中使用 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) Cmdlet，並將搜尋字串傳至 `-SearchString` 參數，尋找應用程式的 objectid。

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

若要尋找使用者的 objectid，請依照前述[使用者](#users)一節中的步驟操作。

### <a name="add-your-applications-and-users-to-the-group"></a>將您的應用程式和使用者新增至群組

現在，將 objectid 新增至新建立的 Azure AD 群組。

在 Azure CLI 中使用 [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)，並將 objectId 傳至 `--member-id` 參數。


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

在 Azure PowerShell 中使用 [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) Cmdlet，並將 objectId 傳至 `-MemberObjectId` 參數。

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>為 AD 群組授與金鑰保存庫的存取權

最後，使用 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令或 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) Cmdlet，為 AD 群組授與金鑰保存庫的權限。 例如，請參閱前述的[為應用程式、Azure AD 群組或使用者授與金鑰保存庫的存取權](#give-the-principal-access-to-your-key-vault)一節。

應用程式也需要至少一個指派給金鑰保存庫的身分識別與存取管理 (IAM) 角色。 否則，應用程式將無法登入，且將會因為沒有足夠的權限可存取訂用帳戶而失敗。

## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 安全性：身分識別和存取管理](overview-security.md#identity-and-access-management)
- [使用 App Service 受控識別提供 Key Vault 驗證](managed-identity.md)
- [關於金鑰、密碼與憑證](about-keys-secrets-and-certificates.md)
- [保護您的 Key Vault](key-vault-secure-your-key-vault.md)。
- [Azure Key Vault 開發人員指南](key-vault-developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](key-vault-best-practices.md)
