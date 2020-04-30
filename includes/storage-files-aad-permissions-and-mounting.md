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
ms.openlocfilehash: 47bd550bbd8d75a06d38babe88b5a95f3790af50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106539"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. 將存取權限指派給身分識別

若要使用以身分識別為基礎的驗證來存取 Azure 檔案儲存體資源，身分識別（使用者、群組或服務主體）必須具有共用層級的必要許可權。 此程式類似于指定 Windows 共用許可權，您可以在其中指定特定使用者對檔案共用的存取類型。 本節中的指引示範如何將檔案共用的讀取、寫入或刪除權限指派給身分識別。 

我們引進了三個 Azure 內建角色，可將共用層級許可權授與使用者：

- **儲存體檔案資料 Smb 共用讀取器**允許透過 smb 在 Azure 儲存體檔案共用中進行讀取存取。
- **儲存體檔案資料 SMB 共用參與者**允許透過 smb 在 Azure 儲存體檔案共用中進行讀取、寫入和刪除存取。
- **儲存體檔案資料 SMB 共用提高許可權參與者**允許透過 smb 在 Azure 儲存體檔案共用中進行讀取、寫入、刪除及修改 NTFS 許可權。

> [!IMPORTANT]
> 檔案共用的完整系統管理控制權，包括取得檔案擁有權的能力，需要使用儲存體帳戶金鑰。 Azure AD 認證不支援管理控制。

您可以使用 Azure 入口網站、PowerShell 或 Azure CLI，將內建角色指派給使用者的 Azure AD 身分識別，以授與共享層級許可權。

> [!NOTE]
> 如果您打算使用內部部署 AD DS 進行驗證，請記得將[AD DS 認證同步到 Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) 。 從 AD DS 到 Azure AD 的密碼雜湊同步處理是選擇性的。 系統會將共用層級許可權授與從內部部署 AD DS 同步的 Azure AD 身分識別。

一般建議是針對代表使用者和身分識別群組的 AD 群組使用「高層級存取管理」的「共用層級」許可權，然後利用 NTFS 許可權在目錄/檔案層級進行細微的存取控制。 

#### <a name="azure-portal"></a>Azure 入口網站
若要使用[Azure 入口網站](https://portal.azure.com)將 RBAC 角色指派給 Azure AD 身分識別，請依照下列步驟執行：

1. 在 Azure 入口網站中，移至您的檔案共用，或[建立檔案共用](../articles/storage/files/storage-how-to-create-file-share.md)。
2. 選取 **[存取控制（IAM）**]。
3. 選取 [**新增角色指派**]
4. 在 [**新增角色指派**] 分頁中，從 [**角色**] 清單中選取適當的內建角色（儲存體檔案資料 smb 共用讀取器、儲存體檔案資料 smb 共用參與者）。 將 [**指派存取權**] 保留為預設設定： [ **Azure AD 使用者]、[群組] 或 [服務主體**]。 依名稱或電子郵件地址選取目標 Azure AD 身分識別。
5. 選取 [**儲存**] 以完成角色指派作業。

#### <a name="powershell"></a>PowerShell

下列 PowerShell 範例說明如何根據登入名稱，將 RBAC 角色指派給 Azure AD 身分識別。 如需使用 PowerShell 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure PowerShell 管理存取](../articles/role-based-access-control/role-assignments-powershell.md)。

執行下列範例腳本之前，請記得使用您自己的值來取代預留位置值（包括括弧）。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
下列 CLI 2.0 命令說明如何根據登入名稱，將 RBAC 角色指派給 Azure AD 身分識別。 如需有關使用 Azure CLI 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure CLI 來管理存取權](../articles/role-based-access-control/role-assignments-cli.md)。 

執行下列範例腳本之前，請記得使用您自己的值來取代預留位置值（包括括弧）。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. 透過 SMB 設定 NTFS 許可權 
使用 RBAC 指派共用層級權限之後，必須在根、目錄或檔案層級指派適當的 NTFS 權限。 將共用層級的許可權視為用來決定使用者是否可以存取共用的高階閘道管理員。 NTFS 許可權會以更細微的層級來決定使用者可以在目錄或檔案層級執行哪些作業。

