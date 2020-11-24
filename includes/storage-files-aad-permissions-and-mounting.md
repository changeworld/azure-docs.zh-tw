---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 4773446ec0007ffbed99bc01939d1f92f5823d99
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557071"
---
## <a name="assign-access-permissions-to-an-identity"></a>將存取權限指派給身分識別

若要存取以身分識別為基礎的驗證 Azure 檔案儲存體資源，使用者、群組或服務主體的身分識別 () 必須在共用層級擁有必要的許可權。 此程式類似于指定 Windows 共用許可權，您可以在其中指定特定使用者對檔案共用的存取類型。 本節中的指引示範如何將檔案共用的讀取、寫入或刪除權限指派給身分識別。 

我們引進了三個 Azure 內建角色，以將共用層級許可權授與使用者：

- **儲存體檔案資料 SMB 共用讀取器** 允許在 SMB 的 Azure 儲存體檔案共用中進行讀取存取。
- **儲存體檔案資料 SMB 共用參與者** 允許在 SMB 的 Azure 儲存體檔案共用中進行讀取、寫入和刪除存取。
- **儲存體檔案資料 SMB 共用提升許可權的參與者** 允許在 SMB 的 Azure 儲存體檔案共用中讀取、寫入、刪除及修改 NTFS 許可權。

> [!IMPORTANT]
> 檔案共用的完整系統管理控制權，包括取得檔案擁有權的能力，需要使用儲存體帳戶金鑰。 Azure AD 認證不支援管理控制。

您可以使用 Azure 入口網站、PowerShell 或 Azure CLI，將內建角色指派給使用者的 Azure AD 身分識別，以授與共享層級許可權。 請注意，共用層級的 Azure 角色指派可能需要一些時間才會生效。 

> [!NOTE]
> 如果您打算使用內部部署 AD DS 進行驗證，請記得將 [AD DS 認證同步至 Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) 。 從 AD DS 到 Azure AD 的密碼雜湊同步處理是選擇性的。 系統會將共用層級許可權授與從內部部署 AD DS 同步的 Azure AD 身分識別。

一般建議是將高階存取管理的共用層級許可權用於代表一組使用者和身分識別的 AD 群組，然後利用 NTFS 許可權在目錄/檔案層級進行細微的存取控制。 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>將 Azure 角色指派給 AD 身分識別

