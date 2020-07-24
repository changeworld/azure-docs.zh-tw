---
title: 使用 Azure AD Domain Services 來授權透過 SMB 存取檔案資料
description: 瞭解如何透過伺服器訊息區（SMB）啟用以身分識別為基礎的驗證，以 Azure 檔案儲存體通過 Azure Active Directory Domain Services。 接著，已加入網域的 Windows 虛擬機器（Vm）就可以使用 Azure AD 認證來存取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fe2bbc1d6f42819354f48812a34371a49e5acbac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999627"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>啟用 Azure 檔案儲存體上的 Azure Active Directory Domain Services 驗證

[Azure 檔案儲存體](storage-files-introduction.md)  支援透過兩種網域服務的伺服器訊息區（SMB）進行以身分識別為基礎的驗證：內部部署 Active Directory Domain Services （AD DS）和 Azure Active Directory Domain Services （Azure AD DS）。我們強烈建議您參閱[其運作方式一節](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works)，以選取正確的網域服務進行驗證。 安裝程式會有所不同，取決於您選擇的網域服務。 這篇文章著重于啟用和設定 Azure AD DS，以使用 Azure 檔案共用進行驗證。

如果您是 Azure 檔案共用的新手，建議您先閱讀我們的[規劃指南](storage-files-planning.md)，再閱讀下列系列文章。

> [!NOTE]
> Azure 檔案儲存體支援使用具有 RC4-HMAC 加密的 Azure AD DS 來進行 Kerberos 驗證。 尚不支援 AES Kerberos 加密。
> Azure 檔案儲存體支援與 Azure AD 進行完整同步處理 Azure AD DS 的驗證。 如果您已在 Azure AD DS 中啟用範圍同步處理，而這只會從 Azure AD 同步處理有限的身分識別集，則不支援驗證和授權。

## <a name="prerequisites"></a>先決條件

啟用 Azure 檔案共用的 SMB Azure AD 之前，請確定您已完成下列必要條件：

1.  **選取或建立 Azure AD 租用戶。**

    您可以針對「透過 SMB 進行 Azure AD 驗證」使用全新或現有租用戶。 租用戶和您想要存取的檔案共用必須與相同的訂用帳戶相關聯。

    若要建立新的 Azure AD 租用戶，您可以[新增 Azure AD 租用戶和 Azure AD 訂用帳戶](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果您有現有的 Azure AD 租使用者，但想要建立新的租使用者以與 Azure 檔案共用搭配使用，請參閱[建立 Azure Active Directory 租](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)使用者。

1.  **在 Azure AD 租用戶上啟用 Azure AD Domain Services。**

    若要支援使用 Azure AD 認證進行驗證，您必須為 Azure AD 租用戶啟用 Azure AD Domain Services。 如果您不是 Azure AD 租用戶的系統管理員，請連絡系統管理員，並遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) 的逐步指導。

    Azure AD DS 部署通常需要大約15分鐘才能完成。 在繼續下一個步驟之前，請確認 Azure AD DS 的健全狀況**狀態顯示為**[執行中，已啟用密碼雜湊同步處理]。

