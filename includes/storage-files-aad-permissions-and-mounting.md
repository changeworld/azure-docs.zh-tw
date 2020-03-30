---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 23550c83e76631e44d5036e0a038f01b61a79f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208238"
---
## <a name="assign-access-permissions-to-an-identity"></a>將存取權限指派給身分識別

要使用基於標識的身份驗證訪問 Azure 檔資源，標識（使用者、組或服務主體）必須在共用級別具有必要的許可權。 此過程類似于指定 Windows 共用許可權，其中指定特定使用者對檔共用的訪問類型。 本節中的指引示範如何將檔案共用的讀取、寫入或刪除權限指派給身分識別。

我們引入了三個 Azure 內置角色，用於向使用者授予共用級許可權：

- **存儲檔 SMB 共用讀取器**允許通過 SMB 在 Azure 存儲檔共用中讀取存取。
- **存儲檔 SMB 共用參與者**允許通過 SMB 在 Azure 存儲檔共用中讀取、寫入和刪除訪問。
- **存儲檔 SMB 共用提升參與者**允許通過 SMB 讀取、寫入、刪除和修改 Azure 存儲檔共用中的 NTFS 許可權。

> [!IMPORTANT]
> 檔共用的完全管理控制（包括獲取檔擁有權的能力）需要使用存儲帳戶金鑰。 Azure AD 認證不支援管理控制。

可以使用 Azure 門戶、PowerShell 或 Azure CLI 將內置角色指派給使用者的 Azure AD 標識，以便授予共用級別許可權。

> [!NOTE]
> 如果計畫使用 AD 進行身份驗證，請記住將 AD 憑據同步到 Azure AD。 從 AD 到 Azure AD 的密碼雜湊同步是可選的。 共用級別許可權將授予從 AD 同步的 Azure AD 標識。

#### <a name="azure-portal"></a>Azure 入口網站
要將 RBAC 角色指派給 Azure AD 標識，請使用[Azure 門戶](https://portal.azure.com)，請按照以下步驟操作：

1. 在 Azure 門戶中，轉到檔共用，或[創建檔共用](../articles/storage/files/storage-how-to-create-file-share.md)。
2. 選擇**存取控制 （IAM）。**
3. 選擇 **"添加角色指派"**
4. 在 **"添加角色指派**"邊欄選項卡中，從**角色**清單中選擇適當的內置角色（存儲檔資料 SMB 共用讀取器、存儲檔資料 SMB 共用參與者）。 在預設設置下保留**對的"分配"存取權限****：Azure AD 使用者、組或服務主體**。 按名稱或電子郵件地址選擇目標 Azure AD 標識。
5. 選擇 **"保存"** 以完成角色指派操作。

#### <a name="powershell"></a>PowerShell

下面的 PowerShell 示例演示如何根據登錄名稱將 RBAC 角色指派給 Azure AD 標識。 如需使用 PowerShell 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure PowerShell 管理存取](../articles/role-based-access-control/role-assignments-powershell.md)。

在運行以下示例腳本之前，請記住將預留位置值（包括括弧）替換為您自己的值。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
以下 CLI 2.0 命令演示如何根據登錄名稱將 RBAC 角色指派給 Azure AD 標識。 有關使用 Azure CLI 分配 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure CLI 管理訪問](../articles/role-based-access-control/role-assignments-cli.md)。 

在運行以下示例腳本之前，請記住將預留位置值（包括括弧）替換為您自己的值。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>透過 SMB 設定 NTFS 權限 
使用 RBAC 指派共用層級權限之後，必須在根、目錄或檔案層級指派適當的 NTFS 權限。 將共用級別許可權視為高級網守，用於確定使用者是否可以訪問共用。 而 NTFS 許可權在更精細的級別執行，以確定使用者可以在目錄或檔級別執行哪些操作。

Azure 檔案支援全套 NTFS 基本和進階權限。 您可以通過安裝共用，然後使用 Windows 檔資源管理器或運行 Windows [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl)命令來查看和配置 Azure 檔共用中的目錄和檔的 NTFS 許可權。 

要配置具有超級使用者許可權的 NTFS，您必須使用域加入的 VM 中的存儲帳戶金鑰來裝載共用。 按照下一節中的說明從命令提示符裝載 Azure 檔共用，並相應地配置 NTFS 許可權。

檔案共用的根目錄支援下列權限集合：

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>使用 icacls 設定 NTFS 權限
使用以下 Windows 命令，授與完整權限至檔案共用下的所有目錄和檔案，包括根目錄。 請記得使用您自己的值取代預留位置值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

有關如何使用 Icacls 設置 NTFS 許可權以及不同類型的受支援許可權的詳細資訊，請參閱[icacls 的命令列引用](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

### <a name="mount-a-file-share-from-the-command-prompt"></a>從命令提示字元裝載檔案共用

使用 Windows **net use** 命令以裝載 Azure 檔案共用。 請記住，將以下示例中的預留位置值替換為您自己的值。 有關安裝檔共用的詳細資訊，請參閱使用[Windows 檔共用](../articles/storage/files/storage-how-to-use-files-windows.md)。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>使用 Windows 檔資源管理器配置 NTFS 許可權
使用 Windows 檔資源管理器向檔共用下的所有目錄和檔（包括根目錄）授予完全許可權。

1. 打開 Windows 檔資源管理器並按右鍵檔/目錄並選擇**屬性**
2. 按一下"**安全**"選項卡
3. 按一下**編輯.**. .按鈕以更改許可權
4. 您可以更改現有使用者的許可權，或按一下"**添加..."** 向新使用者授予許可權
5. 在添加新使用者的提示視窗中，在 **"輸入要選擇物件名稱**"框中輸入要授予許可權的目標使用者名，然後按一下 **"選中名稱"** 以查找目標使用者的完整 UPN 名稱。
7.  按一下 **"確定"**
8.  在"安全"選項卡中，選擇要授予新添加的使用者的擁有權限
9.  按一下 [套用]****

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>從已加入網域的 VM 中裝載檔案共用

以下過程驗證檔共用和存取權限是否設置正確，以及可以從加入域的 VM 訪問 Azure 檔共用：

使用已授予許可權的 Azure AD 標識登錄到 VM，如下圖所示。 如果已為 Azure 檔啟用 AD 身份驗證，請使用 AD 憑據。 對於 Azure AD DS 身份驗證，請使用 Azure AD 憑據登錄。

![螢幕擷取畫面顯示使用者驗證的 Azure AD 登入畫面](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

使用以下命令裝載 Azure 檔共用。 請記得以您自己的值取代預留位置值。 由於已過身份驗證，因此無需提供存儲帳戶金鑰、AD 憑據或 Azure AD 憑據。 支援使用 AD 或 Azure AD DS 進行身份驗證的單一登入體驗。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
