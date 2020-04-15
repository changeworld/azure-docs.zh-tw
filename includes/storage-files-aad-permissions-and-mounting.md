---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/11/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: b6a8bc083b589463b67f2e25e262b15456355d05
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383854"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. 向識別分配存取權限

要使用基於識別的身份驗證訪問 Azure 檔資源,標識(使用者、組或服務主體)必須在共用級別具有必要的許可權。 此過程類似於指定 Windows 共享許可權,其中指定特定使用者對檔共享的訪問類型。 本節中的指引示範如何將檔案共用的讀取、寫入或刪除權限指派給身分識別。 

我們引入了三個 Azure 內建角色,用於向使用者授予共用級許可權:

- **儲存檔案 SMB 共用讀取器**允許透過 SMB 在 Azure 儲存檔案共享中讀取存取。
- **儲存檔案 SMB 共用參與者**允許透過 SMB 在 Azure 儲存檔案共享中讀取、寫入和刪除存取。
- **儲存檔案 SMB 共用提升參與者**允許透過 SMB 讀取、寫入、刪除和修改 Azure 儲存檔案共用中的 NTFS 許可權。

> [!IMPORTANT]
> 檔案共用的完全管理控制(包括獲取檔擁有權的能力)需要使用存儲帳戶密鑰。 Azure AD 認證不支援管理控制。

可以使用 Azure 門戶、PowerShell 或 Azure CLI 將內建角色分配給使用者的 Azure AD 標識,以便授予共用級別許可權。

> [!NOTE]
> 如果計劃使用 AD 進行身份驗證,請記住將 AD 認證同步到 Azure AD。 從 AD 到 Azure AD 的密碼哈希同步是可選的。 共用等級權限將授予從 AD 同步的 Azure AD 標識。

一般建議是,對表示一組使用者和身份的 AD 組使用高級別訪問管理共用級別許可權,然後利用 NTFS 許可權在目錄/檔級別進行精細存取控制。 

#### <a name="azure-portal"></a>Azure 入口網站
要將 RBAC 角色分配給 Azure AD 識別,請使用[Azure 門戶](https://portal.azure.com),請按照以下步驟操作:

1. 在 Azure 門戶中,轉到檔案分享,或[建立檔案共享](../articles/storage/files/storage-how-to-create-file-share.md)。
2. 選擇**訪問控制 (IAM)。**
3. 選擇 **"添加角色分配"**
4. 在 **「添加角色分配**」邊欄選項卡中,從**角色**清單中選擇適當的內建角色(儲存檔案數據 SMB 共用讀取器、儲存檔案數據 SMB 共用參與者)。 在預設設定下保留**對的「設定」 權限****:Azure AD 使用者、組或服務主體**。 按名稱或電子郵件地址選擇目標 Azure AD 標識。
5. 選擇 **「保存」** 以完成角色分配操作。

#### <a name="powershell"></a>PowerShell

下面的 PowerShell 範例展示如何根據登入名稱將 RBAC 角色分配給 Azure AD 標識。 如需使用 PowerShell 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure PowerShell 管理存取](../articles/role-based-access-control/role-assignments-powershell.md)。

在執行以下範例文本之前,請記住將占位符值(包括括弧)替換為您自己的值。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
以下 CLI 2.0 命令展示如何根據登入名稱將 RBAC 角色分配給 Azure AD 標識。 有關使用 Azure CLI 分配 RBAC 角色的詳細資訊,請參閱[使用 RBAC 和 Azure CLI 管理存取](../articles/role-based-access-control/role-assignments-cli.md)。 

在執行以下範例文本之前,請記住將占位符值(包括括弧)替換為您自己的值。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. 透過 SMB 設定 NTFS 權限 
使用 RBAC 指派共用層級權限之後，必須在根、目錄或檔案層級指派適當的 NTFS 權限。 將共用級別許可權視為高級網守,用於確定使用者是否可以訪問共用。 而 NTFS 許可權在更精細的級別執行,以確定用戶可以在目錄或檔級別執行哪些操作。

Azure 檔案支援全套 NTFS 基本和進階權限。 您可以透過安裝共享,然後使用 Windows 檔案資源管理員或執行 Windows [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl)命令來查看和設定 Azure 檔案共享中的目錄和檔案的 NTFS 許可權。 

要配置具有超級使用者許可權的 NTFS,您必須使用域加入的 VM 中的儲存帳戶金鑰來裝載共用。 按照下一節中的說明從命令提示符裝載 Azure 檔共享,並相應地配置 NTFS 許可權。

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

有關如何使用 Icacls 設定 NTFS 權限以及不同類型的受支援權限的詳細資訊,請參閱[icacls 的命令列參考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

### <a name="mount-a-file-share-from-the-command-prompt"></a>從命令提示字元裝載檔案共用

使用 Windows **net use** 命令以裝載 Azure 檔案共用。 請記住,將以下範例中的占位符值替換為您自己的值。 有關安裝檔案分享的詳細資訊,請參閱使用[Windows 檔案分享](../articles/storage/files/storage-how-to-use-files-windows.md)。 

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>使用 Windows 檔案資源管理員設定 NTFS 權限
使用 Windows 檔案資源管理員向檔案共用下的所有目錄和檔(包括根目錄)授予完全許可權。

1. 開啟 Windows 檔案資源管理員並右鍵按下檔案/目錄並選擇**屬性**
2. 點選「**安全**」選項卡
3. 按下**編輯.**. .按鈕以變更權限
4. 您可以變更現有使用者的權限,或按下「**新增」 (** 請新增權限 」
5. 在新增新使用者的提示視窗中,在 **「輸入要選擇物件名稱**」框中輸入要授予權限的目標使用者名,然後按下 **「選中名稱」** 以查找目標使用者的完整 UPN 名稱。
7.  單擊 **"確定"**
8.  在「安全」選項卡中,選擇要授予新添加的使用者的所有許可權
9.  按一下 [套用]****

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. 從加入網域的 VM 載入檔案分享

以下過程驗證檔共享和訪問許可權設置是否正確,以及可以從加入網域的 VM 訪問 Azure 檔共用。 請注意,共用級別 RBAC 角色分配可能需要一些時間才能生效。 

使用已授予許可權的 Azure AD 標識登錄到 VM,如下圖所示。 如果已為 Azure 檔啟用 AD 身份驗證,請使用 AD 認證。 對於 Azure AD DS 身份驗證,請使用 Azure AD 認證登錄。

![螢幕擷取畫面顯示使用者驗證的 Azure AD 登入畫面](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

使用以下命令裝載 Azure 文件共用。 請記得以您自己的值取代預留位置值。 由於已過身份驗證,因此無需提供存儲帳戶密鑰、AD 認證或 Azure AD 認證。 支援使用 AD 或 Azure AD DS 進行身份驗證的單一登錄體驗。 如果在使用 AD 認證時遇到問題,請檢查[Windows 中的「解決 Azure 檔案問題」 程式,](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)以便進行自我診斷指導。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