1.  **網域-加入具有 Azure AD DS 的 Azure VM。**

    若要使用來自 VM Azure AD 認證來存取檔案共用，您的 VM 必須已加入 Azure AD DS 的網域。 如需如何加入虛擬機器網域的詳細資訊，請參閱[將 Windows Server 虛擬機器加入受控網域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > 只有在 Windows 7 或 Windows Server 2008 R2 以上的作業系統版本上執行的 Azure Vm 上，才支援透過 SMB 使用 Azure 檔案共用 Azure AD DS 驗證。

1.  **選取或建立 Azure 檔案共用。**

    選取與 Azure AD 租用戶相同訂閱關聯的全新或現有檔案共用。 如需建立新檔案共用的資訊，請參閱 [在 Azure 檔案中建立檔案共用](storage-how-to-create-file-share.md)。
    為了達到最佳效能，我們建議您的檔案共用與您規劃用來存取共用的 VM 位於相同的區域。

1.  **藉由使用儲存體帳戶金鑰裝載 Azure 檔案共用，驗證 Azure 檔案連線。**

    若要驗證是否已正確設定虛擬機器和檔案共用，請嘗試使用儲存體帳戶金鑰裝載檔案共用。 如需詳細資料，請參閱[裝載 Azure 檔案共用並在 Windows 中存取共用](storage-how-to-use-files-windows.md)。

## <a name="regional-availability"></a>區域可用性

[所有 Azure 公用區域](https://azure.microsoft.com/global-infrastructure/locations/)皆可使用 Azure AD DS 的 Azure 檔案儲存體驗證。

## <a name="overview-of-the-workflow"></a>工作流程概觀

針對 Azure 檔案共用啟用透過 SMB 進行 Azure AD DS 驗證之前，請確認您的 Azure AD 和 Azure 儲存體環境已正確設定。 我們建議您逐步完成[必要條件](#prerequisites)，以確定您已完成所有必要的步驟。

接下來，請執行下列動作，使用 Azure AD 認證來授與 Azure 檔案儲存體資源的存取權：

1. 針對您的儲存體帳戶啟用透過 SMB Azure AD DS 驗證，以向相關聯的 Azure AD DS 部署註冊儲存體帳戶。
2. 將共用的存取權限指派至 Azure AD 身分識別 (使用者、群組或服務主體)。
3. 透過 SMB 為目錄和檔案設定 NTFS 權限。
4. 從已加入網域的虛擬機器中裝載 Azure 檔案共用。

下圖說明針對 Azure 檔案儲存體啟用透過 SMB 進行 Azure AD DS 驗證的端對端工作流程。

![此圖顯示針對 Azure 檔案使用「透過 SMB 進行 Azure AD 驗證」的工作流程](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. 為您的帳戶啟用 Azure AD DS 驗證

若要啟用透過 SMB 進行 Azure AD DS 驗證以進行 Azure 檔案儲存體，您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI，在儲存體帳戶上設定屬性。 以隱含方式將此屬性設定為 [網域加入] 具有相關聯 Azure AD DS 部署的儲存體帳戶。 接著，會針對儲存體帳戶中的所有新的和現有檔案共用，啟用透過 SMB 進行的 DS 驗證 Azure AD。

請記住，只有在您成功將 Azure AD DS 部署至 Azure AD 租使用者之後，才可以啟用透過 SMB 進行 Azure AD DS 驗證。 如需詳細資訊，請參閱[必要條件](#prerequisites)。

### <a name="azure-portal"></a>Azure 入口網站

若要使用[Azure 入口網站](https://portal.azure.com)啟用透過 SMB 進行 Azure AD DS 驗證，請遵循下列步驟：

1. 在 Azure 入口網站中，移至您現有的儲存體帳戶，或[建立儲存體帳戶](../common/storage-account-create.md)。
1. 在 [設定]**** 區段中，選取 [組態]****。
1. 在 [檔案**共用的識別式存取**] 下，將**Azure Active Directory 網域服務（AAD DS）** 的切換切換為 [**已啟用**]。
1. 選取 [儲存]。

下圖顯示如何針對您的儲存體帳戶啟用透過 SMB 的 Azure AD DS 驗證。

![在 Azure 入口網站中啟用透過 SMB 的 Azure AD DS 驗證](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

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

若要建立新的儲存體帳戶，請呼叫[az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)，並將 `--enable-files-aadds` 屬性設定為**true**。 在下列範例中，請記得以您自己的值取代預留位置值。 （如果您使用先前的預覽模組，功能啟用的參數會是檔案**aad**）。

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

若要在現有的儲存體帳戶上啟用這項功能，請使用下列命令：

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

您現在已成功啟用透過 SMB Azure AD DS 驗證，並已指派自訂角色，可讓您存取具有 Azure AD 身分識別的 Azure 檔案共用。 若要將檔案共用的存取權授與其他使用者，請遵循[指派存取權限](#2-assign-access-permissions-to-an-identity)以使用身分識別和透過[SMB 設定 NTFS 許可權一節](#3-configure-ntfs-permissions-over-smb)中的指示。

## <a name="next-steps"></a>接下來的步驟

如需 Azure 檔案儲存體以及如何透過 SMB 使用 Azure AD 的詳細資訊，請參閱下列資源：

- [瞭解 SMB 存取的 Azure 檔案儲存體身分識別型驗證支援](storage-files-active-directory-overview.md)
- [常見問題集](storage-files-faq.md)