# <a name="portal"></a>[入口網站](#tab/azure-portal)
若要使用 [Azure 入口網站](https://portal.azure.com)將 Azure 角色指派給 Azure AD 身分識別，請遵循下列步驟：

1. 在 Azure 入口網站中，移至您的檔案共用，或 [建立檔案共用](../articles/storage/files/storage-how-to-create-file-share.md)。
2. 選取 [存取控制 (IAM)]。
3. 選取 [**新增角色指派**]
4. 在 [ **新增角色指派** ] 分頁中，從 [ **角色** ] 清單中選取適當的內建角色 (儲存體檔案資料 smb 共用讀取者、儲存體檔案資料 smb 共用參與者) 。 在預設設定下保留 [ **指派存取權** ]： **Azure AD 使用者、群組或服務主體**。 依名稱或電子郵件地址選取目標 Azure AD 身分識別。
5. 選取 [ **儲存** ] 以完成角色指派作業。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

下列 PowerShell 範例說明如何根據登入名稱，將 Azure 角色指派給 Azure AD 身分識別。 如需使用 PowerShell 指派 Azure 角色的詳細資訊，請參閱 [使用 RBAC 和 Azure PowerShell 管理存取權](../articles/role-based-access-control/role-assignments-powershell.md)。

在您執行下列範例腳本之前，請記得使用您自己的值來取代預留位置值（包含括弧）。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
下列 CLI 2.0 命令顯示如何根據登入名稱，將 Azure 角色指派給 Azure AD 身分識別。 如需使用 Azure CLI 指派 Azure 角色的詳細資訊，請參閱 [使用 RBAC 和 Azure CLI 管理存取權](../articles/role-based-access-control/role-assignments-cli.md)。 

在您執行下列範例腳本之前，請記得使用您自己的值來取代預留位置值（包含括弧）。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>透過 SMB 設定 NTFS 權限

使用 RBAC 指派共用層級權限之後，必須在根、目錄或檔案層級指派適當的 NTFS 權限。 將共用層級的許可權視為可決定使用者是否可以存取共用的高階閘道管理員。 雖然 NTFS 許可權會以更細微的層級來決定使用者可以在目錄或檔案層級進行哪些作業。

Azure 檔案支援全套 NTFS 基本和進階權限。 您可以透過掛接共用，然後使用 Windows 檔案總管或執行 Windows [icacls](/windows-server/administration/windows-commands/icacls) 或 [Set ACL](/powershell/module/microsoft.powershell.security/set-acl) 命令，來查看和設定 Azure 檔案共用中的目錄和檔案的 NTFS 許可權。 

若要使用超級使用者權限設定 NTFS，您必須從已加入網域的 VM 使用您的儲存體帳戶金鑰來掛接共用。 遵循下一節中的指示，從命令提示字元掛接 Azure 檔案共用，並據以設定 NTFS 許可權。

檔案共用的根目錄支援下列權限集合：

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>從命令提示字元裝載檔案共用

使用 Windows **net use** 命令以裝載 Azure 檔案共用。 請記得使用您自己的值來取代下列範例中的預留位置值。 如需掛接檔案共用的詳細資訊，請參閱搭配 [Windows 使用 Azure 檔案共用](../articles/storage/files/storage-how-to-use-files-windows.md)。 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

如果您在連接到 Azure 檔案儲存體時遇到問題，請參閱 [我們針對 Windows 上的 Azure 檔案儲存體掛接錯誤所發佈的疑難排解工具](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)。 我們也會在埠445遭到封鎖時，提供解決案例的 [指引](../articles/storage/files/storage-files-faq.md#on-premises-access) 。 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>使用 Windows 檔案總管設定 NTFS 許可權

使用 Windows 檔案總管將完整許可權授與檔案共用下的所有目錄和檔案，包括根目錄在內。

1. 開啟 [Windows 檔案總管並以滑鼠右鍵按一下檔案/目錄，然後選取 [ **屬性**]。
2. 選取 [安全性] 索引標籤。
3. 選取 [ **編輯]。** 變更許可權。
4. 您可以變更現有使用者的許可權， **或選取 [新增]** ，將許可權授與新的使用者。
5. 在 [新增使用者的提示] 視窗中，在 [ **輸入物件名稱來選取** ] 方塊中輸入您想要授與許可權的目標使用者名稱，然後選取 [ **檢查名稱** ] 以尋找目標使用者的完整 UPN 名稱。
7.    選取 [確定]。
8.    在 [ **安全性** ] 索引標籤中，選取您要授與新使用者的擁有權限。
9.    選取 [套用]。

### <a name="configure-ntfs-permissions-with-icacls"></a>使用 icacls 設定 NTFS 權限

使用以下 Windows 命令，授與完整權限至檔案共用下的所有目錄和檔案，包括根目錄。 請記得使用您自己的值取代預留位置值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

如需有關如何使用 icacls 來設定 NTFS 許可權以及不同類型的支援許可權的詳細資訊，請參閱 [icacls 的命令列參考](/windows-server/administration/windows-commands/icacls)。

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>從已加入網域的 VM 中裝載檔案共用

下列程式會確認您的檔案共用和存取權限已正確設定，而且您可以從已加入網域的 VM 存取 Azure 檔案共用。 請注意，共用層級的 Azure 角色指派可能需要一些時間才會生效。 

使用您已授與許可權的 Azure AD 身分識別來登入 VM，如下圖所示。 如果您已啟用 Azure 檔案儲存體的內部部署 AD DS 驗證，請使用您的 AD DS 認證。 針對 Azure AD DS 驗證，請使用 Azure AD 認證登入。

![螢幕擷取畫面顯示使用者驗證的 Azure AD 登入畫面](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

使用下列命令來掛接 Azure 檔案共用。 請記得以您自己的值取代預留位置值。 因為您已通過驗證，所以不需要提供儲存體帳戶金鑰、內部部署 AD DS 認證或 Azure AD DS 認證。 使用內部部署 AD DS 或 Azure AD DS 進行驗證時，支援單一登入體驗。 如果您遇到裝載 AD DS 認證的問題，請參閱 [Windows 中的疑難排解 Azure 檔案儲存體問題](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) 以取得指引。

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```