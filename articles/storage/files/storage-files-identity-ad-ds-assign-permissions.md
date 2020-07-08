---
title: 控制對 Azure 檔案共用的存取-內部部署 AD DS 驗證
description: 瞭解如何將許可權指派給代表您儲存體帳戶的 Active Directory Domain Services 身分識別。 這可讓您使用以身分識別為基礎的驗證來控制存取。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 3335bfbed592c9e13d5be437e8013a89ae86e970
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510627"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>第二部分：將共用層級許可權指派給身分識別

在開始本文之前，請先確定您已完成上一篇文章，[為您的帳戶啟用 AD DS 驗證](storage-files-identity-ad-ds-enable.md)。

在您的儲存體帳戶上啟用 Active Directory Domain Services （AD DS）驗證之後，您必須設定共用層級許可權，才能存取您的檔案共用。 您想要用來存取 Azure 檔案共用資源的身分識別，必須是同時存在於 AD DS 和 Azure AD 中的混合式身分識別。 例如，假設您的 AD DS 中有一個使用者， user1@onprem.contoso.com 而且您已同步處理至 Azure AD， user1@contoso.com Azure AD Connect 同步處理。若要允許這位使用者存取 Azure 檔案儲存體，您必須將共用層級許可權指派給 user1@contoso.com 。 相同的概念適用于群組或服務主體。 因此，您必須使用 Azure AD Connect 同步，將 AD DS 中的使用者和群組同步至 Azure AD。 

共用層級許可權必須指派給 Azure AD 身分識別，代表您 AD DS 中的相同使用者或群組，以支援 Azure 檔案共用的 AD DS 驗證。 AD DS authentication 不支援對僅存在於 Azure AD 中的身分識別進行驗證和授權，例如 Azure 受控識別（Msi）。 本文示範如何將檔案共用的共用層級許可權指派給身分識別。


## <a name="share-level-permissions"></a>共用層級許可權

一般來說，我們建議您針對代表使用者和身分識別群組的 Azure AD 群組，使用共用層級的存取管理許可權，然後利用 Windows Acl 進行目錄/檔案層級的細微存取控制。 

有三個 Azure 內建角色可將共用層級許可權授與使用者：

- **儲存體檔案資料 Smb 共用讀取器**允許透過 smb 在 Azure 儲存體檔案共用中進行讀取存取。
- **儲存體檔案資料 SMB 共用參與者**允許透過 smb 在 Azure 儲存體檔案共用中進行讀取、寫入和刪除存取。
- **儲存體檔案資料 SMB 共用提高許可權參與者**允許透過 smb 在 Azure 儲存體檔案共用中讀取、寫入、刪除及修改 Windows acl。

> [!IMPORTANT]
> 檔案共用的完整系統管理控制權，包括取得檔案擁有權的能力，需要使用儲存體帳戶金鑰。 Azure AD 認證不支援管理控制。

您可以使用 [Azure 入口網站]、[Azure PowerShell] 或 [Azure CLI]，將內建角色指派給使用者的 Azure AD 身分識別，以授與共享層級許可權。

## <a name="assign-an-rbac-role"></a>指派 RBAC 角色

### <a name="azure-portal"></a>Azure 入口網站

若要使用[Azure 入口網站](https://portal.azure.com)將 RBAC 角色指派給 Azure AD 身分識別，請依照下列步驟執行：

1. 在 Azure 入口網站中，移至您的檔案共用，或[建立檔案共用](storage-how-to-create-file-share.md)。
1. 選取 [存取控制 (IAM)]。
1. 選取 [**新增角色指派**]
1. 在 [**新增角色指派**] 分頁中，從 [**角色**] 清單中選取適當的內建角色（儲存體檔案資料 smb 共用讀取器、儲存體檔案資料 smb 共用參與者）。 將 [**指派存取權**] 保留為預設設定： [ **Azure AD 使用者]、[群組] 或 [服務主體**]。 依名稱或電子郵件地址選取目標 Azure AD 身分識別。 選取的 Azure AD 身分識別必須是混合式身分識別，而且不能是僅限雲端的身分識別。 這表示相同的身分識別也會以 AD DS 表示。
1. 選取 [**儲存**] 以完成角色指派作業。

### <a name="powershell"></a>PowerShell

下列 PowerShell 範例說明如何根據登入名稱，將 RBAC 角色指派給 Azure AD 身分識別。 如需使用 PowerShell 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure PowerShell 管理存取](../../role-based-access-control/role-assignments-powershell.md)。

執行下列範例腳本之前，請以您的值取代預留位置值（包括括弧）。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
下列 CLI 2.0 命令會根據登入名稱，將 RBAC 角色指派給 Azure AD 身分識別。 如需有關使用 Azure CLI 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure CLI 來管理存取權](../../role-based-access-control/role-assignments-cli.md)。 

執行下列範例腳本之前，請記得使用您自己的值來取代預留位置值（包括括弧）。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>後續步驟

既然您已指派共用層級許可權，就必須設定目錄和檔案層級的許可權。 繼續閱讀下一篇文章。

[第三部分：透過 SMB 設定目錄和檔案層級許可權](storage-files-identity-ad-ds-configure-permissions.md)