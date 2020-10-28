---
title: 使用 Azure AD Domain Services 來授權透過 SMB 存取檔案資料
description: 瞭解如何透過伺服器訊息區啟用以身分識別為基礎的驗證， (SMB) 透過 Azure Active Directory Domain Services 進行 Azure 檔案儲存體。 您已加入網域的 Windows 虛擬機器 (Vm) 接著可以使用 Azure AD 認證來存取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: rogarana
ms.subservice: files
ms.custom: contperfq1, devx-track-azurecli
ms.openlocfilehash: 906ec80ecc198675fdb5b163403267be1d13de00
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746849"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>在 Azure 檔案儲存體上啟用 Azure Active Directory Domain Services authentication

[Azure 檔案儲存體](storage-files-introduction.md)  透過兩種類型的網域服務，支援透過伺服器訊息區進行以身分識別為基礎的驗證 (SMB) ：內部部署 Active Directory Domain Services (AD DS) 和 Azure Active Directory Domain Services (DS Azure AD。強烈建議您複習[它的運作方式一節](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works)，以選取正確的網域服務進行驗證。 安裝程式不同，視您選擇的網域服務而定。 本文著重于啟用和設定 Azure AD DS 以使用 Azure 檔案共用進行驗證。

如果您不熟悉 Azure 檔案共用，建議您閱讀我們的 [規劃指南](storage-files-planning.md) ，再閱讀下列系列文章。

> [!NOTE]
> Azure 檔案儲存體支援搭配 RC4-HMAC 加密的 Azure AD DS 進行 Kerberos 驗證。 尚不支援 AES Kerberos 加密。
> Azure 檔案儲存體支援 Azure AD DS 驗證與 Azure AD 的完整同步處理。 如果您已啟用 Azure AD DS 中的限域同步處理，只會從 Azure AD 同步處理一組有限的身分識別，就不支援驗證和授權。

## <a name="prerequisites"></a>必要條件

針對 Azure 檔案共用啟用 Azure AD over SMB 之前，請確定您已完成下列必要條件：

1.  **選取或建立 Azure AD 租用戶。**

    您可以針對「透過 SMB 進行 Azure AD 驗證」使用全新或現有租用戶。 租用戶和您想要存取的檔案共用必須與相同的訂用帳戶相關聯。

    若要建立新的 Azure AD 租用戶，您可以[新增 Azure AD 租用戶和 Azure AD 訂用帳戶](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果您有現有的 Azure AD 租使用者，但想要建立新的租使用者以與 Azure 檔案共用搭配使用，請參閱 [建立 Azure Active Directory 租](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)使用者。

1.  **在 Azure AD 租用戶上啟用 Azure AD Domain Services。**

    若要支援使用 Azure AD 認證進行驗證，您必須為 Azure AD 租用戶啟用 Azure AD Domain Services。 如果您不是 Azure AD 租用戶的系統管理員，請連絡系統管理員，並遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) 的逐步指導。

    Azure AD DS 部署通常需要約15分鐘的時間才能完成。 繼續進行下一個步驟之前，請先確認 Azure AD DS 的健康狀態顯示為 [已啟用密碼雜湊同步處理 **]，然後** 再繼續進行下一個步驟。

1.  **使用 Azure AD DS 將 Azure VM 加入網域。**

    若要使用來自 VM 的 Azure AD 認證來存取檔案共用，您的 VM 必須已加入 Azure AD DS 網域。 如需如何加入虛擬機器網域的詳細資訊，請參閱[將 Windows Server 虛擬機器加入受控網域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > 只有在執行 Windows 7 或 Windows Server 2008 R2 的作業系統版本上執行的 Azure Vm 上，才支援透過 SMB 使用 Azure 檔案共用的 DS 驗證 Azure AD。

1.  **選取或建立 Azure 檔案共用。**

    選取與 Azure AD 租用戶相同訂閱關聯的全新或現有檔案共用。 如需建立新檔案共用的資訊，請參閱 [在 Azure 檔案中建立檔案共用](storage-how-to-create-file-share.md)。
    為了達到最佳效能，建議您將檔案共用與您打算用來存取共用的 VM 位於相同的區域中。

1.  **藉由使用儲存體帳戶金鑰裝載 Azure 檔案共用，驗證 Azure 檔案連線。**

    若要驗證是否已正確設定虛擬機器和檔案共用，請嘗試使用儲存體帳戶金鑰裝載檔案共用。 如需詳細資料，請參閱[裝載 Azure 檔案共用並在 Windows 中存取共用](storage-how-to-use-files-windows.md)。

## <a name="regional-availability"></a>區域可用性

在 [所有 Azure 公用和 Gov 區域](https://azure.microsoft.com/global-infrastructure/locations/)中都有提供 Azure AD DS 的 Azure 檔案儲存體驗證。

## <a name="overview-of-the-workflow"></a>工作流程概觀

針對 Azure 檔案共用啟用透過 SMB 進行 Azure AD DS 驗證之前，請確認已正確設定您的 Azure AD 和 Azure 儲存體環境。 建議您逐步完成 [必要條件](#prerequisites) ，以確保您已完成所有必要的步驟。

接下來，請執行下列動作，以 Azure AD 認證來授與 Azure 檔案儲存體資源的存取權：

1. 為您的儲存體帳戶啟用透過 SMB Azure AD DS 驗證，以向相關聯的 Azure AD DS 部署註冊儲存體帳戶。
2. 將共用的存取權限指派至 Azure AD 身分識別 (使用者、群組或服務主體)。
3. 透過 SMB 為目錄和檔案設定 NTFS 權限。
4. 從已加入網域的虛擬機器中裝載 Azure 檔案共用。

下圖說明透過 SMB 啟用 Azure AD DS 驗證以進行 Azure 檔案儲存體的端對端工作流程。

![此圖顯示針對 Azure 檔案使用「透過 SMB 進行 Azure AD 驗證」的工作流程](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>為您的帳戶啟用 Azure AD DS 驗證

若要啟用透過 SMB 進行 Azure AD DS 驗證以進行 Azure 檔案儲存體，您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來設定儲存體帳戶的屬性。 設定此屬性時，會隱含地「網域聯結」具有相關聯 Azure AD DS 部署的儲存體帳戶。 接著，會針對儲存體帳戶中的所有新的和現有的檔案共用，啟用透過 SMB Azure AD DS 驗證。

請記住，只有在您成功將 Azure AD DS 部署到 Azure AD 租使用者之後，才可以啟用透過 SMB Azure AD DS 驗證。 如需詳細資訊，請參閱 [必要條件](#prerequisites)。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要使用 [Azure 入口網站](https://portal.azure.com)透過 SMB 啟用 Azure AD DS 驗證，請遵循下列步驟：

1. 在 Azure 入口網站中，移至您現有的儲存體帳戶，或 [建立儲存體帳戶](../common/storage-account-create.md)。
1. 在 [設定]  區段中，選取 [組態]  。
1. 在檔案共用的 [ **以身分識別為基礎的存取** ] 下，將 **Azure Active Directory 網域服務 (AAD DS)** 切換為 [ **已啟用** ]。
1. 選取 [儲存]。

下圖顯示如何針對您的儲存體帳戶啟用透過 SMB 進行 Azure AD DS 驗證。

![在 Azure 入口網站中啟用透過 SMB Azure AD DS 驗證](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 啟用透過 SMB Azure AD DS 驗證，請安裝最新的 Az 模組 (2.4 或更新版本的) 或 Az. Storage 模組 (1.5 或更新版本的) 。 如需安裝 PowerShell 的詳細資訊，請參閱 [使用 PowerShellGet 在 Windows 上安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

若要建立新的儲存體帳戶，請呼叫 [new-new-azstorageaccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)，然後將 **EnableAzureActiveDirectoryDomainServicesForFile** 參數設定為 **true** 。 在下列範例中，請記得使用您自己的值來取代預留位置值。  (如果您使用先前的預覽模組，則啟用功能的參數是 **>enableazurefilesaadintegrationforsmb** 。 ) 

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


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 啟用透過 SMB 進行 Azure AD 驗證，請安裝最新的 CLI 版本 (版本2.0.70 或更新版本的) 。 如需安裝 Azure CLI 的詳細資訊，請參閱 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要建立新的儲存體帳戶，請呼叫 [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)，然後將 `--enable-files-aadds` 屬性設定為 **true** 。 在下列範例中，請記得使用您自己的值來取代預留位置值。  (如果您使用先前的預覽模組，功能啟用的參數會是檔案 **aad** 。 ) 

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

若要在現有的儲存體帳戶上啟用這項功能，請使用下列命令：

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

您現在已成功啟用透過 SMB Azure AD DS 驗證，並且指派自訂角色，以使用 Azure AD 身分識別來存取 Azure 檔案共用。 若要授與其他使用者對檔案共用的存取權，請遵循「 [指派存取權限](#assign-access-permissions-to-an-identity) 來使用身分識別和 [設定 NTFS 許可權的 SMB 區段](#configure-ntfs-permissions-over-smb)」中的指示。

## <a name="next-steps"></a>後續步驟

如需有關 Azure 檔案儲存體的詳細資訊，以及如何透過 SMB 使用 Azure AD，請參閱下列資源：

- [支援 Azure 檔案儲存體身分識別型驗證進行 SMB 存取的概觀](storage-files-active-directory-overview.md)
- [常見問題集](storage-files-faq.md)
