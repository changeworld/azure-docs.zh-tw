---
title: 使用 Azure AD 域服務授權通過 SMB 訪問檔資料
description: 瞭解如何通過 Azure 活動目錄域服務通過伺服器訊息區 （SMB） 為 Azure 檔啟用基於身份的身份驗證。 然後，加入域的 Windows 虛擬機器 （VM） 可以使用 Azure AD 憑據訪問 Azure 檔共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e2e3c7763a13c8850554b079a426ed4172b74d28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599271"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>在 Azure 檔上啟用 Azure 活動目錄域服務身份驗證

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

有關 Azure 檔共用的 Azure AD 身份驗證概述，請參閱[Azure 檔通過 SMB 進行 Azure 活動目錄身份驗證的概述](storage-files-active-directory-overview.md)。 本文重點介紹如何在 Azure 檔上啟用 Azure 活動目錄域服務 （Azure AD DS） 的身份驗證。

> [!NOTE]
> Azure 檔支援使用 RC4-HMAC 加密的 Azure AD DS 進行 Kerberos 身份驗證。 AES Kerberos 加密尚不受支援。

## <a name="prerequisites"></a>Prerequisites

在為 Azure 檔共用啟用 Azure AD 過 SMB 之前，請確保已完成以下先決條件：

1.  **選取或建立 Azure AD 租用戶。**

    您可以針對「透過 SMB 進行 Azure AD 驗證」使用全新或現有租用戶。 租用戶和您想要存取的檔案共用必須與相同的訂用帳戶相關聯。

    若要建立新的 Azure AD 租用戶，您可以[新增 Azure AD 租用戶和 Azure AD 訂用帳戶](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果您有現有的 Azure AD 租戶，但希望創建新租戶以與 Azure 檔共用一起使用，請參閱[創建 Azure 活動目錄租戶](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)。

1.  **在 Azure AD 租用戶上啟用 Azure AD Domain Services。**

    若要支援使用 Azure AD 認證進行驗證，您必須為 Azure AD 租用戶啟用 Azure AD Domain Services。 如果您不是 Azure AD 租用戶的系統管理員，請連絡系統管理員，並遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) 的逐步指導。

    Azure AD DS 部署通常需要大約 15 分鐘才能完成。 在繼續下一步之前，驗證 Azure AD DS 的運行狀況是否顯示 **"正在運行**"，並啟用了密碼雜湊同步。

