---
title: 使用 Azure AD Domain Services 來授權透過 SMB 存取檔案資料
description: 瞭解如何透過伺服器訊息區（SMB）啟用以身分識別為基礎的驗證，以 Azure 檔案儲存體通過 Azure Active Directory Domain Services。 接著，已加入網域的 Windows 虛擬機器（Vm）就可以使用 Azure AD 認證來存取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06ff14b23057755a643e5a57fbaf711798cca00e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210477"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>啟用 Azure 檔案儲存體上的 Azure Active Directory Domain Services 驗證

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

如需 Azure 檔案儲存體上支援之身分識別型驗證的總覽，請參閱[Azure 檔案儲存體的 SMB Azure Active Directory 驗證的總覽](storage-files-active-directory-overview.md)。 本文著重于如何使用 Azure 檔案儲存體上的 Azure Active Directory Domain Services （Azure AD DS）來啟用驗證。 
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>工作流程概觀

針對 Azure 檔案儲存體啟用透過 SMB 進行 Azure AD DS 驗證之前，請確認您的 Azure AD 和 Azure 儲存體環境已正確設定。 我們建議您逐步完成[必要條件](#prerequisites)，以確定您已完成所有必要的步驟。

接著，遵循下列步驟，授與對 Azure AD 認證之 Azure 檔案資源的存取權：

1. 針對您的儲存體帳戶啟用透過 SMB Azure AD DS 驗證，以向相關聯的 Azure AD DS 部署註冊儲存體帳戶。
2. 將共用的存取權限指派至 Azure AD 身分識別 (使用者、群組或服務主體)。
3. 透過 SMB 為目錄和檔案設定 NTFS 權限。
4. 從已加入網域的虛擬機器中裝載 Azure 檔案共用。

下圖說明針對 Azure 檔案儲存體啟用透過 SMB 進行 Azure AD DS 驗證的端對端工作流程。

![此圖顯示針對 Azure 檔案使用「透過 SMB 進行 Azure AD 驗證」的工作流程](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Prerequisites

針對 Azure 檔案服務啟用透過 SMB 進行 Azure AD 驗證之前，請確定您已完成下列必要條件：

1.  **選取或建立 Azure AD 租用戶。**

    您可以針對「透過 SMB 進行 Azure AD 驗證」使用全新或現有租用戶。 租用戶和您想要存取的檔案共用必須與相同的訂用帳戶相關聯。

    若要建立新的 Azure AD 租用戶，您可以[新增 Azure AD 租用戶和 Azure AD 訂用帳戶](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果您有現有的 Azure AD 租用戶，但想要建立新的租用戶與 Azure 檔案搭配使用，請參閱[建立 Azure Active Directory 租用戶](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)。

2.  **在 Azure AD 租用戶上啟用 Azure AD Domain Services。**

    若要支援使用 Azure AD 認證進行驗證，您必須為 Azure AD 租用戶啟用 Azure AD Domain Services。 如果您不是 Azure AD 租用戶的系統管理員，請連絡系統管理員，並遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) 的逐步指導。

    Azure AD DS 部署通常需要大約15分鐘才能完成。 在繼續下一個步驟之前，請確認 Azure AD DS 的健全狀況**狀態顯示為**[執行中，已啟用密碼雜湊同步處理]。

3.  **網域-加入具有 Azure AD DS 的 Azure VM。**

    若要使用來自 VM Azure AD 認證來存取檔案共用，您的 VM 必須已加入 Azure AD DS 的網域。 如需如何加入虛擬機器網域的詳細資訊，請參閱[將 Windows Server 虛擬機器加入受控網域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > 只有在 Windows 7 或 Windows Server 2008 R2 以上的作業系統版本上執行的 Azure Vm 上，才支援透過 SMB 進行 Azure AD DS 驗證與 Azure 檔案儲存體。

4.  **選取或建立 Azure 檔案共用。**

    選取與 Azure AD 租用戶相同訂閱關聯的全新或現有檔案共用。 如需建立新檔案共用的資訊，請參閱 [在 Azure 檔案中建立檔案共用](storage-how-to-create-file-share.md)。
    為了達到最佳效能，我們建議您的檔案共用與您規劃用來存取共用的 VM 位於相同的區域。

5.  **藉由使用儲存體帳戶金鑰裝載 Azure 檔案共用，驗證 Azure 檔案連線。**

    若要驗證是否已正確設定虛擬機器和檔案共用，請嘗試使用儲存體帳戶金鑰裝載檔案共用。 如需詳細資料，請參閱[裝載 Azure 檔案共用並在 Windows 中存取共用](storage-how-to-use-files-windows.md)。

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>為您的帳戶啟用 Azure AD DS 驗證

若要啟用透過 SMB 進行 Azure AD DS 驗證以進行 Azure 檔案儲存體，您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI，在儲存體帳戶上設定屬性。 以隱含方式將此屬性設定為 [網域加入] 具有相關聯 Azure AD DS 部署的儲存體帳戶。 接著，會針對儲存體帳戶中的所有新的和現有檔案共用，啟用透過 SMB 進行的 DS 驗證 Azure AD。

請記住，只有在您成功將 Azure AD DS 部署至 Azure AD 租使用者之後，才可以啟用透過 SMB 進行 Azure AD DS 驗證。 如需詳細資訊，請參閱[必要條件](#prerequisites)。

### <a name="azure-portal"></a>Azure 入口網站

若要使用[Azure 入口網站](https://portal.azure.com)啟用透過 SMB 進行 Azure AD DS 驗證，請遵循下列步驟：

1. 在 Azure 入口網站中，移至您現有的儲存體帳戶，或[建立儲存體帳戶](../common/storage-account-create.md)。
2. 在 [設定] 區段中，選取 [組態]。
3. 從 [Azure 檔案驗證的身分**識別型目錄服務**] 下拉式清單中，選取 [ **AZURE ACTIVE DIRECTORY DOMAIN SERVICES （Azure AD DS）** ]。

下圖顯示如何針對您的儲存體帳戶啟用透過 SMB 的 Azure AD DS 驗證。

![在 Azure 入口網站中啟用透過 SMB 進行 Azure AD 驗證](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

若要啟用透過 Azure PowerShell 對 SMB 進行 Azure AD DS 驗證，請安裝最新的 Az 模組（2.4 或更新版本）或 Az. Storage 模組（1.5 或更新版本）。 如需安裝 PowerShell 的詳細資訊，請參閱[使用 PowerShellGet 在 Windows 上安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

若要建立新的儲存體帳戶，請呼叫[new-azstorageaccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)，然後將**EnableAzureActiveDirectoryDomainServicesForFile**參數設定為**true**。 在下列範例中，請記得以您自己的值取代預留位置值。 （如果您使用先前的預覽模組，功能啟用的參數會是**EnableAzureFilesAadIntegrationForSMB**）。

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

若要在現有的儲存體帳戶上啟用這項功能，請使用下列命令：

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 啟用透過 SMB 的 Azure AD 驗證，請安裝最新的 CLI 版本（2.0.70 或更新版本）。 如需有關安裝 Azure CLI 的詳細資訊，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要建立新的儲存體帳戶，請呼叫[az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)，然後將 `--enable-files-aadds` 屬性設定為**true**。 在下列範例中，請記得以您自己的值取代預留位置值。 （如果您使用先前的預覽模組，功能啟用的參數會是檔案**aad**）。

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

若要在現有的儲存體帳戶上啟用這項功能，請使用下列命令：

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>將存取權限指派給身分識別

若要存取具有 Azure AD 認證的 Azure 檔案儲存體資源，身分識別（使用者、群組或服務主體）必須具有共用層級的必要許可權。 此程式類似于指定 Windows 共用許可權，您可以在其中指定特定使用者對檔案共用的存取類型。 本節中的指引示範如何將檔案共用的讀取、寫入或刪除權限指派給身分識別。

我們引進了兩個 Azure 內建角色，可將共用層級許可權授與使用者：

- **儲存體檔案資料 Smb 共用讀取器**允許透過 smb 在 Azure 儲存體檔案共用中進行讀取存取。
- **儲存體檔案資料 SMB 共用參與者**允許透過 smb 在 Azure 儲存體檔案共用中進行讀取、寫入和刪除存取。
- **儲存體檔案資料 SMB 共用提高許可權參與者**允許透過 smb 在 Azure 儲存體檔案共用中進行讀取、寫入、刪除及修改 NTFS 許可權。

> [!IMPORTANT]
> 完整管理控制檔案共用 (包括將角色指派給身分識別的能力) 需要使用儲存體帳戶金鑰。 Azure AD 認證不支援管理控制。

您可以使用 Azure 入口網站、PowerShell 或 Azure CLI，將內建角色指派給使用者的 Azure AD 身分識別，以授與共享層級許可權。

#### <a name="azure-portal"></a>Azure 入口網站
若要使用[Azure 入口網站](https://portal.azure.com)將 RBAC 角色指派給 Azure AD 身分識別，請依照下列步驟執行：

1. 在 Azure 入口網站中，移至您的檔案共用，或[在 Azure 檔案儲存體中建立檔案共用](storage-how-to-create-file-share.md)。
2. 選取 [存取控制 (IAM)]。
3. 選取 [**新增角色指派**]
4. 在 [**新增角色指派**] 分頁中，從 [**角色**] 清單中選取適當的內建角色（儲存體檔案資料 smb 共用讀取器、儲存體檔案資料 smb 共用參與者）。 將 [**指派存取**權] 選項保留為預設設定： [ **Azure AD 使用者、群組或服務主體**]。 依名稱或電子郵件地址選取目標 Azure AD 身分識別。
5. 選取 [**儲存**] 以完成角色指派作業。

#### <a name="powershell"></a>PowerShell

下列 PowerShell 命令說明如何根據登入名稱，將 RBAC 角色指派給 Azure AD 身分識別。 如需使用 PowerShell 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure PowerShell 管理存取](../../role-based-access-control/role-assignments-powershell.md)。

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

下列 CLI 2.0 命令說明如何根據登入名稱，將 RBAC 角色指派給 Azure AD 身分識別。 如需有關使用 Azure CLI 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure CLI 來管理存取權](../../role-based-access-control/role-assignments-cli.md)。

執行下列範例腳本之前，請記得使用您自己的值來取代預留位置值（包括括弧）。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>透過 SMB 設定 NTFS 權限
使用 RBAC 指派共用層級權限之後，必須在根、目錄或檔案層級指派適當的 NTFS 權限。 將共用層級的許可權視為用來決定使用者是否可以存取共用的高階閘道管理員。 NTFS 許可權會以更細微的層級來決定使用者可以在目錄或檔案層級執行哪些作業。

Azure 檔案支援全套 NTFS 基本和進階權限。 您可以藉由掛接共用，然後使用 Windows 檔案瀏覽器或執行 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl)命令，來查看和設定 Azure 檔案共用中目錄和檔案的 NTFS 許可權。

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

使用 Windows **net use** 命令以裝載 Azure 檔案共用。 請記得使用您自己的值來取代下列範例中的預留位置值。 如需裝載檔案共用的詳細資訊，請參閱[裝載 Azure 檔案共用並存取 Windows 中的共用](storage-how-to-use-files-windows.md)。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>使用 Windows File Explorer 設定 NTFS 許可權
使用 Windows 檔案瀏覽器，將完整許可權授與檔案共用下的所有目錄和檔案，包括根目錄。

1. 開啟 Windows 檔案瀏覽器，並以滑鼠右鍵按一下檔案/目錄，然後選取 [**屬性**]
2. 按一下 [**安全性**] 索引標籤
3. 按一下 [**編輯 ...** ]變更許可權的按鈕
4. 您可以變更現有使用者的許可權，或按一下 [**新增 ...** ] 將許可權授與新使用者
5. 在新增使用者的 [提示] 視窗中，于 [**輸入要選取的物件名稱**] 方塊中輸入您要授與許可權的目標使用者名稱，然後按一下 [**檢查名稱**] 以尋找目標使用者的完整 UPN 名稱。
7.  按一下 [設定] 功能表中的 [App Service 方案]
8.  在 [安全性] 索引標籤中，選取您想要授與新新增使用者的擁有權限。
9.  按一下 [套用]

### <a name="configure-ntfs-permissions-with-icacls"></a>使用 icacls 設定 NTFS 權限
使用以下 Windows 命令，授與完整權限至檔案共用下的所有目錄和檔案，包括根目錄。 請記得使用您自己的值取代預留位置值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

如需有關如何使用 icacls 設定 NTFS 許可權和不同類型的支援許可權的詳細資訊，請參閱[icacls 的命令列參考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>從已加入網域的 VM 中裝載檔案共用

下列程式會驗證您的 Azure AD 認證是否已正確設定，而且您可以從已加入網域的 VM 存取 Azure 檔案共用：

使用您已授與許可權的 Azure AD 身分識別來登入 VM，如下圖所示。

![螢幕擷取畫面顯示使用者驗證的 Azure AD 登入畫面](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

使用下列命令來掛接 Azure 檔案共用。 請記得以您自己的值取代預留位置值。 因為您已經過驗證，所以您不需要提供儲存體帳戶金鑰或 Azure AD 的使用者名稱和密碼。 透過 SMB 的 Azure AD 支援具有 Azure AD 認證的單一登入體驗。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

您現在已成功啟用透過 SMB 的 Azure AD 驗證，並已指派自訂角色，可讓您存取具有 Azure AD 身分識別的 Azure 檔案共用。 若要將檔案共用的存取權授與其他使用者，請依照將[存取權限指派給身分識別](#assign-access-permissions-to-an-identity)和透過[SMB 設定 NTFS 許可權](#configure-ntfs-permissions-over-smb)一節中的指示進行。

## <a name="next-steps"></a>後續步驟

如需 Azure 檔案儲存體以及如何透過 SMB 使用 Azure AD 的詳細資訊，請參閱下列資源：

- [Azure 檔案服務簡介](storage-files-introduction.md)
- [針對 Azure 檔案儲存體透過 SMB 進行 Azure Active Directory 驗證的概觀](storage-files-active-directory-overview.md)
- [常見問題集](storage-files-faq.md)