Azure 檔案支援全套 NTFS 基本和進階權限。 您可以藉由掛接共用，然後使用 Windows 檔案瀏覽器或執行 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl)命令，來查看和設定 Azure 檔案共用中目錄和檔案的 NTFS 許可權。 

若要以超級使用者權限設定 NTFS，您必須從已加入網域的 VM 使用您的儲存體帳戶金鑰來掛接共用。 依照下一節中的指示，從命令提示字元掛接 Azure 檔案共用，並據以設定 NTFS 許可權。

檔案共用的根目錄支援下列權限集合：

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>從命令提示字元裝載檔案共用

使用 Windows **net use** 命令以裝載 Azure 檔案共用。 請記得使用您自己的值來取代下列範例中的預留位置值。 如需裝載檔案共用的詳細資訊，請參閱搭配[Windows 使用 Azure 檔案共用](../articles/storage/files/storage-how-to-use-files-windows.md)。 

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
```

如果您在連接到 Azure 檔案儲存體時遇到問題，請參閱[我們針對 Windows 上的 Azure 檔案儲存體掛接錯誤所發行的疑難排解工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)。 我們也會提供[指引](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access)，以解決埠445遭到封鎖的案例。 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>使用 Windows File Explorer 設定 NTFS 許可權
使用 Windows 檔案瀏覽器，將完整許可權授與檔案共用下的所有目錄和檔案，包括根目錄。

1. 開啟 Windows 檔案瀏覽器，並以滑鼠右鍵按一下檔案/目錄，然後選取 [**屬性**]。
2. 選取 [安全性]**** 索引標籤。
3. 選取 [**編輯]。** 變更許可權。
4. 您可以變更現有使用者的許可權，或選取 [**新增**] 以將許可權授與新的使用者。
5. 在新增使用者的 [提示] 視窗中，于 [**輸入要選取的物件名稱**] 方塊中輸入您要授與許可權的目標使用者名稱，然後選取 [**檢查名稱**] 以尋找目標使用者的完整 UPN 名稱。
7.    選取 [確定]  。
8.    在 [**安全性**] 索引標籤中，選取您想要授與新使用者的擁有權限。
9.    選取 [套用]  。

### <a name="configure-ntfs-permissions-with-icacls"></a>使用 icacls 設定 NTFS 權限
使用以下 Windows 命令，授與完整權限至檔案共用下的所有目錄和檔案，包括根目錄。 請記得使用您自己的值取代預留位置值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

如需有關如何使用 icacls 設定 NTFS 許可權和不同類型的支援許可權的詳細資訊，請參閱[icacls 的命令列參考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. 從已加入網域的 VM 掛接檔案共用

下列程式會確認您的檔案共用和存取權限已正確設定，而且您可以從已加入網域的 VM 存取 Azure 檔案共用。 請注意，共用層級 RBAC 角色指派可能需要一些時間才會生效。 

使用您已授與許可權的 Azure AD 身分識別來登入 VM，如下圖所示。 如果您已針對 Azure 檔案儲存體啟用內部部署 AD DS 驗證，請使用您的 AD DS 認證。 針對 Azure AD DS 驗證，請使用 Azure AD 認證登入。

![螢幕擷取畫面顯示使用者驗證的 Azure AD 登入畫面](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

使用下列命令來掛接 Azure 檔案共用。 請記得以您自己的值取代預留位置值。 由於您已通過驗證，因此不需要提供儲存體帳戶金鑰、內部部署 AD DS 認證，或 Azure AD DS 認證。 單一登入體驗支援使用內部部署 AD DS 或 Azure AD DS 進行驗證。 如果您遇到以 AD DS 認證掛接的問題，請參閱針對[Windows 中的 Azure 檔案儲存體問題進行疑難排解](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)，以取得指導方針。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