1.  **域將 Azure VM 與 Azure AD DS 聯接。**

    要使用來自 VM 的 Azure AD 憑據訪問檔共用，VM 必須已加入 Azure AD DS。 如需如何加入虛擬機器網域的詳細資訊，請參閱[將 Windows Server 虛擬機器加入受控網域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > Azure AD DS 身份驗證上具有 Azure 檔共用，僅在 Windows 7 或 Windows 伺服器 2008 R2 上方作業系統版本的 Azure VM 上支援。

1.  **選取或建立 Azure 檔案共用。**

    選取與 Azure AD 租用戶相同訂閱關聯的全新或現有檔案共用。 如需建立新檔案共用的資訊，請參閱 [在 Azure 檔案中建立檔案共用](storage-how-to-create-file-share.md)。
    為了獲得最佳性能，我們建議您的檔共用與計畫從其中訪問共用的 VM 位於同一區域。

1.  **藉由使用儲存體帳戶金鑰裝載 Azure 檔案共用，驗證 Azure 檔案連線。**

    若要驗證是否已正確設定虛擬機器和檔案共用，請嘗試使用儲存體帳戶金鑰裝載檔案共用。 如需詳細資料，請參閱[裝載 Azure 檔案共用並在 Windows 中存取共用](storage-how-to-use-files-windows.md)。

## <a name="overview-of-the-workflow"></a>工作流程概觀

在為 Azure 檔共用啟用通過 SMB 進行 Azure AD DS 身份驗證之前，請驗證 Azure AD 和 Azure 存儲環境是否配置正確。 我們建議您演練[先決條件](#prerequisites)，以確保已完成所有必需的步驟。

接下來，執行以下操作以授予使用 Azure AD 憑據的 Azure 檔資源的許可權：

- 為存儲帳戶通過 SMB 啟用 Azure AD DS 身份驗證，以便將存儲帳戶註冊到關聯的 Azure AD DS 部署。
- 將共用的存取權限指派至 Azure AD 身分識別 (使用者、群組或服務主體)。
- 透過 SMB 為目錄和檔案設定 NTFS 權限。
- 從已加入網域的虛擬機器中裝載 Azure 檔案共用。

下圖說明瞭用於通過 SMB 對 Azure 檔啟用 Azure AD DS 身份驗證的端到端工作流。

![此圖顯示針對 Azure 檔案使用「透過 SMB 進行 Azure AD 驗證」的工作流程](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>為您的帳戶啟用 Azure AD DS 身份驗證

要通過 Azure 檔的 SMB 啟用 Azure AD DS 身份驗證，可以使用 Azure 門戶、Azure PowerShell 或 Azure CLI 在存儲帳戶上設置屬性。 將此屬性隱式設置"域聯接"存儲帳戶與關聯的 Azure AD DS 部署。 然後，對存儲帳戶中的所有新檔和現有檔共用啟用通過 SMB 的 Azure AD DS 身份驗證。

請記住，只有在成功地將 Azure AD DS 部署到 Azure AD 租戶後，才能通過 SMB 啟用 Azure AD DS 身份驗證。 有關詳細資訊，請參閱[先決條件](#prerequisites)。

### <a name="azure-portal"></a>Azure 入口網站

要使用[Azure 門戶](https://portal.azure.com)通過 SMB 啟用 Azure AD DS 身份驗證，請按照以下步驟操作：

1. 在 Azure 門戶中，轉到現有存儲帳戶，或[創建存儲帳戶](../common/storage-account-create.md)。
1. 在 [設定]**** 區段中，選取 [組態]****。
1. 在**基於身份的檔共用訪問**下，將**Azure 活動目錄域服務 （AAD DS） 的**切換切換到**啟用**。
1. 選取 [儲存]****。

下圖演示如何通過存儲帳戶的 SMB 啟用 Azure AD DS 身份驗證。

![在 Azure 門戶中通過 SMB 啟用 Azure AD DS 身份驗證](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

要使用 Azure PowerShell 通過 SMB 啟用 Azure AD DS 身份驗證，請安裝最新的 Az 模組（2.4 或更高版本）或 Az.Storage 模組（1.5 或更高）。 有關安裝 PowerShell 的詳細資訊，請參閱[使用 PowerShell 在 Windows 上安裝 Azure PowerShell。](https://docs.microsoft.com/powershell/azure/install-Az-ps)

要創建新的存儲帳戶，請調用[New-AzStorage 帳戶](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)，然後將**啟用 AzureActiveDirectoryDomain服務ForFile**參數設置為**true**。 在下面的示例中，請記住將預留位置值替換為您自己的值。 （如果使用上一個預覽模組，則功能啟用的參數為**啟用 AzureFilesAad 集成為 SMB**。

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

要在現有存儲帳戶上啟用此功能，請使用以下命令：

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

要使用 Azure CLI 通過 SMB 啟用 Azure AD 身份驗證，請安裝最新的 CLI 版本（版本 2.0.70 或更高版本）。 有關安裝 Azure CLI 的詳細資訊，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

要創建新的存儲帳戶，請調用[az 存儲帳戶創建](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)，`--enable-files-aadds`並將屬性設置為**true**。 在下面的示例中，請記住將預留位置值替換為您自己的值。 （如果使用上一個預覽模組，則功能啟用的參數為**file-ad**.）

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

要在現有存儲帳戶上啟用此功能，請使用以下命令：

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

現在，您已成功通過 SMB 啟用 Azure AD DS 身份驗證，並分配了一個自訂角色，該角色提供使用 Azure AD 標識訪問 Azure 檔共用的功能。 要授予其他使用者對檔共用的存取權限，請按照["分配存取權限"](#assign-access-permissions-to-an-identity)中的說明使用標識並在[SMB 部分上配置 NTFS 許可權](#configure-ntfs-permissions-over-smb)。

## <a name="next-steps"></a>後續步驟

有關 Azure 檔以及如何在 SMB 上使用 Azure AD 的詳細資訊，請參閱以下資源：

- [基於 Azure 檔的基於身份的 SMB 訪問的身份驗證支援的概述](storage-files-active-directory-overview.md)
- [常見問題集](storage-files-faq.md)
